# Stratégie de Conception et de Sécurisation de pire2pire.com

## Authentification et mots de passe

### 1. L'authentification

L'authentification est au cœur des applications comportant un espace de connexion. 
Cette authentification se doit d'être sécurisée au maximum pour préserver la pérénité de l'application et la sécurité des apprenants ainsi que des formateurs voulant s'authentifier.

Chez pire2pire.com, la dissociation du corps enseignant et des apprenants devait être présente lors de l'authentification. Un enseignant ayant des droits de lecture et d'écriture verra son compte plus sécurisé qu'un étudiant n'ayant qu'un droit de lecture des formations.

#### 1.1 Principe de l'authentification

Un prouveur est un utilisateur du système d’information cherchant à s’authentifier. Le vérifieur est quant à lui classiquement un serveur du système d’information qui a la charge de vérifier l’identité d’un utilisateur.

Le prouveur va ainsi prouver son identité au vérifieur grâce à un moyen d’authentification, élément qui est généralement connu ou possédé uniquement par le prouveur et qui permet de l’authentifier de manière unique. Ce moyen d’authentification peut prendre par exemple la forme d’un mot de passe, d’une donnée biométrique ou bien de la clé privée associée à un certificat électronique contenu dans une carte à puce. Le moyen d’authentification est créé lors de l’enregistrement.

Une fois l’authentification réalisée, cette dernière (ou un autre service) va permettre d’autoriser l’accès d’un utilisateur (selon des critères propres à chaque utilisateur) à des ressources (données, services, applications, etc.).

#### 1.2 Les mots de passe

Chacune des deux entités a sa propre conformité de mot de passe. 
Un apprenant par exemple doit avoir un mot de passe composé de 8 caractères, avec des minuscules et des majuscules ainsi que des caractères spéciaux. 

Une génération de mot de passe est également proposée afin que la personne voulant s'inscrire sur la plateforme puisse créer son mot de passe sécurisé en 1 clique. 

### 1.3 Sécurité

Pour s'authentifier, chacun des deux acteurs doit utilisé son mot de passe précédemment créé.
Cependant, suite à 10 échecs de connexion, le compte sera bloqué pour éviter tout risque d'attaque par force brute (tests automatisés de tous les mots de passe et de toutes les clés
cryptographiques possibles). 

Le formateur doit, quant à lui, se protéger davantage à l'aide d'une authentification multifacteur à l'aide de son adresse mail. Le formateur recevra donc un e-mail sur la boîte mail renseigné afin d'authentifier qu'il s'agîsse bien de lui.

Lors de l'authentification, chaque envoie en base de données (pour checker s'il s'agît bien du bon mot de passe par exemple) est chiffré de bout en bout grâce au protocole de cryptage TLS.

Lorsque l'utilisateur (apprenant ou formateur) s'inscrit, son mot de passe est stocké en base de données. Pour éviter tout problème lors d'une éventuelle infiltration sur le serveur stockant les données, chaque mot de passe est hashé grâce à SHA-256, une fonction de hashage permettant de transformer le mot de passe de l'utilisateur afin que celui-ci soit inexploitable aux premiers abords.

### 1.4 Les durées

Lorsqu'une session est créée (lors de l'authentification par exemple), celle-ci est valable pour une durée d'une semaine pour les formateurs. Les informations présentes y étant plus sensible, il faut vérifier régulièrement que les facteurs d'authentification ne soient pas compromis. Ainsi, la session stockée dans un cookie est supprimée au bout de 7 jours. 

Egalement, tous les ans, les formateurs doivent réinitialiser leur mot de passe afin de conserver la conformité des facteurs d'authentification.

### 1.5 La journalisation

Chaque événement est inscrit dans un journal. Celui-ci doit être privé au développeur afin de connaître et suivre d'éventuels problème lié à l'authentification. Les informations suivantes sont recencées : date et heure de connexion, IP, localisation et e-mail utilisé pour la tentative d'authentification. 
En cas de tentative d'authentification étrange, venant d'une même IP par exemple, le compte pourrait être banni jusqu'à confirmation de l'adresse mail par exemple.


## Standars de sécurité côté navigateur

### 1. Règles d'hygiene. 

Avant d'aborder en détails les préventions qui seront mises en place, nous devons nous rappeler des princiapl étapes liée au développement de notre application web, qui sont : 
 - La conception du site ou de l'application
 - L'intégrité du comportemet de l'application côté client
 - La configuration de l'infrasctructure d'hébergement
 - Détection et information

Ces différentes étapes vont nous permmettre de mettre en place nos règles de sécurité des le début, afin d'annalyser les besoin de sécurité liés à notre projet, de la manières la plus adapté et efficace possible, et veiller à les respecter tout au long de la phase de développement. 

#### 1.1 Moindre privilèges

Nous devrons définir les roles de chaque utilisateur pour lui octroyer les permissions qui lui conviennent, leurs roles à chacun, comme dit plus haut lors de l'authentification nous devons dissocier les droit des professeur et des apprenant. L'apprenant pourras donc seulement consulter, s'inscrire à une ou plusieurs formation, tandis que le professeur pourras lui publier, modifier, et évaluer les apprenant.

### 2. Mise en œuvre des mécanismes de sécurité web

#### 2.1 Same-Origin Policy (SOP)

SOP nous sert à mettre en place un cadre permettant de controller et restreindre la communication entre composant lorsque ceux-ci sont d'Origin différente, l’Origin d’une page étant définie par le triplet protocole, destination et port présent dans la barre d’adresse, dans notre cas il d'agit de pire2pire.com. Dans notre cas à nous, nous souhaitont que notre Origin communique uniquement avec les composant nécéssaire. Nos cookies, fichier multimédia, code JavaScript/CSS, ne sera accessible que par notre Origin. Dans le cas d'une utilisation d'API, nous pourront contourner cettre restriction par défaut grace au CORS, c'est ce que nous allons voir dans la section suivante.