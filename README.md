# Calendrier du Club Pays

Page web légère (un seul fichier `index.html`, sans bibliothèque) qui affiche le calendrier Google public
« Club Pays - Montréal » en vue mensuelle. Elle est conçue pour être intégrée dans un iframe (Squarespace ou autre).

- En ligne : <https://oui-quebec.github.io/calendrier-club-pays/>
- Aperçu avec des données fictives : <https://oui-quebec.github.io/calendrier-club-pays/?demo>

Fonctionnalités : vue par mois, navigation (boutons, flèches du clavier, balayage sur mobile), heures de début et de fin,
plusieurs activités par jour (« + N autres » quand la case est pleine), fiche détaillée (lieu, description, ajout à son
agenda), abonnement au calendrier, et affichage en liste sur les petits écrans.

## Fonctionnement

La page lit les événements avec l'API Google Calendar (v3) et une clé API publique. On ne peut pas passer par le flux iCal :
Google n'en autorise pas la lecture depuis un navigateur (CORS). De plus, l'API déplie elle-même les événements récurrents
et gère les fuseaux horaires. Les heures sont toujours affichées à l'heure de Montréal.

Le calendrier utilisé est `c_991bd2f5…@group.calendar.google.com` : les deux URL fournies (lien `cid=` et lien `embed`)
désignent ce même calendrier.

## 1. Créer la clé API (une seule fois, environ 5 minutes)

1. Ouvrir <https://console.cloud.google.com/> et créer un projet (ex. « Calendrier Club Pays »).
2. **API et services → Bibliothèque** : chercher « Google Calendar API », puis cliquer sur **Activer**.
3. **API et services → Identifiants → Créer des identifiants → Clé API**.
4. Modifier la clé pour la restreindre :
   - **Restrictions relatives aux applications** : *Sites Web*, puis ajouter `https://oui-quebec.github.io/*`
     (et `http://localhost:8765/*` pour tester en local).
   - **Restrictions relatives aux API** : *Google Calendar API* seulement.
5. Coller la clé dans `index.html`, dans le bloc `CONFIG` : `apiKey: '…'`.

La clé est visible dans le code de la page. C'est normal pour ce type de clé : les restrictions ci-dessus l'empêchent
de servir ailleurs. L'API Google Calendar est gratuite, il n'y a aucune facturation à configurer.

## 2. Intégrer dans Squarespace

Ajouter un bloc **Code** (ou **Intégrer**) avec :

```html
<iframe src="https://oui-quebec.github.io/calendrier-club-pays/"
        title="Calendrier du Club Pays" loading="lazy"
        style="width:100%; height:760px; border:0;"></iframe>
```

La grille remplit toute la hauteur de l'iframe : plus l'iframe est haute, plus les cases des journées sont grandes.
Sous 700 px de large (mobile), la page passe en mini-calendrier suivi de la liste des activités du mois.

## Personnalisation

- **Couleurs, police, arrondis** : variables CSS en haut de `index.html` (`--accent`, `--bg`, `--font`, `--row-min`…).
- **Premier jour de la semaine** et **bouton « S'abonner »** : bloc `CONFIG` (`weekStartsOn`, `subscribe`).
- **Couleurs des activités** : la couleur choisie pour un événement dans Google Agenda est reprise automatiquement.

## Tester en local

```bash
python -m http.server 8765
```

Puis ouvrir <http://localhost:8765/?demo> (données fictives) ou <http://localhost:8765/> (vraies données, clé requise).

## Déploiement

GitHub Pages publie la branche `main` (racine du dépôt). Chaque `git push` met la page en ligne en une minute environ.
