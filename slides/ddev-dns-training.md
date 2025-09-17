# **DNS & Name Resolution**  
### with

<img src="images/ddev-logo.svg" alt="DDEV Logo" class="ddev-logo">


Note:
Introduce the goal: understand name resolution basics and how DDEV uses them. Informal, hands-on tone.

---

## Why This Matters
- Local dev uses hostnames (e.g., `myproj.ddev.site`)
- If names don’t resolve → sites don’t load
- Understanding resolution = faster troubleshooting

Note:
Consider demoing a non-existent hostname for a quick failure example.

---

## What is DNS?
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
- **No wildcards** supported

Note:
Show an example: `127.0.0.1 foo.local` then ping/browse.

---

## Wildcards & Custom Domains
- Wildcard DNS: `*.example.com`
- Hosts file **cannot** do wildcards
- DDEV config: `additional_hostnames`, `additional_fqdns`

Note:
Clarify difference: short names vs FQDNs, and why teams might add explicit names.

---

## How DDEV Resolves by Default
- `*.ddev.site` → `127.0.0.1` (loopback)
- DDEV-router uses hostname to route to the correct project
- Works out-of-the-box (no custom DNS setup)

Note:
Quick diagram verbally: Browser → 127.0.0.1 → DDEV router → project web container.

---

## When DNS Isn’t Enough
- Offline usage (no DNS lookup)
- Corporate DNS blocks wildcard queries
- **Fallback**: hosts file entries for the project

Note:
This is where explicit additional hostnames help.

---

## Key DDEV Settings
- `project_tld`: change `.ddev.site`
- `use_dns_when_possible: true|false`
- `additional_hostnames` (short)
- `additional_fqdns` (FQDNs)

```yaml
# .ddev/config.yaml (excerpt)
project_tld: ddev.site
use_dns_when_possible: true
additional_hostnames:
  - admin
  - api
additional_fqdns:
  - shop.example.test
```
Note:
Explain practical scenarios and remind that hosts fallback doesn’t support wildcards.

---

## Security Considerations
- **DNS rebinding** risks (malicious DNS → localhost)
- Hosts file can override real domains (dangerous if careless)
- Principle of least privilege

Note:
Encourage attendees to avoid “real” production domains in local configs.

---

## `ddev-hostname`
- Small helper binary for privileged hosts changes
- Reduces security risk by limiting elevated actions
- Works on macOS, Linux, Windows, WSL2

Note:
Tie this to least-privilege discussion. Only hosts updates need elevation.

---

## Troubleshooting Checklist
- `ping test.ddev.site` → expect `127.0.0.1`
- Inspect hosts file entries
- Flush DNS cache
- Check VPN / corporate DNS / Zscaler
- Remember: **no wildcard** in hosts file

```bash
# Examples
ping test.ddev.site
nslookup test.ddev.site
# On macOS
sudo dscacheutil -flushcache; sudo killall -HUP mDNSResponder
# On Windows (Admin)
ipconfig /flushdns
```
Note:
Have a prepared “broken” config to fix live.

---

## Best Practices
- Stick with defaults unless needed
- Prefer DNS (`use_dns_when_possible: true`)
- Avoid collisions with real domains
- Keep hosts clean & consistent across team

Note:
Provide a short team checklist at the end or as a gist.

---

## Extra (If Time)
- Local DNS servers (dnsmasq, etc.)
- HTTPS certs with custom domains
- DNS over HTTPS/DoT
- Cross-OS quirks (macOS vs Linux vs Windows)

Note:
Optional deep dives; avoid rabbit holes if time is short.

---

## References (Pre‑Reading)
- DDEV Name Resolution & Wildcards  
  https://ddev.com/blog/ddev-name-resolution-wildcards/
- DDEV Hostname Security Improvements  
  https://ddev.com/blog/ddev-hostname-security-improvements/
- Trainings category  
  https://ddev.com/blog/category/training/

Note:
Consider sending these links ahead of the session.

---

<!-- .slide: data-background-color="#0b2d4d" -->

# **Q&A + Hands‑On**
<img src="https://raw.githubusercontent.com/ddev/ddev/main/docs/content/developers/logos/SVG/DDEV-LogoWordmark.svg" alt="DDEV" style="height:56px;margin-top:24px;">