# Compte Rendu TP Jukebox

## Binôme
- Ben Moha Abderrahim
- Bennani Ilyass

## Choix de conception et de réalisation

Le projet est organisé de manière à séparer les différentes composants de l'application.

Description des fichiers :

-Jukebox.vue : Composant principal de l'application qui gère l'affichage du lecteur et du gestionnaire de playlists.
-Player.vue : Composant qui gère la lecture des pistes audio.
-PlaylistManager.vue : Composant qui permet de gérer les différentes playlists.
-Playlist.vue : Composant qui affiche les pistes d'une playlist.
-NewTrack.vue : Composant qui permet d'ajouter de nouvelles pistes à une playlist.
-App.vue : Composant racine de l'application.
-style.css : Fichier de styles global pour l'application.


Fonctionnalités:
L'application Jukebox permet de gérer des playlists musicales avec les fonctionnalités suivantes :

-Contrôleur de lecture : Permet de lire, mettre en pause et naviguer dans les pistes audio.
-Gestion des playlists : Permet de créer, sélectionner et supprimer des playlists.
-Ajout de pistes : Permet d'ajouter des pistes à une playlist via une URL ou un fichier local.
-Modes de répétition : Permet de choisir entre la répétition de la liste, de la piste ou aucune répétition.


Choix de conception:
Composants réutilisables : Nous avons divisé l'application en plusieurs composants réutilisables pour faciliter la maintenance et l'évolution du code.
Stockage local : Les playlists sont sauvegardées dans le localStorage du navigateur pour persister entre les sessions.
Styles CSS : Nous avons utilisé un fichier CSS global pour styliser l'application de manière cohérente.

## Difficultés rencontrées (optionnel)

-Synchronisation de l'audio et de l'interface : L'une des principales difficultés a été de synchroniser la position de lecture de l'audio avec la barre de progression et de permettre à l'utilisateur de naviguer dans la piste en utilisant cette barre.

-Gestion des événements : La gestion des événements entre les composants parents et enfants a nécessité une attention particulière pour garantir une communication fluide et éviter les erreurs.

## Extensions réalisées (optionnel)
-Sauvegarde des titres publics : Nous avons implémenté une fonctionnalité permettant de sauvegarder les titres ajoutés par lien public entre les rafraîchissements de page. Cette fonctionnalité utilise localStorage pour stocker les playlists, ce qui permet de conserver les données même après un rafraîchissement de la page. Les titres ajoutés par fichier ne sont pas sauvegardés pour éviter de surcharger le stockage local avec des fichiers volumineux.

Organisation de plusieurs listes de lecture : Nous avons ajouté la possibilité pour l'utilisateur de créer et gérer plusieurs listes de lecture. Chaque liste de lecture a son propre nom et peut contenir plusieurs titres. Pour visualiser et organiser les listes de lecture, l'utilisateur peut naviguer vers la vue de gestion des listes en cliquant sur le bouton "Manage Playlists". Cette vue affiche la liste des noms des listes de lecture, ainsi que le nombre de titres dans chaque liste. Chaque item de la liste de lecture est accompagné d'un bouton de suppression et d'un bouton de sélection. La liste actuellement sélectionnée ne peut pas être supprimée pour éviter toute perte accidentelle de données en cours de lecture. En sélectionnant une liste, l'utilisateur est redirigé vers la vue principale de l'application avec la liste sélectionnée chargée. Un formulaire permet également de créer une nouvelle liste de lecture, offrant ainsi une flexibilité maximale à l'utilisateur pour organiser sa musique.