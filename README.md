# AdGuard-Lists

Eigene Block- und Whitelists gegen Phishing und Spam für **pfBlockerNG**, **AdGuard Home** und **Pi-hole**.

## Welche Liste wofür?

| Datei | Inhalt | Format | Einsetzen in |
|---|---|---|---|
| `phishing-list` | Phishing- und Spam-Domains, jede Domain und Subdomain einzeln | eine Domain pro Zeile | pfBlockerNG (DNSBL), hosts-basierte Filter, alle Systeme |
| `phishing-list-wildcard` | Elterndomains, unter denen mehrere Phishing-Subdomains auftraten | eine Domain pro Zeile | pfBlockerNG mit „Wildcard Blocking (TLD)“ |
| `phishing-list-adguard` | Inhalt von `phishing-list` und `phishing-list-wildcard` zusammen | `\|\|domain^` | AdGuard Home, Pi-hole |
| `phishing-patterns` | Regex-Filter und TLD-Wildcards | Adblock-Syntax | AdGuard Home, mit Vorsicht (siehe unten) |
| `tracking-list` | Marketing-, Newsletter-, Tracking-Infrastruktur | eine Domain pro Zeile | optional, alle Systeme |
| `whitelist-all` | Allow-Regeln für legitime Domains | `@@\|domain^` / `@@\|\|domain^` | AdGuard Home (Allowlist) |
| `ip-whitelist` | IP-Bereiche (Outlook, Netflix, Google Mail …) | IP/CIDR | Referenz |

### Warum drei Fassungen der Phishing-Liste?

Eine einfache Zeile `example.org` sperrt in AdGuard Home und Pi-hole **nur genau diese Domain**, nicht `www.example.org`. Subdomains sperren diese Systeme nur mit `||example.org^`. pfBlockerNG dagegen arbeitet mit einfachen Domainlisten und sperrt Subdomains nur, wenn „Wildcard Blocking (TLD)“ aktiv ist.

- **`phishing-list`** ist die Grundliste und funktioniert überall. Jede bekannte Phishing-Subdomain steht einzeln drin.
- **`phishing-list-wildcard`** enthält nur Domains, die als Ganzes einem Angreifer zuzuordnen sind, etwa weil dort viele wechselnde Subdomains auftauchten. Mit Wildcard-Blocking sperrt ein Eintrag auch künftige Subdomains. Ohne Wildcard-Funktion sperrt er nur die Domain selbst.
- **`phishing-list-adguard`** fasst beides für AdGuard Home und Pi-hole zusammen. Für diese Systeme reicht diese eine Liste.

## Verwendung

### pfBlockerNG

Firewall → pfBlockerNG → DNSBL → DNSBL Groups, je Liste ein Feed:

```
https://raw.githubusercontent.com/tdierks28/AdGuard-Lists/main/phishing-list
https://raw.githubusercontent.com/tdierks28/AdGuard-Lists/main/phishing-list-wildcard
```

Für `phishing-list-wildcard` muss „Wildcard Blocking (TLD)“ aktiv sein. Hinweise aus Forum und Issue-Tracker: Die Funktion braucht reichlich RAM (empfohlen ab 4 GB) und soll im Python-Modus von Unbound teils wirkungslos sein. Nach dem Aktivieren mit einer Domain aus der Liste prüfen.

### AdGuard Home

Filter → DNS-Sperrlisten → Sperrliste hinzufügen:

```
https://raw.githubusercontent.com/tdierks28/AdGuard-Lists/main/phishing-list-adguard
https://raw.githubusercontent.com/tdierks28/AdGuard-Lists/main/tracking-list
```

`phishing-list` wird daneben nicht gebraucht, ihr Inhalt steckt bereits in `phishing-list-adguard`.

Allowlist (Filter → DNS-Zulassungslisten):

```
https://raw.githubusercontent.com/tdierks28/AdGuard-Lists/main/whitelist-all
```

### Pi-hole

Adlists → Neue Liste:

```
https://raw.githubusercontent.com/tdierks28/AdGuard-Lists/main/phishing-list-adguard
```

Pi-hole versteht aus der Adblock-Syntax nur `||domain^`. Genau dieses Format nutzt die Liste.

## Warnung: `phishing-patterns`

Diese Liste enthält **TLD-Wildcards** wie `||*.tk^`, `||*.xyz^`, `||*.top^`, `||*.com.br^`, `||*.cfd^`, `||*.fun^`, `||*.love^`, `||*.xn^`. Sie blockieren **alle** Domains der jeweiligen Top-Level-Domain. So blockiert `||*.com.br^` ganz Brasilien und `||*.xyz^` Millionen legitimer Sites. Vor Aktivierung prüfen, ob das gewünscht ist.

## Pflege

- `phishing-list` wird von Hand und mit einem eigenen Werkzeug aus eingehenden Phishing- und Spam-Mails gepflegt. Neue Domains kommen erst nach einer Bewertung und einer manuellen Freigabe hinzu.
- Domains, die in `whitelist-all` freigegeben sind, werden bei jeder Aktualisierung aus `phishing-list` entfernt.
- `phishing-list-wildcard` und `phishing-list-adguard` werden automatisch erzeugt. Änderungen von Hand gehen beim nächsten Lauf verloren.
- Gemeinsam genutzte Plattformen (z. B. cloudfront.net, googleapis.com, workers.dev) und Länderendungen (com.br, co.za …) kommen nie auf die Wildcard-Liste. Dort werden nur einzelne Hosts gesperrt.
- Bereinigung 2026-04-27: Duplikate entfernt, legitime Domains (Banken, Behörden, Stadtwerke, Konzerne) in `whitelist-all` verschoben, Tracking-Domains in eine eigene `tracking-list` ausgegliedert.
- Verbesserungen, False Positives, Hinweise → gerne als Issue oder PR.
