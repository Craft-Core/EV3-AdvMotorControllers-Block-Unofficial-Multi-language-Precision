# OFDL Acceleration Controller — Guide d'utilisation

Génère un profil de vitesse fluide qui **monte** de `MinPwr` à `MaxPwr` sur `AccelDist` degrés, maintient `MaxPwr` au milieu, puis **descend** à `MinPwr` sur `DecelDist` degrés — le tout basé sur des lectures d'encodeur en temps réel.

---

## Concept

```
Encoder position →

0 ────── AccelDist ──────── (FullDist − DecelDist) ──────── FullDist
│ ramp up │       full speed        │      ramp down      │
MinPwr → MaxPwr              MaxPwr              MaxPwr → MinPwr
```

Le bloc **produit une valeur de vitesse** — connectez-la à l'entrée de puissance moteur à chaque itération de boucle. Il ne pilote pas les moteurs directement.

---

## Configuration

### Étape 1 — Bloc de configuration (exécuter une fois avant la boucle)

| Paramètre | Description | Valeur typique |
|-----------|-------------|----------------|
| **EncPort** | Ports moteurs encodeurs | `1.B+C` |
| **MaxPwr** | Vitesse maximale (−100 à 100) | `90` |
| **MinPwr** | Vitesse minimale au départ/à l'arrivée (−100 à 100) | `15` |
| **AccelDist** | Distance d'accélération (degrés) | `300` |
| **DecelDist** | Distance de décélération (degrés) | `300` |
| **FullDist** | Distance totale de déplacement (degrés) | `1000` |

### Étape 2 — Bloc de sortie (exécuter à chaque itération de boucle)

#### Sorties

| Sortie | Description |
|--------|-------------|
| **Output** | Vitesse actuelle à appliquer aux moteurs |
| **E1Output** | Lecture actuelle de l'encodeur 1 (degrés) |
| **E2Output** | Lecture actuelle de l'encodeur 2 (degrés) |
| **Finish** | `True` quand `FullDist` est atteint |

---

## Modes

| Mode | Description |
|------|-------------|
| `Configuration` | Définir tous les paramètres (appeler une fois avant la boucle) |
| `ACDC_Output1Degs` | Sortie vitesse en utilisant l'encodeur 1 uniquement |
| `ACDC_Output2Degs` | Sortie vitesse en utilisant les deux encodeurs (moyenne) |

---

## Structure de boucle typique

```
[Reset encoders (Advanced Encoder block)]
[Configuration: EncPort=B+C, MaxPwr=90, MinPwr=15, AccelDist=300, DecelDist=300, FullDist=1000]

Loop until Finish = True:
  [ACDC_Output2Degs] → Output, E1Output, E2Output, Finish
  [Drive motors with Output]
  [PD Controller if line following]
```

---

## Conseils

- **Réinitialisez toujours les encodeurs** avant de démarrer (utilisez le bloc Advanced Encoder `EncReset`).
- `AccelDist + DecelDist` doit être inférieur à `FullDist`, sinon les profils se chevauchent.
- Combinez avec le **PD Controller** (modes `PDpwr_*`) pour un suivi de ligne fluide à vitesse variable : envoyez `Output` comme entrée de puissance dans le bloc PD.
- Utilisez `ACDC_Output1Degs` pour les tâches à moteur unique ; utilisez `ACDC_Output2Degs` pour les entraînements à deux moteurs afin de compenser la dérive des encodeurs.
