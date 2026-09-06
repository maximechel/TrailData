# Loadscope

Un tableau de bord de suivi de la charge d'entraînement pour coachs et athlètes d'endurance — pensé pour être **lisible en un coup d'œil**, là où beaucoup d'outils du marché (PlaySharp, Strava, TrainingPeaks...) noient l'information utile dans des tableaux denses.

Application web autonome : un seul fichier `index.html`, aucune inscription, aucun serveur, aucune dépendance à installer. Les données restent dans le navigateur de la personne qui l'utilise.

## Ce que ça fait

Pour chaque athlète, Loadscope calcule et visualise :

- **La charge quotidienne (TRIMP)** à partir du temps passé dans 5 zones de fréquence cardiaque (méthode d'Edwards : `Z1×1 + Z2×2 + Z3×3 + Z4×4 + Z5×5`, en minutes pondérées).
- **La charge aiguë** (moyenne glissante sur 7 jours) et **la charge chronique** (moyenne glissante sur 28 jours).
- **L'ACWR** (*Acute:Chronic Workload Ratio*, ratio charge aiguë / charge chronique), avec une lecture à quatre niveaux directement inspirée de la littérature en sciences du sport :
  - `< 0.8` — sous-charge (risque de désentraînement)
  - `0.8 – 1.3` — zone optimale
  - `1.3 – 1.5` — zone de prudence
  - `> 1.5` — risque élevé de blessure
- **Le volume hebdomadaire** (km) et **le dénivelé** (D+ / D-).
- **La répartition du temps en zones de FC**, semaine par semaine.
- Un **message d'analyse en langage clair**, généré automatiquement à partir de la tendance récente (ex. *"Charge en hausse de 61 % sur 7 jours — envisager une semaine de décharge"*), plutôt qu'un chiffre brut à interpréter soi-même.

C'est ce dernier point qui est le vrai différenciateur : la plupart des outils affichent la donnée, peu la traduisent en décision d'entraînement.

## Prise en main

1. Ouvrez `index.html` (double-clic, ou via GitHub Pages une fois déployé — voir plus bas).
2. L'application démarre avec deux athlètes d'exemple et ~10 semaines de séances simulées, pour que le tableau de bord ne soit jamais vide au premier lancement. Supprimez-les dès que vous ajoutez vos vrais athlètes (bouton *Supprimer* sur la fiche athlète).
3. Ajoutez un athlète (*+ Athlète*), puis ses séances (*+ Séance*), ou importez-les en une fois via un fichier CSV (voir `data/modele-import.csv` pour le format attendu).
4. Les données sont sauvegardées automatiquement dans le navigateur (`localStorage`). Pensez à **exporter régulièrement une sauvegarde JSON** (bouton *Exporter*) : vider le cache du navigateur efface les données locales.

### Format du CSV d'import

Colonnes attendues (voir `data/modele-import.csv`) :

```
date,type,duration,distance,dPlus,dMinus,z0,z1,z2,z3,z4,rpe
2026-01-15,Course à pied,55,10.2,120,110,10,30,10,4,1,5
```

`z0` à `z4` correspondent aux minutes passées en zones FC 1 à 5. `duration` est en minutes, `distance` en km, `dPlus`/`dMinus` en mètres, `rpe` optionnel (1-10).

## Déployer sur GitHub Pages

```bash
git init
git add .
git commit -m "Première version de Loadscope"
git branch -M main
git remote add origin <url-de-votre-dépôt>
git push -u origin main
```

Puis dans les paramètres du dépôt GitHub : **Settings → Pages → Source : branche `main`, dossier `/ (root)`**. L'application sera accessible à une URL du type `https://<votre-compte>.github.io/<nom-du-dépôt>/`.

Aucune étape de build n'est nécessaire : `index.html` charge Chart.js et les polices depuis des CDN publics au moment de l'ouverture de la page.

## Limites connues (v1)

- **Aucune synchronisation entre appareils** : les données vivent dans le navigateur utilisé. Pour un usage multi-appareils ou multi-coachs, il faudra ajouter un backend (voir pistes ci-dessous).
- **Pas de connexion directe à Strava / Garmin** : la saisie se fait manuellement ou par import CSV. C'est un choix délibéré pour la v1 (zéro backend, zéro clé API à gérer) — voir pistes d'évolution.
- **L'ACWR a besoin d'historique** : le ratio n'est affiché qu'à partir de 14 jours de données pour un athlète donné, le temps que le calcul se stabilise.

## Pistes d'évolution

- Connexion à l'API Strava/Garmin pour importer les séances automatiquement (webhook à l'upload, dans l'esprit de ce que documente Enduraw pour l'ajustement météo/altitude).
- Backend léger (ex. Supabase) pour partager les données entre le coach et ses athlètes, et entre appareils.
- Ajustement contextuel de la charge par les conditions externes (météo, altitude, dénivelé) — l'angle sur lequel Enduraw s'est positionné.
- Comparaison inter-athlètes et alertes automatiques (email/notification) quand un ACWR passe en zone de risque élevé.

## Méthodologie

Le TRIMP (*Training Impulse*) et le rapport charge aiguë/chronique (ACWR) sont des mesures reconnues en sciences du sport pour objectiver la charge d'entraînement et son évolution, mais restent des **indicateurs d'aide à la décision**, pas des vérités absolues : ils ne remplacent pas le regard du coach sur la fatigue, le sommeil, le contexte de vie de l'athlète, etc.

## Licence

MIT — voir `LICENSE`.
