# serverlessProject

# 1) Real-World scenario

Problème: 
ShopNet, une entreprise de vente de produit, est une entreprise qui connait un fort succès et donc connait une affluance de plus en plus forte dans leur magasin. A ce titre, elle souhaite donc exporter son commerce sur le net en lançant sa vente en ligne. Pensant que l'affluence serait faible, elle decide de créer un architecture très simple pour maintenir son site. (voir 2) 
Mais finalement, suite à une affluance plus qu'élevé en raison de nombreuses promotions sur ces articles, les temps d'arrêt dus à des pannes d'équipement constituent une préoccupation majeure. La maintenance non planifiée peut être coûteuse et perturber les calendriers de production.
ce non planifiée peut être coûteuse et perturber les calendriers de production. La problèmatique ici serait de savoir comment minimiser les temps d'attentes des commandes et gérer mieux cet afflux massif de clients.

# 2) Design Architecture

# Avant solution
![image](https://github.com/WinnMBG/serverlessProject/assets/77972619/d618814b-2a1a-4f0d-8401-4776e3602de6)

La solution utilisée de base consistait à prendre une machine virtuelle sur azure afin de pouvoir héberger le back end de l'application sur celle ci afin de ne pas avoir a s'occuper de la partie hardware d'une part et d'autre part car d'après les statisques réalisées sur plusieurs mois, elle s'est aperçu qu'il y avait une faible quantité de clients ( < 50000 / mois). 

# Après Solution
![image](https://github.com/WinnMBG/serverlessProject/assets/77972619/25248209-d544-4b7a-a4ff-f0ebac4c48f8)

La solution utilisée pour répondre à la problèmatique consiste à transformer l'ancien backend en plusieurs fonctions cloud azure afin de découpler les charges et de rendre l'application beaucoup plus flexible. De plus , en ce quii concerne les commandes, on peut voir qu'on va avoir recours à une message queue avec un certain quota pour justement s'adapter au flux grandissant de l'application.

## 3) Solution Implementation

### Implement the database

![image](https://github.com/WinnMBG/serverlessProject/assets/77972619/f576cb5e-3624-485b-8ff4-a5e2e649fa86)

Pour implémenter sa base de données, l'entreprise à choisit de la créer directement sur le portail Azure grâce au service Azure Cosmo DB afin qu'elle soit directement gérée dans le cloud. Cela aurait pu se faire aussi grâce à la CLI suivante :

```
// Login to the Azure 
# az login

// Create a Cosmo DB Account and Database
# az cosmosdb create --name products --resource-group myResourceGroup --kind GlobalDocumentDB --locations "France Central" --default-consistency-level Eventual --enable-automatic-failover true --enable-multiple-write-locations true

//Get connection string
# az cosmosdb keys list --name products --resource-group myResourceGroup
```

### Implement the azure functions


Pour implémenter sa base de données, l'entreprise à choisit de la créer directement sur le portail Azure grâce au service Azure Cosmo DB afin qu'elle soit directement gérée dans le cloud. Cela aurait pu se faire aussi grâce à la CLI suivante :

```
// Login to the Azure 
# az login

// Create a Cosmo DB Account and Database
# az cosmosdb create --name products --resource-group myResourceGroup --kind GlobalDocumentDB --locations "France Central" --default-consistency-level Eventual --enable-automatic-failover true --enable-multiple-write-locations true

//Get connection string
# az cosmosdb keys list --name products --resource-group myResourceGroup
```

