# Installation et Configuration de TrueNAS-Scale et Debian

## Installation de l'ISO TrueNAS-Scale
Téléchargez l'ISO de TrueNAS-Scale depuis le site officiel :  
🔗 [TrueNAS Download](https://www.truenas.com/download-truenas-scale/)

---

## Création d'une Machine Virtuelle TrueNAS
### Configuration recommandée :
- **Processeur** : 2 cœurs
- **Mémoire vive (RAM)** : 4 Go
- **Disques durs (DD)** :
  - 2 disques de 16 Go
  - 5 disques de 2 To (pour le stockage en RAID 6)
- **Ajout de l'ISO TrueNAS**

### Configuration du stockage :
Après l'installation de TrueNAS, vous devrez configurer un RAID 6 :
1. Accédez à **Stockage** → **Créer un volume**
2. Sélectionnez **RZ2**, l'équivalent du RAID 6
3. Ajoutez les 5 disques de 2 To pour créer un espace de stockage sécurisé

---

## Création d'une Machine Virtuelle Debian
### Configuration recommandée :
- **Processeur** : 2 cœurs
- **Mémoire vive (RAM)** : 2 Go
- **Disques durs (DD)** : 1 disque de 8 Go
- **Ajout de l'ISO Debian**

---

## Accès à l'interface graphique TrueNAS
Une fois TrueNAS paramétré, accédez à l'interface web via :  
🔗 `https://<IP_DE_LA_MACHINE>`

Une page de connexion s'affichera :
- **Identifiant** : `truenas_admin`
- **Mot de passe** : Celui que vous avez défini lors de l'installation

Vous pouvez désormais gérer votre stockage et configurer vos partages réseau depuis cette interface.

---

## Configuration d'un RAID 6
1. Accédez à **Stockage** → **Créer un volume**
2. Sélectionnez **RZ2**, l'équivalent du RAID 6
3. Choisissez les 5 disques de 2 To

---

## Paramétrage de l'Authentification à Deux Facteurs
*À compléter selon la méthode souhaitée.*
1. Aller dans Système>Réglages avancés>Authentification globale à deux facteurs > Configurer l'authentification globale à deux facteurs
2. Cocher "Activer l'authentification à deux facteurs à l'échelle mondiale" puis Enregistrer
3. Cliquer sur "Renouveler le secret 2FA" 
4. Utiliser une application Authenticator pour scanner le QR Code proposé.

---

## Création d'un Dataset
Vous avez un dataset créé pour votre RAID. Cliquez dessus, puis sélectionnez **Ajouter un Dataset** et créez deux datasets pour les deux utilisateurs (exemple : Alice et Samuel).

---

## Création et Configuration : Groupe et Utilisateur
### Création d'un groupe pour SSH et FTP
1. Accédez à **Identifiants** → **Groupes**
2. Choisissez un nom de groupe
3. Définissez les privilèges : *Read-Only Administrateur / Sharing Administrateur*
4. Cochez **Autoriser toutes les commandes sudo**
5. Cochez **SMB Group**
6. Enregistrez

### Création des utilisateurs
1. Accédez à **Identifiants** → **Utilisateurs**
2. Ajoutez un nouvel utilisateur (exemple : Alice)
3. Définissez un mot de passe (exemple : Alice1234)
4. Décocher **Créer un nouveau groupe principal**
5. Dans **Groupe primaire**, sélectionnez le groupe créé précédemment
6. Définissez le **Répertoire utilisateur** vers le dataset du RAID 6
7. Activez **Connexion par mot de passe SSH**
8. Définissez la console sur *bash*
9. Cochez **Autoriser toutes les commandes sudo**
10. Enregistrez

Effectuez ces étapes une seconde fois pour créer un deuxième utilisateur.

---

## Tester le SSH et SFTP
### Vérification du service SSH sur TrueNAS
1. Accédez à **Système** → **Services**
2. Assurez-vous que **SSH** est actif et démarre automatiquement
3. Cliquez sur **Modifier**
4. Changez le **Port TCP** (exemple : 456)
5. Dans **Groupes de connexion par mot de passe**, ajoutez le groupe créé précédemment
6. Activez **Autoriser l'authentification par mot de passe**

### Connexion SSH depuis un terminal
```bash
ssh -p 456 alice@IP_DU_SERVER_TRUENAS
```
Si la connexion réussit, vous verrez *Welcome to TrueNAS* et *alice@truenas*.

### Connexion SFTP
```bash
sftp -P 456 alice@IP_DU_SERVER_TRUENAS
```

---

## Configuration de Samba
### Création d'un dossier partagé
1. Dans le dataset du RAID 6, créez un dossier nommé `public`
2. Accédez à **Partages** → **Partages Windows (SMB)**
3. Cliquez sur **Ajouter**
4. Définissez le chemin vers le dossier `public`
5. Nommez le partage `public`
6. Enregistrez

### Accès au partage SMB depuis un PC
1. Ouvrez **Explorateur de fichiers**
2. Accédez à **Réseaux**
3. Saisissez dans la barre d'adresse :
   ```
   \\IP_DE_TRUENAS\public
   ```
4. Connectez-vous avec l'utilisateur Alice

---

## Dataset Application
1. Dans **Stockages**, créez un volume avec le second disque de 16 Go
2. Nommez-le **Application** (stockage des applications du serveur)

---

## Installation de Vaultwarden
1. Accédez à **Applications** et recherchez *Vaultwarden*
2. Installez et configurez :
   - Définissez un mot de passe pour **Database Password**
   - Définissez un mot de passe pour **Admin Token**
   - **Configuration réseau** → **Certificate ID** : sélectionnez *truenas_default Certificate*
   - **Vaultwarden Data Storage** :
     - Type : *Host Path (Path that already exists on the system)*
     - **Host Path** : chemin du dataset `Application`
   - Enregistrez

Une fois installé, accédez à Vaultwarden via **Web UI** et créez un compte.

### Configuration dans *Admin Portal* → *General Settings*
- **Allow new Signups** : décochez *Default: True*
- **Require email verification** : cochez *Default: False*

Cela empêche les inscriptions non autorisées.

---

## Configuration VM (uniquement pour les utilisateurs Windows 11)

### Désactivez l'intégrité de la mémoire
  1. Allez dans paramètre système
  2. Entrez dans Confidentialité et Sécurité
  3. Allez dans Sécurité Windows
  4. Sécurité de l'appareil
  5. Détail de l'isolation du noyau
  6. Désactivez l'intégrité de la mémoire

    

### Désactiver les fonctionnalité Windows en rapport à Hyper-V
  1. Faites un Win + R et executez ```optionalfeatures```
  2. Désactivez :
    - Hyper-V
    - Pltaeforme de l'hyperviseur Windows
    - Sous-système Windows pour Linux
    - Virtual Machine Plateform
   
      

### Modifier le bcdedit
  1. Ouvrez l'invité de commande avec les droits d'administarteur
  2. entrez ```bcdedit```
  3. entrez la commande ```bcdedit /set hypervisorlaunchtype off```

    

### Paramétrer la VM pour la virtualisation imbriquée
  1. Modifiez les paramètre de la VM
  2. Allez dans Processors
  3. Chochez la case **Virtualize Intel VT-x/EPT or AMD-V/RVI

---
