# AdGuard-Lists

Eigene Block- und Whitelists für **AdGuard Home** und **PiHole**.

## Listen

| Datei | Inhalt | Zweck |
|---|---|---|
| `phishing-list` | Suspekte Domains aus E-Mail-Phishing/Spam | Blocken |
| `phishing-patterns` | Regex-Filter und TLD-Wildcards | Blocken (mit Warnung — siehe unten) |
| `tracking-list` | Marketing-, Newsletter-, Tracking-Infrastruktur | Optional blocken |
| `whitelist-all` | Allow-Regeln (`@@`-Format) für legitime Domains | Whitelist |
| `ip-whitelist` | IP-Bereiche (Outlook, Netflix, Google Mail …) | Referenz |

## Verwendung in AdGuard Home

**Block-Listen** (DNS-Blocklisten -> Liste hinzufügen):

```
https://raw.githubusercontent.com/tdierks28/AdGuard-Lists/main/phishing-list
https://raw.githubusercontent.com/tdierks28/AdGuard-Lists/main/tracking-list
https://raw.githubusercontent.com/tdierks28/AdGuard-Lists/main/phishing-patterns
```

**Whitelist** (DNS-Allowlists -> Liste hinzufügen):

```
https://raw.githubusercontent.com/tdierks28/AdGuard-Lists/main/whitelist-all
```

## Warnung — `phishing-patterns`

Diese Liste enthält **TLD-Wildcards** wie `||*.tk^`, `||*.xyz^`, `||*.top^`, `||*.com.br^`, `||*.cfd^`, `||*.fun^`, `||*.love^`, `||*.xn^`. Sie blockieren **alle** Domains der jeweiligen Top-Level-Domain — z. B. blockiert `||*.com.br^` ganz Brasilien, `||*.xyz^` Millionen legitimer Sites. Vor Aktivierung prüfen, ob das gewünscht ist.

## Pflege

- Manuell gepflegt aus eingehenden Phishing-/Spam-Mails.
- Bereinigung 2026-04-27: Duplikate entfernt, legitime Domains (Banken, Behörden, Stadtwerke, Konzerne) in `whitelist-all` verschoben, Tracking-Domains aus der Phishing-Liste in eigene `tracking-list` ausgegliedert.
- Verbesserungen, False Positives, Hinweise → gerne als Issue oder PR.
