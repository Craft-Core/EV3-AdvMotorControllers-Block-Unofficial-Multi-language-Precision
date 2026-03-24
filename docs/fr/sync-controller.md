# OFDL MoveSync Controller — Guide d'utilisation

Maintient deux moteurs à la même vitesse pendant les déplacements en ligne droite en corrigeant continuellement la dérive d'encodeur à l'aide d'un contrôleur P.

---

## Concept

```
drift      = E1 − E2
correction = Kp × drift

left_motor  = Power1 − correction
right_motor = Power2 + correction
```

Sans synchronisation, même des moteurs identiques à la même vitesse commandée dériveront avec le temps en raison de différences mécaniques. Ce bloc élimine cette dérive.

---

## Configuration

### Étape 1 — Bloc de configuration (exécuter une fois avant la boucle)

| Paramètre | Description | Valeur typique |
|-----------|-------------|----------------|
| **Ports** | Ports moteurs | `1.B+C` |
| **Kp** | Gain de correction de synchronisation | `0.12` |
| **Power1** | Vitesse de base moteur gauche (−100 à 100) | `50` |
| **Power2** | Vitesse de base moteur droit (−100 à 100) | `50` |
| **LeftInversed** | Inverser la direction du moteur gauche | `False` |
| **RightInversed** | Inverser la direction du moteur droit | `False` |

### Étape 2 — Bloc de synchronisation (exécuter à chaque itération de boucle)

| Paramètre | Description |
|-----------|-------------|
| **E1** | Lecture de l'encodeur gauche |
| **E2** | Lecture de l'encodeur droit |

---

## Modes

| Mode | Description |
|------|-------------|
| `Configuration` | Définir Kp, Power1, Power2, Ports, indicateurs d'inversion (appeler une fois) |
| `SYNC_Std` | Synchronisation avec Power1/Power2 configurés |
| `SYNC_byPwr` | Synchronisation avec Power1/Power2 passés à chaque itération (remplace la config) |

---

## Structure de boucle typique

```
[Reset encoders]
[Configuration: Ports=B+C, Kp=0.12, Power1=60, Power2=60]

Loop:
  [Read Encoder B] → E1
  [Read Encoder C] → E2
  [SYNC_Std: E1, E2]
```

Avec puissance dynamique :

```
Loop:
  [Read Encoder B] → E1
  [Read Encoder C] → E2
  [SYNC_byPwr: Power1=speed, Power2=speed, E1, E2]
```

---

## Conseils

- Définissez `Power1 = Power2` pour une trajectoire rectiligne. Des valeurs intentionnellement inégales produiront un arc constant.
- Utilisez `LeftInversed` / `RightInversed` si vos moteurs sont physiquement en miroir (l'un orienté vers l'avant, l'autre vers l'arrière).
- Augmentez `Kp` pour une synchronisation plus précise ; si le robot oscille, réduisez-le.
- Combinez avec l'**Acceleration Controller** pour maintenir les moteurs synchronisés pendant la montée et la descente en vitesse : envoyez `Output` dans `SYNC_byPwr`.
