# 1P1A (One Page One App) & OJapp Dynamic PWA Suite

> Transformez une page, un répertoire ou un site entier en Progressive Web App (PWA) autonome grâce à un script léger côté client et à un manifeste Data URL généré dynamiquement.

---

## 🌟 Présentation

**1P1A (One Page One App)** est une approche PWA côté client qui évite de créer et de maintenir un fichier `manifest.json` statique pour chaque page ou outil.

Ajoutez un script OJapp dans le `<head>` de la page : il génère dynamiquement un manifeste Web App sous forme d’URL `data:application/manifest+json`. Selon le mode choisi, les visiteurs peuvent ajouter à leur écran d’accueil une page, un groupe de répertoires ou un site entier, avec son propre nom d’application, sa description, son icône, son identité, son URL de démarrage, son périmètre de navigation et sa présentation d’installation.

Les scripts principaux ne nécessitent ni étape de build, ni inscription, ni API externe, ni génération de manifeste côté serveur.

---

## 🚀 Fonctionnalités principales

- **Aucun build et aucun backend :** fonctionne sur les hébergeurs statiques comme Cloudflare Pages, GitHub Pages, Vercel et les serveurs web traditionnels.
- **Manifestes Data URL dynamiques :** génère le manifeste Web App dans le navigateur au moment de l’exécution.
- **Trois structures d’application :** prend en charge les applications par page (1P1A), par répertoire (1G1A) et pour tout le site (1S1A).
- **Gestion facultative des paramètres de requête :** ajoute la chaîne de requête actuelle à `id`, `start_url` et `scope` lorsqu’elle est activée.
- **Métadonnées personnalisées :** configurez le titre, la description, l’icône, l’identité, l’URL de démarrage et le périmètre de navigation avec des balises meta.
- **Présentation d’installation :** ajoutez une description dédiée et une ou plusieurs captures d’écran (jusqu’à cinq) sans maintenir de manifeste statique.
- **Exécution entièrement côté client :** les scripts Free fonctionnent sans compte utilisateur ni dépendance à une API externe.

---

## 💻 Démarrage rapide et structures d’application

OJapp lit ses balises meta au moment où le script s’exécute. Placez toutes les balises meta OJapp **avant** la balise script.

### 1. 1P1A : One Page One App

Transformez la page actuelle — ou une instance de cette page configurée par requête — en application sur l’écran d’accueil de l’utilisateur.

```html
<!-- Les paramètres facultatifs doivent précéder le script -->
<meta name="ojapp:query" content="true">
<meta name="ojapp:title" content="Mon outil personnalisé">
<meta name="ojapp:icon" content="/icon.png">

<script src="https://ojapp.app/js/ojapp_1p1a.js"></script>
```

Sans balises meta facultatives, l’URL, le titre et l’icône disponible de la page sont utilisés automatiquement.

### 2. 1S1A : One Site One App

Transformez l’origine entière en une seule application unifiée.

```html
<script src="https://ojapp.app/js/ojapp_1s1a.js"></script>
```

Par défaut, `id`, `start_url` et `scope` utilisent la racine de l’origine (`/`).

### 3. 1G1A : One Group One App

Transformez un répertoire tel que `/dashboard/`, `/tools/` ou `/docs/` en application distincte sous la même origine.

```html
<meta name="ojapp:id" content="/dashboard/">
<meta name="ojapp:start-url" content="/dashboard/">
<meta name="ojapp:scope" content="/dashboard/">

<script src="https://ojapp.app/js/ojapp_1s1a.js"></script>
```

Chaque groupe de répertoires peut utiliser un ID et un scope différents, ce qui permet de créer plusieurs applications de groupe sous une même origine sans fichiers de manifeste ni pipelines de build séparés.

---

## 🔗 Applications configurées par requête

Activez la gestion des requêtes avec :

```html
<meta name="ojapp:query" content="true">
```

Par exemple :

```text
/timer/?time=5&mode=down&seconds=on&icon=blue
```

OJapp inclut la chaîne de requête actuelle dans les valeurs suivantes du manifeste généré :

- `id`
- `start_url`
- `scope`

L’URL peut ainsi conserver l’état sélectionné d’une application, tandis que `id` distingue les instances configurées et que `start_url` restaure cet état au lancement.

Les navigateurs traitent les champs du manifeste selon leur propre implémentation. Un navigateur peut notamment normaliser ou supprimer la requête du `scope` traité. OJapp écrit tout de même la requête configurée dans les trois champs et laisse le navigateur effectuer cette normalisation.

Si votre page crée ou modifie la requête après le chargement initial, accédez à l’URL finale ou rechargez-la avant de demander à l’utilisateur de l’ajouter à l’écran d’accueil. Une modification via la seule History API peut ne pas actualiser l’URL capturée par le processus iOS **Sur l’écran d’accueil**.

---

## 🖼️ Présentation d’installation

OJapp peut personnaliser la description et les captures d’écran affichées dans les interfaces d’installation compatibles.

### Description d’installation

Utilisez `ojapp:description` lorsque l’interface d’installation nécessite un texte différent de la description destinée aux moteurs de recherche.

```html
<meta
  name="ojapp:description"
  content="Installez cet outil pour y accéder rapidement depuis votre écran d’accueil."
>
```

OJapp applique l’ordre de priorité suivant :

1. `ojapp:description`
2. Balise standard `<meta name="description">`
3. Aucune `description` dans le manifeste si aucune des deux n’existe

Le texte destiné à la recherche reste ainsi distinct du message d’installation.

### Une capture d’écran

Utilisez la balise d’origine non numérotée pour une seule image.

```html
<meta
  name="ojapp:screenshot"
  content="/images/install.png"
>
```

### Plusieurs captures d’écran

Utilisez des balises numérotées pour ajouter jusqu’à cinq images.

```html
<meta name="ojapp:screenshot-1" content="/images/install-1.png">
<meta name="ojapp:screenshot-2" content="/images/install-2.png">
<meta name="ojapp:screenshot-3" content="/images/install-3.png">
```

Si au moins une balise numérotée est présente, l’ensemble numéroté est prioritaire et la balise non numérotée `ojapp:screenshot` est ignorée.

Toutes les captures d’écran doivent utiliser le même format. Une **image carrée 1:1** est recommandée, car elle reste facile à consulter dans les interfaces d’installation sur ordinateur et mobile. OJapp attribue les captures sélectionnées à `wide` sur ordinateur et à `narrow` sur Android ; la mise en page finale dépend du navigateur.

---

## 🛠️ Référence des métadonnées

| Balise meta | S’applique à | Description | Valeur par défaut / Repli |
| :--- | :--- | :--- | :--- |
| `ojapp:title` | 1P1A / 1S1A | Définit le nom de l’application | 1P1A : `<title>` de la page ; 1S1A : nom d’hôte |
| `ojapp:description` | 1P1A / 1S1A | Définit la description d’installation du manifeste | Meta description standard de la page ; sinon omise |
| `ojapp:icon` | 1P1A / 1S1A | Définit l’URL de l’icône | Icône disponible de la page, puis icône OJapp par défaut |
| `ojapp:screenshot` | 1P1A / 1S1A | Définit une capture d’écran d’installation | Omise |
| `ojapp:screenshot-1` à `ojapp:screenshot-5` | 1P1A / 1S1A | Définit jusqu’à cinq captures ; l’ensemble numéroté est prioritaire | Omises |
| `ojapp:query` | 1P1A / 1S1A | Définir sur `"true"` pour inclure la requête actuelle dans `id`, `start_url` et `scope` | Désactivé ; requête supprimée |
| `ojapp:id` | 1S1A / 1G1A | Définit l’identité de l’application dans le manifeste | Racine de l’origine |
| `ojapp:start-url` | 1S1A / 1G1A | Définit l’URL ouverte depuis l’écran d’accueil | Racine de l’origine |
| `ojapp:scope` | 1S1A / 1G1A | Définit le périmètre de navigation | Racine de l’origine |
| `ojapp:exclude` | 1P1A / 1S1A | Définir sur `"true"` pour désactiver OJapp sur la page | Désactivé |

Toutes les valeurs personnalisées de `id`, `start_url` et `scope` doivent correspondre à la même origine que la page actuelle.

---

## 📱 Comportement selon la plateforme

### iOS / iPadOS (Safari)

Les entrées d’écran d’accueil configurées par requête ont été vérifiées sur iPhone. Plusieurs entrées créées depuis la même page peuvent conserver des états de lancement, des noms et des icônes différents.

L’icône installée peut également dépendre de `apple-touch-icon`. Les pages qui choisissent dynamiquement une icône doivent donc la définir avant l’exécution du script OJapp.

### Android (Chrome)

OJapp inclut la requête dans les valeurs générées de `id`, `start_url` et `scope`. Chrome peut normaliser le périmètre de navigation traité conformément à la spécification Web App Manifest. L’identité de plusieurs installations basées sur des requêtes peut varier selon la version du navigateur et de la plateforme ; testez donc le comportement souhaité sur l’appareil Android cible.

---

## 🧭 Choisir un mode

| Mode | Unité d’application | Identité / démarrage / scope par défaut | Usage courant |
| :--- | :--- | :--- | :--- |
| **1P1A** | Page actuelle | Chemin de la page actuelle | Outils, produits, articles, profils |
| **1G1A** | Groupe de répertoires | Chemin de répertoire explicite | Tableaux de bord, documentation, outils groupés |
| **1S1A** | Site entier | Racine de l’origine `/` | PWA conventionnelle pour tout le site |

---

## OJapp FREE

OJapp FREE permet de mettre facilement en œuvre les modèles de conception PWA 1P1A (One Page. One App.) et 1S1A (One Site. One App.).

Pour une utilisation normale, OJapp FREE est fourni via les scripts officiels hébergés à l’adresse :

https://ojapp.app/

Le runtime de production OJapp FREE n’est pas distribué sous forme de fichier JavaScript dans ce dépôt.

Du code source de référence peut être proposé dans ce dépôt sous forme de fichiers `.txt` à des fins d’apprentissage, de recherche, de modification et d’implémentation indépendante.

Si vous souhaitez simplement utiliser OJapp FREE, utilisez le script officiel hébergé décrit dans la documentation.

---

## Licence

Sauf indication contraire, le code source contenu dans ce dépôt est fourni sous licence MIT.

Consultez le fichier `LICENSE` pour plus de détails.

La licence MIT de ce dépôt s’applique uniquement au code source effectivement publié ici.

Le runtime OJapp FREE hébergé sur `ojapp.app` et OJapp PRO sont distincts de ce dépôt et ne sont pas couverts par sa licence MIT.

OJapp PRO est un logiciel commercial propriétaire.

---

## 1P1A et UDA sont des concepts de conception

**1P1A (One Page. One App.)** et **UDA (User Defined App)** sont des concepts de conception PWA, et non des produits.

1P1A décrit une approche où la limite de l’application est définie au niveau d’une page plutôt qu’au niveau du site entier.

UDA pousse cette idée plus loin en permettant aux utilisateurs de définir une application au moyen d’un état d’URL précis, par exemple des paramètres de requête.

OJapp est une implémentation et un service construits autour de ces concepts.

La licence MIT de ce dépôt s’applique uniquement au code source publié ici. Elle ne s’applique pas aux concepts 1P1A ou UDA eux-mêmes.

---

## 🤝 Communauté et retours

Les questions, résultats de tests, cas limites et demandes de fonctionnalités sont les bienvenus dans ce dépôt.
Les implémentations de référence peuvent différer de la version de production actuelle d’OJapp FREE et ne sont pas destinées à suivre chaque mise à jour du runtime officiel hébergé.

- **Site web / Utilitaires :** [OJapp 1P1A](https://ojapp.app/one-page-one-app/en/)
- **Développeur :** OJapp / Ojach
