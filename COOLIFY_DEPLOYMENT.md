# Déploiement de Postiz sur Coolify (Port 15000)

Ce guide vous explique comment déployer Postiz sur Coolify en utilisant le port 15000.

## 📋 Prérequis

- Un serveur Coolify configuré et fonctionnel
- Docker et Docker Compose installés sur le serveur
- Un compte Cloudflare R2 (pour le stockage des images et avatars)
- (Optionnel) Un compte Resend pour l'envoi d'emails

## 🚀 Instructions de déploiement

### 1. Créer votre fichier .env

```bash
# Copiez le fichier d'exemple
cp .env.coolify .env
```

### 2. Configurer les variables d'environnement obligatoires

Éditez le fichier `.env` et modifiez au minimum ces valeurs :

#### a) URL de l'application
Remplacez `localhost` par votre domaine ou IP :
```env
FRONTEND_URL=http://votre-domaine.com:15000
NEXT_PUBLIC_BACKEND_URL=http://votre-domaine.com:15000/api
BACKEND_INTERNAL_URL=http://localhost:3000
```

#### b) Secret JWT
Générez une chaîne aléatoire sécurisée :
```bash
# Exemple de génération
openssl rand -base64 32
```
Puis modifiez :
```env
JWT_SECRET=votre-chaine-aleatoire-securisee-ici
```

#### c) Cloudflare R2 (REQUIS)
Postiz utilise Cloudflare R2 pour stocker les avatars des comptes de réseaux sociaux. Configurez :
```env
CLOUDFLARE_ACCOUNT_ID=votre-account-id
CLOUDFLARE_ACCESS_KEY=votre-access-key
CLOUDFLARE_SECRET_ACCESS_KEY=votre-secret-access-key
CLOUDFLARE_BUCKETNAME=votre-bucket-name
CLOUDFLARE_BUCKET_URL=https://votre-bucket-url.r2.cloudflarestorage.com/
CLOUDFLARE_REGION=auto
```

**Comment obtenir ces valeurs :**
1. Connectez-vous à votre compte Cloudflare
2. Allez dans R2 Object Storage
3. Créez un nouveau bucket
4. Générez des tokens API R2

### 3. Configurer les réseaux sociaux (Optionnel)

Pour chaque plateforme que vous souhaitez utiliser, configurez les clés API correspondantes dans le fichier `.env`.

Par exemple, pour Twitter/X :
```env
X_API_KEY=votre-cle
X_API_SECRET=votre-secret
```

Consultez la documentation de chaque plateforme pour obtenir les clés :
- [Twitter/X Developer Portal](https://developer.twitter.com/)
- [LinkedIn Developers](https://www.linkedin.com/developers/)
- [Facebook Developers](https://developers.facebook.com/)
- etc.

### 4. Déployer sur Coolify

#### Option A : Via l'interface Coolify

1. Créez un nouveau service dans Coolify
2. Sélectionnez "Docker Compose"
3. Collez le contenu du fichier `docker-compose.yml`
4. Configurez les variables d'environnement depuis l'interface
5. Le port 15000 sera automatiquement exposé

#### Option B : Via ligne de commande

```bash
# Depuis le répertoire du projet
docker-compose up -d
```

### 5. Vérifier le déploiement

Après quelques minutes (le premier build peut prendre du temps), accédez à :
```
http://votre-domaine.com:15000
```

## 🔧 Configuration avancée

### Utiliser un nom de domaine avec HTTPS

Pour utiliser HTTPS via Coolify :

1. Dans Coolify, configurez un reverse proxy
2. Mettez à jour les variables d'environnement :
```env
FRONTEND_URL=https://postiz.votredomaine.com
NEXT_PUBLIC_BACKEND_URL=https://postiz.votredomaine.com/api
```

### Activer l'envoi d'emails

Pour activer la vérification par email :

1. Créez un compte sur [Resend](https://resend.com/)
2. Générez une clé API
3. Décommentez et configurez dans `.env` :
```env
RESEND_API_KEY=votre-cle-api-resend
EMAIL_FROM_ADDRESS=noreply@votredomaine.com
EMAIL_FROM_NAME=Postiz
```

### Configurer Stripe (paiements)

Si vous souhaitez activer les paiements :
```env
STRIPE_PUBLISHABLE_KEY=pk_...
STRIPE_SECRET_KEY=sk_...
STRIPE_SIGNING_KEY=whsec_...
```

## 📊 Architecture

L'application déployée comprend :

- **Postiz App** (port 15000) : Application principale
  - Frontend Next.js (port interne 4200)
  - Backend NestJS (port interne 3000)
  - Nginx reverse proxy (port interne 5000)
- **PostgreSQL** : Base de données
- **Redis** : File d'attente et cache

## 🔍 Dépannage

### L'application ne démarre pas

Vérifiez les logs :
```bash
docker-compose logs -f postiz
```

### Erreur de connexion à la base de données

Attendez que PostgreSQL soit complètement démarré :
```bash
docker-compose logs postgres
```

### Erreur Cloudflare

Vérifiez que vos clés Cloudflare R2 sont correctes et que le bucket existe.

## 📝 Commandes utiles

```bash
# Voir les logs
docker-compose logs -f

# Redémarrer l'application
docker-compose restart postiz

# Arrêter l'application
docker-compose down

# Arrêter et supprimer les volumes (ATTENTION : perte de données)
docker-compose down -v

# Rebuild après modification
docker-compose up -d --build
```

## 🆘 Support

- Documentation officielle : [docs.postiz.com](https://docs.postiz.com)
- Discord : [discord.postiz.com](https://discord.postiz.com)
- GitHub Issues : [github.com/gitroomhq/postiz-app/issues](https://github.com/gitroomhq/postiz-app/issues)

## 📄 Licence

Ce projet est sous licence AGPL-3.0. Voir le fichier LICENSE pour plus de détails.
