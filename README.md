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


Pour implémenter ses fonctions azure qui remplaceront le backend actuel, l'entreprise à choisit de la créer directement sur le portail Azure aussi grâce au service Azure Functions afin qu'elle soit directement gérée dans le cloud. Pour se faire, il a fallut tout d'abord créer une application de fonction sur azure comme suit:

![image](https://github.com/WinnMBG/serverlessProject/assets/77972619/90da7bb4-ffe5-49b7-92e6-cd332a536092)


```
// Equivalent en cli
# az functionapp create --name myFunctionApp --resource-group myResourceGroup --consumption-plan-location your_location --runtime node --runtime-version 14 --functions-version 3 --disable-app-insights true
```

Ensuite, après avoir créer une application de fonction, on pourra créer nos futures fonctions cloud qui permettront de communiquer avec le base de données cosmo db qui a été crée juste avant:

![image](https://github.com/WinnMBG/serverlessProject/assets/77972619/1797e8d5-b088-4d93-a4fe-2ac90ebd0664)

```
// Equivalent en cli
# func init MyFunctionProj
cd MyFunctionProj
func new --name hello --template "HTTP trigger"

// Deploy to azure
# func azure functionapp publish myFunctionApp
```

Elle aura crée au final ... azure cloud functions dont une qui sera appelée spécialement lorsque notre message queue sera sollicité (soit par la publication d'un message ou bien le traitement d'un).

### Implement the service bus (message queue) to handle the growth of the traffic

ite, nous avons implémenter une message queue en utilisant le service bus Azure afin de pouvoir gérer lorsque l'application va avoir un nombre de commandes simultanément effectuées en même temps. Pour se faire, il a fallut d'abord créer un espace de nom : 

![image](https://github.com/WinnMBG/serverlessProject/assets/77972619/85891210-9bd7-4dc4-95c8-e2f933984245)

![image](https://github.com/WinnMBG/serverlessProject/assets/77972619/8938bd22-a34e-4cfd-ba7d-24ebb1a71ede)


Après avoir créer l'espace de service bus, il faut maintenant créer notre message queue :

![image](https://github.com/WinnMBG/serverlessProject/assets/77972619/c744f699-0fb3-4f32-b6f2-77fdefa1e574)

![image](https://github.com/WinnMBG/serverlessProject/assets/77972619/3a269fb9-543f-4baf-aa2c-c395cbc05c39)

Enfin pour gérer les commandes, on a créer une fonction azure chainée regroupant 2 fonctions:

- Une "HTTP Trigeer" qui va prendre en paramètre les onfos de la commandes et va les publier sur la message queue.
- Une "Service Bus Trigger" qui va, en fonction d'une nouvelle publication dans la queue, traiter le mesaage et le stocker dans la database comso DB.

  ![image](https://github.com/WinnMBG/serverlessProject/assets/77972619/7cb13d25-c0a6-481d-aaeb-efff5450d14c)


