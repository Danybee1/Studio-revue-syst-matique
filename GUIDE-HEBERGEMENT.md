# SR·MA Studio — Mettre le site en ligne

Ce dossier contient **la version web** de SR·MA Studio. L'application est un seul
fichier `index.html` autonome (HTML + CSS + JavaScript, sans dépendance) : elle
fonctionne dès qu'elle est ouverte dans un navigateur.

---

## ⚠️ À lire avant de publier

Deux fichiers **ne doivent PAS être mis en ligne** :

| Fichier | Pourquoi |
|---------|----------|
| `generateur-licence.html` | Il contient la clé secrète qui fabrique les codes d'activation. Publié, n'importe qui pourrait générer des licences gratuites. **Gardez-le uniquement sur votre ordinateur.** |
| `relais-ia/` (sources) | Ce dossier se **déploie** séparément (voir §4), il ne se dépose pas sur le site public tel quel. |

Ce qui se publie sur le site : **`index.html`**, `README.md`, `LICENSE`,
`CITATION.cff`, `.nojekyll`.

> Rappel : ne diffusez pas non plus le code source de façon modifiable. Le
> contrôle d'abonnement est intégré à `index.html` ; il est dissuasif mais pas
> inviolable. Publier le site pour utilisation est normal ; publier le code
> comme « projet ouvert » à copier ne l'est pas.

---

## 1. Solution la plus simple — GitHub Pages (gratuit)

1. Créez un compte sur https://github.com puis un **nouveau dépôt** (par ex.
   `srma-studio`), en **privé** de préférence.
2. Déposez-y **uniquement** : `index.html`, `README.md`, `LICENSE`,
   `CITATION.cff`, `.nojekyll`.
   (Glissez-déposez les fichiers dans *Add file → Upload files*.)
3. Dans le dépôt : **Settings → Pages → Branch : `main` / `/root` → Save**.
4. Au bout d'une minute, votre site est en ligne à l'adresse :
   `https://VOTRE-NOM.github.io/srma-studio/`

Le fichier `.nojekyll` (déjà présent) évite que GitHub retraite la page.

---

## 2. Autres hébergeurs gratuits

Tous acceptent un site d'un seul fichier. Il suffit d'y déposer `index.html`.

- **Netlify** (https://app.netlify.com/drop) : glissez le dossier, c'est en ligne.
- **Cloudflare Pages** (https://pages.cloudflare.com) : connectez le dépôt.
- **Vercel** (https://vercel.com) : *Import project*.

---

## 3. Hébergement classique (cPanel, OVH, ionos…)

Envoyez `index.html` (et les documents) dans le dossier `public_html/` de votre
hébergeur par FTP ou par le gestionnaire de fichiers. L'adresse devient
`https://votre-domaine.com/index.html`.

---

## 4. Activer l'assistant IA (facultatif)

L'application marche **sans** l'IA : sélection, biais, extraction, méta-analyse
et PRISMA sont pleinement opérationnels. L'assistant IA, lui, a besoin d'un
**relais** qui garde votre clé API secrète côté serveur.

1. Créez un compte Cloudflare (gratuit).
2. Suivez `relais-ia/LISEZMOI.md` (5 étapes) pour déployer `relais-ia/worker.js`.
3. Copiez l'URL du relais obtenue.
4. Ouvrez `index.html`, trouvez en haut du script la ligne
   `const AI_ENDPOINT="";` et mettez-y l'URL du relais entre les guillemets.
5. Dans `relais-ia/worker.js`, réglez `ORIGINE_AUTORISEE` sur l'adresse de votre
   site (celle du §1, 2 ou 3), pour que seul votre site puisse utiliser le relais.
6. Republiez `index.html`.

Sans cette étape, l'onglet Assistant IA affiche simplement qu'il est désactivé ;
tout le reste fonctionne.

---

## 5. Générer les codes d'activation (pour vous, l'éditeur)

Quand un client a payé (Orange Money +226 66 84 11 81 ou Telecel Money
+226 78 55 18 67) :

1. Ouvrez **`generateur-licence.html`** sur **votre** ordinateur (double-clic).
2. Choisissez la formule (mensuel / annuel), cliquez **Générer**.
3. Copiez le code `SRMA-XXXX-XXXX-XXXX` et envoyez-le au client.

Le client le saisit dans l'onglet **Licence** du site : sa version complète se
débloque jusqu'à l'échéance. Le champ « Client » du générateur sert uniquement à
votre suivi (il n'est pas inclus dans le code).

---

## 6. Où sont enregistrés les projets des utilisateurs

Dans le navigateur de chaque utilisateur, sur son appareil (`localStorage`).
Concrètement :

- Les projets **restent** d'une session à l'autre sur le même appareil/navigateur.
- Ils **ne sont pas** stockés sur votre serveur : vous n'avez aucune donnée à
  gérer, et la confidentialité des travaux est préservée.
- Pour passer d'un appareil à un autre, l'utilisateur clique **Exporter** (fichier
  `.json`) puis **Importer** sur l'autre appareil.
- La licence suit la même logique : elle est enregistrée sur l'appareil où le
  code a été saisi. Pour l'utiliser ailleurs, ressaisir le même code.

---

## Récapitulatif des fichiers

| Fichier | Rôle | À mettre en ligne ? |
|---------|------|----------------------|
| `index.html` | L'application complète | **Oui** |
| `README.md` | Documentation générale | Oui (facultatif) |
| `LICENSE` | Conditions d'utilisation | Oui (facultatif) |
| `CITATION.cff` | Métadonnées de citation | Oui (facultatif) |
| `.nojekyll` | Réglage GitHub Pages | Oui |
| `GUIDE-HEBERGEMENT.md` | Ce guide | Non (pour vous) |
| `generateur-licence.html` | Génère les codes | **Non — privé** |
| `relais-ia/` | Relais IA à déployer à part | Se déploie (§4), ne se dépose pas |

---

Éditeur : **OUEDRAOGO W. Daniel** — danielouedraogo1@gmail.com
+226 61 00 25 69 / 66 84 11 81 / 78 55 18 67
