# **DNS & Name Resolution**  
### with

<img src="images/ddev-logo.svg" alt="DDEV Logo" class="ddev-logo">


---

## Training Overview

**Goal**: Understand how DDEV resolves hostnames and troubleshoot when things go wrong

**What we'll cover**:
- How URLs and DNS work (basics)
- DDEV's default resolution (*.ddev.site) 
- When DNS fails and hosts file fallback
- Key configuration options
- Troubleshooting common issues

**Hands-on**: We'll demo broken setups and fix them together

Note:
Set expectations - this is practical, not deep DNS theory. Focus on DDEV-specific scenarios.

---

## How URLs work
- **Protocol**: `http://` or `https://`
- **Domain**: `example.com`
- **Port**: `80` or `443` (omitted if default)
- **Path**: `/` or `/path/to/page`
- **Full URL**: `https://example.com/path` (port 443)


---

## Why DNS/Hostname Resolution Matters
- It's important to use "normal" hostnames in local development instead of weird IP addresses with ports.
- DDEV uses hostnames: `myproject.ddev.site` 
- and URLs like `https://myproject.ddev.site`
- Your browser uses the hostname in the URL to figure out where to go for the content.
- Understanding resolution = faster troubleshooting

Note:
Consider demoing a non-existent hostname for a quick failure example.

---

## What is DNS?
- "Domain Name System"
- “Phone book” that maps names → IPs
- Basic flow: Root → TLD → Authoritative server
- Recursive resolvers, caching, TTLs

Note:
Keep it high-level. Emphasize that DNS is distributed and cached at many layers.

---

## Resolution Path (Typical)
1. **Hosts file**
2. **OS / Local resolver cache**
3. **Network DNS** (ISP, corporate, DoH/DoT)
4. Returns the **first match**

Note:
Mention OS differences briefly and that hosts file usually wins over DNS.

---

## Hosts File Basics
- macOS/Linux: `/etc/hosts`
- Windows: `C:\Windows\System32\drivers\etc\hosts`
- Needs elevated permissions to edit
- **No wildcards** supported with hosts file

Note:
`cat /etc/hosts` to show 3.example.com

---

## Wildcards & Custom Domains
- Wildcard DNS: `*.example.com`
- Hosts file **cannot** do wildcards
- DDEV config: `additional_hostnames`, `additional_fqdns`

Note:
Clarify the difference: short names vs FQDNs, and why teams might add explicit names.

---

## Default Resolution Setup
- `*.ddev.site` → `127.0.0.1` (loopback)
- The `ddev.site` DNS is managed by DDEV
- Mostly only the browser cares about the resolution
- But after browser looks up 127.0.0.1, it delivers that to the `ddev-router`
- Then `ddev-router` (traefik) uses hostname in the URL to route to the correct project
- Works out-of-the-box (no custom DNS setup)

Note:
Quick diagram verbally: Browser → 127.0.0.1 → DDEV router → project web container.

---

## When DNS Isn’t Enough
- Offline usage (no DNS lookup)
- Fritzbox Routers (Aargh!)
- Broken or otherwise unavailable DNS
- **Fallback**: hosts file entries for the project

Note:
This is where explicit additional hostnames help.

---

## Key DDEV Settings
- `project_tld`: change `.ddev.site`
- `use_dns_when_possible: true|false`
- `additional_hostnames` (short)
- `additional_fqdns` (Avoid real sites!)

```yaml
# .ddev/config.yaml (excerpt)
project_tld: ddev.site
use_dns_when_possible: true
additional_hostnames:
  - admin
  - api
additional_fqdns:
  - shop.example.com
```
Note:
Explain practical scenarios and remind that hosts fallback doesn’t support wildcards.

---

## `ddev-hostname`
- Small helper binary for privileged hosts changes
- Reduces security risk by limiting elevated actions
- Works on macOS, Linux, Windows, WSL2

Note:
Tie this to least-privilege discussion. Only hosts updates need elevation.

---

## Troubleshooting Checklist
- `ping xxx.ddev.site` → expect `127.0.0.1`
- Inspect hosts file entries
- Flush DNS cache
- Check VPN / corporate DNS / Zscaler
- Remember: **no wildcards** in hosts file

```bash
# Examples
ping test.ddev.site
nslookup test.ddev.site
# On macOS
sudo dscacheutil -flushcache; sudo killall -HUP mDNSResponder
# On Windows (Admin)
ipconfig /flushdns
```


## Best Practices
- Use the defaults! `ddev.site` with DNS lookup
- Prefer DNS (`use_dns_when_possible: true`)
- Avoid collisions with real domains

Note:
Provide a short team checklist at the end or as a gist.

---

## Extra (If Time)
- `ddev share` (with ngrok)
- Tailscale for DDEV
- Local DNS servers (dnsmasq, etc.)
- HTTPS certs with custom domains

Note:
Optional deep dives; avoid rabbit holes if time is short.

---

## References
- DDEV Name Resolution & Wildcards  
  https://ddev.com/blog/ddev-name-resolution-wildcards/
- `ddev-hostname` Security Improvements  
  https://ddev.com/blog/ddev-hostname-security-improvements/
- DDEV Contributor trainings  
  https://ddev.com/blog/category/training/

Note:
Consider sending these links ahead of the session.

---

