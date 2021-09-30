---
title: "Backup Dans le cloud avec restic"
date: 2021-09-29T11:12:36-04:00
tags: [2021,restic,backup,borg]
draft: false
---

![restic](/img/restic_drive.png)

Mon ordinateur se trouvais ralentie par mon système de backup. j'avais aussi un mount réseau qui ne fonctionnait pas toujours parce que j'avais un disque dur externe de défectueux. Je me suis alors rendu à l'évidance qu'il falait que je trouve une nouvelle façon d'effectuer mes backups.
Après plusieurs recherche j'ai finalement trouvé une stratégie de backup qui me convient parfaitement. 
Cette solution est **restic** parce quel correspondait à tout les critères que je m'étais donnés.

### Mes critères de sélection
- Un système de backup qui utilise de la deduplication comme borg.
- Doit être capable d'effectuer les backups directement dans le cloud comme duplicati et rclone.
- Doit supporter l'encryption
- Doit être léger et être utilisé directement sur le poste.
- Doit être intégré de base dans la majorité des distributions linux

### Comment mettre en place
L'implantation c'est en fait en quatre étapes.
1. Installation de rclone
2. Création d'un Google Drive Client ID pour rclone 
3. Configuration de restic
4. Configuration de rclone
5. Création du script de backup
6. Configuration de l'automatisme d'exécution via systemd

#### 1. installation de rclone
L'installation avec opensuse ce fait comme tout autre logiciel

{{< highlight terminal >}}
 $ zypper install rclone
{{< /highlight >}}

#### 2. Création d'un Google Drive Client ID pour rclone 
La création d'un identification de Google Drive qui sera utilisé seulement dans rclone se fait via la [Google API Console](https://console.developers.google.com/)

Ci-bas la procédure qui vient du site [https://rclone.org](https://rclone.org/drive/)
    
    Voici comment créer votre propre ID client Google Drive pour rclone :
    
    Connectez-vous à la console API Google avec votre compte Google. Peu importe le compte Google que vous utilisez. (Il nest pas nécessaire que ce soit le même compte que le Google Drive auquel vous souhaitez accéder)
    
    Sélectionnez un projet ou créez un nouveau projet.
    
    Sous « ACTIVER LES API ET LES SERVICES », recherchez « Drive » et activez « l''API Google Drive ».
    
    Cliquez sur "Identifiants" dans le panneau de gauche (pas sur "Créer des identifiants", qui ouvre l''assistant), puis sur "Créer des identifiants"
    
    Si vous avez déjà configuré un « écran de consentement Oauth », passez à l'étape suivante ; sinon,     cliquez sur le bouton « CONFIGURER L'ÉCRAN DE CONSENTEMENT » (près du coin supérieur droit du     panneau de droite), puis sélectionnez « Externe » et cliquez sur « CRÉER » ; sur l''écran suivant,     entrez un "Nom de l''application" ("rclone" est OK) puis cliquez sur "Enregistrer" (toutes les autres données sont facultatives). Cliquez à nouveau sur "Identifiants" dans le panneau de gauche pour revenir à l''écran "Identifiants".
    
    (PS : si vous êtes un utilisateur de GSuite, vous pouvez également sélectionner "Interne" au lieu de "Externe" ci-dessus, mais cela n'a pas été testé/documenté jusqu'à présent).
    
    Cliquez sur le bouton "+ CREATE CREDENTIALS" en haut de l''écran, puis sélectionnez "OAuth client ID".
    
    Choisissez un type d''application "Application de bureau" si vous utilisez un compte Google ou     "Autre" si vous utilisez un compte GSuite et cliquez sur "Créer". (le nom par défaut est bien)
    
    Il vous montrera un ID client et un secret client. Notez-les.
    
    Allez sur « Écran de consentement Oauth » et appuyez sur « Publier l''application »
    
    Fournissez l''ID client et le secret client notés à rclone.
    
#### 3. Configuration de rclone
J'ai suivi la documentation du site de [https://rclone.org/drive](https://rclone.org/drive/)

{{< highlight terminal >}}
n/r/c/s/q> n) New remote
name> remote_name
Storage> drive
scope> 1
Auto config y/n> y
{{< /highlight >}}

#### 4. Configuration de restic
Initialiser restic dans le disque Google Drive

{{< highlight terminal >}}
 $ restic -r rclone:remote_name:backupdir init
{{< /highlight >}}

#### 5. Création du script de backup
Créer .bin/backup_restic.sh
{{< highlight bash >}}
#!/usr/bin/env bash

BACKUP_PATHS="/home/myuser/Programmations /home/myuser/Documents /home/myuser/Ebook  /home/myuser/Images"
BACKUP_EXCLUDES="--exclude-file /home/myuser/Documents/config_linux/excludelist"

export RESTIC_REPOSITORY="rclone:remote_drive:backup"
export RESTIC_PASSWORD_FILE="/home/myuser/Documents/config_linux/resticpw.txt"

# Backup
info "Backuping"
restic backup \
        $BACKUP_EXCLUDES \
        $BACKUP_PATHS 

info "Forgeting"
# Ménage
restic forget \
        --prune \
        --keep-daily 14 \
        --keep-weekly 10 \
        --keep-monthly 18 \
        --keep-yearly 3 

{{< /highlight >}}



#### 6. Configuration de l'automatisme d'exécution via systemd

Créer .config/systemd/user/restic.service
{{< highlight terminal >}}
[Unit]
Description=restic backup

[Service]
Type=oneshot
ExecStart=%h/.bin/backup_restic.sh

{{< /highlight >}}

Créer .config/systemd/user/restic.timer
{{< highlight terminal >}}
[Unit]
Description=backup journalier avec restic
Documentation=man:restic

[Timer]
OnBootSec=30min
OnUnitActiveSec=1d

[Install]
WantedBy=timers.target

{{< /highlight >}}

Activation et démarrage du service de type user
{{< highlight terminal >}}
$ systemctl --user enable  restic.timer
$ systemctl --user start  restic.timer
$ systemctl --user list-timers --all
{{< /highlight >}}