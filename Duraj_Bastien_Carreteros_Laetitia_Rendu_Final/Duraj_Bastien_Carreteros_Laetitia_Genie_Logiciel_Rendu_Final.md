# Analyse logiciel du jeu libre 0 A.D.
![alt text](https://github.com/Bhastyen/AnalyseGenieLogiciel/blob/master/Images/header_0ad.jpg?raw=true "Bannière du jeu 0 A.D.")

Duraj Bastien et Carreteros Laetitia
UQAC, 05 décembre 2018

#### Plan
1. Introduction
2. Description du logiciel
3. Analyse architecturale
	1. Les différents acteurs et scénarios possibles
		1. Présentation des grandes fonctionnalités
		2. Disposition des fonctionnalités
		3. Détail d'une fonctionnalité
	2. Development View
		1. Disposition des packages
		2. Répartition du code dans l'architecture
		3. Lien entre les packages
	3. Logical View
		1. Structure globale du code C++
		2. Implémentation de la gestion des unités
		3. Problème de la structuration des classes
	4. Process View
		1. Diagramme de séquences lors de l'action d'un joueur
		2. Analyse du fonctionnement
	5. Conclusion sur l'architecture
4. Analyse de la qualité du code
	1. source/simulation2
	2. binaries/data
	3. Design Patterns
	4. Conclusion sur la qualité du code
5. Conclusion


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
Pour comprendre le fonctionnement de ce logiciel nous avons tout d'abord analysé l'architecture de celui-ci en commençant par identifier les différents acteurs qui peuvent intervenir dans le déroulement du jeu et surtout les fonctionnalités proposées par le jeu. Pour représenter ces acteurs et les fonctionnalités auxquels ils sont rattachés nous avons décidé de modéliser nos résultats avec, dans un premier temps, un diagramme regroupant les différentes parties du logiciel et les acteurs, puis dans un second temps un diagramme détaillant de manière hiérarchique les fonctionnalités proposées dans le jeu. Pour finir sur cette partie nous avons créé un diagramme des cas d'utilisation pour la partie la plus intéressante et la plus complète : le déroulement d'une partie.

#### 1. Présentation des grandes fonctionnalités
![Diagramme des packages](https://github.com/Bhastyen/AnalyseGenieLogiciel/blob/master/Images/PackageDiagram1v2.png?raw=true)

fig. 6 Diagramme représentant les grandes fonctionnalités du jeu


Nous avons donc diviser en 6 parties les différentes utilisations possibles du jeu avec 2 acteurs humains, les joueurs et les développeurs, et un acteur système représentant l'intelligence artificiel du jeu: Petra. Alors que le développeur peut faire tout ce que le joueur peut, en plus de la possibilité d'utiliser une console, l'IA ne peut que jouer une partie. Le joueur a donc un nombre important de fonctionnalités à disposition ce qui montre la volonté des développeurs de créer un jeu rivalisant en terme de fonctionnalité avec des jeux commerciaux. Cet aspet est important car si le jeu a le mérite de proposer beaucoup de fonctionnalités nous verrons par la suite que l'architecture en souffre quelque peu.

#### 2. Présentation des fonctionnalités
![Diagramme des packages](https://github.com/Bhastyen/AnalyseGenieLogiciel/blob/master/Images/ArbreFonctionnalite.png?raw=true)

En ce qui concerne l'organisation des fonctionnalités, celle-ci est très classique et on sent que le jeu s'est inspiré de son père spirituel Age of Empires. Les parties solo et multijoueur du titre sont séparées ce qui montre une envie de proposer plusieurs façons de jouer. On trouve également en premier dans le menu du jeu la possibilité d'apprendre à jouer avec un manuel, des tutoriels et des informations sur les éléments du jeu, encore une fois beaucoup de possibilités et une envie évidente d'inviter le joueur à rejoindre la communauté du jeu dans les meilleurs conditions. Le jeu propose encore deux fonctionnalités. La première permet de régler le jeu selon ses envies : graphismes, contrôle, son, ... on peut même apercevoir la gestion des mods qui permet à qui le veut de personnaliser le logiciel de manière très poussé. Encore une fois très complet. La dernière très intéressante permet de créer à l'envie des cartes pour le jeu, cet outil très puissant est d'ailleurs utilisé par les game designers pour générer des fichiers xml automatiquement représentant la carte à intégrer dans le jeu. Elle est ensuite lu par le moteur qui pourra la stocker en mémoire et l'afficher. L'outil qui s'appelle Atlas propose de nombreuses fonctionnalités non détaillé ici comme la création de terrains avec différents peinceaux, la possibilité de peindre le terrain avec des textures, ... . Le jeu se veut donc à tout point de vue complet et intègre un nombre impressionnant de fonctionnalité.

#### 3. Cas d'utilisation d'une fonctionnalité
Pour finir notre analyse des fonctionnalités proposées par le jeu nous avons détaillé une des grandes fonctionnalités à l'aide d'un diagramme de cas d'utilisation pour nous permettre de mieux comprendre les objectifs et les besoins d'un tel logiciel.

![Diagramme des cas d'utilisations](https://github.com/Bhastyen/AnalyseGenieLogiciel/blob/master/Images/useCaseJouerPartie.png?raw=true "Diagramme représentant les différentes possibilitées qui s'offrent aux acteurs lors du déroulement d'une partie")

fig. 7 Diagramme des cas d'utilisations pour la fonctionnalité "Jouer partie"

Le diagramme qui nous intéresse ici est celui qui concerne le déroulement d'une partie. Il est le centre du jeu et justifie l'existence même du moteur graphique, c'est aussi l'une des parties les plus complètes où chaque acteur à un rôle à jouer. Ce diagramme montre bien les nombreuses actions que le joueur ou l'IA peuvent faire pour remporter le jeu, qu'ils s'agissent des relations diplomatiques avec d'autres joueurs ou de la création de troupe. Le jeu a donc besoin d'implémenter un nombre important d'élément comme la gestion des troupes, la construction de bâtiment, le commerce, la guerre, la récolte de ressources, ... . Il met également en avant un point important de tout jeu vidéo d'envergure, la possibilité de faire apparaître une console qui permet d'appeler directement des fonctions javascript pour par exemple tester de nouveaux élèments non implémentés.

Ce diagramme permet donc de mieux appréhender les fonctionnalités mises en avant dans le jeu et de voir comment ces fonctionnalités ont été intégré dans l'architecture du projet grâce à la partie suivante portant sur les packages.

### Diagramme des packages (Development View)
Notre analyse des packages a pour but de mieux comprendre comment le projet a été construit et s'il mériterait ou non des améliorations. Comme premier point nous avons remarqué que les packages du projet représentent souvent la fonctionnalité "technique" qu'ils traitent comme le rendu, les graphismes, ou encore l'intégration des scripts dans le moteur. Ce qui donne le diagramme suivant:

#### 1. Disposition des packages
Comme point de départ pour comprendre la répartition des packages dans le code source nous avons utilisé le diagramme fourni par CodeScene ci-dessous.
![Diagramme codescene](https://github.com/Bhastyen/AnalyseGenieLogiciel/blob/master/Images/SchemaCodeScene.JPG?raw=true "Diagramme représentant les packages organisés par langage et taille de fichier")

fig. 8 Diagramme CodeScene avec les langages les plus utilisées par packages

Celui-ci a la particularité de montrer la répartition des langages à travers les packages mais aussi un autre problème dont nous parlerons en détail dans la partie suivante. Dans source on retrouve majoritairement le moteur du jeu écrit en C++. Dans le dossier build on retrouve les outils externes au logiciel qui seront ajoutés au moment de la compilation comme Premake un outil open source pour optimiser la compilation, les langages majoritaires sont le C pour premake notamment et le lua. Libraries contient comme son nom l'indique les libraries que le projet utilise, le langage utilisé est ici majoritairement du C++. Enfin le dossier mods qui se situt non à la racine du code source mais dans Binaries contient comme son nom l'indique les mods mais également les scripts javascript qui seront interprétés par le moteur Pyrogenesis.

Grâce à ce diagramme nous avons pu voir que le jeu était en faite divisé en 2 parties. D'un côté le moteur du jeu Pyrogenesis écrit en C++ dans le dossier source et de l'autre les élèments du jeu lui-même comme le joueur ou l'IA dans le dossier mods/public/ écrit en javascript et relié dans le code source du moteur par une classe C++ : CScriptInterface. Celle-ci permet d'appeler des méthodes ou de récupérer des données d'un script.


#### 2. Répartition du code dans l'architecture
Nous allons maintenant aborder un point important de l'architecture et qui selon mériterait une amélioration. Le problème qui concerne la répartition du code source dans les différents dossiers est illustré dans le diagramme suivant.

![Diagramme des packages simplifié](https://github.com/Bhastyen/AnalyseGenieLogiciel/blob/master/Images/ProblemePackage.png?raw=true "Diagramme des packages simplifié")

Le diagramme représente les principaux packages du projet organisé sous la forme d'un arbre. Le but de ce diagramme est de montrer la répartition des lignes de codes à travers les packages. La première chose qu'on peut remarquer avec ce diagramme c'est que parmi tous les package représentés, seuls 4 d'entre eux intègrent la quasi totalité du code source dont le plus important "public" avec ses plus de 2 000 000 de lignes dont 112.152 lignes de javascript, le reste étant essentiellement des fichiers xmls servant à stocker les informations. Le second point concerne la profondeur de l'arbre à laquelle se trouve les fichiers javascript dans "public", ils sont difficiles à trouver de plus leur emplacement ne mets pas en avant leur lien très étroit avec le moteur du jeu, il est facile de s'imaginer qu'il s'agit de modes supplémentaires et optionelles alors qu'il n'en est rien. Nous avons donc affaire ici à un problème de visibilité sur le projet qui impacte la compréhension du projet plus que les performances par exemple.

Pour résoudre ce problème nous pourrions imaginer une organisation différente du code source notamment en ce qui concerne le javascript du jeu. Ceci obligerai par contre une distinction dans le projet entre les modes des utilisateurs et les modes dit public ce qui demanderai une réécriture partielle du moteur pouvant créer des instabilités involontaires.

#### 3. Lien entre les packages

Le second problème de cette architecture c'est qu'elle occassionne beaucoup de dépendances entre les packages ce qui peut compliquer énormément le refactoring. Il aurait pu être intéressant de diviser le projet en services et fonctionnalités orientées utilisateurs à la place comme on peut le voir avec le package "Atlas" qui est l'éditeur de scénarios du jeu. Le diagramme des dépendances qui suit montre bien le problème. On remarque rapidement que les packages s'appellent beaucoup entre eux ce qui rend le tout assez illisible.

![Diagramme des dépendances](https://github.com/Bhastyen/AnalyseGenieLogiciel/blob/master/Images/ArchInternalDependencies-DirectoryStructure.png?raw=true "Diagramme des dépendances entre packages")

fig. 9 Diagramme des dépendances entre packages

Celui-ci permet de remarquer que les différents packages ne sont pas tous bien organisé en dossiers plus petit, ce qui peut poser problème pour la compréhension du projet, par exemple dans le package "graphics" qui est très petit, on trouve 118 fichiers, 40 classes et 25 735 lignes de code ce qui rends l'exploration laborieuse. De plus le diagramme de package ne permet pas sous cette forme de reconnaître les élèments les plus importants de l'architecture comme la gestion des graphismes dans "graphics". En effet ce package possède un diagramme de classe très imposant comme montré ci-dessous à la figure 11.

![Diagramme des classes graphics](https://github.com/Bhastyen/AnalyseGenieLogiciel/blob/master/Images/ClassDiagramGraphics.JPG?raw=true "Diagramme de classe de graphics")

fig. 11 Diagramme des classes du package "graphics"

Mais celui-ci n'est pas divisé en sous-partie comme dans le package tools (voir figure 12).

![Diagramme de packages de tools et graphics](https://github.com/Bhastyen/AnalyseGenieLogiciel/blob/master/Images/PackageToolsDiagram.png?raw=true "Diagramme de packages du package Tools et du package Graphics")

fig. 12 Diagramme de packages du package "Tools" et du package "Graphics"

L'organisation des packages pourrait donc être un point intéressant à améliorer pour le projet pour offrir une meilleure visibilité et facilité le refactoring de certaines parties du code.  

### Logical View
Le diagramme des classes étant assez grands et complexes, regroupant 655 classes dans 17 packages. Il comprend également 298 228 lignes de code, en voici un aperçu non exhaustif:

![Grand diagramme des classes](https://github.com/Bhastyen/AnalyseGenieLogiciel/blob/master/Images/BigClassDiagram.JPG?raw=true "Diagramme des classes représentant les packages les plus importants")

fig. 13 Diagramme des classes non exhaustif du package source

#### 1. Structure globale du code source
Pour représenter la structure globale du package nous avons fait un diagramme avec les principales classes permettant de gérer le jeu.

![Structure du diagramme des classes](https://github.com/Bhastyen/AnalyseGenieLogiciel/blob/master/Images/ConstructionLogique.png?raw=true "Diagramme des classes représentant les classes les plus importante permettant le déroulement du jeu")

Le principale point à retenir est cette architecture en arbre. Il existe plusieurs grandes classes dans le code qui regroupe d'autres éléments plus petits et ainsi de suite on retrouve donc une certaine hiérarchie dans la structure logique du moteur. Globalement le jeu est orchestré par la classe <CGame> qui contient le monde de manière générale et <CSimulation2> qui permet de connecter les éléments entre eux pour créer un monde vivant. On utilise donc ici des classes englobantes qui favorisent la composition plutôt que l'héritage entre les classes, ceci permet également de mieux organisé le code.

#### 2. Implémentation de la gestion des unités

Pour donner un exemple plus précis nous avons donc décidé de présenter un élèment en particulier du diagramme et une des fonctionnalités de base du jeu : la gestion des unités.

![Diagramme des classes Unité](https://github.com/Bhastyen/AnalyseGenieLogiciel/blob/master/Images/ClassDiagramCUnit.JPG?raw=true "Diagramme représentant les classes permettant de gérer un ensemble de troupes")

fig. 14 Partie du diagramme des classes consernant la gestion des unités

Cette partie du diagramme est assez représentative du reste du code. On trouve une classe de base gérant les unités appelées "CUnit" celle-ci comprend notamment un objet animation et un objet model pour la représentation graphique de l'unité. Ce qui est intéressant ici c'est qu'il existe une classe spécialisée appelée "CUnitManager" qui permet de gérer toutes les unités au même endroit et de rapidement faire des opérations dessus. Ce schéma se retrouve souvent dans le logiciel sans doute pour pourvoir plus facilement optimiser l'usage de la mémoire, ce problème étant très courant dans les jeux vidéos. La classe "CUnit" utilise également un Abstract Design Pattern avec un constructeur privée et une méthode static "Create" se chargeant des allocations mémoires à faire, mais nous reviendrons sur les Design Patterns dans une autre partie. La classe <CUnitManager> est ensuite relié à une autre partie du code que voici.

![Diagramme des classes  SimContext](https://github.com/Bhastyen/AnalyseGenieLogiciel/blob/master/Images/ClassDiagramSimContext.JPG?raw=true "Diagramme représentant le contexte de la simulation")

fig. 15 Diagramme UML de la classe "CSimContext" du package Simulation2

Il s'agit du contexte auquel va se référer le logiciel lorsqu'une partie sera lancée. Ce contexte possède donc un gestionnaire d'unités avec un terrain, un gestionnaire d'entité regroupant tous les types d'objet qui peuvent intervenir dans une partie et un gestionnaire de composants. Cet object regroupe donc un grand nombre d'informations essentielles au bon déroulement d'une partie.

#### 3. Problème de la structuration des classes

Dans cette partie nous allons juste aborder un point interessant dans lequel nous reviendrons dans la partie sur la qualité du code. Un problème flagrant lorsque l'on analyse les classes c'est la taille de certaines d'entre elles. En effet certaines classes arrivent à dépasser le milliers de lignes. Il pourrait donc être intéressant de subdiviser ces classes en classes plus petites ce qui encore une fois permettrait de débuguer ou de faire du refactoring plus facilement. De plus cette façon de faire irait  dans le sens de la structure logique générale du code.

### Process View
#### 1. Diagramme de séquences lors de l'action d'un joueur

![Diagramme de séquence](https://github.com/Bhastyen/AnalyseGenieLogiciel/blob/master/Images/SequenceDiagram1.JPG?raw=true "Diagramme de séquence dans le cas d'une partie jouée contre une IA")

fig. 16 Diagramme de séquence dans le cas d'une partie jouée contre une IA

Ce diagramme de séquence nous apprends un peu plus de chose quand au fonctionnement du logiciel. Le moteur de jeu gère ici l'aspect controller, il reçoit des évènements puis appel en consèquence les bons modules chacun gèrant une partie du logiciel comme l'IA, le rendu, la caméra, le pathfinding, les animations, ect ... .

#### 2. Analyse du fonctionnement

### Conclusion sur l'architecture

D'après notre analyse le logiciel possède deux problèmes qui mériterait une amélioration, le problème des packages pas toujours très clair et le problème des classes trop grandes qui mériterait d'être subdiviser.

## Analyse de la qualité du code
Pour analyser la qualité du code nous allons utiliser différentes métriques sur certaines parties de celui-ci. Pour déterminer si le code répond à ces critères nous avons utilisé différents logiciels , notamment CodeScene , BetterCodeHub et CppCheck.
Les critères que nous avons retenu sont :

La longueur de classe /

La longueur des méthodes /

Le nombre de paramètres /


Sur les 3.436.103 de lignes de code disponibles nous avons porté notre analyse sur certains dossiers qui nous semble plus important pour le jeu.

### source/simulation2

Nous avons choisi de regarder ce dossier car il nous semble une des parties centrales du jeu.
Il définit les classes utilisées par la simulation. Les composants qui implémentent la logique du moteur sont inclus ici, ainsi que les classes de sérialisation et d'aide. Certains composants sont implémentés en JavaScript et définissent optionnellement les interfaces JavaScript et C++ (ces composants sont inclus par mods, dans le répertoire binaries\data).
![Analyse BetterCodeHub](https://github.com/Bhastyen/AnalyseGenieLogiciel/blob/master/Images/testBetterCodeSimulation2.png?raw=true "Analyse de la qualité du dossier simulation2 avec BetterCodeHub")

fig. 17 Analyse de la qualité du dossier simulation2 avec BetterCodeHub


D'après la figure ci-dessus nous pouvons constater que ce dossier malgré son importance comporte de nombreux points faibles du point de vue de BetterCodeHub.

![Analyse BetterCodeHub](https://github.com/Bhastyen/AnalyseGenieLogiciel/blob/master/Images/Write%20Short%20Units%20of%20Code%20Better%20Code%20Hub.png?raw=true "Analyse BetterCodeHub sur la taille des méthodes")

fig. 18 Analyse de la qualité sur la taille des méthodes
![Analyse BetterCodeHub](https://github.com/Bhastyen/AnalyseGenieLogiciel/blob/master/Images/Write%20Simple%20Units%20of%20Code%20Better%20Code%20Hub.png?raw=true "
Analyse BetterCodeHub sur la simplicité des méthodes")

fig. 19 Analyse de la qualité sur la simplicité des méthodes

Dans ces analyses nous pouvons voir que certaines méthodes font largement plus que les 15 lignes de codes recommandées. En regardant plus attentivement les fichiers concernés , nous pouvons en déduire qu'il serait effectivement possible de plus fractionner en des sous fonctions pour réduire ce nombre de lignes. Ce qui aurait aussi comme conséquences d'augmenter la simplicité des méthodes.


![Analyse BetterCodeHub](https://github.com/Bhastyen/AnalyseGenieLogiciel/blob/master/Images/Nombre%20parametres%20methodes%20Code%20Better%20Code%20Hub.png?raw=true "
Analyse BetterCodeHub sur le nombre de paramètres des méthodes")

fig. 20 Analyse BetterCodeHub sur le nombre de paramètres des méthodes

Nous pouvons voir un problème sur le nombre de paramètre pouvant atteindre le nombre de 10. Certains cas pourrait être évité , notamment en créant des structures. Comme par exemple dans la fonction suivante où une structure de coordonnées aurait pu être mise en place :

```
static void ConstructCircleOrClosedArc(
	const CSimContext& context, float x, float z, float radius,
	bool isCircle,
	float start, float end,
	SOverlayLine& overlay, bool floating, float heightOffset)

```

De plus certains paramètres ne sont pas clair sur leur signification comme par exemple dans la méthode :

```
static void AddTerrainEdges(std::vector<Edge>& edges, std::vector<Vertex>& vertexes,
	int i0, int j0, int i1, int j1,
	pass_class_t passClass, const Grid<NavcellData>& grid)

```

![Analyse BetterCodeHub](https://github.com/Bhastyen/AnalyseGenieLogiciel/blob/master/Images/AutomatesTest%20Code%20Better%20Code%20Hub.png?raw=true "
Analyse BetterCodeHub sur l'automatisation des test")
fig. 21 Analyse BetterCodeHub sur l'automatisation des test


Nous pouvons voir que d'après BetterCodeHub il n'y a aucun test automatisé. Ce serait donc un point à améliorer. Cependant il y a quand même un sous-dossier tests dans cette section.


### binaries/data

Binaries/data nous semblait un dossier également intéréssant à analyser.
Avec CodeScene nous avons pu retirer quelques informations parmi les fichiers proposés:

![Analyse CodeScene](https://github.com/Bhastyen/AnalyseGenieLogiciel/blob/master/Images/refactoring%20target.png?raw=true "
Analyse CodeScene refactoring candidats")
fig. 22 Analyse CodeScene pour trouver les candidats au refactoring

Nous avons regardé le fichier UnitIA.js suite à cela , et nous avons pu noter la grosseur d'un tel fichier : 6140 lignes. Nous émettons donc l'hypothèse que ce fichier pourrait être réduit en étant séparé en plusieurs modules.

Cependant parmi tous les fichiers Javascript disponible nous trouvons que la quantité de fichiers proposés en refactoring était moindre.

Gamesetup.js possède également un nombre de ligne assez conséquent : 2331 , qui devrait probablement être remanié.


### Qualité du travail d'équipe

Malgré quelques problèmes de qualité , nous trouvons que pour un logiciel libre de jeu vidéo nous avons une bonne documentation du projet. Cette documentation explique l'utilité des différents dossiers pour les programmeurs et les artistes.
Nous pouvons trouver également un système de tickets répertoriant les différentes tâches à faire ou qui sont en cours de production , ainsi qu'un forum.
Nous pouvons trouver aussi des informations sur la raison du choix des languages , des consignes de conventions de codage , la suggestion d'utiliser des objets mocks, un endroit où reporter les beugs.
Cependant il est précisé à certains endroits que certaines informations peuvent daté , notamment en ce qui concerne le design.
Le code possède des commentaires clairs pour des sujets importants. Notamment sur l'utilisation de design pattern. Nous avons pu trouver des fichiers singleton avec en commentaires leurs utilité et dans quel cas les utiliser. Ils font également partie des recommendations sur l'utilisation de ce design pattern dans la documentation du logiciel.

### Design Pattern
Notamment sur l'utilisation de design pattern. Nous avons pu trouver des fichiers singleton avec en commentaires leurs utilité et dans quel cas les utiliser. Ils font également partie des recommendations sur l'utilisation de ce design pattern dans la documentation du logiciel.
Nous avons également trouvé un boost flyweight qui pourrait suggérer l'utilisation du pattern flyweight.


### Conclusion sur la qualité du code

Il y a plusieurs points faibles dans les parties que nous avons étudié. Cependant on peut noter une envie d'accomplir ce projet dans de bonnes conditions stucturées, mise en évidence par la documentation. Cette documentation a beaucoup de détails sur comment les developpeurs doivent travailler par exemple :

![Analyse CodeScene](https://github.com/Bhastyen/AnalyseGenieLogiciel/blob/master/Images/exemple%20documentations.png?raw=true "
Exemple de la documentation")
fig. 22 Exemple de la documentation

## Conclusion
En conclusion le projet est plutôt bien structuré et beaucoup d'efforts sont fait dans ce sens. Hormis les deux problèmes architecturaux soulignés dans la partie correspondante le projet fonctionne de toute évidence bien. Du côté de la qualité du code source de gros efforts sont fait pour instaurer de bonnes pratiques lors de la création du code cependant on remarque sur les fichiers analysés un manque de synthétisations des données dans les méthodes par exemple. Un manque du côté des tests automatisés d'après BetterCodeHub est également présent. Le projet pourrait donc être améliorer en suivant des pratiques supplémentaires comme l'automatisation des tests.
