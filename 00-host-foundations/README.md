# P0 — Host & Foundations

## Lab isolation
How I isolated the lab in VirtualBox: every VM runs on Internal Networks
(lab-lan/servers/guest/mgmt); Shared Clipboard and Drag'n'Drop disabled; no
shared folders.

## Key decisions
- Verify download hashes: an altered image = a compromises lab from day one.
- Public repo: it's portfolio evidence employers can read.
- Push protection: stops me ever committing a key or password by accident.

## Download verification (SHA-256)

All images verified before use — hash computed with `Get-FileHash -Algorithm SHA256`, 
compared to the vendor's published value. ✅ = exact match.

| File | Version | SHA-256 | Verified |
| --- | --- | --- | --- |
| OPNsense (dvd, amd64) | 26.7 | `95CAFEDDA6D5B22CE832E249DC2309110FBEE19F813AD78CF28BB3D387186BFB` | ✅ |
| Kali (VirtualBox image) | 2026.3 | `84863C4FA46B2F1B29B152348583C6FB20F68F91971617C0D684E0EB163AAB6D` | ✅ |

## Evidence
<img width="980" height="190" alt="hash-check-P0-nov-7" src="https://github.com/user-attachments/assets/88c5b4bb-fab8-40fa-bac4-62b35108da2a" />
<img width="1183" height="231" alt="push-protection" src="https://github.com/user-attachments/assets/541ca676-2a94-4c64-9b13-a3967730d92b" />
