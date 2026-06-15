# 🔍 Nmap — Procédure de scan réseau

> Procédure de reconnaissance active avec Nmap

## 📋 Contexte d'utilisation

Utilisé en phase de **reconnaissance active** pour identifier :
- Les ports ouverts sur une cible
- Les services et leurs versions
- Les vulnérabilités potentielles

## 🛠️ Prérequis

- Nmap installé : `sudo apt install nmap`
- **Autorisation écrite** de scanner la cible
- IP ou range de la cible

## ⚙️ Procédure

### Étape 1 : Scan rapide initial

```bash
nmap -Pn -sS --min-rate 5000 -p- 192.168.1.10
```

**Explication :**
- `-Pn` : ne ping pas avant
- `-sS` : scan SYN (semi-ouvert, discret)
- `--min-rate 5000` : envoie minimum 5000 paquets/sec
- `-p-` : tous les ports (1-65535)

### Étape 2 : Scan détaillé des ports trouvés

```bash
nmap -sC -sV -p 22,80,445 192.168.1.10 -oA scan_detaille
```

**Explication :**
- `-sC` : exécute les scripts NSE par défaut
- `-sV` : détecte les versions des services
- `-oA` : sauvegarde dans 3 formats (xml, gnmap, nmap)

### Étape 3 : Scan de vulnérabilités

```bash
nmap --script vuln 192.168.1.10
```

## 📊 Résultats attendus

| Information | Source |
|-------------|--------|
| Ports ouverts | `-sS` |
| Versions de services | `-sV` |
| Vulnérabilités | `--script vuln` |

## ⚠️ Points de vigilance

> **⚖️ Légal** : Ne scanner QUE les cibles pour lesquelles vous avez une **autorisation écrite**. Le scan non autorisé est puni par la loi (article 323-1 du Code pénal).

## 📚 Ressources

- [Documentation officielle Nmap](https://nmap.org/book/man.html)
- [Cheatsheet HackTricks](https://book.hacktricks.xyz)

---

*Procédure rédigée le 15/01/2025 — Arthur Logeais*