# 🛡️ Enzo Demaretz — Analyste CTI/DFIR & Ingénieur Sécurité

Analyste CTI/DFIR & Ingénieur Sécurité en purple team, avec une expertise en threat hunting, DFIR, pentest web et sensibilisation des utilisateurs, développée notamment en tant que consultant indépendant. Passionné par les réseaux, l'architecture système, le développement d'outils de sécurité et l'analyse de malwares.

📍 Consultant cybersécurité purple team indépendant **actuellement en pause** (activité professionnelle actuelle prenant tout mon temps) — les outils ci-dessous ont été développés dans ce cadre et continuent d'évoluer sur mon temps personnel.

---

## 🎖️ Certifications

<table>
<tr>
<td align="center"><img src="https://raw.githubusercontent.com/Spellskite-coding/Spellskite-coding/main/badge_blue_team_l1.png" alt="Blue Team Level 1" width="110"><br><sub><b>Blue Team Level 1</b><br>Security Blue Team</sub></td>
<td align="center"><img src="https://raw.githubusercontent.com/Spellskite-coding/Spellskite-coding/main/badge_ccdl1.png" alt="Certified CyberDefender L1" width="110"><br><sub><b>Certified CyberDefender L1</b><br>CyberDefenders</sub></td>
<td align="center"><img src="https://raw.githubusercontent.com/Spellskite-coding/Spellskite-coding/main/badge_sal1.png" alt="Security Analyst SAL1" width="110"><br><sub><b>Security Analyst (SAL1)</b><br>TryHackMe</sub></td>
<td align="center"><img src="https://raw.githubusercontent.com/Spellskite-coding/Spellskite-coding/main/badge_sec1.png" alt="Cyber Security 101 SEC1" width="110"><br><sub><b>Cyber Security 101 (SEC1)</b><br>TryHackMe</sub></td>
<td align="center"><img src="https://raw.githubusercontent.com/Spellskite-coding/Spellskite-coding/main/badge_security_plus.png" alt="CompTIA Security+" width="110"><br><sub><b>Security+</b><br>CompTIA</sub></td>
</tr>
<tr>
<td align="center"><img src="https://raw.githubusercontent.com/Spellskite-coding/Spellskite-coding/main/badge_network_plus.png" alt="CompTIA Network+" width="110"><br><sub><b>Network+</b><br>CompTIA</sub></td>
<td align="center"><img src="https://raw.githubusercontent.com/Spellskite-coding/Spellskite-coding/main/badge_osint.png" alt="Permis d'Osinter" width="110"><br><sub><b>Permis d'Osinter</b><br>Oscar Zulu Crew</sub></td>
<td align="center"><img src="https://raw.githubusercontent.com/Spellskite-coding/Spellskite-coding/main/badge_pentester_m2i.png" alt="Pentester M2i" width="110"><br><sub><b>Pentester</b><br>M2i Formation</sub></td>
<td align="center"><img src="https://raw.githubusercontent.com/Spellskite-coding/Spellskite-coding/main/badge_admin_reseau.png" alt="Administrateur Reseau FormIP" width="110"><br><sub><b>Administrateur Réseau</b><br>FormIP</sub></td>
<td align="center"><img src="https://raw.githubusercontent.com/Spellskite-coding/Spellskite-coding/main/badge_tosa_digcomp.png" alt="Tosa DigComp" width="110"><br><sub><b>Tosa DigComp</b><br>Isograd — Expert</sub></td>
</tr>
</table>

---

## 📌 Sommaire

- [🔵 Blue Team & DFIR](#-blue-team--dfir)
- [🔴 Red Team & Offensive Security](#-red-team--offensive-security)
- [🛠️ Outils Open-Source](#️-outils-open-source)
- [📚 Autres projets](#-autres-projets)
- [📫 Contact](#-contact)

---

## 🔵 Blue Team & DFIR

### [RansomShield](https://github.com/Spellskite-coding/RansomShield)
Démon de détection de ransomware **basé sur le comportement**, écrit en Rust, pour serveurs Linux — bâti sur `fanotify(7)` (API mainline du noyau, aucun module noyau, aucun eBPF, aucun code `unsafe` dans le démon).

- Ne réagit pas à la modification de fichiers en soi, mais au **pattern spécifique du chiffrement** : rafale d'écritures à haute entropie sur de nombreux fichiers distincts dans une fenêtre de temps courte, ou déclenchement d'un fichier **honeypot** qu'aucun processus légitime ne devrait jamais toucher
- **Baseline par répertoire** : une écriture haute entropie ne compte que si le répertoire contenait auparavant du contenu en clair — évite les faux positifs sur les dossiers de sauvegarde/export qui reçoivent légitimement des archives
- **Liste blanche d'exécutables de confiance** (chemin + SHA-256), utilisable finement pour un script de sauvegarde/chiffrement connu — ne s'applique jamais à la détection honeypot
- **Réponse en mode enforce** : SIGSTOP immédiat du processus, mise en quarantaine des fichiers touchés, SIGKILL, puis rapport d'incident et hook de notification externe (email/Slack/PagerDuty)
- **Tests exclusivement en conteneurs Docker jetables**, jamais sur un hôte ni avec de vrais échantillons de ransomware — simulateurs d'attaque et de charges légitimes (sauvegarde, chiffrement de confiance) sur 5 distributions
- Résultats de test : sur une rafale de 300 fichiers, seuls 8 (2,7 %) touchés avant l'arrêt du processus, tous récupérés intacts en quarantaine, réponse en moins d'une seconde

<img src="https://raw.githubusercontent.com/Spellskite-coding/Spellskite-coding/main/ransomshield_demo.png" alt="Exemple de détection RansomShield" width="700">

![Rust](https://img.shields.io/badge/Rust-000000?style=flat-square&logo=rust&logoColor=white)
![fanotify](https://img.shields.io/badge/Linux-fanotify-FCC624?style=flat-square&logo=linux&logoColor=black)
![systemd](https://img.shields.io/badge/systemd-service-informational?style=flat-square)

---

### [win_malware_analyzer](https://github.com/Spellskite-coding/win_malware_analyzer)
Analyse statique poussée de binaires Windows (EXE/DLL/SYS) : extraction d'IOCs, cartographie des capacités comportementales et amorce automatisée de reverse engineering.

- Parsing structurel PE via **LIEF** : sections, entropie, mitigations de sécurité (ASLR, DEP/NX, CFG), imports
- Cartographie des capacités malveillantes potentielles (injection de processus, anti-debug, persistance, keylogging, ransomware, C2) à partir des API importées
- Extraction d'IOCs (IP, domaines, URLs, chemins et clés de registre Windows, commandes) y compris depuis du contenu encodé en base64
- Amorce de reverse engineering via **r2pipe** (Rizin) : décompilation du point d'entrée, cross-références des API sensibles
- Export du rapport complet au format JSON

<img src="https://raw.githubusercontent.com/Spellskite-coding/Spellskite-coding/main/win_malware_analyzer_demo.png" alt="Exemple de sortie win_malware_analyzer" width="700">

![Python](https://img.shields.io/badge/Python-3776AB?style=flat-square&logo=python&logoColor=white)
![LIEF](https://img.shields.io/badge/LIEF-PE%20Parsing-informational?style=flat-square)
![Rizin](https://img.shields.io/badge/Rizin-r2pipe-informational?style=flat-square)

---

### [LinuxHunt](https://github.com/Spellskite-coding/LinuxHunt)
Équivalent Linux de **DeepBlueCLI** (SANS) : parsing et threat hunting sur les logs système Linux (`auth.log`, `syslog`, `audit.log`, `journalctl`) pour faire ressortir les comportements malveillants et suspects lors d'une réponse à incident.

- Détection : bruteforce SSH, persistance (clés SSH, cron, comptes UID 0), élévation de privilèges, usages anormaux de `sudo`
- Sortie directement exploitable dans le terminal, pensé pour aller vite en investigation
- Utilisé en conditions réelles sur systèmes Debian

<img src="https://raw.githubusercontent.com/Spellskite-coding/Spellskite-coding/main/linuxhunt_demo.png" alt="Exemple de sortie LinuxHunt" width="700">

![Python](https://img.shields.io/badge/Python-3776AB?style=flat-square&logo=python&logoColor=white)

---

### [static_doc_analyzer](https://github.com/Spellskite-coding/static_doc_analyzer)
Analyseur statique de documents (PDF, OOXML — docx/xlsx...) permettant de décider si un fichier reçu peut être ouvert en toute sécurité, **sans jamais l'exécuter**.

- Zéro dépendance externe (aucune lib tierce, aucun binaire appelé) : outil totalement autonome
- Conçu comme couche de protection supplémentaire sous Linux, en l'absence d'antivirus
- Priorité à la précision : détection puissante avec un minimum de faux positifs

<img src="https://raw.githubusercontent.com/Spellskite-coding/Spellskite-coding/main/static_doc_analyzer_demo.png" alt="Exemple de sortie static_doc_analyzer" width="700">

![Python](https://img.shields.io/badge/Python-3776AB?style=flat-square&logo=python&logoColor=white)

---

### [linux_forensics](https://github.com/Spellskite-coding/linux_forensics) — *DFIR Linux Sniper*
Script de **live forensics** Linux pour la réponse à incident : zéro dépendance externe, exécution entièrement en mémoire, aucune altération de l'état de la machine analysée.

- Détection de binaires communiquant avec un C2, de processus en mémoire au comportement anormal (ex. `memfd_create`, binaire supprimé du disque), de rootkits et de fichiers cachés dans `/tmp`, `/var/tmp`, `/dev/shm`
- Comportement adapté selon l'exécution en user ou en root, avec confirmation explicite avant activation du mode complet
- Reporting du SHA256 de chaque fichier suspect détecté, pour du CTI immédiat sur les hashs

<img src="https://raw.githubusercontent.com/Spellskite-coding/Spellskite-coding/main/linux_forensics_demo.png" alt="Exemple de sortie linux_forensics" width="700">

![Python](https://img.shields.io/badge/Python-3776AB?style=flat-square&logo=python&logoColor=white)

---

## 🔴 Red Team & Offensive Security

### SQLi_XSS_tester
Script d'automatisation de l'énumération de vulnérabilités **SQL Injection** et **XSS** sur une cible web lors d'un pentest : test des paramètres d'URL et des formulaires détectés automatiquement sur la page (via BeautifulSoup), avec des payloads couvrant l'injection classique, le blind/time-based et plusieurs techniques de bypass de filtres.

<img src="https://raw.githubusercontent.com/Spellskite-coding/Spellskite-coding/main/sqli_xss_tester_demo.png" alt="Exemple de sortie SQLi_XSS_tester" width="700">

![Python](https://img.shields.io/badge/Python-3776AB?style=flat-square&logo=python&logoColor=white)
![Requests](https://img.shields.io/badge/Requests-black?style=flat-square)

### LFI_tester
Script dédié à l'énumération de vulnérabilités **Local File Inclusion** : traversal classique, bypass par encodage (simple/double, UTF-8), wrappers PHP (`php://filter`, `php://input`, `expect://`), et détection de log poisoning sur les fichiers de logs Apache/Nginx courants.

<img src="https://raw.githubusercontent.com/Spellskite-coding/Spellskite-coding/main/lfi_tester_demo.png" alt="Exemple de sortie LFI_tester" width="700">

![Python](https://img.shields.io/badge/Python-3776AB?style=flat-square&logo=python&logoColor=white)

### Fuzzer de binaires
Fuzzer générant des patterns cycliques pour identifier des crashs (buffer overflow) dans un binaire cible, avec calcul automatique de l'offset de crash à partir de l'adresse retournée.

<img src="https://raw.githubusercontent.com/Spellskite-coding/Spellskite-coding/main/fuzzer_demo.png" alt="Exemple de sortie du fuzzer" width="700">

![Python](https://img.shields.io/badge/Python-3776AB?style=flat-square&logo=python&logoColor=white)

### Secrets Finder
Scanner de secrets pour fichiers et répertoires : détection de clés AWS, tokens GitHub/Slack, clés privées, mots de passe et chaînes base64 suspectes via un jeu de règles regex, avec export JSON du rapport — utile en revue de code lors d'un audit ou d'un pentest.

<img src="https://raw.githubusercontent.com/Spellskite-coding/Spellskite-coding/main/secrets_finder_demo.png" alt="Exemple de sortie Secrets Finder" width="700">

![Python](https://img.shields.io/badge/Python-3776AB?style=flat-square&logo=python&logoColor=white)

---

## 🛠️ Outils Open-Source

Des outils grand public, développés pour être simples, sûrs et respectueux de la vie privée — sans télémétrie ni dépendance cachée.

### [La Meuh 🐄](https://github.com/Spellskite-coding/La_meuh)
Automatise la mise à jour de tous les programmes Windows en un clic via `winget upgrade --all`. Un seul exécutable, aucune dépendance externe.

<img src="https://raw.githubusercontent.com/Spellskite-coding/Spellskite-coding/main/la_meuh_demo.png" alt="Fenêtre de mise à jour La Meuh" width="500">

![C++](https://img.shields.io/badge/C%2B%2B-00599C?style=flat-square&logo=cplusplus&logoColor=white)
![Windows](https://img.shields.io/badge/Windows%2011-0078D6?style=flat-square&logo=windows&logoColor=white)

### [Hus-Clean](https://github.com/Spellskite-coding/hus-clean)
Nettoyeur de fichiers temporaires et de cookies indésirables (Chrome, Firefox, Brave, Edge, Opera, Vivaldi) — l'alternative sobre à un CCleaner-like, sans droits administrateur requis et avec confirmation explicite avant toute suppression.

<img src="https://raw.githubusercontent.com/Spellskite-coding/Spellskite-coding/main/hus_clean_demo.png" alt="Interface Hus-Clean" width="500">

![C++](https://img.shields.io/badge/C%2B%2B-00599C?style=flat-square&logo=cplusplus&logoColor=white)
![Qt](https://img.shields.io/badge/Qt-41CD52?style=flat-square&logo=qt&logoColor=white)

### [Colibri Converter](https://github.com/Spellskite-coding/Colibri_Converter)
Conversion PDF ↔ DOCX multi-plateformes, 100% locale : aucun accès réseau nécessaire, dans la même lignée que La Meuh et Hus-Clean — sécurité et simplicité avant tout.

<img src="https://raw.githubusercontent.com/Spellskite-coding/Spellskite-coding/main/colibri_converter_demo.png" alt="Aperçu de l'interface Colibri Converter" width="500">

---

## 📚 Autres projets

### [Security System Administrator](https://github.com/Spellskite-coding/Security_System_Administrator)
Homelab de sécurité intégrant plusieurs briques SOC, détection et gestion IT, interconnectées entre elles :

- **SOC sous Wazuh** : centralisation et corrélation des alertes
- **Intégration Wazuh ↔ GLPI** : les alertes critiques déclenchent automatiquement la création de tickets dans GLPI via l'API
- **Détection de malwares à la volée** : un partage **Samba** héberge SOC, GLPI et les fichiers partagés ; dès qu'un fichier y est déposé, Wazuh remonte l'alerte de dépôt, **ClamAV** analyse le fichier, et le résultat est reporté dans Wazuh
- **Hôte FreeBSD** : exécute l'IDS **Suricata** et héberge une jail dédiée exposant le honeypot **Cowrie**
- **Réseaux d'entreprise Cisco** : conception et sécurisation sur Packet Tracer

![Wazuh](https://img.shields.io/badge/Wazuh-SOC-3AB0FF?style=flat-square&logo=wazuh&logoColor=white)
![GLPI](https://img.shields.io/badge/GLPI-Ticketing-FF7900?style=flat-square)
![ClamAV](https://img.shields.io/badge/ClamAV-Antivirus-000000?style=flat-square&logo=clamav&logoColor=white)
![Samba](https://img.shields.io/badge/Samba-0555C7?style=flat-square&logo=samba&logoColor=white)
![FreeBSD](https://img.shields.io/badge/FreeBSD-Host%20%2F%20IDS-AB2B28?style=flat-square&logo=freebsd&logoColor=white)
![Cowrie](https://img.shields.io/badge/Cowrie-Honeypot%20(jail)-grey?style=flat-square)
![Suricata](https://img.shields.io/badge/Suricata-IDS-D0242B?style=flat-square)
![Cisco Packet Tracer](https://img.shields.io/badge/Cisco-Packet%20Tracer-1BA0D7?style=flat-square&logo=cisco&logoColor=white)

### [Analyse de binaires](https://github.com/Spellskite-coding/Analyse_de_binaires)
Rapports d'analyses de binaires malveillants, couvrant :

- **CTI** : extraction et corrélation d'IOCs
- **Analyse statique** de malwares
- **Reverse engineering**

![CTI](https://img.shields.io/badge/CTI-Threat%20Intelligence-1f6feb?style=flat-square)
![Reverse Engineering](https://img.shields.io/badge/Reverse%20Engineering-8957e5?style=flat-square)

### [Guide cybersécurité](https://github.com/Spellskite-coding/Guide_cybersecurite)
Guide d'outils et de commandes pour la communauté cybersécurité.

---

## 📫 Contact

[![LinkedIn](https://img.shields.io/badge/LinkedIn-0A66C2?style=flat-square&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/enzo-demaretz-40b5083a6/)
[![TryHackMe](https://img.shields.io/badge/TryHackMe-Top%201%25-212C42?style=flat-square&logo=tryhackme&logoColor=white)](https://tryhackme.com/p/Spellskite)
[![Site Web](https://img.shields.io/badge/Site%20Web-safeblueconseil.fr-000000?style=flat-square)](https://safeblueconseil.fr/)
[![Email](https://img.shields.io/badge/Email-enzo.demaretz.pro%40gmail.com-D14836?style=flat-square&logo=gmail&logoColor=white)](mailto:enzo.demaretz.pro@gmail.com)
