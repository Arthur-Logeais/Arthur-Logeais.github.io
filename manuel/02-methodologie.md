# 2. Méthodologie

## Les 5 phases d'un audit de sécurité

Tout audit de sécurité offensive suit une **méthodologie structurée** en 5 phases :
Phase 1 : Reconnaissance
↓
Phase 2 : Scanning & Énumération
↓
Phase 3 : Exploitation
↓
Phase 4 : Post-exploitation
↓
Phase 5 : Reporting

---

## Phase 1 — Reconnaissance

Collecte d'informations sur la cible avant toute interaction directe.

### Reconnaissance passive (OSINT)
- Google Dorks
- LinkedIn, réseaux sociaux
- WHOIS, DNS public
- Recherche de fuites de données

### Reconnaissance active
- Énumération DNS (transferts de zone, brute force)
- Scan de ports (Nmap)

➜ Voir la [procédure Nmap](../procedures/recon/nmap.md)

---

## Phase 2 — Scanning & Énumération

Identification précise des services, versions et points d'entrée potentiels.

### Outils principaux

| Outil | Usage |
|-------|-------|
| **Nmap** | Scan de ports et scripts NSE |
| **Gobuster** | Bruteforce de répertoires web |
| **Nikto** | Scan de vulnérabilités web |
| **enum4linux** | Énumération SMB |
| **CrackMapExec** | Énumération multi-protocoles |
| **BloodHound** | Cartographie Active Directory |

---

## Phase 3 — Exploitation

Exploitation des vulnérabilités identifiées pour obtenir un **accès initial**.

### Vecteurs courants

| Vecteur | Technique |
|---------|-----------|
| **Web** | SQLi, XSS, LFI/RFI, upload bypass |
| **Réseau** | NTLM Relay, SMB exploits |
| **Credentials** | Brute force, password spraying |
| **Exploits publics** | Searchsploit, CVE connus |

➜ Voir la [procédure SQL Injection](../procedures/exploitation/sqli.md)

---

## Phase 4 — Post-exploitation

Une fois l'accès initial obtenu, on cherche à **élever les privilèges** et **étendre l'accès**.

### Étapes clés

1. **Stabiliser le shell** — Upgrade TTY, persistance
2. **Élévation de privilèges** — Sudo, SUID, kernel exploit
3. **Collecte de credentials** — Hashes, mots de passe en clair
4. **Mouvement latéral** — Pass-the-Hash, WMI, WinRM
5. **Persistance** — Backdoors, clés SSH, tâches planifiées

➜ Voir la [procédure Linux PrivEsc](../procedures/privesc/linux.md)  
➜ Voir la [procédure Kerberoasting](../procedures/active-directory/kerberoasting.md)

---

## Phase 5 — Reporting

La **phase la plus importante** pour le client. Le rapport doit être :

### Structure du rapport

1. **Résumé exécutif** — Non technique, destiné à la direction
2. **Périmètre et méthodologie** — Cibles, durée, outils utilisés
3. **Findings** — Classés par criticité (CVSS)
   - Description de la vulnérabilité
   - Preuve d'exploitation (screenshots, output)
   - Impact technique et business
   - Recommandation de remédiation
4. **Annexes** — Scans complets, configurations testées

### Échelle de criticité CVSS

| Score | Niveau | Couleur |
|-------|--------|---------|
| 9.0 - 10.0 | **Critique** | 🔴 Rouge |
| 7.0 - 8.9 | **Élevé** | 🟠 Orange |
| 4.0 - 6.9 | **Moyen** | 🟡 Jaune |
| 0.1 - 3.9 | **Faible** | 🔵 Bleu |

---

*Rédigé par Arthur Logeais — Juin 2026*