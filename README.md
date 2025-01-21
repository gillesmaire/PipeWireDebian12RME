

# Présentation de Pipewire Debian 12

## Pipewire

PipeWire est un serveur multimédia moderne pour Linux, qui vise à remplacer
- PulseAudio (serveur audio)
- JACK (serveur audio à faible latence), tout en offrant une gestion unifiée du
son, de la vidéo et des périphériques multimédia.

Pipewire prend en charge la gestion de l'audio mais aussi de la vidéo. Ainsi
grâce à Pipewire ont peut gérer plusieurs vidéos et écouter à la fois plusieurs
sources audio.

PipeWire permet de générer des périphériques multimédia tels que des microphones,
des caméras, des hauts parleurs, des tables de mixage.

## Avantages

Les applications qui utilisent PulseAudio ou Jack peuvent fonctionner avec
PipeWire sans modification.

PipeWire garde la gestion des faibles latences qu'offrait Jack.

Il supporte Wayland au niveau de la gestion de la vidéo

Il gère la sécurité et le sandboxing c'est à dire que chaque ressource ne peut
accédéer qu'à certains périphériques et pas à d'autres.

Ces caractéristiques font qu'un vieux logiciel qui utilisait ALSA peut être
utilisé dans Pipewire et mixé avec une carte son Jack de façon tout à fait simle.



## Les différents modules de PipeWire

### Pipewire Core

Le Core gère l'interconnexion de tous les autres modules. Il est responsable de
la gestion du flux multimédia et de la communication entre les périphériques.

Ainsi il prend en compte les connexions entre les applications clients et les
périphériques (micros, HP, webcams)

Il gère la sychronisation des flux audio et vidéo et leur communication

Il gère la latence, l'optimisation des ressources et l'accès sécurisé aux
périphériques.

### PiperWire Pulse

PipeWire Audio est l'interface qui permet de gérer le son de manière similaire
à PulseAudio. En d'autres termes, c’est le module qui permet à PipeWire de
prendre en charge le son sur Linux de manière transparente, en remplaçant
PulseAudio.

Pipewire-pulse émule PulseAudio, permettant à PipeWire de se comporter
exactement comme PulseAudio aux yeux des applications.

Ce module permet de gérer les flux audio (entrées et sorties), de
configurer les périphériques audio, d’appliquer des effets sonores, etc.

PipeWire pulse se fera passer pour serveur Pulse Audio pour les applications
qui utilisent Pulse Audio. C'est ainsi qu'on pourra écouter Youtube en Pulse
audio sur une carte RME qui ne supporte que le protocole Jack.

### Pipewire Jack

Le module pipewire-jack permet à PipeWire de remplacer JACK, qui est utilisé
pour les applications nécessitant de la haute performance audio, notamment
en production musicale, en temps réel et en audio à faible latence.

- PipeWire peut fonctionner avec JACK en émulation, permettant aux applications
conçues pour JACK de fonctionner sans modification avec PipeWire
- Le module pipewire-jack permet la gestion d'audio en temps réel avec une
latence faible, ce qui est essentiel pour des applications professionnelles
comme des DAW (Digital Audio Workstations) ou des logiciels de production
musicale.

### PiperWire Video

Le module pipewire-media-session permet de gérer la vidéo dans PipeWire. Il
s'occupe de la gestion des périphériques vidéo comme les webcams ou les cartes
de capture.

- Ce module est souvent utilisé avec Wayland, car PipeWire gère les flux vidéo,
permettant de capturer des vidéos et de diffuser des écrans via des
applications utilisant Wayland.
- Par exemple, il permet la gestion des flux vidéo dans des applications vidéo
ou les logiciels de capture d'écran.
- Le module peut également interagir avec des technologies comme V4L2
(Video4Linux2), ce qui permet d'utiliser des webcams et des caméras USB avec
PipeWire et ce de façon simultannée.


### PiperWire Bluetooth

Le module pipewire-bluetooth permet de gérer les périphériques Bluetooth,
comme les écouteurs, casques ou enceintes sans fil.

- Ce module prend en charge le profil A2DP (audio haute définition), le profil
HSP/HFP (pour les conversations téléphoniques) et le profil HID pour des
périphériques d'entrée Bluetooth.
- Il offre une intégration fluide avec le système audio PipeWire, permettant
aux périphériques Bluetooth de se connecter et d'être utilisés comme
périphériques audio classiques.

## Pipewire-media-session

Pipewire-media-session est responsable de la gestion des flux multimédia dans
PipeWire. Il s'agit d'un module qui orchestre les différentes connexions et
leur état, ainsi que les politiques de gestion des flux (audio et vidéo).

- Ce module s'occupe de la découverte des périphériques, de la gestion des
connexions entre applications et périphériques, et de la configuration des flux
multimédia.
- Il peut être configuré pour gérer des profils spécifiques pour l'audio, comme
des profils haute-fidélité ou des profils pour des périphériques spécifiques
comme des haut-parleurs Bluetooth.

## Pipewire-udev

Pipewire-udev est un module qui permet à PipeWire de détecter dynamiquement
les périphériques audio et vidéo en se basant sur udev, le gestionnaire de
périphériques sous Linux.

- Il gère l'ajout et la suppression des périphériques (comme les microphones
ou les haut-parleurs) en temps réel.

- Par exemple, si on connecte un nouveau périphérique audio à son ordinateur,
pipewire-udev détectera ce périphérique et le rendra disponible pour être
utilisé par les applications.

## Lib Pipewire

La bibliothèque libpipewire est utilisée pour la gestion des connexions et
des flux multimédia. C'est l'interface que les applications utilisent pour
interagir avec PipeWire.

Les applications peuvent utiliser cette API pour créer, modifier, et gérer
des flux audio/vidéo via PipeWire.

Elle permet aux développeurs de concevoir des applications qui fonctionnent
de manière transparente avec PipeWire, sans avoir à se soucier de la gestion
détaillée des périphériques.


# Installation


## Introduction

Pipewire peut être installé pour tous les utilisateurs d'une machine ou juste
pour certains utilisateurs ou plutôt, les logiciels sont installés sur une
machine mais utilisables que par certains utilisateurs.

C'est une solution pratique pour faire des tests et ne pas risquer de créer
des dommages dans une configuration qui foncionne.

Vérifions que PipeWire est disponible sur sa station Linux :

~~~
dpkg -l | grep pipewire
~~~

Si une liste de modules apparaît c'est que PipeWire est installé

Vérifions si Pipewire est actif

~~~
systemctl --user status pipewire.service
~~~

La réponse Unit pipewire.service could not be found. nous indique que
Pipewire n'est pas actif.

Vérifions la version de PipeWire installée :

~~~
pipewire --version
~~~

Vérifions le serveur de son actuellement utilisé

~~~
pactl info
~~~

La ligne  suivante nous intique que nous ne nous servons pas de pipewire
mais bien de pulseaudio

~~~
Nom du serveur : pulseaudio
~~~

Vérifions ou est installé le binaire de pipewire

~~~
which pipewire
~~~

## Installation de Pipewire

~~~
sudo apt install pipewire pipewire-pulse pipewire-alsa pipewire-jack
sudo apt install pipewire-audio-client-libraries
~~~

Afin que pipewire ne soit pas activé pour tous les utilisateurs, on peut
empêcher son activation par

~~~
sudo systemctl mask pipewire.service
sudo systemctl mask pipewire-pulse.service
~~~

Ceci est souvent ainsi que sont livrées les distributions, notamment
la Debian.


## Activation de Pipewire  pour un utilisateur

Une fois connecté sur son compte on activera les services pipewire
avec :

~~~
systemctl --user enable pipewire.service
systemctl --user enable pipewire-pulse.service
~~~

On pourra ainsi les démarrer avec :

~~~
systemctl --user start pipewire.service
systemctl --user start pipewire-pulse.service
~~~


Et on vérifiera que les services sont activés par :

~~~
systemctl --user status pipewire.service
systemctl --user status pipewire-pulse.service
~~~


Notons que nous aurions pu omettre l'option --user, dans ce cas les
manipulations auraient été activées pour tous les utilisateurs.


# Quelques premières commandes


## Les commandes PulseAudio

- Affichage des cartes audio ou périphériques de sorties en mode condensé

~~~
pactl list short sinks
~~~

- Affichage des cartes audio en mode très détaillé

~~~
pactl list sinks
~~~

- Affichage des périphériques d'entrées en mode conensé

~~~
pactl list sources
~~~


- Affichage des périphériques d'entrées en mode détaillé

~~~
pactl list sources
~~~

## Les commandes PipeWire  pour un utilisateur


Nous allons installer Pipewire uniquement pour un utilisateur.
Afin de ne pas pertuber votre environnement créer un utilisateur en root
via la commande

~~~
sudo add user test
~~~

Connectez vous avec ce nom.

Nous allons maintenant créer des fichiers de services manuellement dans le
répertoire de cet utilisateur test


~~~
mkdir -p ~/.config/.systemd/user
vim ~/.config/systemd/user/pipewire.service
~~~

Dans ce fichier ajouter le contenu suivant

~~~
[Unit]
Description=PipeWire Multimedia Service
Documentation=man:pipewire(1)

[Service]
ExecStart=/usr/bin/pipewire
Restart=on-failure

[Install]
WantedBy=default.target
~~~

Créons maintenant
~~~
vim ~/.config/systemd/user/pipewire-pulse.service
~~~

Dans ce fichier on ajoute le contenu suivant

~~~
[Unit]
Description=PipeWire PulseAudio Service
After=pipewire.service

[Service]
ExecStart=/usr/bin/pipewire-pulse
Restart=on-failure

[Install]
WantedBy=default.target
~~~

Ensuite on démarre les services PipeWire par :

~~~
sudo systemctl start pipewire.service
sudo systemctl start pipewire-pulse.service
sudo systemctl enable pipewire.service
sudo systemctl enable pipewire-pulse.service
~~~


Installation de jack pour pipewire

~~~
sudo apt install pipewire-jack
~~~

Activer le serveur Jack avec Pipewire


## Cas particulier la carte RME

La carte RME ne fonctionne pas en mode  PulseAudio on va devoir installer pulseaudio-module-jack

~~~
sudo apt install pulseaudio-module-jack
~~~

Et charger les modules PulseAudio

~~~
pactl load-module module-jack-source
pactl load-module module-jack-sink
~~~


# Cas général PipeWire pour chaque les utilisateurs


## Démarrage de  PipeWire préinstallé

Chaque utilisateur qui veut utiliser Pipewire devra activer pipewire.

Nous avions vu que PipeWire pouvait être installé mais non activé.


Nous pourrions être tenté de faire ce qui ne marche pas.

~~~
sudo systemctl start pipewire.service
~~~

En effet Pipewire se gère par utilisateur

La commande doit être

~~~
systemctl --user start pipewire.service
~~~

Mais là Pipewire répond que le service est masqué et vous recevez le message :

~~~
Failed to start pipewire.service: Unit pipewire.service is masked.
~~~


En effet, lorsqu'un service est masqué, il est désactivé de manière stricte
et ne peut pas être démarré tant qu'il n'est pas démasqué.

Pour démasquer le service :

~~~
systemctl --user unmask pipewire.service
~~~

Procédons de même pour pipewire-pulse.service

~~~
systemctl --user unmask pipewire-pulse.service
~~~



Pour voir la liste des services pipewire disponibles  :

~~~
systemctl --user list-unit-files | grep pipewire
~~~


On peut démarrer ces services par :

~~~
systemctl --user start pipewire.service
systemctl --user start pipewire-pulse.service
~~~

Si par exemple on recoit la commande Failed to start pipewire-pulse.service:
Unit pipewire-pulse.service not found,
cela signifie que  pipewire-pulse n'est pas installé.

On l'installe par :

~~~
sudo apt install pipewire pipewire-pulse
~~~

Puis à nouveau

~~~
systemctl --user start pipewire-pulse.service
~~~

Ensuite on active les services :

~~~
systemctl --user start pipewire-pulse.service
systemctl --user  enable pipewire-pulse.service
~~~


On peut contôler qu'ils sont bien lancés par :

~~~
systemctl --user status pipewire.service
systemctl --user status pipewire-pulse.service
~~~

# Utilisation

## Ce qu'on ne lance plus

- On ne lance plus qjackctl


## Ce qu'on lance toujours

- Si on utilisse une carte RME on doit toujours utiliser hdspmixer
- On utilise Firefox comme avant car il pensera jouer du son pour une carte ALSA
- On ulise clémentine comme avant mais dans les préférences on lui demande de
router le son vers Jack si sa carte RME ne supporte que Jack


## Ce qu'on doit maintenant utiliser

- On doit utiliser pavucontrol

[Pavucontrol](pavucontrol.png)









