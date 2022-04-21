# Exercice 10 -  Kubernetes -  Orchestrateur 

Cette exercice a pour but de vous faire explorer et découvrir kubernetes. 

## Pre-requis - Activer kubernetes dans docker desktop

- Allez dans la configuration de docker desktop
- Sélectionnez l'option kubernetes, cochez "Enable kubernetes" et faites "Apply and restart"

## Exercice 1 - Exploration

Vous pouvez exécutez les commandes kubectl dans un PowerShell autonome ou encore dans VSCode(vous pouvez ajouter le module K8S).

- Afficher la configuration de votre client kubectl
- Affichez les noeuds de votre cluster
- Affichez l'ensemble de namespaces de votre cluster
- Affichez l'ensemble des pods de votre cluster en y incluant tous les namespaces
- Réessayez les commandes précédentes en demandant un affichage plus complet avec un -o wide. Quelles informations obtenez-vous en plus ?
- Réessayez les commandes en spécifiant non plus une sortie étendue, mais plus json, puis yaml
- Affichez le contexte dans lequel vous êtes en faisant un "kubectl config view"
- Affichez la liste des contextes avec un "kubectl config get-contexts"
- Affichez le contexte courant avec un "kubectl config current-context"
- Listez vos pods et choisissez en un dans la liste. Repérez le nom et le namespace :
  - Faites un "get pod" suivi du nom du pod suivi de "--namespace=\<lenom du namespace>"
  - Modifiez l'affichage pour extraire les données avec le format "yaml" et redirigez la sortie vers un fichier nommé "exempleExtractionPod.yml"
  - Ouvrez ce fichier et faite-en une première lecture, nous reviendrons sur ce genre de fichiers par la suite


## Exercice 2 - Premier déploiement

Dans cet exercice, validez tout le temps que vous êtes bien si vous êtes dans le pod ou non en listant par exemple la liste des interfaces réseau. Vous noterez aussi que le prompt du shell est différent de celui de Powershell.

- Utilisez la commande run de kubectl afin de démarrer une image busybox
  - Appelez votre déployment (~ pods) "monbash"
  - Redirigez stdin et utilisez le tty avec l'option -it
  - Et dans le conteneur, exécutez la commande "sh sans argument en la passant après le séparateur de paramètres du kubectl et du pod (" -- ")
- Tapez quelques commandes afin de valider que vous êtes bien dans votre pod et sortez du pod
- Notez la commande qu'il vous suggère
- Listez les pods. Qu'observez-vous ?
- Retournez dans votre pod avec la commande "attach" précédemment fournie par lorsque vous êtes sorti du conteneur
- Sortez du pod
- Écrivez une commande qui permet d'exécuter la commande "sh" en mode intéractif dans le pod précédent et sortez du pod
- Supprimez votre précédent déployment avec la commande "kubectl delete pods monbash"
- Validez que le pod est bien en train d'être supprimé en listant les pods : notez l'idée que l'orchestrateur essaie de répondre à vos commandes avec une certaine latence

<details>
    <summary>Solution</summary>

```bash
kubectl run monbash --image=busybox -it -- sh
kubectl get pods -o wide
kubectl attach monbash-78c9b77fb9-wb27p -c monbash -i -t
kubectl exec monbash-78c9b77fb9-wb27p -it -- sh
kubectl delete pods monbash
```

</details>

## Exercice 3 - Nginx

### Exercice 3.1 - Nginx - Premier déploiement

- Créez un déploiement nommé "nginx" avec la commande : ```kubectl create deployment nginx --image=nginx```. L'image expose un site sur le port 80.
- Créez un déploiement nommé "navigateur" à partir de l'image "browsh/browsh" en mode intératif en ajoutant "--rm" afin de supprimer le déploiement automatiquement à la terminaison du pod
- Essayez d'accéder à votre site à partir de la machine locale. Que se passe-t-il ? Pourquoi ?
- Supprimez votre déploiement

<details>
    <summary>Solution</summary>

```bash
kubectl create deployment nginx --image=nginx
kubectl run navigateur --image=browsh/browsh -it --rm
kubectl delete deployment nginx
```

</details>

### Exercice 3.2 - Nginx - Liaison d'un port

- Créez un déploiement nommé "mon-nginx" avec l'image nginx
- Validez que le pod est bien créé en visualisant le pod et le déploiement
- Créez de nouveau réplica de votre pod en tapant la commande suivante : ```kubectl scale deployment --replicas 2 mon-nginx```
- Listez les pods et validez que vous en avez bien 2 maintenant
- Viréfier les services (kubectl get service), vous devrier avoir un service de type LoadBalancer avec le port 80
- Prenez un capture d'écran pour la déposer sur LÉA pour l'exercice 
- Exposez vos pods sur votre hôte local avec la commande suivante : ```kubectl expose deployments mon-nginx --port=80 --type=LoadBalancer```
- Testez votre site
- Supprimez les ressources créées

<details>
    <summary>Solution</summary>

```bash
kubectl create deployment mon-nginx --image=nginx
kubectl get pods -o wide
kubectl get deployments -o wide
kubectl scale deployment --replicas 2 mon-nginx
kubectl expose deployments mon-nginx --port=80 --type=LoadBalancer
kubectl get deployments
kubectl get service
kubectl delete service mon-nginx
kubectl delete deployment mon-nginx
```

</details>

# Attention, déposer votre capture d'écran sur LÉA
