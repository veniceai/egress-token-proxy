# egress-token-proxy

Release binaries of `token-proxy`, a small loopback proxy that Venice's Amp orbs use for outbound
traffic. Clients send it ordinary `HTTP_PROXY`/`HTTPS_PROXY` requests without credentials;
it forwards them to an upstream [Smokescreen](https://github.com/stripe/smokescreen) proxy with
an OIDC ID token from a command such as `amp orb id-token`, renewing the token before it expires.

The binaries are static Linux builds for `amd64` and `arm64`, attached to each
[release](https://github.com/veniceai/egress-token-proxy/releases) with a `SHA256SUMS` file.
They hold no configuration or credentials: the upstream, audience, and token command are flags.
This repository holds no source; each release names the commit and Go version it was built from.

## Install

Pin the sha256 of the release you use, and check it before installing:

```sh
version=v0.1.0
sha256=<sha256 of token-proxy-linux-amd64 from SHA256SUMS>
curl -fsSL -o /tmp/token-proxy \
  "https://github.com/veniceai/egress-token-proxy/releases/download/$version/token-proxy-linux-amd64"
echo "$sha256  /tmp/token-proxy" | sha256sum -c -
sudo install -m 0755 /tmp/token-proxy /usr/local/bin/token-proxy
```

## Run

```sh
token-proxy \
  --listen 127.0.0.1:4750 \
  --upstream http://<smokescreen host>:4750 \
  --token-command 'amp orb id-token --audience <audience>'
```

Run `token-proxy --help` for every flag. Anyone who can reach the listening address can use the
token, so keep it on the loopback interface.

## License

MIT; see [LICENSE](LICENSE).
