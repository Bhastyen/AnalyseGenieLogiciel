# Analyse logicielle du jeu libre 0 A.D.
![alt text](https://github.com/Bhastyen/AnalyseGenieLogiciel/blob/master/Images/header_0ad.jpg?raw=true "Bannière du jeu 0 A.D.")

Duraj Bastien et Carreteros Laetitia
UQAC, 01 novembre 2018

#### Plan
1. Introduction
2. Description du logiciel
3. Analyse architecturale
	1. Les différents acteurs et scénarios possibles
	2. Development View
	3. Logical View
	4. Process View
	5. Conclusion sur l'architecture
4. Analyse de la qualité du code
5. Design Patterns
6. Problème de conception
7. Conclusion


## Introduction

 Nous allons ici analyser un jeu vidéo libre : 0 A.D. . Il s'agit d'un jeu de stratégie en temps réel, en opposition au jeu de stratégie au tour par tour. Celui-ci propose de jouer avec une dizaine de civilisations ayant vécu de l'an 500 av. JC jusqu'à l'an 0. Le jeu peut être joué aussi bien tout seul qu'à plusieurs.

 Né du jeu de stratégie en temps réel, Age of empires, lequel a été développé en parti par Relic Entertainment et édité par Microsoft Studios, le jeu était au départ un mod, c'est-à-dire qu'il utilisait les technologies et les ressources d'un jeu existant. Dans ce cas précis il s'agissait d'un mod du second volet de la saga: Age of empires II: The Age of  Kings. Le but premier était de recréer Age of empires avec les technologies du second jeu mais le projet a fini par évoluer et devenir totalement autonome.

 0 A.D. a, dans un premier temps, été en développement fermé avec la société Wildfire Games en 2002. Mais au cours de l'année 2009 l'entreprise décide d'ouvrir le développement et le code, permettant ainsi à qui le souhaite de participer.

 Pour commencer nous allons expliquer ce qui nous a interessé dans ce projet. Tout d'abord nous avons choisi un jeu vidéo car cela nous semblait intéressant de voir leur construction et leur architecture mais également leur évolution au cours du développement. Les jeux vidéos sont des logiciels de divertissement, ils ont donc des particularités qui leur sont propres notamment celle de faire collaborer des artistes et des designer avec des programmeurs, ou encore de devoir, sur un projet à long terme, changer de moteur graphique pour un meilleur rendu par exemple. Nous sommes donc intéressés pour voir comment ces contraintes sont gérées dans un développement ouvert.

 Ensuite notre choix s'est porté sur 0 A.D. car il est encore assez actif et ceci depuis plusieurs années : la dernière mise à jour date du 17 mai 2018 ce qui est plutôt récent pour un tel projet. La documentation du projet est également très complète et aborde tous les sujets: comment participer, documentation technique, développer un patch, comment apporter une traduction, ... . Le code et les ressources ainsi que l'exécutable sont facile d'accès et le forum très actif.

 De plus depuis l'ouverture du projet celui-ci a beaucoup progressé et est devenue très complet :  partie solo, partie multijoueur, didacticiel, éditeur de scénarios, support pour les mods, ect... , cherchant à atteindre un niveau de qualité proche des jeux commerciaux.

Il serait donc intéressant d'analyser ce logiciel et ceci sous plusieurs angles en commençant par une analyse architecturale en prenant en compte 4 vues : Logical view, Process view, Development view et Deployment view. Une fois cette analyse effectuée nous analyserons la qualité du code grâce à plusieurs métriques puis nous finirons avec l'analyse des Design Patterns présent dans le projet ainsi que leurs avantages et inconvénients.

## Description du logiciel
Avant de nous plonger dans l'analyse formelle du logiciel nous allons décrire le jeu dans son fonctionnement et ses principales interfaces. Il s'agit d'un jeu de stratégie, le but est de battre l'adversaire ou les adversaires suivant un objectif précis. pour cela on choisit une civilisation que l'on va diriger sur plusieurs aspects. Le jeu se présente avec une Caméra en vue de dessus assurant une vue globale sur la partie et permettant de mieux gérer ses unités et ses bâtiments. La figure 5 permet d'avoir un aperçu de ce concept. Passons maintenant à la présentation du jeu tel que les développeurs l'on conçut.

Au lancement du logiciel nous nous retrouvons donc avec une interface nous permettant soit d'apprendre à jouer, de jouer une partie en solo, de jouer en multijoueur, de régler les paramètres ainsi que d'autres fonctionnaltés pouvant aider à l'amélioration du jeu. L'image ci-dessous permet de voir comment ce menu est présenté.

![alt text](https://github.com/Bhastyen/AnalyseGenieLogiciel/blob/master/Images/accueil0ad.png?raw=true)

fig. 1 Menu principale du jeu


Si nous décidons de jouer une partie, ce qui est la fonctionnalité centrale de tout jeu vidéo, nous pouvons , comme montré à la figure 2 , 3 et 4, choisir la faction que nous voulons jouer, le type de carte (qui définit le nombre de joueurs), la difficulté des IA , et la possibilité de créer des équipes. Pour le choix du type de cartes il y a à disposition différents filtres : nouvelles cartes, cartes navales, carte de démonstration , carte à déclencheurs et toutes les cartes, ceci permet de ne montrer que les cartes qui nous intéressent.
Une fois la carte choisie nous pouvons décider si nous voulons avoir la carte révélée ou non ainsi que la présence de trésors en jeu.

![choix carte 1 ](https://github.com/Bhastyen/AnalyseGenieLogiciel/blob/master/Images/choixCarte1.png?raw=true)

fig. 2 Configuration nouvelle partie / Paramètres carte

Une autre possibilité est de choisir la capacité limite de population dans une civilisation ainsi que le nombre de ressources de départ pour personnaliser sa partie et ne pas vivre toujours la même expérience de jeu.
![choix carte 2 ](https://github.com/Bhastyen/AnalyseGenieLogiciel/blob/master/Images/choixCarte2.png?raw=true)

fig. 3 Configuration nouvelle partie / Paramètres global joueurs


La manière de terminer / gagner une partie peut également être déterminée par l'onglet "type de jeu". Nous pouvons aussi ajouter plusieurs conditions simultanées pour gagner ce qui complexifi grandement le jeu.
![choix carte 3 ](https://github.com/Bhastyen/AnalyseGenieLogiciel/blob/master/Images/choixCarte3.png?raw=true)

fig. 4 Configuration nouvelle partie / Paramètres fin de partie

La configuration d'une partie est donc très complète et au même niveau que des triple A tels que Starcraft ou encore Age of empires.

Une fois la partie démarrée nous nous retrouvons avec notre civilisation et comme point de départ un centre ville entouré de quatre ouvrières, deux soldats de corps à corps (infanterie) , un cavalier, et deux soldats de combat à distance (archerie). Ces unités de départ peuvent récupérer différentes ressources et chacune des unités a des bonus différents dans la récolte d'une ressource ce qui permet de les différencier lorsque l'on doit récolter. Il existe plusieurs ressources récoltables qui sont la nourriture , les pierres , le métal et le bois. Grâce à cela les unités peuvent construire des bâtiments pour faire évoluer et développer notre civilisation. L'évolution se fait à travers 3 phases. La première de base est celle des villages , la seconde la phase des villes , et la dernière celle des cités. Le fait de changer de phase permet de débloquer de nouveaux bâtiments avec de nouvelles fonctionnalités. Mais pour débloquer ses phases avec les avantages qu'elles possèdent il faut remplir des conditions , par exemple un nombre de population , la présence de certains bâtiments de l'âge actuel, forçant le joueur à devoir se développer un peu avant de voir les meilleurs unités débloquées.

Pour combattre les autres civilisations nous utilisons les unités à dispositions qu'il faut entraîner dans des bâtiments spéciaux comme les casernes. Le jeu propose d'ailleurs une grande diversité suivant la civilisations choisi lors de la configuration du jeu. Le but est donc, dans le cas d'une victoire militaire, de détruire les bâtiments et unités adverses grâce à nos propres unités. Il existe plusieurs catégories : l'infanterie, l'archerie, la cavalerie et les armes de sièges. Chacune ayant un rôle précis et devant être utilisée corrrectement pour s'assurer la victoire.

Nous pouvons également mettre des unités dans certains bâtiments soit pour les protéger soit pour qu'ils attaquent automatiquement les ennemis. Mais le jeu ne se limite pas à la gueurre on peut également faire du commerce avec des joueurs neutres ou alliés, ou utiliser la diplomatie pour gagner contre un joueur puissant.

Le jeu intègre également une notion de territoires qui limite la construction des bâtiment à notre territoire uniquement (sauf les postes avancées qui peuvent être en territoire neutres) . Pour étendre notre territoire nous devons bâtir nos bâtiments proches de nos frontières , cela va participer à son expansion au fil du temps et ainsi notre domination sur la carte. Il existe une façon plus rapide de s'étendre, la construction de nouveaux centre ville sur la carte. Ceux-ci peuvent se construire sur les territoires neutres. Cette mécanique n'est pas à prendre à la légère puiqu'elle permet de bloquer l'avancé d'autres civilisations en récupérant des ressources.

![Interface lors d'une partie](https://github.com/Bhastyen/AnalyseGenieLogiciel/blob/master/Images/debutjeu0ad.png?raw=true)

fig. 5 Interface de jeu lors d'une partie

Ce premier pas dans les nombreuses fonctionnalités du jeu permet dans un premier temps de comprendre le but recherché par les développeur : créer un jeu de stratégie open source le plus complet possible avec un grand niveau de qualité. Puis dans un second temps de mieux comprendre l'analyse des fonctionnalités présente dans la prochaine partie.   

## Analyse architecturale
### Les différents acteurs et scénarios possibles
Pour comprendre ce logiciel nous avons tout d'abord analysé l'architecture de celui-ci en commençant par identifier les différents acteurs qui peuvent intervenir dans le déroulement du jeu. Pour représenter ces acteurs et les fonctionnalités auxquels ils sont rattachés nous avons décidé de modéliser nos résultats avec, dans un premier temps, un diagramme regroupant les différentes parties du logiciel et les acteurs, puis dans un second temps un diagramme des cas d'utilisation pour la partie la plus interessante et la plus complète : le déroulement d'une partie.

![Diagramme des packages](https://github.com/Bhastyen/AnalyseGenieLogiciel/blob/master/Images/PackageDiagram1v2.png?raw=true)

fig. 6 Diagramme représentant les grandes fonctionnalités du jeu


Nous avons donc diviser en 5 parties les différentes utilisations possibles du jeu avec 2 acteurs humains, les joueurs et les développeurs, et un acteur système représentant l'intelligence artificiel du jeu: Petra. Alors que le développeur peut faire tout ce que le joueur peut, en plus de la possibilité d'utiliser une console, l'IA ne peut que joueur une partie.

Nous avons ensuite détaillé chaque partie à l'aide d'un diagramme de cas d'utilisation pour nous permettre de mieux comprendre les objectifs et les besoins d'un tel logiciel.

![Diagramme des cas d'utilisations](https://github.com/Bhastyen/AnalyseGenieLogiciel/blob/master/Images/useCaseJouerPartie.png?raw=true "Diagramme représentant les différentes possibilitées qui s'offrent aux acteurs lors du déroulement d'une partie")

fig. 7 Diagramme des cas d'utilisations pour la fonctionnalité "Jouer partie"

Le diagramme qui nous intéresse le plus ici est celui qui concerne le déroulement d'une partie. Il est le centre du jeu et justifie l'existence même du moteur graphique, c'est aussi l'une des parties les plus complètes où chaque acteur à un rôle à jouer. Ce diagramme montre bien les nombreuses actions que le joueur ou l'IA peuvent faire pour remporter le jeu, qu'ils s'agissent des relations diplomatiques avec d'autres joueurs ou de la création de troupe. Il met également en avant un point important de tout jeu vidéo d'envergure, la possibilité de faire apparaître une console qui permet d'appeler directement des fonctions javascript pour par exemple tester de nouveaux élèments non implémentés.

Ce diagramme permet également de mieux appréhender les fonctionnalités mises en avant dans le jeu et de voir comment ces fonctionnalités ont été intégré dans l'architecture du projet grâce à la partie suivante portant sur les packages.

### Diagramme des packages (Development View)
Notre analyse des packages a pour but de mieux comprendre comment le projet a été construit et comme premier point nous avons remarqué que les packages du projet représentent souvent la fonctionnalité "technique" qu'ils traitent comme le rendu, les graphismes, ou encore l'intégration des scripts dans le moteur. Ce qui donne le diagramme suivant:


![Diagramme des packages simplifié](https://github.com/Bhastyen/AnalyseGenieLogiciel/blob/master/Images/SimplePackageDiagram.png?raw=true "Diagramme des packages simplifié")

fig. 8 Diagramme des packages simplifié avec les dépendances principales

Le principal problème de cette architecture c'est qu'elle occassionne beaucoup de dépendances entre les packages ce qui peut compliquer énormément le refactoring. Il aurait pu être intéressant de diviser le projet en services et fonctionnalités orientées utilisateurs à la place comme on peut le voir avec le package "Atlas" qui est l'éditeur de scénarios du jeu. Le diagramme des dépendances qui suit montre bien le problème. On remarque rapidement que les packages s'appellent beaucoup entre eux ce qui rend le tout assez illisible.

![Diagramme des dépendances](https://github.com/Bhastyen/AnalyseGenieLogiciel/blob/master/Images/ArchInternalDependencies-DirectoryStructure.png?raw=true "Diagramme des dépendances entre packages")

fig. 9 Diagramme des dépendances entre packages

Plus précisèment le diagramme des packages est comme ceci:
![Diagramme des packages](https://github.com/Bhastyen/AnalyseGenieLogiciel/blob/master/Images/RealPackageDiagram2.JPG?raw=true "Diagramme représentant les différents packages")

fig. 10 Diagramme des packages avec leurs dépendances

Celui-ci permet de remarquer que les différents packages ne sont pas tous bien organisé en dossiers plus petit, ce qui peut poser problème pour la compréhension du projet, par exemple dans le package "graphics" qui est très petit, on trouve 118 fichiers, 40 classes et 25 735 lignes de code ce qui rends l'exploration laborieuse. De plus le diagramme de package ne permet pas sous cette forme de reconnaître les élèments les plus importants de l'architecture comme la gestion des graphismes dans "graphics". En effet ce package possède un diagramme de classe très imposant comme montré ci-dessous à la figure 11.

![Diagramme des classes graphics](https://github.com/Bhastyen/AnalyseGenieLogiciel/blob/master/Images/ClassDiagramGraphics.JPG?raw=true "Diagramme de classe de graphics")

fig. 11 Diagramme des classes du package "graphics"

Mais celui-ci n'est pas divisé en sous-partie comme dans le package tools (voir figure 12).

![Diagramme de packages de tools et graphics](https://github.com/Bhastyen/AnalyseGenieLogiciel/blob/master/Images/PackageToolsDiagram.png?raw=true "Diagramme de packages du package Tools et du package Graphics)

fig. 12 Diagramme de packages du package "Tools" et du package "Graphics"



### Logical View
Le diagramme des classes étant assez grands et complexes, regroupant 655 classes dans 17 packages. Il comprend également 298 228 lignes de code, en voici un aperçu non exhaustif:

![Grand diagramme des classes](https://github.com/Bhastyen/AnalyseGenieLogiciel/blob/master/Images/BigClassDiagram.JPG?raw=true "Diagramme des classes représentant les packages les plus importants")

fig. 13 Partie du diagramme des classes consernant la gestion des unités

Nous avons donc décidé de réduire la présentation de cette partie à un élèment unique du diagramme et une des fonctionnalités de base du jeu : la gestion des unités.

![Diagramme des classes Unité](https://github.com/Bhastyen/AnalyseGenieLogiciel/blob/master/Images/ClassDiagramCUnit.JPG?raw=true "Diagramme représentant les classes permettant de gérer un ensemble de troupes")

fig. 14 Partie du diagramme des classes consernant la gestion des unités

Cette partie du diagramme est assez représentative du reste du code. On trouve une classe de base gérant les unités appelées "CUnit" celle-ci comprend notamment un objet animation et un objet model pour la représentation graphique de l'unité. Ce qui est intéressant ici c'est qu'il existe une classe spécialisée appelée "CUnitManager" qui permet de gérer toutes les unités au même endroit et de rapidement faire des opérations dessus. Ce schéma se retrouve souvent dans le logiciel sans doute pour pourvoir plus facilement optimiser l'usage de la mémoire, ce problème étant très courant dans les jeux vidéos. La classe "CUnit" utilise également un Abstract Design Pattern avec un constructeur privée et une méthode static "Create" se chargeant des allocations mémoires à faire, mais nous reviendrons sur les Design Patterns dans une autre partie. La classe <CUnitManager> est ensuite relié à une autre partie du code que voici.

![Diagramme des classes  SimContext](https://github.com/Bhastyen/AnalyseGenieLogiciel/blob/master/Images/ClassDiagramSimContext.JPG?raw=true "Diagramme représentant le contexte de la simulation")

fig. 15 Diagramme UML de la classe "CSimContext" du package Simulation2

Il s'agit du contexte auquel va se référer le logiciel lorsqu'une partie sera lancée. Ce contexte possède donc un gestionnaire d'unités avec un terrain, un gestionnaire d'entité regroupant tous les types d'objet qui peuvent intervenir dans une partie et un gestionnaire de composants. Cet object regroupe donc un grand nombre d'informations essentielles au bon déroulement d'une partie.

### Process View

![Diagramme de séquence](https://github.com/Bhastyen/AnalyseGenieLogiciel/blob/master/Images/SequenceDiagram1.JPG?raw=true "Diagramme de séquence dans le cas d'une partie jouée contre une IA")

fig. 16 Diagramme de séquence dans le cas d'une partie jouée contre une IA

Ce diagramme de séquence nous apprends un peu plus de chose quand au fonctionnement du logiciel. Le moteur de jeu gère ici l'aspect controller, il reçoit des évènements puis appel en consèquence les bons modules chacun gèrant une partie du logiciel comme l'IA, le rendu, la caméra, le pathfinding, les animations, ect ... .

## Analyse de la qualité du code
Pour analyser la qualité du code nous allons utiliser différentes métrique sur certaines parties de celui-ci. Pour déterminer si le code réponds à ses critères nous avons utilisé différents logiciels , notamment CodeScene , BetterCodeHub et CppCheck.
Les critères que que nous avons retenu sont :

La longueur de classe
La longueur des méthodes
Le nombre de paramètres
La complexité cyclomatique

Sur les 3.436.103 lines de code disponible nous avons porté notre analyse sur certains dossiers qui nous semble plus important pour le jeu.

##source/simulation2

Nous avons choisi de regarder ce dossier car il nous semble une des parties centrale du jeu. Les fichiers étant ayant comme language C++ , ils permettent notamments de gérer les fondations du jeu qui vont être par la suite compléter par du javascript (dossier binaries qui sera aussi analyser tantôt).
![Analyse BetterCodeHub](https://github.com/Bhastyen/AnalyseGenieLogiciel/blob/master/Images/testBetterCodeSimulation2.png?raw=true "Analyse de la qualité du dossier simulation2 avec BetterCodeHub")

fig. 17 Analyse de la qualité du dossier simulation2 avec BetterCodeHub


D'après la figure ci-dessus nous pouvons constater que ce dossier malgré son importance comporte de nombreux points faibles du point de vue de BetterCodeHub.

![Analyse BetterCodeHub](https://github.com/Bhastyen/AnalyseGenieLogiciel/blob/master/Images/Write%20Short%20Units%20of%20Code%20Better%20Code%20Hub.png?raw=true "Analyse BetterCodeHub sur la taille des méthodes")

fig. 18 Analyse de la qualité sur la taille des méthodes
![Analyse BetterCodeHub](https://github.com/Bhastyen/AnalyseGenieLogiciel/blob/master/Images/Write%20Simple%20Units%20of%20Code%20Better%20Code%20Hub.png?raw=true "
Analyse BetterCodeHub sur la simplicité des méthodes")

fig. 19 Analyse de la qualité sur la simplicité des méthodes

Dans ces analyses nous pouvons voir que certaines méthodes font largements plus que les 15 lines de codes recommandées. En regardant plus attentivement les fichiers concerné , nous pouvons en déduire qu'il serait effectivement possible de plus fractionner en des sous fonctions pour réduire se nombre de lignes. Ce qui aurait aussi comme conséquences de d'augmenter la simplicité des méthodes.


![Analyse BetterCodeHub](https://github.com/Bhastyen/AnalyseGenieLogiciel/blob/master/Images/Nombre%20parametres%20methodes%20Code%20Better%20Code%20Hub.png?raw=true "
Analyse BetterCodeHub sur le nombre de paramètres des méthodes")

fig. 20 Analyse BetterCodeHub sur le nombre de paramètres des méthodes

Nous pouvons voir un problème sur le nombre de paramètre pouvant atteindre le nombre de 10. Certains cas pourrait être éviter , notamment en créeant des structures. Comme par exemple dans la fonction suivante où une structure de coordonnées aurait pu être mise en place :

'static void ConstructCircleOrClosedArc(
	const CSimContext& context, float x, float z, float radius,
	bool isCircle,
	float start, float end,
	SOverlayLine& overlay, bool floating, float heightOffset)'

De plus certains paramètres ne sont pas clair sur leur signification comme par exemple dans la méthode :

'static void AddTerrainEdges(std::vector<Edge>& edges, std::vector<Vertex>& vertexes,
	int i0, int j0, int i1, int j1,
	pass_class_t passClass, const Grid<NavcellData>& grid)'

		



##binaries
=


25 Auteurs.
Fichiers compléxité haute : 0ad/binaries/data/mods/public/gui/gamesetup/gamesetup.js 
fonction launch complexity
There are no structural recommendations. Keep up the good work! 
Trop de paramètres dans des méthodes/fonctions  parfois même 8 ou 10
### Conclusion sur l'architecture
Les différents diagrammes que nous avons présentés nous ont permis d'en déduire une architecture pour le logiciel que voici:

![Architecture logicielle](https://github.com/Bhastyen/AnalyseGenieLogiciel/blob/master/Images/GameEngine.png?raw=true "Architecture du logiciel")

fig. 17 Architecture globale du logiciel

L'architecture est donc centré autour du moteur de jeu qui regroupe les différents composants essentiels au fonctionnement du jeu. Le moteur s'occupe de récupérer et d'interpréter les évènements transmis par le joueur (selectionner un élèment, charger une partie, ...) et de synchroniser l'utilisation de tous ces composants
pour éviter les problèmes de concurrences.
