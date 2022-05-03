# Exercice 14 - Kubernetes - Mise en place d'un cluster local  (Facultatif)


Dans cet exercice, nous allons mettre en place un cluster Kubernetes avec Microk8s

Voici ce qu'en dis Canonical :

    MicroK8s est un package unique qui permet aux développeurs de faire fonctionner un système Kubernetes complet, conforme et sécurisé en moins de 60 secondes. Conçu pour le développement local, les appareils IoT, le CI/CD et l'utilisation à la périphérie, MicroK8s est disponible sous forme de snap et disponible sur Linux, Windows et Mac.


## Installation 
- Si ce n'est pas déjà fait, installé kubectl

```bash
sudo snap install kubectl --classic
# Sortie :
kubectl 1.23.6 par Canonical✓ installé
```
- Procédons maintenant à l'installation de MicroK8s
```bash
sudo snap install microk8s --classic
```

Se donner les droits d'exécuter microk8s sans devoir toujours passer par route :

```bash
sudo usermod -a -G microk8s $USER
# Pour prendre en considération les modifications, reconnectez-vous :
su $user

```

### Activer les addons

Par défaut, nous obtenons un Kubernetes de base. Les services supplémentaires, tels que le tableau de bord, core-dns ou le stockage local peuvent être activés en exécutant la commande microk8s suivante :

```

sudo microk8s enable dns dashboard storage
```
Par la suite, la commande microk8s status vas nous permettre de voir les nouveau services installé :

```
sudo microk8s status
```

### Accéder au tableau de bord Kubernetes

Maintenant que nous avons activé les addons dns et dashboard, nous pouvons accéder au tableau de bord disponible. Pour ce faire, nous vérifions d'abord la progression du déploiement de nos addons avec microk8s kubectl get all --all-namespaces. Cela ne prend que quelques minutes pour obtenir tous les pods dans l'état "Running"

```
microk8s kubectl get all --all-namespaces
```
**Astuce** : Pour ne pas avoir à taper à chaque fois la commande microk8s kubectl, créez-vous un alias. Voici un exemple :

```bash
alias mkubectl="microk8s kubectl"
```
    Si vous voulez rendre l'alias permanent, insérer le dans la section alias du fichier $USER/.bashrc

### Kubernetes dashboard

Trouvez l'adresse IP  dans la sortie ci-dessus, le service kubernetes-dashboard dans l'espace de noms kube-system et écoute sur le port TCP 443. Le ClusterIP est attribué de manière aléatoire, donc si vous suivez ces étapes sur votre hôte, assurez-vous de vérifier l'adresse IP que vous avez obtenue. Dirigez votre navigateur vers l'adresse dans mon cas https://10.152.183.64:443 et vous verrez l'interface utilisateur du tableau de bord de Kubernetes. Pour accéder au tableau de bord, utilisez le jeton par défaut créer  avec :

```bash
token=$(microk8s kubectl -n kube-system get secret | grep default-token | cut -d " " -f1)
microk8s kubectl -n kube-system describe secret $token
```
et récupérer par un copier-coller

![Image du Dashboard](images/dashboard.png)

## Hébergez votre premier service dans Kubernetes

Nous commençons par créer un déploiement microbot avec deux pods via le cli kubectl :




```bash
microk8s kubectl create deployment microbot --image=dontrebootme/microbot:v1
microk8s kubectl scale deployment microbot --replicas=2
```

Pour exposer notre déploiement, nous devons créer un service :

```bash
microk8s kubectl expose deployment microbot --type=NodePort --port=80 --name=microbot-service
```
Après quelques minutes, notre cluster ressemble à ceci :

![État de microk8s](images\scale.png)


Nous avons maintenant deux pods microbot et le service/microbot-service est le dernier de la liste des services. Notre service a un ClusterIP par lequel nous pouvons y accéder. Remarquez, cependant, que notre service est de type NodePort. Cela signifie que notre déploiement est également disponible sur un port de la machine hôte ; ce port est choisi au hasard et dans ce cas, il s'agit de 31065. Tout ce que nous devons faire est de pointer notre navigateur vers http://localhost:31065.

![Site Web](images/microbot.png)

## Notoyer votre cluster 

Suprimmer le déploiement  

```bash
microk8s kubectl delete deployment microbot
microk8s kubectl delete service microbot-service

# Vérifier l'état du cluster
microk8s kubectl get all
```

## Comandes de intégrées
Il y a beaucoup de commandes qui sont livrées avec les MicroK8s. Nous n'avons vu que les commandes essentielles dans ce tutoriel. Explorez les autres à votre convenance :

* microk8s status : Fournit une vue d'ensemble de l'état de MicroK8s (en cours d'exécution / non en cours d'exécution) ainsi que l'ensemble des modules complémentaires activés.
* microk8s enable : Active un addon
* microk8s disable : Désactive un addon
* microk8s kubectl : Interaction avec kubernetes
* microk8s config : Affiche le fichier de configuration de Kubernetes
* microk8s istioctl : Interagit avec les services istio ; nécessite que l'addon istio soit activé
* microk8s inspect : Effectue une inspection rapide de l'installation de MicroK8s
* microk8s reset : Réinitialise l'infrastructure à un état propre
* microk8s stop : Arrête tous les services kubernetes
* microk8s start : Démarre MicroK8s après l'avoir arrêté


## Remise
- Faite une capture d'écran de la commande *kubectl get all* et déposer celle-ci sur LÉA comme preuve de réalisation de l'exercice.

## Source :
- Canonical :

    https://ubuntu.com/tutorials/install-a-local-kubernetes-with-microk8s?&_ga=2.77536341.1398588099.1651518788-225111492.1649513457&_gac=1.187168346.1651258531.Cj0KCQjwma6TBhDIARIsAOKuANwIY5p5S9YmtP6v_DlmiFLT-S_l-T2RzLwomWr86ylvziTo34caLikaAkt2EALw_wcB#5-host-your-first-service-in-kubernetes