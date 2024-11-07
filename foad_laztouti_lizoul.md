## 1_Infrastructure AWS pour le Déploiement de l’Application
# 1. Amazon Elastic Container Service (ECS) avec Fargate

Rôle : Exécuter les conteneurs Docker pour les services frontend et backend de manière serverless.
Avantages : ECS Fargate est entièrement managé, éliminant le besoin de gérer des serveurs ou des clusters de conteneurs, et il est compatible avec un modèle serverless.
Justification : Cette option simplifie l'administration et répond aux contraintes de facilité d'utilisation pour les développeurs. Fargate est particulièrement adapté pour les architectures serverless nécessitant une haute disponibilité.

# 2. Amazon Elastic Container Registry (ECR)

Rôle : Stocker et gérer les images Docker (frontend et backend) de manière sécurisée.
Avantages : ECR est intégré à ECS, simplifiant le déploiement des conteneurs sans configurations supplémentaires pour accéder aux images Docker.
Justification : Utiliser un dépôt Docker managé, sécurisé et optimisé pour l'intégration avec ECS permet de simplifier le workflow DevOps et d'assurer une compatibilité transparente avec les autres services AWS.

# 3. Amazon API Gateway

Rôle : Servir de passerelle pour gérer les requêtes HTTP(s) vers le backend.
Avantages : API Gateway gère les accès, les authentifications, et offre une couche de sécurité supplémentaire au backend en protégeant contre les requêtes malveillantes ou excessives.
Justification : Il simplifie la gestion de l'accès au backend, permettant une protection renforcée et une gestion des accès plus fine.


# 4. Application Load Balancer (ALB)

Rôle : Gérer la distribution du trafic entre les instances ECS Fargate des conteneurs frontend et backend.
Avantages : Le Load Balancer assure une distribution équilibrée du trafic et est compatible avec le déploiement multi-zone, ce qui améliore la résilience et la tolérance aux pannes.
Justification : Utiliser un ALB permet de répartir les requêtes entre plusieurs zones de disponibilité, garantissant la haute disponibilité et une répartition équitable de la charge.

# 5. Amazon Route 53

Rôle : Fournir un routage DNS pour l'application et assurer la redondance multi-zone.
Avantages : Route 53 est fiable, compatible avec le routage multi-zone, et permet un basculement automatique en cas de panne, améliorant la résilience.
Justification : Route 53 améliore la résilience de l'application grâce à son routage intelligent, permettant une redirection automatique en cas de panne d'une zone de disponibilité.

# 6. Amazon Virtual Private Cloud (VPC)

Rôle : Fournir un réseau isolé pour toute l'infrastructure, permettant un contrôle de la connectivité entre les différents services et assurant la sécurité.
Avantages : Le VPC permet de créer des sous-réseaux (publics et privés) pour mieux gérer la sécurité et l'accès aux services. Les subnets privés garantissent que seuls les composants externes nécessaires sont exposés à Internet.
Justification : Le VPC garantit l'isolation de l'infrastructure et améliore la sécurité, permettant de contrôler finement les connexions réseau.

# 7. Subnets (Publics et Privés)
Rôle : Organiser l'infrastructure en segments de réseau, avec des subnets publics pour les services accessibles depuis Internet et des subnets privés pour les composants internes comme les conteneurs ECS.
Avantages : Les subnets publics hébergent l'ALB et les NAT Gateways, permettant un accès sécurisé à Internet pour certains services, tandis que les subnets privés hébergent les conteneurs, limitant leur exposition à l'Internet.
Justification : Cette organisation réduit l'exposition de l'infrastructure, augmentant ainsi la sécurité en limitant les points d'accès externes aux seules ressources nécessaires.

# 8. NAT Gateway
Rôle : Permettre aux conteneurs dans les subnets privés d'accéder à Internet pour des mises à jour ou des dépendances sans être exposés directement.
Avantages : La NAT Gateway autorise des connexions sortantes sécurisées pour les instances dans les subnets privés, empêchant tout accès entrant non autorisé depuis Internet.
Justification : Utiliser une NAT Gateway assure que les services en subnets privés peuvent accéder aux ressources en ligne pour les mises à jour ou dépendances, sans compromettre la sécurité du réseau.

## 2. Schéma de l'Infrastructure

![Schéma de l'Infrastructure](\Users\marye\OneDrive\Desktop\P_math\Campagne_marketing.png)

## 3. Justification des Choix Techniques


# 1. Simplicité et Gestion Managée

Amazon ECS avec Fargate : ECS Fargate est entièrement managé, ce qui permet de se concentrer sur le développement de l'application sans avoir à gérer des serveurs ou clusters. L’intégration avec les autres services AWS, comme ECR et ALB, simplifie les workflows DevOps.
Amazon ECR : ECR est une solution managée pour stocker et gérer les images Docker. Elle se connecte facilement avec ECS Fargate, éliminant la complexité de gestion des images et offrant un pipeline de déploiement fluide.
API Gateway : En tant que passerelle managée, API Gateway simplifie la gestion de l'accès au backend sans configuration complexe de serveur. Il gère également les authentifications et le throttling des requêtes.

# 2. Résilience

Multi-Zone de Disponibilité : L'application est déployée sur deux zones de disponibilité (AZ) pour assurer une haute disponibilité. En cas de défaillance d'une AZ, le trafic est automatiquement redirigé vers l'autre AZ grâce au Application Load Balancer (ALB).
Amazon Route 53 : Fournit un routage DNS fiable avec basculement automatique. En cas de panne de l'une des zones, Route 53 peut rediriger le trafic vers une zone disponible, améliorant la résilience de l'infrastructure.

# 3. Scalabilité

ECS avec Fargate : ECS Fargate permet un dimensionnement automatique des conteneurs en fonction de la charge. Cela signifie que les ressources peuvent s'ajuster dynamiquement pour répondre aux fluctuations de trafic, assurant une performance stable de l'application.
API Gateway : L'API Gateway est conçue pour s'adapter automatiquement aux pics de trafic, assurant que le backend reste accessible et performant sous forte charge.
Application Load Balancer (ALB) : ALB répartit automatiquement le trafic entre les instances dans plusieurs AZ, garantissant une scalabilité horizontale et une distribution efficace du trafic.

# 4. Sécurité

Amazon VPC (Virtual Private Cloud) : Le VPC isole les ressources de l'application dans un réseau privé, offrant un contrôle strict de la connectivité et des permissions réseau.
Subnets (Publics et Privés) : Les subnets publics hébergent l'ALB et la NAT Gateway, tandis que les subnets privés hébergent les instances ECS, limitant leur exposition directe à Internet et augmentant la sécurité.
NAT Gateway : Permet aux ressources dans les subnets privés de sortir vers Internet pour des mises à jour ou des dépendances, tout en évitant l'exposition aux connexions entrantes, renforçant ainsi la sécurité du réseau.
API Gateway : En tant que couche de protection pour le backend, API Gateway gère l'authentification, les autorisations, et limite le nombre de requêtes pour éviter les attaques par déni de service (DDoS).

## 4_Indiquez quel service AWS vous avez utilisé pour uploader vos images Docker et les rendre disponibles dans votre projet AWS
Pour rendre nos images Docker disponibles dans notre projet AWS, nous avons utilisé ExclaDiRaw. À la place d'Amazon ECR, nous avons installé les bibliothèques Amazon nécessaires dans ExclaDiRaw, qui nous permet d'héberger et de gérer nos images Docker de manière personnalisée. Ces images sont ensuite tirées et déployées sur Amazon ECS, en cohérence avec l'architecture de notre infrastructure.






