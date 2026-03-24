# OFDL PD Controller — Guide d'utilisation

Un contrôleur PD (Proportionnel-Dérivé) de suivi de ligne qui lit deux capteurs de couleur et pilote deux moteurs pour maintenir le robot centré sur la ligne.

---

## Concept

```
error      = P1 − P2
derivative = error − prev_error

correction = Kp × error + Kd × derivative

left_motor  = Power + correction
right_motor = Power − correction
```

- **Contrôle P uniquement** (modes `P_*`) : utilise seulement `Kp × error`
- **Contrôle PD** (modes `PD_*`) : utilise `Kp` et `Kd`
- **PDpwr** (modes `PDpwr_*`) : identique à PD, mais lit Power depuis la configuration globale

---

## Configuration

### Étape 1 — Bloc de configuration (exécuter une fois avant la boucle)

| Paramètre | Description | Valeur typique |
|-----------|-------------|----------------|
| **Ports** | Ports moteurs (B+C) | `1.B+C` |
| **Kp** | Gain proportionnel | `0.3` |
| **Kd** | Gain dérivé | `0.7` |
| **Power** | Vitesse moteur de base (−100 à 100) | `50` |

### Étape 2 — Bloc de contrôle (exécuter à chaque itération de boucle)

| Paramètre | Description |
|-----------|-------------|
| **P1** | Valeur brute du capteur de couleur gauche |
| **P2** | Valeur brute du capteur de couleur droit |

---

## Modes

| Mode | Catégorie | Description |
|------|-----------|-------------|
| `Configuration` | — | Définir Kp, Kd, Power, Ports (appeler une fois) |
| `P_Large` | P\_byVars | Contrôle P, ports grands moteurs |
| `P_Medium` | P\_byVars | Contrôle P, ports moteurs moyens |
| `P_twoRev` | P\_byVars | Contrôle P, direction moteur inversée |
| `PD_Large` | PD\_byVars | Contrôle PD, ports grands moteurs |
| `PD_Medium` | PD\_byVars | Contrôle PD, ports moteurs moyens |
| `PD_twoRev` | PD\_byVars | Contrôle PD, direction moteur inversée |
| `PDpwr_Large` | PDpwr\_byVars | PD + puissance configurable, grands moteurs |
| `PDpwr_Medium` | PDpwr\_byVars | PD + puissance configurable, moteurs moyens |
| `PDpwr_twoRev` | PDpwr\_byVars | PD + puissance configurable, inversé |

---

## Structure de boucle typique

```
[Configuration: Ports=B+C, Kp=0.3, Kd=0.7, Power=50]

Loop:
  [Read Color Sensor 1] → P1
  [Read Color Sensor 2] → P2
  [PD_Large: P1, P2]
```

---

## Conseils

- Commencez avec `Kp=0.3, Kd=0.7`. Augmentez `Kp` pour une correction plus rapide ; augmentez `Kd` pour réduire les oscillations.
- Utilisez les modes `PDpwr_*` pour varier la vitesse dynamiquement (par ex., combiné avec l'Acceleration Controller).
- Utilisez `P_twoRev` / `PD_twoRev` si les moteurs gauche et droit de votre robot sont montés en orientations opposées.
