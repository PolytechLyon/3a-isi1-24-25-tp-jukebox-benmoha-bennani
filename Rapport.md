# Compte Rendu TP Jukebox

## Binôme
- Ben Moha Abderrahim
- Bennani Ilyass

## Choix de conception et de réalisation

Pour la réalisation de notre projet Jukebox, nous avons opté pour les choix de conception suivants :

1-Framework Utilisé :
Vue.js : Nous avons choisi Vue.js comme framework principal pour construire l'application. Vue.js est connu pour sa simplicité et sa réactivité, ce qui facilite la création d'interfaces utilisateur dynamiques et interactives.

2-Structure du Projet :
Fichier Unique : Nous avons décidé de mettre tout le code dans un seul fichier App.vue.

3-Gestion de l'État :
Propriétés de Données : Nous utilisons les propriétés de données de Vue pour gérer l'état de l'application, comme la liste de lecture (playlist), la piste en cours de lecture (currentTrackIndex), et le mode de répétition (repeatMode).
Réactivité : Les propriétés de données sont réactives, ce qui signifie que l'interface utilisateur se met à jour automatiquement lorsque les données changent.

4-Lecture Audio :
API Audio de JavaScript : Nous utilisons l'API Audio de JavaScript pour gérer la lecture des pistes audio. Cela inclut des fonctionnalités telles que la lecture, la pause, et la navigation dans la piste.
Événements Audio : Nous avons ajouté des écouteurs d'événements pour gérer des actions spécifiques comme la mise à jour du temps de lecture (timeupdate) et la fin de la piste (ended).

5-Interface Utilisateur :
Contrôles de Lecture : L'interface utilisateur comprend des boutons de contrôle de lecture (lecture/pause) et une barre de progression interactive qui permet à l'utilisateur de naviguer dans la piste en cours de lecture.
Affichage Dynamique : L'affichage de la piste en cours de lecture et des contrôles de lecture est dynamique, ce qui améliore l'expérience utilisateur.
Ajout de Pistes : Nous avons implémenté des options pour ajouter des pistes via URL ou téléchargement de fichiers, offrant ainsi plus de flexibilité à l'utilisateur.

6-Modes de Répétition :
Options de Répétition : Nous avons implémenté plusieurs modes de répétition (répéter la liste, répéter la piste, ne pas répéter) pour offrir plus de flexibilité à l'utilisateur.

7-Design et Style :
CSS Scoped : Nous utilisons des styles CSS scoped pour styliser l'application, ce qui garantit que les styles sont appliqués uniquement aux composants spécifiques.
Design Réactif : L'application est conçue pour être réactive et s'adapter à différentes tailles d'écran, grâce à l'utilisation de propriétés CSS comme max-width et margin.

## Difficultés rencontrées (optionnel)

-Synchronisation de l'audio et de l'interface : L'une des principales difficultés a été de synchroniser la position de lecture de l'audio avec la barre de progression et de permettre à l'utilisateur de naviguer dans la piste en utilisant cette barre.

-Gestion des fichiers audio : La gestion des fichiers audio téléchargés et leur conversion en URL utilisables par l'API Audio a également posé quelques défis techniques.
## Extensions réalisées (optionnel)
-Sauvegarde des titres publics : Nous avons implémenté une fonctionnalité permettant de sauvegarder les titres ajoutés par lien public entre les rafraîchissements de page. Cette fonctionnalité utilise localStorage pour stocker les playlists, ce qui permet de conserver les données même après un rafraîchissement de la page. Les titres ajoutés par fichier ne sont pas sauvegardés pour éviter de surcharger le stockage local avec des fichiers volumineux.

Organisation de plusieurs listes de lecture : Nous avons ajouté la possibilité pour l'utilisateur de créer et gérer plusieurs listes de lecture. Chaque liste de lecture a son propre nom et peut contenir plusieurs titres. Pour visualiser et organiser les listes de lecture, l'utilisateur peut naviguer vers la vue de gestion des listes en cliquant sur le bouton "Manage Playlists". Cette vue affiche la liste des noms des listes de lecture, ainsi que le nombre de titres dans chaque liste. Chaque item de la liste de lecture est accompagné d'un bouton de suppression et d'un bouton de sélection. La liste actuellement sélectionnée ne peut pas être supprimée pour éviter toute perte accidentelle de données en cours de lecture. En sélectionnant une liste, l'utilisateur doit appuyer sur 'Back to Player' pour être redirigé vers la vue principale de l'application avec la liste sélectionnée chargée. Un formulaire permet également de créer une nouvelle liste de lecture, offrant ainsi une flexibilité maximale à l'utilisateur pour organiser sa musique.