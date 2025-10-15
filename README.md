# YubiGeek.com

Un site WordPress moderne containerisé avec Docker, optimisé pour le déploiement sur Coolify et autres plateformes PaaS.

## 🚀 Fonctionnalités

- **WordPress 6.x** avec PHP 8.3-FPM
- **MariaDB 11** pour la base de données
- **Nginx** comme serveur web
- **Configuration SMTP** intégrée
- **Optimisations WordPress** (cache OPcache, désactivation du cron WP, etc.)
- **Health checks** pour une surveillance automatique
- **Configuration sécurisée** avec variables d'environnement

## 📋 Prérequis

- Docker et Docker Compose (pour le déploiement local)
- Coolify ou autre plateforme de déploiement compatible Docker
- Serveur SMTP (optionnel, pour l'envoi d'emails)

## 🛠️ Installation

### Déploiement local avec Docker Compose

1. **Clonez le repository :**
   ```bash
   git clone https://github.com/yubigeek/yubigeek.git
   cd yubigeek
   ```

2. **Configurez les variables d'environnement :**
   ```bash
   cp .env.example .env
   ```
   
   Éditez le fichier `.env` avec vos paramètres :
   ```env
   # Base de données
   MYSQL_DATABASE=yubigeek_db
   MYSQL_USER=yubigeek_user
   MYSQL_PASSWORD=your_secure_password
   MYSQL_HOST=mariadb

   # Clés de sécurité WordPress (générez-les sur https://api.wordpress.org/secret-key/1.1/salt/)
   WP_AUTH_KEY=your_auth_key
   WP_SECURE_AUTH_KEY=your_secure_auth_key
   WP_LOGGED_IN_KEY=your_logged_in_key
   WP_NONCE_KEY=your_nonce_key
   WP_AUTH_SALT=your_auth_salt
   WP_SECURE_AUTH_SALT=your_secure_auth_salt
   WP_LOGGED_IN_SALT=your_logged_in_salt
   WP_NONCE_SALT=your_nonce_salt

   # Configuration SMTP (optionnel)
   SMTP_HOST=smtp.example.com
   SMTP_PORT=587
   SMTP_AUTH=true
   SMTP_USER=your_email@example.com
   SMTP_PASS=your_smtp_password
   SMTP_FROM=noreply@yubigeek.com
   SMTP_FROMNAME=YubiGeek
   ```

3. **Lancez l'application :**
   ```bash
   docker-compose up -d
   ```

4. **Accédez à votre site :**
   - Site WordPress : http://localhost
   - Installation WordPress : http://localhost/wp-admin/install.php

### 🌐 Déploiement avec Coolify

Coolify est une plateforme de déploiement self-hosted qui simplifie le déploiement d'applications Docker.

#### Méthode 1 : Déploiement depuis GitHub

1. **Connectez votre repository GitHub à Coolify**
2. **Créez un nouveau projet** dans Coolify
3. **Sélectionnez "Docker Compose"** comme type de déploiement
4. **Configurez les variables d'environnement** dans l'interface Coolify :
   - Utilisez les mêmes variables que dans l'exemple `.env` ci-dessus
   - Générez des clés WordPress sécurisées
5. **Configurez le domaine** dans les paramètres Coolify
6. **Déployez** l'application

#### Méthode 2 : Déploiement manuel

1. **Uploadez votre code** sur votre serveur Coolify
2. **Créez un nouveau service** de type "Docker Compose"
3. **Collez le contenu** de votre `docker-compose.yaml`
4. **Configurez les variables d'environnement**
5. **Définissez les volumes persistants** pour les données WordPress et MariaDB
6. **Lancez le déploiement**

#### Configuration recommandée pour Coolify

```yaml
# Variables d'environnement recommandées pour Coolify
COOLIFY_URL=https://www.yubigeek.com
COOLIFY_BRANCH=main

# Volumes persistants (Coolify les gère automatiquement)
wordpress-files: /var/www/html
mariadb-data: /var/lib/mysql
```

## 🔧 Configuration

### Structure du projet

```
yubigeek/
├── .docker/
│   ├── nginx/          # Configuration Nginx
│   ├── php/            # Configuration PHP
│   └── supervisor/     # Configuration Supervisor
├── docker-compose.yaml # Orchestration des conteneurs
├── Dockerfile         # Image Docker personnalisée
├── .env              # Variables d'environnement
└── README.md
```

### Optimisations WordPress incluses

- **Cache OPcache** activé pour de meilleures performances
- **Cron WordPress désactivé** (utilisez un cron système à la place)
- **Révisions limitées** à 10 pour économiser l'espace disque
- **Mémoire PHP augmentée** à 512M
- **Méthode de fichier directe** pour les uploads
- **Mises à jour automatiques** du core WordPress activées

### Configuration SMTP

Le projet inclut une configuration SMTP prête à l'emploi. Configurez simplement les variables d'environnement SMTP pour activer l'envoi d'emails :

```env
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_AUTH=true
SMTP_USER=votre-email@gmail.com
SMTP_PASS=votre-mot-de-passe-app
SMTP_FROM=noreply@yubigeek.com
SMTP_FROMNAME=YubiGeek
```

## 🔒 Sécurité

- **Variables d'environnement** pour toutes les informations sensibles
- **Clés WordPress** générées automatiquement
- **Modification de fichiers** désactivée en production
- **Health checks** pour surveiller l'état des services
- **Utilisateur non-root** dans les conteneurs

## 🚀 Production

### Recommandations pour la production

1. **Utilisez HTTPS** avec un certificat SSL valide (Coolify peut le gérer automatiquement)
2. **Configurez des sauvegardes** régulières des volumes Docker
3. **Surveillez les logs** avec les outils de monitoring de Coolify
4. **Mettez à jour régulièrement** les images Docker
5. **Configurez un CDN** pour les fichiers statiques

### Sauvegardes

Pour sauvegarder vos données :

```bash
# Sauvegarde de la base de données
docker-compose exec mariadb mysqldump -u${MYSQL_USER} -p${MYSQL_PASSWORD} ${MYSQL_DATABASE} > backup.sql

# Sauvegarde des fichiers WordPress
docker-compose exec wordpress tar -czf /tmp/wordpress-backup.tar.gz /var/www/html
docker cp container_name:/tmp/wordpress-backup.tar.gz ./wordpress-backup.tar.gz
```

## 🐛 Dépannage

### Problèmes courants

1. **Site inaccessible** : Vérifiez que les ports sont correctement exposés
2. **Erreur de base de données** : Vérifiez les variables d'environnement MySQL
3. **Emails non envoyés** : Vérifiez la configuration SMTP
4. **Performances lentes** : Activez le cache OPcache et vérifiez les ressources

### Logs utiles

```bash
# Logs du conteneur WordPress
docker-compose logs wordpress

# Logs du conteneur MariaDB
docker-compose logs mariadb

# Logs en temps réel
docker-compose logs -f
```

## 📞 Support

- **Issues GitHub** : [https://github.com/yubigeek/yubigeek/issues](https://github.com/yubigeek/yubigeek/issues)
- **Site web** : [https://www.yubigeek.com](https://www.yubigeek.com)

## 📄 Licence

Ce projet est sous licence [MIT](LICENSE).

---

**Note** : Ce projet est optimisé pour fonctionner avec Coolify, mais peut être déployé sur n'importe quelle plateforme supportant Docker Compose.

