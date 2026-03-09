# Documentation de déploiement — ProjetVin

**Serveur applicatif :** `172.28.32.10`  
**Serveur base de données :** `172.28.32.11`

---

## Partie 1 — Serveur base de données (172.28.32.11)

### 1. Installer SQL Server Express

Télécharger et exécuter l'installeur :  
[SQL Server Express — Télécharger](https://go.microsoft.com/fwlink/p/?linkid=2216019&clcid=0x409&culture=en-us&country=us)

Suivre l'assistant d'installation en choisissant l'installation **Express**.

---

### 2. Installer SSMS (SQL Server Management Studio)

Télécharger SSMS depuis le site officiel de Microsoft et l'installer sur la machine `172.28.32.11`.

---

### 3. Récupérer le fichier de sauvegarde

Aller sur le lien SharePoint suivant et télécharger le fichier `cave(1).bak` :  
[Télécharger cave(1).bak](https://lyceefulbert-my.sharepoint.com/:f:/g/personal/paul_redler_lyceefulbert_fr/IgDYhI0RL4iRTZCxMAIRJ7T0AawglNgZZ6AIhGp-x8qob90?e=amQgGe)

Copier le fichier `.bak` dans le dossier de backup SQL Server :

```
C:\Program Files\Microsoft SQL Server\MSSQL17.SQLEXPRESS03\MSSQL\Backup\
```

---

### 4. Restaurer la base de données
Installer Sql Server Management Studio (SSMS).

Ouvrir **SSMS** et se connecter à l'instance locale.

- Clic droit sur **Bases de données** → **Restaurer la base de données...**
- Choisir **Périphérique** → cliquer sur `...` → **Ajouter** → sélectionner `cave(1).bak`
- Cliquer sur **OK** pour lancer la restauration

---

### 5. Créer l'utilisateur SQL

#### Activer l'authentification mixte (Windows + SQL)

- Clic droit sur le **serveur** dans SSMS → **Propriétés**
- Onglet **Sécurité**
- Cocher **Authentification Windows et SQL Server**
- Cliquer sur **OK**

#### Créer le login SQL

- Développer **Sécurité** → clic droit sur **Connexions** → **Nouvelle connexion...**
- Nom de connexion : `cave`
- Sélectionner **Authentification SQL Server**
- Mot de passe : `1234`
- Décocher **Appliquer la stratégie de mot de passe** si besoin
- Cliquer sur **OK**

#### Mapper l'utilisateur sur la base cave

- Clic droit sur la base **cave** → **Propriétés** → **Mappage d'utilisateur**
- Cocher la base `cave` dans la liste
- Dans les rôles, cocher **db_owner**
- Cliquer sur **OK**

---

### 6. Activer TCP/IP et redémarrer le service

#### Activer TCP/IP

- Ouvrir **SQL Server Configuration Manager**
- Aller dans **Configuration du réseau SQL Server** → **Protocoles pour SQLEXPRESS03**
- Clic droit sur **TCP/IP** → **Activer**

#### Redémarrer le service

- Ouvrir `services.msc`
- Trouver **SQL Server (SQLEXPRESS03)**
- Clic droit → **Redémarrer**

---

### 7. Ouvrir le port dans le pare-feu Windows

Autoriser le port **1433** en entrée :

```powershell
New-NetFirewallRule -DisplayName "SQL Server 1433" -Direction Inbound -Protocol TCP -LocalPort 1433 -Action Allow
```

---

## Partie 2 — Serveur applicatif (172.28.32.10)

### 1. Installer le .NET 10 Hosting Bundle

Télécharger et exécuter l'installeur :  
[.NET 10 Hosting Bundle — Télécharger](https://dotnet.microsoft.com/en-us/download/dotnet/thank-you/runtime-aspnetcore-10.0.3-windows-hosting-bundle-installer)

Redémarrer le serveur après l'installation.

---

### 2. Déployer l'application

Aller sur le même lien SharePoint et télécharger le dossier **ProjetVin** :  
[Télécharger ProjetVin](https://lyceefulbert-my.sharepoint.com/:f:/g/personal/paul_redler_lyceefulbert_fr/IgDYhI0RL4iRTZCxMAIRJ7T0AawglNgZZ6AIhGp-x8qob90?e=amQgGe)

Copier le dossier à l'emplacement souhaité, par exemple :

```
C:\Sites\ProjetVin\
```


---

### 3. Lancer l'application

Ouvrir une invite de commande dans le dossier de l'application et exécuter :

```cmd
dotnet ProjetVinWeb.dll --urls "http://172.28.32.10:5000"
```

L'application est accessible depuis le réseau à l'adresse :  
**http://172.28.32.10:5000**
