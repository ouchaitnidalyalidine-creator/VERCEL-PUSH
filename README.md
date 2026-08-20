# Fiche d'intervention — Déploiement Vercel + Firestore

L'application est un **fichier HTML unique** (`index.html`) qui utilise déjà
Firebase Firestore côté navigateur (import direct via CDN, config codée en
dur dans le script). Il n'y a **aucun backend à écrire** : Vercel sert juste
le fichier statique, et Firestore reste la base de données (déjà branchée).

---

## 1. Sécuriser Firestore (à faire une seule fois)

1. Va sur https://console.firebase.google.com → projet `fiche-intervention-12ea6`
2. Firestore Database → onglet **Rules**
3. Colle le contenu de `firestore.rules` (fourni dans ce dossier) et clique **Publier**

Ça limite l'accès au seul document utilisé par l'app
(`fiche-intervention/gestion-data`) et bloque le reste par défaut.

> Le `apiKey` visible dans le HTML n'est PAS un secret — c'est normal et
> attendu pour une app Firebase côté client. La vraie protection vient des
> **Rules** ci-dessus, pas du fait de cacher la clé.

---

## 2. Déployer sur Vercel

### Option A — en ligne de commande (rapide)

```bash
npm install -g vercel        # si pas déjà installé
cd deploy                    # dossier contenant index.html
vercel login                 # ouvre le navigateur pour te connecter
vercel                       # déploiement de test (preview)
vercel --prod                # déploiement en production
```

Vercel détecte automatiquement un site statique (pas de build nécessaire).
À la fin, il te donne une URL du type `https://ton-projet.vercel.app`.

### Option B — via GitHub (recommandé pour les mises à jour futures)

1. Crée un dépôt GitHub et pousse ce dossier dedans :
   ```bash
   cd deploy
   git init
   git add .
   git commit -m "Fiche d'intervention - version initiale"
   git branch -M main
   git remote add origin https://github.com/TON-COMPTE/fiche-intervention.git
   git push -u origin main
   ```
2. Sur https://vercel.com → **Add New → Project** → importe le dépôt GitHub
3. Vercel détecte le projet statique automatiquement (aucun réglage de build à changer)
4. Clique **Deploy**

Avec cette méthode, chaque `git push` redéploie automatiquement.

---

## 3. Restreindre l'accès à l'app elle-même (optionnel mais recommandé)

Le PIN `2112` protège seulement la modification des fiches passées dans
l'Historique — pas l'accès général au site. Si tu veux que seuls tes agents
y accèdent :

- **Simple** : ne partage pas l'URL Vercel publiquement (elle n'est pas
  indexée par défaut, mais reste accessible à qui a le lien).
- **Vercel Password Protection** (plan Pro) : Project Settings → Deployment
  Protection → active la protection par mot de passe.
- **Authentification Firebase** : voir la note dans `firestore.rules` pour
  ajouter une connexion (email/mot de passe) avant l'accès aux données.

---

## 4. Domaine personnalisé (optionnel)

Project Settings → Domains → ajoute ton domaine (ex: `fiche.yalidine-interne.dz`)
et suis les instructions DNS affichées par Vercel.

---

## Fichiers de ce dossier

| Fichier | Rôle |
|---|---|
| `index.html` | L'application complète (formulaire + admin + historique) |
| `vercel.json` | Config Vercel (headers de sécurité, URLs propres) |
| `firestore.rules` | Règles de sécurité Firestore à publier dans la console Firebase |
| `README.md` | Ce guide |
