---
title:  "Zabbix installation Guide"
date:   2025-11-26
tags: [posts]
excerpt: "i show you how to install zabbix"
---

```yaml
---
title: Installation de Zabbix 7.4 sur Debian 13
tags: [zabbix, monitoring, debian, linux, admin]
---
```

# Installation de Zabbix 7.4 sur Debian 13

## 1. Connexion SSH
```bash
ssh student@vmip
```

## 2. Téléchargement de Zabbix
- **Site officiel** : [https://www.zabbix.com/fr/download](https://www.zabbix.com/fr/download)
- **Options à sélectionner** :
  - Version : 7.4
  - OS : Debian 13 Trixie (amd64/arm64)
  - Base de données : MySQL
  - Serveur web : nginx

## 3. Installation du dépôt Zabbix
```bash
sudo -i
wget https://repo.zabbix.com/zabbix/7.4/release/debian/pool/main/z/zabbix-release/zabbix-release_latest_7.4+debian13_all.deb
dpkg -i zabbix-release_latest_7.4+debian13_all.deb
apt update
```

## 4. Installation des paquets Zabbix
```bash
apt install zabbix-server-mysql zabbix-frontend-php zabbix-nginx-conf zabbix-sql-scripts zabbix-agent
```

## 5. Installation et sécurisation de MariaDB
```bash
cd /run
mariadb-secure-installation
```
- Réponses au questionnaire :
  1. non
  2. non
  3. oui
  4. oui
  5. oui

## 6. Configuration de la base de données
```bash
mysql -uroot -p
```
```sql
CREATE DATABASE zabbix CHARACTER SET utf8mb4 COLLATE utf8mb4_bin;
CREATE USER zabbix@localhost IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON zabbix.* TO zabbix@localhost;
SET GLOBAL log_bin_trust_function_creators = 1;
QUIT;
```
```bash
zcat /usr/share/zabbix/sql-scripts/mysql/server.sql.gz | mysql --default-character-set=utf8mb4 -uzabbix -p zabbix
mysql -uroot -p
```
```sql
SET GLOBAL log_bin_trust_function_creators = 0;
QUIT;
```

## 7. Configuration de Zabbix
### Fichier `zabbix_server.conf`
```bash
nano /etc/zabbix/zabbix_server.conf
```
```ini
DBPassword=password
```

### Fichier `nginx.conf`
```bash
nano /etc/zabbix/nginx.conf
```
```nginx
listen 8080;
server_name example.com;
```

## 8. Démarrage des services
```bash
systemctl restart zabbix-server zabbix-agent nginx php8.4-fpm
systemctl enable zabbix-server zabbix-agent nginx php8.4-fpm
```

---
**⚠️ Notes importantes :**
- Remplacez `password` et `example.com` par vos propres valeurs.
- Vérifiez la version de PHP installée (`php8.4-fpm` peut varier selon votre système).
- Accédez à l'interface web via `http://<IP_SERVEUR>:8080`.
