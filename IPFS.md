# IPFS Deployment

InstanceIO can build a static site, publish the contents of `dist/` as the CID root, verify the local content, start a local Kubo gateway, and optionally submit the CID to a remote pinning service.

IPFS serves files, not a PHP runtime. Dynamic controllers and API routes must either be rendered into the static build or remain on a separate HTTPS service.

## Requirements

Install Kubo and make sure the `ipfs` executable is on `PATH`:

```bash
ipfs version
```

From the application root, initialize and inspect the local repository:

```bash
php console/cmd.php Ipfs setup
php console/cmd.php Ipfs status
```

IIO does not install Kubo. Use `--binary=/path/to/ipfs` when the executable is not on `PATH`, or `--ipfs-path=/path/to/repository` for a non-default repository.

## Build and publish locally

Build and publish the site in one command:

```bash
php console/cmd.php Ipfs deploy --build
```

Publish an existing validated build:

```bash
php console/cmd.php Build
php console/cmd.php Ipfs deploy
```

The deployment command:

1. Validates the IIO build marker.
2. Refuses symlinks and special files.
3. Adds the contents of `dist/` as the CID root.
4. Pins the root CID locally by default.
5. Verifies the root directory and generated index files.
6. Prints the CID and local gateway URL.

The root layout is:

```text
/ipfs/<CID>/index.html
/ipfs/<CID>/assets/...
```

IIO does not wrap the build in another `dist/` directory.

## Verify and serve a CID

Verify an existing local CID without starting the daemon:

```bash
php console/cmd.php Ipfs check --cid=<CID>
```

Verify the CID and start the foreground Kubo daemon:

```bash
php console/cmd.php Ipfs serve --cid=<CID>
```

Wait for:

```text
Daemon is ready
```

Then open:

```text
http://127.0.0.1:8080/ipfs/<CID>/
```

Keep the terminal running. Press `Ctrl-C` to stop the daemon. IIO checks the configured API socket rather than relying only on `ipfs id`, because Kubo can read a local repository even when no daemon is listening.

## Static HTMx/API routes

IPFS cannot execute PHP API controllers. If an HTMx demo uses `/api/example/`, include that route in the build manifest:

```json
[
  "/",
  "/api/example/"
]
```

The build then requests the real endpoint and writes its response to:

```text
dist/api/example/index.html
```

The generated page can request `api/example/` under the CID. The response is a build-time snapshot. A live, changing API must remain on a dynamic HTTPS host.

## Remote pinning with Pinata

A local recursive pin only protects the CID in the local Kubo repository. Use a remote pinning provider to keep the content available when the local node is offline or unreachable.

Create a Pinata API key with pinning permissions and copy its JWT. Keep the token in an environment variable:

```bash
read -s PINATA_JWT
export PINATA_JWT
```

Configure Pinata through Kubo:

```bash
php console/cmd.php Ipfs remote setup \
  --service=pinata \
  --endpoint=https://api.pinata.cloud/psa \
  --token-env=PINATA_JWT
```

Pin an existing CID:

```bash
php console/cmd.php Ipfs remote pin \
  --service=pinata \
  --cid=<CID>
```

Check the provider status:

```bash
php console/cmd.php Ipfs remote status \
  --service=pinata \
  --cid=<CID>
```

Wait for the remote status to become `pinned`. IIO does not accept the secret as a command-line value or print it. Kubo stores the configured service credentials in its local repository.

## Pin during deployment

After configuring the service, submit every deployment after local verification:

```bash
php console/cmd.php Ipfs deploy \
  --build \
  --remote \
  --remote-service=pinata
```

Queue the request without waiting for the provider to finish:

```bash
php console/cmd.php Ipfs deploy \
  --build \
  --remote \
  --remote-service=pinata \
  --remote-background
```

Remote settings can also be placed in `config.json`:

```json
{
  "ipfs": {
    "binary": "ipfs",
    "path": "~/.ipfs",
    "gateway": "http://127.0.0.1:8080/ipfs",
    "cid_version": 1,
    "pin": true,
    "remote": {
      "service": "pinata",
      "endpoint": "https://api.pinata.cloud/psa",
      "token_env": "PINATA_JWT",
      "name": "my-iio-site",
      "background": false,
      "deploy": true
    }
  }
}
```

With `deploy: true`, `Ipfs deploy --build` submits the CID automatically. Never commit the JWT to application configuration or source control.

## Gateways, NAT, and availability

`ipfs.io` is no longer a dependable public gateway. Use the gateway supplied by your remote pinning provider or another active gateway.

A node behind double NAT or carrier-grade NAT can make outbound connections but may not accept direct inbound connections. Local browsing still works. Remote pinning is recommended for public availability.

The warning can be hidden with:

```bash
ipfs config --json Internal.CGNATCheck false
```

That only hides the notice; it does not improve network reachability. For a public self-hosted node, ask the ISP for a public address, configure IPv6, or use a suitable relay/NAT setup.

## Immutable releases

A changed build produces a new CID. Old CIDs never change and remain available only while at least one node or pinning provider retains the content.
