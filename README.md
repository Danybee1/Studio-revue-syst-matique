# SR·MA Studio

**Revue systématique et méta-analyse, de la sélection des articles à l'analyse.**
Application web autonome, bilingue français/anglais, sans installation ni serveur.

> Développé au sein de l'UFR/SEA de l'Université Joseph KI-ZERBO (Ouagadougou),
> dans le cadre de travaux de radioprotection et dosimétrie menés avec l'ARSN.

---

## Ce que fait l'application

Huit modules couvrant le cycle complet d'une revue, dans l'ordre où on la mène :

| Module | Contenu |
|---|---|
| **Projet** | Question PICO, critères d'éligibilité, sources, équation de recherche, enregistrement PROSPERO |
| **Sélection** | Import multi-formats, détection des doublons, criblage en deux phases (façon Rayyan / Covidence) avec surlignage de mots-clés, mode « un article à la fois », liens vers le texte intégral et motifs d'exclusion normalisés |
| **Qualité & biais** | RoB 2, ROBINS-I, Newcastle-Ottawa, QUADAS-2, AMSTAR-2, JBI (transversale, séries de cas) — l'outil est recommandé selon le type d'étude ; graphique *traffic-light* |
| **Extraction** | Grille de saisie des données binaires ou continues, sous-groupes, notes |
| **Analyse** | OR, RR, RD, MD, SMD ; effets fixes et aléatoires ; hétérogénéité ; forest plot ; funnel plot ; test d'Egger ; sous-groupes |
| **PRISMA & rapport** | Diagramme de flux PRISMA 2020, synthèse rédigée, tableau des caractéristiques |
| **Assistant IA** | Facultatif — pré-criblage, extraction et rédaction assistés (voir plus bas) |
| **Licence** | Essai (300 articles) puis abonnement ; activation par code |

### Formats d'import reconnus (détection automatique)

Déposez un ou plusieurs fichiers dans la zone d'import — le format et la base
d'origine sont détectés automatiquement :

- **RIS** — PubMed, Cochrane, Scopus, Web of Science, Embase, Zotero, Mendeley
- **MEDLINE / `.nbib`** — PubMed « Send to → Citation manager »
- **BibTeX** — Zotero, Google Scholar, Mendeley
- **CSL JSON** — Zotero « CSL JSON »
- **EndNote XML** — Zotero « EndNote XML »
- **Zotero RDF** — export par défaut de Zotero
- **CSV / TSV** — Zotero, Rayyan, Covidence, PubMed, Scopus, Web of Science
- **Excel `.xlsx`** — lecture native (colonnes reconnues en français et en anglais)

Les résumés, DOI, PMID, mots-clés et le type d'étude sont récupérés quand ils
sont présents. Le type d'étude sert à recommander l'outil d'évaluation adapté.

### Exports

- **Figures** (forest, funnel, PRISMA, risque de biais) : **PNG** et **SVG**,
  bouton dédié sur chaque figure, ou « Toutes les figures » d'un coup.
- **Rapport complet en Word `.docx`** : page de garde, PICO, PRISMA (image +
  tableau des flux), caractéristiques des études incluses, risque de biais,
  forest et funnel plots, synthèse rédigée. Généré nativement, sans logiciel ni
  connexion.
- **Projet** complet en `.json`, **études incluses** en `.csv`, **rapport
  imprimable** en PDF.

> Les exports PNG et Word reposent sur des fonctions standard du navigateur
> (`canvas`, `DecompressionStream`). Ils fonctionnent sur les versions récentes
> de Chrome, Edge, Firefox et Safari. Sur un navigateur ancien, l'export PNG
> bascule vers le SVG et l'import `.xlsx` invite à passer par un CSV.

---

## Héberger l'application

L'application est **un seul fichier**, `index.html`. Aucune dépendance à
installer, aucune base de données.

### GitHub Pages (recommandé)

```bash
git clone https://github.com/votre-pseudo/srma-studio.git
cd srma-studio
# déposez vos fichiers, puis :
git add . && git commit -m "Première version" && git push
```

Puis dans le dépôt : **Settings → Pages → Source : Deploy from a branch →
`main` / `(root)`**. L'application est en ligne une minute plus tard à
`https://votre-pseudo.github.io/srma-studio/`.

### Autres options

- **Netlify Drop** (`app.netlify.com/drop`) : glissez le dossier, aucune inscription.
- **Serveur institutionnel** : copiez `index.html` dans le répertoire web
  (`scp index.html utilisateur@serveur:/var/www/html/srma/`).
- **Usage local** : ouvrez `index.html` par double-clic. Tout fonctionne, sauf
  l'assistant IA. Hors connexion, les polices basculent sur une alternative
  système — l'outil reste pleinement utilisable.

---

## Où vont les données

**Nulle part.** Tout est traité dans le navigateur de l'utilisateur et
enregistré dans son `localStorage`. Aucune référence, aucune donnée d'étude
n'est transmise à un serveur. Chacun sauvegarde son travail par le bouton
**Exporter** (fichier `.json`) — c'est aussi ce fichier qu'on échange entre
relecteurs, ou qu'on archive comme donnée supplémentaire d'un article.

Conséquence à connaître : vider les données du navigateur efface le projet en
cours. Exportez régulièrement.

Seule exception : si l'assistant IA est activé, les titres et résumés soumis au
pré-criblage transitent par le relais vers l'API Anthropic. Ne l'activez pas
pour des données non publiées ou confidentielles sans vérifier les règles de
votre institution.

---

## Méthodes statistiques implémentées

### Tailles d'effet

| Mesure | Estimateur | Variance |
|---|---|---|
| **OR** | ln(ad / bc) | 1/a + 1/b + 1/c + 1/d |
| **RR** | ln[(a/n₁) / (c/n₂)] | 1/a − 1/n₁ + 1/c − 1/n₂ |
| **RD** | a/n₁ − c/n₂ | ab/n₁³ + cd/n₂³ |
| **MD** | m₁ − m₂ | s₁²/n₁ + s₂²/n₂ |
| **SMD** | *g* de Hedges = J·d, J = 1 − 3/[4(n₁+n₂) − 9] | J²·[(n₁+n₂)/(n₁n₂) + d²/(2(n₁+n₂−2))] |

Correction de continuité de 0,5 appliquée automatiquement aux tableaux
comportant un zéro (désactivable dans l'onglet Analyse).

### Agrégation

- **Effets fixes** : pondération par l'inverse de la variance, wᵢ = 1/vᵢ.
- **Effets aléatoires** : DerSimonian–Laird, τ² = max(0, (Q − df)/C) avec
  C = Σwᵢ − Σwᵢ²/Σwᵢ ; poids wᵢ* = 1/(vᵢ + τ²).
- **Hétérogénéité** : Q de Cochran (loi du χ² à k−1 ddl), I² = 100·(Q − df)/Q
  selon la définition de Higgins reprise par RevMan, H = √(Q/df).
- **Intervalle de prédiction** (k ≥ 3) : θ ± t₍k−₂₎ · √(τ² + SE²).
- **Biais de publication** : test d'Egger — régression de la déviation normale
  standardisée sur la précision ; ordonnée à l'origine testée par un test *t* à
  k−2 ddl. Peu puissant en deçà de 10 études, conformément à l'avertissement du
  *Cochrane Handbook*, ce que l'application signale.
- **Sous-groupes** : Q entre groupes sur k−1 ddl.

Fonctions numériques écrites sans dépendance externe : erf, χ² par fraction
continue et série de Gauss, loi de Student par fonction bêta incomplète,
quantile normal par l'algorithme d'Acklam.

### Validation

Le moteur a été confronté au jeu de données **BCG** de référence du paquet R
`metafor` (13 essais, risque relatif) :

| Grandeur | `metafor` | SR·MA Studio |
|---|---|---|
| Effets aléatoires (DL), estimation | −0,7145 | −0,7141 |
| Erreur standard | 0,1798 | 0,1787 |
| τ² | 0,3088 | 0,3088 |
| Q (df = 12) | 152,233 | 152,233 |
| Effets fixes, estimation | −0,4303 | −0,4303 |
| Erreur standard | 0,0405 | 0,0405 |

Les distributions ont été vérifiées sur des valeurs critiques tabulées
(χ²₀,₀₅ ; t₀,₀₅ ; z₀,₉₇₅), toutes exactes à trois décimales.

**Écart connu et assumé :** `metafor` publie par défaut un I² fondé sur la
variance intra-étude typique (92,22 % sur ce jeu), là où l'application retient
la définition classique (Q − df)/Q (92,12 %), celle de RevMan et du *Cochrane
Handbook*. Précisez la formule employée dans vos Méthodes.

---

## Assistant IA (facultatif)

Désactivé par défaut. Une fois le relais déployé (voir `relais-ia/LISEZMOI.md`),
il apporte quatre fonctions :

1. **Équation de recherche** générée depuis le PICO, syntaxe PubMed.
2. **Pré-criblage titre/résumé** : décision proposée, motif normalisé, indice de
   confiance, justification brève.
3. **Extraction assistée** depuis un passage collé, avec signalement des valeurs
   introuvables.
4. **Projet de discussion** rédigé à partir des résultats réellement calculés.

### Garde-fous, et pourquoi ils sont là

PRISMA 2020 et le *Cochrane Handbook* font reposer les décisions d'inclusion sur
deux relecteurs humains indépendants. L'IA est donc conçue ici comme un premier
relecteur dont vous êtes le second :

- aucune suggestion n'est appliquée sans un clic explicite ;
- le modèle est instruit d'être conservateur — n'exclure que si l'inéligibilité
  est manifeste, répondre « incertain » sinon ;
- l'extraction interdit toute estimation : une valeur absente du texte reste
  vide ;
- **le jugement du risque de biais n'est pas automatisé**, délibérément. Évaluer
  le secret de l'attribution demande un jugement que le modèle imiterait de
  façon plausible mais peu fiable ; c'est là que le risque pour la crédibilité
  du travail est le plus grand ;
- chaque intervention est horodatée dans un **journal d'audit** exporté avec le
  projet.

Ce journal sert à rédiger la déclaration d'usage d'IA que les revues exigent
désormais : modèle, date, tâches confiées, mode de validation humaine.

---

## Structure du dépôt

```
srma-studio/
├── index.html                 L'application complète (HTML + CSS + JS, sans dépendance)
├── generateur-licence.html    ⚠ PRIVÉ — génère les codes d'activation (ne jamais publier)
├── README.md                  Ce fichier
├── LICENSE                    Licence d'utilisation (propriétaire)
├── CITATION.cff               Métadonnées de citation — à compléter
├── .nojekyll                  Empêche GitHub Pages de retraiter les fichiers
├── windows/                   Sources de l'installateur Windows (NSIS)
└── relais-ia/
    ├── worker.js         Relais Cloudflare gardant la clé API côté serveur
    ├── wrangler.toml     Configuration de déploiement
    └── LISEZMOI.md       Mode d'emploi du relais
```

---

## Obtenir un DOI citable

Liez le dépôt à **Zenodo** (`zenodo.org` → *GitHub* → activez le dépôt), puis
créez une *release* sur GitHub. Zenodo génère un DOI permanent, et
`CITATION.cff` alimente automatiquement la notice. Votre outil devient citable :

> Analyses réalisées avec SR·MA Studio v1.0.0 (doi:10.5281/zenodo.XXXXXXX).

---

## Abonnement et activation

L'application fonctionne selon un modèle **essai puis abonnement** :

| Formule  | Prix          | Limite d'articles importés |
|----------|---------------|-----------------------------|
| Essai    | gratuit       | 300 maximum                 |
| Mensuel  | 10 000 FCFA   | illimité                    |
| Annuel   | 100 000 FCFA  | illimité (2 mois offerts)   |

Le jeu de démonstration n'entre pas dans le quota. Tous les modules (biais,
extraction, méta-analyse, PRISMA) restent utilisables en essai ; seule
l'importation au-delà de 300 articles est réservée à la version complète.

### Paiement (mobile money, Burkina Faso)

- **Orange Money** : +226 66 84 11 81 — OUEDRAOGO W. Daniel
- **Telecel Money** : +226 78 55 18 67 — OUEDRAOGO W. Daniel

### Comment ça marche

1. Le client paie sur l'un des numéros ci-dessus.
2. Il envoie la preuve de paiement et son e-mail à l'éditeur.
3. L'éditeur génère un **code d'activation** avec `generateur-licence.html`
   (voir ci-dessous) et le lui transmet.
4. Le client saisit le code dans l'onglet **Licence** ; la version complète
   se débloque, hors ligne, jusqu'à l'échéance.

### Générateur de codes — `generateur-licence.html`

> ⚠️ **Fichier strictement privé.** Il contient la clé secrète qui fabrique les
> codes valides. **Ne le déposez jamais en ligne** (ni sur GitHub Pages, ni
> ailleurs) et ne le partagez avec personne. Gardez-le sur votre ordinateur.
> Il ne doit **pas** figurer dans la version publiée/hébergée de l'application.

Ouvrez-le dans un navigateur, choisissez la formule, cliquez sur *Générer* :
il produit un code `SRMA-XXXX-XXXX-XXXX` daté, que l'application sait valider.

### Ce que ce dispositif protège — et ce qu'il ne protège pas

Le contrôle de licence se fait **hors ligne**, dans le navigateur. C'est un
dispositif **dissuasif**, honnête et adapté à une diffusion par mobile money :
un client ordinaire ne peut pas fabriquer de code valide ni deviner le vôtre.

En revanche, comme toute application sans serveur, il **n'est pas inviolable** :
une personne techniquement avertie ayant accès au code source pourrait le
contourner. Deux conséquences pratiques :

1. **Ne publiez pas le code source** de l'application (ce serait rendre le
   contournement trivial). Distribuez uniquement l'**installateur Windows** ou
   une version hébergée dont le code n'est pas destiné à être lu/modifié.
2. Pour un contrôle **strict et infalsifiable** (vérification automatique du
   paiement, révocation à distance, quotas côté serveur), il faudrait ajouter
   un petit serveur de licences. Le relais Cloudflare déjà fourni pour l'IA
   pourrait servir de base à cette évolution.

---

## Limites

- Modèle à effets aléatoires par la méthode des moments (DerSimonian–Laird) ;
  ni Paule–Mandel, ni maximum de vraisemblance restreint, ni correction de
  Hartung–Knapp.
- Pas de méta-régression, pas de méta-analyse en réseau, pas de méthode de
  Peto ni de Mantel–Haenszel pour les événements rares.
- Un seul critère de jugement par projet.
- Le criblage à deux relecteurs indépendants se fait en échangeant deux fichiers
  `.json` ; le calcul du kappa de Cohen n'est pas intégré.
- Le contrôle d'abonnement est **hors ligne** et donc dissuasif, non inviolable
  (voir la section Abonnement).

L'application produit des chiffres justes sur les méthodes qu'elle implémente,
mais elle ne remplace pas le jugement méthodologique. **Faites vérifier vos
résultats par un statisticien avant publication.**

## Éditeur

Logiciel conçu et édité par **OUEDRAOGO W. Daniel**.
Contact : danielouedraogo1@gmail.com · +226 61 00 25 69 / 66 84 11 81 / 78 55 18 67

## Licence d'utilisation

Logiciel **propriétaire** — © 2026 OUEDRAOGO W. Daniel, tous droits réservés.
Voir `LICENSE`. L'usage est soumis à l'abonnement décrit ci-dessus. La
redistribution du code source, la modification et la revente ne sont pas
autorisées sans accord écrit de l'éditeur. *(Un modèle propriétaire est
nécessaire pour que le contrôle d'abonnement ait un sens ; une licence libre
type MIT permettrait légalement d'en retirer le paywall.)*
