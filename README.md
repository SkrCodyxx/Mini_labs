# Mini_labs


![WLD_Icone](https://gitlab.com/Codyxxx/windows_container_on_linux/-/raw/main/WLD.png)

# Windows_container_on_Linux

# Exécution de Windows dans un conteneur Docker sous Linux

 Cela fait déjà un certain temps que je cherche à exécuter un conteneur Windows sous Linux, une tâche qui semblait complexe au début. Ce projet m'a permis de concrétiser mon objectif et de mieux comprendre les techniques nécessaires pour y parvenir.

Ce projet, réalisé par [dockur/windows](https://github.com/dockur/windows), permet d'exécuter Windows dans un conteneur Docker sous Linux. L'objectif de ce dépôt est de fournir un environnement Windows virtuel pour tester des applications ou explorer des configurations spécifiques tout en bénéficiant des avantages d'une isolation via des conteneurs.

## À propos de cette démonstration

Ce dépôt ne m'appartient pas. Je réalise cette démonstration pour illustrer comment exécuter Windows dans un conteneur Docker sous Linux, en utilisant le projet original comme base. Cette documentation a été adaptée pour faciliter la compréhension et l'utilisation de l'outil.

## Compatibility ⚙️

| **Product**  | **Platform**   | |
|---|---|---|
| Docker Engine | Linux| ✅ |
| Docker Desktop | Linux | ❌ |
| Docker Desktop | macOS | ❌ |
| Docker Desktop | Windows 11 | ✅ |
| Docker Desktop | Windows 10 | ❌ |

## Prérequis

1. **Linux avec Docker installé** :
   - Installez Docker Engine sur votre machine en suivant les instructions officielles de [Docker](https://docs.docker.com/engine/install/).

2. **Accès à /dev/kvm** :
   - Assurez-vous que la virtualisation est activée dans le BIOS/UEFI et que le module KVM est chargé. Pour vérifier si KVM est activé, exécutez la commande suivante :
     ```bash
     lsmod | grep kvm
     ```

3. **Docker Compose** (optionnel mais recommandé) :
   - **Choix 1** : Docker Compose simplifie la gestion des conteneurs multi-services. Installez-le avec :
     ```bash
     sudo apt install docker-compose
     ```
   - **Choix 2** : Téléchargez le script de Docker-Compose directement :
     ```bash
     sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/bin/docker-compose
     ```
   - Mettez le fichier au format exécutable :
     ```bash
     chmod +x /usr/bin/docker-compose
     ```

## Clônage du projet

Pour commencer, clonez le projet à l'aide de Git :

```bash
git clone https://github.com/dockur/windows.git
cd windows
```

## Exécution avec Docker Compose

Créez un fichier `docker-compose.yml` avec la configuration suivante :

> **Note :** Les options comme `cap_add` ou `devices` sont essentielles pour garantir le bon fonctionnement du conteneur. `devices` permet d'accéder au module KVM pour la virtualisation, et `cap_add: NET_ADMIN` octroie les permissions réseau nécessaires pour la configuration.

```yaml
services:
  windows:
    image: dockurr/windows
    container_name: windows
    environment:
      VERSION: "11"
    devices:
      - /dev/kvm
    cap_add:
      - NET_ADMIN
    ports:
      - 8006:8006
      - 3389:3389/tcp
      - 3389:3389/udp
    stop_grace_period: 2m
```

Démarrez le conteneur avec la commande :

```bash
docker-compose up -d
```

## Exécution avec Docker CLI

Alternativement, exécutez directement le conteneur avec la commande suivante :

> **Note :** L'option `--rm` dans la commande Docker est utilisée pour supprimer automatiquement le conteneur une fois qu'il est arrêté. Cette option est utile pour des tests rapides ou des environnements éphémères. Cependant, si vous souhaitez conserver l'état de votre conteneur entre les sessions ou maintenir des données persistantes, il est conseillé de retirer cette option.

```bash
docker run -it --rm \
  -p 8006:8006 \
  --device=/dev/kvm \
  --cap-add NET_ADMIN \
  --stop-timeout 120 \
  dockurr/windows
```

## Description des options de la commande Docker

- **-it** : Mode interactif avec un terminal.
- **--rm** : Supprime le conteneur automatiquement après son arrêt.
- **-p 8006:8006** : Mappe le port 8006 du conteneur au port 8006 de l'hôte.
- **--device=/dev/kvm** : Accorde l'accès au dispositif de virtualisation KVM.
- **--cap-add NET_ADMIN** : Ajoute la capacité de gestion réseau (administrateur) au conteneur.
- **--stop-timeout 120** : Définit un délai de 120 secondes avant l'arrêt forcé du conteneur.
- **dockurr/windows** : Image Docker à utiliser pour exécuter le conteneur.

## Accès au Windows installé

1. Connectez-vous à `http://localhost:8006` via un navigateur web.
2. Patientez pendant l'installation automatique de Windows.
3. Une fois terminée, le bureau Windows sera prêt à l'emploi et présentera toutes les fonctionnalités d'un système Windows complet.

## Tests et captures d'écran

### Introduction

Dans cette section, je vais documenter chaque étape des tests, en incluant des captures d'écran et des explications détaillées. Cela permettra de mieux comprendre le processus et de fournir une preuve visuelle des résultats obtenus.

1. **Capture d'écran** : Accédez au navigateur et prenez une capture de l'écran au moment de l'installation automatique de Windows.
2. **Documentation des étapes** : Décrivez brièvement chaque étape avec les durées correspondantes et les commentaires pertinents.
3. **Capture du bureau** : Une fois l'installation terminée, capturez une image du bureau Windows opérationnel avec les applications prêtes à l'emploi.

## Personnalisation

### Modifier la version de Windows

Ajoutez ou modifiez l'environnement `VERSION` dans votre fichier Compose pour choisir une version différente :

```yaml
environment:
  VERSION: "10"
```
Options disponibles :
  
  | **Value** | **Version**           | **Size** |
  |---|---|---|
  | `11`   | Windows 11 Pro           | 5.4 GB   |
  | `11l`  | Windows 11 LTSC          | 4.2 GB   |
  | `11e`  | Windows 11 Enterprise    | 5.8 GB   |
  ||||
  | `10`   | Windows 10 Pro           | 5.7 GB   |
  | `10l`  | Windows 10 LTSC          | 4.6 GB   |
  | `10e`  | Windows 10 Enterprise    | 5.2 GB   |
  ||||
  | `8e`   | Windows 8.1 Enterprise   | 3.7 GB   |
  | `7e`   | Windows 7 Enterprise     | 3.0 GB   |
  | `ve`   | Windows Vista Enterprise | 3.0 GB   |
  | `xp`   | Windows XP Professional  | 0.6 GB   |
  ||||
  | `2025` | Windows Server 2025      | 5.0 GB   |
  | `2022` | Windows Server 2022      | 4.7 GB   |
  | `2019` | Windows Server 2019      | 5.3 GB   |
  | `2016` | Windows Server 2016      | 6.5 GB   |
  | `2012` | Windows Server 2012      | 4.3 GB   |
  | `2008` | Windows Server 2008      | 3.0 GB   |
  | `2003` | Windows Server 2003      | 0.6 GB   |

Voir le [README officiel](https://github.com/dockur/windows) pour plus d'options et les détails des versions disponibles.

### Comment configurer le nom d'utilisateur et le mot de passe ?

Par défaut, un utilisateur appelé `Docker` est créé lors de l'installation, avec un mot de passe vide.

Si vous souhaitez utiliser des identifiants différents, vous pouvez les modifier dans votre fichier `docker-compose` :

```yaml
environment:
  USERNAME: "bill"
  PASSWORD: "gates"
```

### Comment sélectionner la langue de Windows ?

Par défaut, la version anglaise de Windows sera téléchargée. Mais vous pouvez ajouter la variable d'environnement `LANGUAGE` dans votre fichier `docker-compose` pour spécifier une langue alternative :

```yaml
environment:
  LANGUAGE: "French"
```

Vous pouvez choisir parmi les langues suivantes : 🇦🇪 Arabe, 🇧🇬 Bulgare, 🇨🇳 Chinois, 🇭🇷 Croate, 🇨🇿 Tchèque, 🇩🇰 Danois, 🇳🇱 Néerlandais, 🇬🇧 Anglais, 🇪🇪 Estonien, 🇫🇮 Finnois, 🇫🇷 Français...

Voir le [README officiel](https://github.com/dockur/windows) pour plus de détails.

### Comment sélectionner la disposition du clavier ?

Si vous souhaitez utiliser une disposition de clavier ou une locale qui n'est pas la disposition par défaut de votre langue sélectionnée, vous pouvez ajouter les variables `KEYBOARD` et `REGION` avec un code de culture, comme ceci :

```yaml
environment:
  REGION: "en-US"
  KEYBOARD: "en-US"
```

### Modifier la RAM et les CPU

Pour ajuster les ressources allouées au conteneur, ajoutez les variables suivantes dans l'environnement :

```yaml
environment:
  RAM_SIZE: "8G"
  CPU_CORES: "4"
```

### Partage de fichiers

Ajoutez un montage pour partager des fichiers entre votre hôte Linux et le conteneur Windows :

```yaml
volumes:
  - /home/user/shared:/data
```

Accédez à ces fichiers depuis Windows sous `\\host.local\shared`.

## Dépannage

### Problèmes courants

- **Erreur de connexion à /dev/kvm** : Vérifiez que le module KVM est chargé et que votre utilisateur a les droits nécessaires pour y accéder.
- **Problèmes de connexion réseau** : Assurez-vous que le port 8006 est ouvert sur votre pare-feu et accessible depuis l'hôte.

## Intégration avec Ansible

L'utilisation de **Ansible** pour gérer le conteneur et automatiser les déploiements et configurations peut considérablement améliorer la flexibilité et l'efficacité des tests et des opérations de maintenance. Pour l'instant, j'ai testé cette intégration et les détails de ces tests seront partagés dans un autre projet GitLab. Vous pourrez accéder à ce projet pour voir comment **Ansible** peut être utilisé pour gérer et déployer des conteneurs Docker exécutant Windows.

## Remarques importantes

- Ce projet repose sur la capacité de Docker à gérer des conteneurs avec des machines virtuelles légères, et l'utilisation de KVM permet d'améliorer les performances.
- L'utilisation de ce conteneur peut nécessiter des autorisations supplémentaires en fonction des configurations de sécurité de votre système.

## Remerciements

Un grand merci à l'équipe de [dockur/windows](https://github.com/dockur/windows) pour leur travail sur ce projet qui permet de démontrer l'exécution de Windows dans un conteneur Docker sous Linux. L'importance de ce projet ne peut pas être sous-estimée, car il révolutionne la manière dont nous pouvons tester et exécuter des applications sur différents systèmes d'exploitation sans nécessiter des machines physiques séparées ou des configurations complexes de dual-boot. L'exécution de Windows dans un conteneur Docker permet de gagner du temps et des ressources tout en simplifiant le développement et les tests dans un environnement contrôlé.
```
