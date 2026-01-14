# TP Final – Serveur Autonome

## Contexte
Projet réalisé dans le cadre du module **R4 – Administration des systèmes (2025-2026)**  
Encadré par **Jimmy Nicolas**

Ce projet consiste à concevoir, développer, versionner avec Git et déposer sur GitHub un **script Bash** permettant l’automatisation de la **maintenance** et de l’**audit de sécurité** d’un serveur Linux.

---

## Objectif du projet
Développer un script Bash nommé **serveur_autonome.sh** permettant de :
- Auditer les performances du serveur
- Gérer automatiquement des services critiques
- Réaliser des sauvegardes compressées
- Effectuer des contrôles de sécurité
- Centraliser l’ensemble des actions dans des fichiers de logs

Le script est versionné avec **Git** et déposé dans un **dépôt GitHub public**, conformément aux exigences du sujet.

Le projet n’est évalué **que s’il est correctement versionné et publié**.  
Cette partie est **éliminatoire** si non respectée.

---

## Structure du dépôt
TP_Final_Bash_Serveur_Autonome/
│
├── serveur_autonome.sh # Script principal
├── README.md # Documentation du projet
└── .gitignore


---

## Fonctionnalités du script

### Audit de performance et gestion des services

#### Vérification des ressources
- Contrôle de l’espace disque libre sur la partition `/boot`
- Contrôle de l’utilisation de la mémoire (RAM / SWAP)
- Définition de seuils critiques :
  - 90 % pour le disque
  - 90 % pour la SWAP
- Génération de messages **WARNING** si les seuils sont dépassés
- Affichage :
  - des 3 processus les plus consommateurs de CPU
  - des 3 processus les plus consommateurs de RAM

#### Redémarrage conditionnel des services
- Services critiques surveillés :
  - `sshd`
  - `postgresql`
  - `apache2`
- Vérification de l’état via `systemctl is-active`
- Tentative de redémarrage unique en cas d’arrêt
- Journalisation :
  - **INFO** si redémarrage
  - **ERROR** si échec

---

### Sauvegarde dynamique et intégrité

#### Cible de sauvegarde
- Le script **n’accepte aucun argument positionnel**
- Le répertoire sauvegardé est :  
  `/usr/local/bin`
- Vérification :
  - existence
  - type répertoire
- En cas d’erreur : sortie avec le **code 102**

#### Archivage et compression
- Répertoire de destination : `/mnt/sauvegardes`
- Création automatique s’il n’existe pas
- Compression **Bzip2**
- Nom dynamique de l’archive :

#### Vérification d’intégrité
- Copie temporaire du contenu sauvegardé
- Comparaison du fichier :

/etc/ssh/sshd_config

avec un fichier de référence fictif :

/tmp/sshd_config.ref

- Génération d’un **WARNING** en cas de différence

---

### Audit de sécurité et nettoyage

#### Identification des utilisateurs inactifs
- Seuil d’inactivité : **60 jours**
- Recherche des répertoires personnels inactifs via `find`
- Stockage de la liste dans un fichier temporaire
- Affichage à l’écran avec **demande de confirmation utilisateur** avant toute action

---

### Journalisation et robustesse

#### Journalisation standardisée
- Répertoire de logs :

/var/log/maintenance

- Fichier de log unique par exécution :

maintenance_YYYYMMDD_HHMMSS.log

- Fonction `log_message` :
- Timestamp précis
- Niveau : `INFO`, `WARNING`, `ERROR`
- Redirection de toutes les sorties :
- stdout
- stderr
vers le fichier de log

#### Gestion des interruptions
- Capture du signal `INT` via `trap`
- En cas d’interruption :
- message **ERROR**
- sortie propre du script

---

## Exécution du script

```bash
sudo ./serveur_autonome.sh
