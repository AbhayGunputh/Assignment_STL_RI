```markdown
# Robot Arm — ROS 2 Jazzy · URDF/Xacro · MoveIt 2

## Présentation

Ce dépôt contient la modélisation complète d'un bras robotique à 6 degrés de liberté
réalisée dans le cadre du TP de Robotique Industrielle (Master 1 — K. Ramoth — 2026).

Le projet couvre l'ensemble de la chaîne :
- Description cinématique en Xacro
- Visualisation dans RViz 2
- Configuration MoveIt 2 via le Setup Assistant

### Architecture du robot

```
world → basement → base_link → base_plate → forward_drive_arm
→ horizontal_arm → claw_support → gripper_right
                              → gripper_left
```

---

## Choix URDF vs Xacro

Nous avons choisi **Xacro** pour les raisons suivantes :

- **Variables** : utilisation de `${PI/2}` pour les orientations des joints
- **Lisibilité** : code plus clair et maintenable
- **Réutilisabilité** : structure modulaire facilitant les modifications
- **Répétition** : les propriétés inertielles et de collision sont définies proprement

**Inconvénients :** nécessite une étape de compilation supplémentaire via `xacro`.

**Dans quel cas aurions-nous choisi URDF ?**
Pour un robot très simple avec moins de 3 liens et sans répétition de structures.

---

## Hypothèses sur les masses, inerties et limites articulaires

### Masses et inerties (estimées)

| Lien | Masse (kg) | Inertie (kg·m²) |
|------|-----------|-----------------|
| basement | 1.0 | 0.01 |
| base_link | 0.5 | 0.005 |
| base_plate | 0.3 | 0.003 |
| forward_drive_arm | 0.5 | 0.005 |
| horizontal_arm | 0.4 | 0.004 |
| claw_support | 0.2 | 0.002 |
| gripper_right | 0.1 | 0.001 |
| gripper_left | 0.1 | 0.001 |

### Limites articulaires

| Joint | Type | Lower | Upper | Effort | Velocity |
|-------|------|-------|-------|--------|----------|
| joint1 | revolute | -3.14 | 3.14 | 100 | 1.0 |
| joint2 | revolute | -1.57 | 1.57 | 100 | 1.0 |
| joint3 | revolute | -1.57 | 1.57 | 100 | 1.0 |
| joint4 | revolute | -1.57 | 1.57 | 100 | 1.0 |
| joint5 | prismatic | 0 | 0.08 | 100 | 0.5 |
| joint6 | prismatic | 0 | 0.08 | 100 | 0.5 |

---

## Structure du dépôt

```
ros2_arm_ws/src/
├── robot_arm_description/
│   ├── meshes/                  # Fichiers STL
│   ├── urdf/
│   │   └── robot_arm.urdf.xacro
│   ├── launch/
│   │   └── display.launch.py
│   ├── config/
│   ├── package.xml
│   └── CMakeLists.txt
└── robot_arm_moveit_config/
    ├── config/
    │   ├── robot_arm.srdf
    │   ├── kinematics.yaml
    │   ├── joint_limits.yaml
    │   └── ros2_controllers.yaml
    ├── launch/
    │   ├── demo.launch.py
    │   └── move_group.launch.py
    ├── package.xml
    └── CMakeLists.txt
```

---

## Installation

### Prérequis

- ROS 2 Jazzy
- Ubuntu 24.04

### 1. Cloner le dépôt

```bash
git clone https://github.com/YOUR_USERNAME/YOUR_REPO.git
cd YOUR_REPO
```

### 2. Installer les dépendances

```bash
sudo apt-get update
sudo apt-get install -y ros-jazzy-joint-state-publisher-gui
sudo apt-get install -y ros-jazzy-joint-state-publisher
sudo apt-get install -y ros-jazzy-moveit
sudo apt-get install -y ros-jazzy-ros2-control
sudo apt-get install -y ros-jazzy-ros2-controllers
```

### 3. Compiler le workspace

```bash
cd ~/ros2_arm_ws
colcon build
source ~/ros2_arm_ws/install/setup.bash
```

---

## Lancer RViz 2

### Terminal 1 — Lancer le robot

```bash
source /opt/ros/jazzy/setup.bash
source ~/ros2_arm_ws/install/setup.bash
ros2 launch robot_arm_description display.launch.py
```

### Terminal 2 — Lancer les sliders

```bash
source /opt/ros/jazzy/setup.bash
ros2 run joint_state_publisher_gui joint_state_publisher_gui
```

---

## Lancer MoveIt 2

```bash
source /opt/ros/jazzy/setup.bash
source ~/ros2_arm_ws/install/setup.bash
ros2 launch robot_arm_moveit_config demo.launch.py
```

---

## Difficultés rencontrées et solutions

| Difficulté | Cause | Solution |
|-----------|-------|----------|
| RViz2 ne s'affichait pas sur macOS | Problème X11 | Docker + noVNC via navigateur |
| Meshes non visibles dans RViz2 | CMakeLists.txt mal configuré | Correction de l'installation des dossiers |
| move_group crash | Limitation mémoire Docker | Suppression du tag mimic, environnement Docker limité |
| package.xml invalide | Email manquant du Setup Assistant | Correction manuelle |
| Container Docker resetté | Données non persistantes | Volumes Docker montés |

### Note sur MoveIt 2 et Docker

Le nœud `move_group` crashait dans l'environnement Docker en raison de limitations
mémoire et de threading. Le Setup Assistant a fonctionné correctement et le package
a été généré et compilé avec succès. La planification de trajectoires nécessiterait
un environnement Linux natif.

---

## Technologies utilisées

- **ROS 2 Jazzy**
- **Xacro** — description du robot
- **RViz 2** — visualisation
- **MoveIt 2** — planification de trajectoires
- **Docker** — environnement de développement sur macOS
- **noVNC** — accès bureau virtuel via navigateur

---
