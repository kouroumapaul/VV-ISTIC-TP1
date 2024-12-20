# Practical Session #1: Introduction

1. Find in news sources a general public article reporting the discovery of a software bug. Describe the bug. If possible, say whether the bug is local or global and describe the failure that manifested its presence. Explain the repercussions of the bug for clients/consumers and the company or entity behind the faulty program. Speculate whether, in your opinion, testing the right scenario would have helped to discover the fault.

2. Apache Commons projects are known for the quality of their code and development practices. They use dedicated issue tracking systems to discuss and follow the evolution of bugs and new features. The following link https://issues.apache.org/jira/projects/COLLECTIONS/issues/COLLECTIONS-794?filter=doneissues points to the issues considered as solved for the Apache Commons Collections project. Among those issues find one that corresponds to a bug that has been solved. Classify the bug as local or global. Explain the bug and the solution. Did the contributors of the project add new tests to ensure that the bug is detected if it reappears in the future?

3. Netflix is famous, among other things we love, for the popularization of *Chaos Engineering*, a fault-tolerance verification technique. The company has implemented protocols to test their entire system in production by simulating faults such as a server shutdown. During these experiments they evaluate the system's capabilities of delivering content under different conditions. The technique was described in [a paper](https://arxiv.org/ftp/arxiv/papers/1702/1702.05843.pdf) published in 2016. Read the paper and briefly explain what are the concrete experiments they perform, what are the requirements for these experiments, what are the variables they observe and what are the main results they obtained. Is Netflix the only company performing these experiments? Speculate how these experiments could be carried in other organizations in terms of the kind of experiment that could be performed and the system variables to observe during the experiments.

4. [WebAssembly](https://webassembly.org/) has become the fourth official language supported by web browsers. The language was born from a joint effort of the major players in the Web. Its creators presented their design decisions and the formal specification in [a scientific paper](https://people.mpi-sws.org/~rossberg/papers/Haas,%20Rossberg,%20Schuff,%20Titzer,%20Gohman,%20Wagner,%20Zakai,%20Bastien,%20Holman%20-%20Bringing%20the%20Web%20up%20to%20Speed%20with%20WebAssembly.pdf) published in 2018. The goal of the language is to be a low level, safe and portable compilation target for the Web and other embedding environments. The authors say that it is the first industrial strength language designed with formal semantics from the start. This evidences the feasibility of constructive approaches in this area. Read the paper and explain what are the main advantages of having a formal specification for WebAssembly. In your opinion, does this mean that WebAssembly implementations should not be tested? 

5.  Shortly after the appearance of WebAssembly another paper proposed a mechanized specification of the language using Isabelle. The paper can be consulted here: https://www.cl.cam.ac.uk/~caw77/papers/mechanising-and-verifying-the-webassembly-specification.pdf. This mechanized specification complements the first formalization attempt from the paper. According to the author of this second paper, what are the main advantages of the mechanized specification? Did it help improving the original formal specification of the language? What other artifacts were derived from this mechanized specification? How did the author verify the specification? Does this new specification removes the need for testing?

## Answers

## Question 1


Bug logiciel : Vulnérabilité de type buffer overflow dans WhatsApp (2023)

Description du bug :
Le bug se situe dans la bibliothèque de codec vidéo de WhatsApp. Lors du traitement des paquets RTP (Real-time Transport Protocol) pendant les appels vidéo, le programme ne vérifiait pas correctement la taille du buffer de destination, ce qui permettait une écriture mémoire non contrôlée.

Portée : Bug global affectant toutes les versions Android de WhatsApp.

Manifestation technique du bug :
Le problème se produisait dans le traitement des paquets RTP (Real-time Transport Protocol) pendant les appels vidéo. Plus précisément :
- Lors de la réception d'un paquet vidéo, le code ne vérifiait pas correctement la taille du buffer de destination
- Si un paquet malformé était reçu avec une taille déclarée incorrecte, le programme écrivait au-delà des limites allouées en mémoire
- Cette corruption de mémoire pouvait provoquer un crash de l'application ou, plus grave, permettre l'exécution de code malveillant

Répercussions :
- Pour les utilisateurs : Risque de crash de l'application et vulnérabilité potentielle à des attaques
- Pour WhatsApp/Meta : Nécessité de déployer un correctif d'urgence à plus de 2 milliards d'utilisateurs
- Impact sur la réputation en matière de sécurité


Analyse des tests :
Ce bug aurait pu être détecté par :

1. Des tests de validation des entrées avec des paquets malformés
2. Des tests unitaires vérifiant spécifiquement les limites de buffer
3. Des tests de fuzzing sur le traitement des paquets RTP

Ces tests auraient probablement permis de détecter le bug avant sa mise en production.

Le correctif a impliqué l'ajout de vérifications de taille strictes avant toute opération d'écriture en mémoire.



## Question 2

### Apache Commons Bug Analysis

**Lien du bug trouvé dans Apache Commons**: [COLLECTIONS-813](https://issues.apache.org/jira/projects/COLLECTIONS/issues/COLLECTIONS-813?filter=doneissues)  

Classification du bug : C'est un bug LOCAL

Il n'affecte qu'une méthode spécifique (CollectionUtils.retainAll)
Le problème est limité à la logique de validation des paramètres, La fonction est censée lever un `Null Point Exception`. 

Il n'a pas d'impact sur d'autres parties du système ou d'effets en cascade


---

## Question 3

### Netflix et l’Ingénierie du Chaos : Expérimentations de Tolérance aux Pannes

**1. Contexte des Expérimentations**  
Netflix a développé le projet **Chaos Monkey** pour tester la tolérance aux pannes de son infrastructure après sa migration vers Amazon Web Services (AWS). Chaos Monkey est un outil open-source qui simule des pannes en production en choisissant aléatoirement une instance de serveur et en la mettant hors ligne.  
- **Objectif** : Tester la résilience du système, identifier les faiblesses et renforcer la confiance dans la stabilité du système.  
- **Intégration** : Chaos Monkey est intégré à **Spinnaker**, qui améliore les déploiements continus et offre une interface utilisateur avancée.

---

**2. Expérimentations Réalisées**  
Les expérimentations suivent cinq principes clés :

1. **Construire une hypothèse sur l’état stable**  
   - Les ingénieurs définissent ce qu’ils considèrent comme un fonctionnement normal (par ex., le **nombre de vidéos démarrées par seconde (SPS)**).  

2. **Choisir des événements réels**  
   - Simuler des pannes réelles telles que :  
     - Des erreurs réseau.  
     - Des requêtes mal formatées.  
     - Des pannes matérielles.  

3. **Exécuter les expérimentations en production**  
   - Tester directement en production pour observer les comportements réels sous charge.  

4. **Automatiser les expérimentations pour les exécuter de manière continue**  
   - Des tests automatisés garantissent une résilience constante malgré les changements d’infrastructure.  

5. **Limiter le rayon d’effet**  
   - Les premiers tests impactent un nombre limité d’utilisateurs ou de services avant une extension progressive.

---

**3. Variables Observées**  
Les principales métriques surveillées lors des tests incluent :  
- **Vidéos démarrées par seconde (SPS)** : Mesure de la stabilité du service.  
- **Disponibilité des services** : Surveillance de l’accès en cas de panne.  
- **Taux d’erreurs** : Identification des défaillances critiques.  
- **Latence** : Délais entre services distribués.

---

**4. Résultats Obtenus**  
Les expérimentations de chaos ont permis de :  
- Identifier des **points de défaillance inattendus**.  
- Améliorer la résilience grâce à la redondance des services critiques.  
- Renforcer la confiance dans la gestion rapide des pannes réelles.

---

**5. Autres Entreprises Pratiquant l’Ingénierie du Chaos**  
Netflix n’est pas seul dans cette pratique. D’autres entreprises l’appliquent, comme :  
- **Amazon**  
- **Google**  
- **Uber** (avec **Chaos Kong** pour des tests à grande échelle).  
- **Microsoft**

---

## Question 4

La spécification formelle de WebAssembly offre de nombreux avantages. Elle garantit une clarté et une précision essentielles pour définir le comportement du langage sans ambiguïté. Cela permet aux développeurs et implémenteurs de comprendre précisément comment WebAssembly doit fonctionner, un aspect crucial pour un langage de bas niveau où des erreurs subtiles peuvent impacter gravement la sécurité et la fiabilité. De plus, une telle spécification facilite le raisonnement sur le comportement des programmes, rendant possible la démonstration de propriétés fondamentales telles que la sûreté des types et la sécurité mémoire, ce qui renforce la fiabilité des applications.

Un autre avantage réside dans la simplification de la validation. Avec une base formelle, il est possible de concevoir des algorithmes de validation efficaces pour s'assurer que le code chargé depuis des sources non fiables est exempt de comportements indéfinis ou malveillants. Cette spécification permet également de soutenir une compilation efficace, en aidant les compilateurs à générer du code machine optimisé tout en réalisant des analyses statiques. Enfin, elle garantit l’interopérabilité entre les différentes implémentations, assurant une compatibilité transparente entre des modules générés avec divers outils et exécutés sur des plateformes variées.

Malgré ces atouts, les implémentations WebAssembly doivent être testées. La complexité des moteurs WebAssembly implique des détails d’implémentation qui ne sont pas toujours explicités dans la spécification, laissant place à des erreurs potentielles. De plus, WebAssembly étant conçu pour être portable, des tests sont indispensables pour vérifier que des variations matérielles ou logicielles ne provoquent pas de divergences. L’interaction entre WebAssembly et son environnement d’exécution est une autre source potentielle de bogues, nécessitant des vérifications minutieuses. Enfin, l’évolution constante de WebAssembly, avec l’ajout régulier de nouvelles fonctionnalités, demande des tests pour garantir la compatibilité et le bon fonctionnement des implémentations.

En résumé, bien que la spécification formelle soit un outil puissant pour assurer la sécurité et la portabilité de WebAssembly, elle ne remplace pas les tests pratiques. Ces derniers restent essentiels pour détecter et corriger les erreurs qui peuvent apparaître dans des scénarios réels, renforçant ainsi la robustesse et la fiabilité des implémentations.



---

## Question 5


La spécification mécanisée de WebAssembly apporte de nombreux avantages significatifs dans le domaine du développement de langage de programmation. Elle permet avant tout une vérification formelle de la spécification, offrant la possibilité de prouver des propriétés cruciales comme la cohérence du système de types. Avant ces travaux, aucune preuve formelle n'existait, ce qui représente une avancée majeure dans la compréhension et la validation du langage.
Un aspect remarquable de cette approche est sa capacité à détecter des erreurs dans la spécification originale. Les chercheurs ont pu identifier et communiquer des contre-exemples précis aux auteurs de la spécification initiale, proposant des solutions qui ont été intégrées dans la version officielle. Cette démarche introduit une rigueur et une clarté supplémentaires dans la définition de la sémantique du langage, éliminant les ambiguïtés potentielles.
La spécification mécanisée a également permis de créer des artefacts vérifiés, notamment un interpréteur et un vérificateur de types garantis conformes à la spécification. Ces outils peuvent être utilisés pour valider différentes implémentations de WebAssembly, renforçant ainsi la fiabilité globale du langage.
Plusieurs améliorations concrètes ont été apportées à la spécification originale. Par exemple, la gestion des exceptions, représentées par l'opération administrative Trap, a été corrigée. Les règles de typage de l'opération Return ont été revues, tout comme les invariants relatifs aux fonctions hôtes, résolvant des problèmes potentiels de progression et de comportement.
Malgré ces avancées significatives, les chercheurs soulignent que la spécification mécanisée ne remplace pas entièrement les tests d'implémentation. La complexité de la mise en œuvre d'un moteur WebAssembly, les variations matérielles et logicielles, les interactions avec l'environnement et l'évolution constante de la spécification nécessitent toujours une batterie de tests approfondis.
En conclusion, la spécification mécanisée de WebAssembly représente une étape cruciale vers un langage plus fiable et portable. Elle a non seulement permis de détecter et corriger des erreurs dans la spécification originale, mais a également fourni des outils vérifiés pour valider les implémentations. Cependant, elle reste complémentaire aux processus traditionnels de test, garantissant ainsi la robustesse et la conformité du langage.