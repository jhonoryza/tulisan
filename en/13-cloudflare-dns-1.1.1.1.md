# Cloudflare DNS 1.1.1.1 Guide: Choose the Right Filter for Your Needs

Cloudflare provides the free public DNS service `1.1.1.1`, known for being fast, private, and global. Besides the standard unfiltered version, Cloudflare also offers variants with automatic filtering to block malware and adult content — just change the DNS address on your device.

## Cloudflare DNS Variantts Summary

| Variant | DNS Address | Purpose |
| :--- | :--- | :--- |
| **Standard (No Filter)** | `1.1.1.1` and `1.0.0.1` | Fastest DNS without filtering. Only for fast and private lookups |
| **Block Malware** | `1.1.1.2` and `1.0.0.2` | Automatically block malware & phishing at the network level |
| **Block Malware + Adult Content** | `1.1.1.3` and `1.0.0.3` | Block malware + adult content (suitable for family / parental control) |

---

## 1. Standard - `1.1.1.1` and `1.0.0.1`

This is Cloudflare's standard public DNS:

* **Fast** — Cloudflare global network (average <15ms)
* **Private** — does not sell data, supports DNS-over-HTTPS (DoH) and DNS-over-TLS (DoT)
* **Zero filtering** — does not block any content, all lookups are forwarded as-is

Ideal if you only need speed and privacy without blocking.

```
1.1.1.1
1.0.0.1
```

## 2. Block Malware & Phishing - `1.1.1.2` and `1.0.0.2`

Variant ini memblokir ancaman di level jaringan sebelum halaman sempat dimuat:

* Blocks known malware domains
* Blocks phishing / scam sites
* Still as fast and private as the standard variant

Suitable for general use, offices, or personal laptops wanting extra protection without blocking adult content.

```
1.1.1.2
1.0.0.2
```

## 3. Block Malware + Adult Content - `1.1.1.3` and `1.0.0.3`

Variant paling ketat, menggabungkan proteksi malware + filter konten dewasa:

* All protection from `1.1.1.2` (malware & phishing)
* Plus blocking of adult / pornographic sites
* Ideal for **Family DNS** — homes with children, schools, or public networks

```
1.1.1.3
1.0.0.3
```

---

## How to Change DNS

### On Router (applies to all devices on the network)

Log in to router admin `192.168.1.1` → find DNS / WAN menu → change Primary & Secondary DNS to the variant above → Save & Reboot router.

### On Windows

Settings → Network & Internet → Change adapter options → right-click active connection → Properties → IPv4 → Use the following DNS server addresses → enter `1.1.1.x` and `1.0.0.x`.

### On macOS

System Settings → Network → select connection → Details → DNS → remove old DNS → add `1.1.1.x` and `1.0.0.x`.

### On Android / iOS

Settings → Wi-Fi → tap connected network → Configure DNS / IP Settings → Manual → add DNS servers.

### DoH / DoT (optional, more private)

* DoH URL: `https://cloudflare-dns.com/dns-query`
* DoT Host: `one.one.one.one` or `1.1.1.1`

For filtered variants, use:
* Malware: `https://security.cloudflare-dns.com/dns-query`
* Family: `https://family.cloudflare-dns.com/dns-query`

---

## Which One Should You Choose?

* **Need the fastest without blocking?** → `1.1.1.1` / `1.0.0.1`
* **Need malware/phishing protection?** → `1.1.1.2` / `1.0.0.2`
* **Need full protection for kids/family?** → `1.1.1.3` / `1.0.0.3`

All variants are free, no registration required, and can be changed at any time.

---

## References

* Official docs: https://one.one.one.one/family/
* Cloudflare Blog: https://blog.cloudflare.com/introducing-1-1-1-1-for-families/
