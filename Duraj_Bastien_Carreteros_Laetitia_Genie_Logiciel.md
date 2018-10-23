# Analyse logicielle du jeu libre 0 A.D.
![alt text](https://play0ad.com/wp-content/gallery/desktops/0ad-wallpaper-stripesvertical01.jpg "Bannière 0 A.D.")
Duraj Bastien et Carreteros Laetitia
UQAC, 20 octobre 2018

#### Plan
1. Quelques mots sur 0 A.D.
2. Analyse architecturale
	1. Les différents acteurs et scénarios possibles (Diagramme des cas d'utilisation)
	2. Diagramme des classes (Logical View)
	3. Diagramme des packages et des composants (Development View)
	4. Diagramme de séquences et d'activité (Process View)
	5. Diagramme de déploiement (Physical View)
	6. Rétrospective
3. Analyse de la qualité du code
4. Design Patterns
5. Problème de conception
6. Conclusion


## Quelques mots sur 0 A.D.

 Notre choix s'est porté sur un jeu vidéo libre, 0 A.D. . Il s'agit d'un jeu de stratégie en temps réel, en opposition au jeu de stratégie au tour par tour, proposant de jouer avec une dizaine de civilisations ayant vécu de l'an 500 av. JC jusqu'à l'an 0. Le jeu peut être joué aussi bien tout seul qu'à plusieurs.

 Né du jeu de stratégie en temps réel, Age of empires, lequel a été développé en parti par Relic  Entertainment et édité par Microsoft Studios, le jeu était au départ, comme beaucoup de jeu vidéo libre, un mod, c'est à dire qu'il utilisait les technologies et les ressources d'un jeu existant. Dans ce cas précis il s'agissait d'un mod du second volet de la saga: Age of empires II: The Age of  Kings. Le but premier était de recréer Age of empires avec les technologies du second jeu mais le projet a fini par évoluer et devenir totalement autonome.

 0 A.D. a dans un premier temps été en développement fermé avec la société Wildfire  Games en 2002. Mais au cours de l'année 2009 l'entreprise décide d'ouvrir le développement et le code, permettant ainsi à qui le souhaite de participer.

 Pour commencer nous avons choisi un jeu vidéo car cela nous semblait intéressant de voir leur construction et leur architecture mais également leur évolution au cours du développement. Les jeux vidéos sont des logiciels de divertissement, ils ont donc des particularités qui leur sont propres notamment celle de faire collaborer des artistes et des designer avec des programmeurs, ou encore de devoir, sur un projet à long terme, changer de moteur graphique pour un meilleur rendu par exemple. Nous sommes donc intéressés pour voir comment ces contraintes sont gérées dans un développement ouvert.

 Ensuite notre choix s'est porté sur 0 A.D. car il est très actif et ceci depuis plusieurs années : la dernière mise à jour date du 17 mai 2018 ce qui est plutôt récent pour un tel projet. La documentation du projet est également très complète et aborde tous les sujets: comment participer, documentation technique, développer un patch, comment apporter une traduction, ... . Le code et les ressources ainsi que l'exécutable sont facile d'accès et le forum très actif, permettant ainsi de poser des questions s'il venait à nous manquer des informations pour notre travail.

 Dernier point intéressant que nous avons aimé :  le jeu est multi-plaforme, il serait donc intéressant de voir comment cela a-t-il été intégré au projet.

## Les différents acteurs et scénarios possibles
Pour ce travail nous avons tout d'abord analysé l'architecture du logiciel en commençant par identifier les différents acteurs qui peuvent intervenir dans le déroulement du programme. Pour représenter ces acteurs et les fonctionnalités auxquels ils sont rattachés nous avons décidé de modéliser nos résultats avec, dans un premier temps, un diagramme regroupant les différentes parties du logiciel et les acteurs, puis dans un second temps un diagramme des cas d'utilisation pour chaque partie que nous avons identifié.

![Diagramme des packages](../../Images/PackageDiagram1v2.JPG "Diagramme montrant les 5 parties du logiciel et les acteurs associés")

Nous avons donc diviser en 5 parties les différentes utilisations possibles du jeu avec 2 acteurs humains, les joueurs et les développeurs, et un acteur système représentant l'intelligence artificiel du jeu: Petra. Alors que le développeur peut faire tout ce que le jeoueur peut, en plus de la possibilité d'utiliser une console, l'IA ne peut que joueur une partie.

Nous avons ensuite détaillé chaque partie à l'aide d'un diagramme de cas d'utilisation pour nous permettre de mieux comprendre les objectifs et les besoins d'un tel logiciel. Nous n'analyserons pas tous les diagrammes de cas d'utilisation car ils ne sont pas tous interessant mais ceux-ci seront tous joint dans les annexes de ce document.

![Diagramme des cas d'utilisations](../../Images/UseCaseJouerPartie.JPG "Diagramme représentant les différentes possibilitées qui s'offrent aux acteurs lors du déroulement d'une partie")

 Le diagramme qui nous intéresse le plus ici est celui qui concerne le déroulement d'une partie. Il est le centre du jeu et justifie l'existence même du moteur graphique, c'est aussi l'une des parties les plus complètes où chaque acteur à un rôle à jouer. Ce diagramme montre bien les nombreuses actions que le joueur ou l'IA peuvent faire pour remporter le jeu, qu'ils s'agissent des relations diplomatiques avec d'autres joueurs ou de la création de troupe. Il met également en avant un point important de tout jeu vidéo d'envergure, la possibilité de faire apparaître une console qui permet d'appeler directement des fonctions javascript pour par exemple tester de nouveaux élèments non implémentés.

 ## Diagramme des classes (Logical View)
 Le diagramme des classes étant assez grands et complexes nous avons décidé de réduire la présentation de cette partie à un élèment unique du logiciel : la gestion des unités. Une vue générale du diagramme des classes est disponible en annexe.

![Diagramme des classes](../../Images/ClassDiagramCUnit.JPG "Diagramme représentant les classes permettant de gérer un ensemble de troupes")

Cette partie du diagramme est assez représentative du reste du code. On trouve une classe de base gérant les unités appelées "CUnit" celle-ci comprend notamment un objet animation et un objet model pour la représentation graphique de l'unité. Ce qui est intéressant ici c'est qu'il existe une classe spécialisée appelée "CUnitManager" qui permet de gérer toutes les unités au même endroit et de rapidement faire des opérations dessus. Ce schéma se retrouve souvent dans le logiciel sans doute pour pourvoir plus facilement optimiser l'usage de la mémoire, ce problème étant très courant dans les jeux vidéos. La classe "CUnit" utilise également un Abstract Design Pattern avec un constructeur privée et une méthode static "Create" se chargeant des allocations mémoires à faire, mais nous reviendrons sur les Design Patterns dans une autre partie. La classe <CUnitManager> est ensuite relié à une autre partie du logiciel que voici.

![Diagramme des classes](../../Images/ClassDiagramSimContext.JPG "Diagramme représentant le contexte de la simulation")

Il s'agit du contexte auquel va se référer le logiciel lorsqu'une partie sera lancée. Ce contexte possède donc un gestionnaire d'unités avec un terrain, un gestionnaire d'entité regroupant tous les types d'objet qui peuvent intervenir dans une partie et un gestionnaire de composants. Cet object regroupe donc un grand nombre d'informations essentiellement au bon déroulement d'une partie.
