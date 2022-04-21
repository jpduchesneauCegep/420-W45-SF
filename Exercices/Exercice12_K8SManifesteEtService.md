# Kubernetes - Exercice 3 -  Manisfeste et Services

Dans les exercices qui le permettent, assurez-vous de toujours indiquer un espace de nom qui correspond à votre numéro de matricule. 

## Partie 1 - Créez plusieurs déploiements - Sélection

- Créez plusieurs déploiements avec les caractéristiques suivantes (un réplica) :
  - dep1.yaml :
    - image : nginxdemos/hello
    - replica : 1
    - labels :
      - tier : frontend
      - env : unit
  - dep2.yaml :
    - image : nginxdemos/hello
    - replica : 1
    - labels :
      - tier : backend
      - env : unit
  - dep3.yaml :
    - image : nginxdemos/hello
    - replica : 1
    - labels :
      - tier : frontend
      - env : fonc
  - dep4.yaml :
    - image : nginxdemos/hello
    - replica : 1
    - labels :
      - tier : backend
      - env : fonc
  - dep5.yaml :
    - image : nginxdemos/hello
    - replica : 1
    - labels :
      - tier : frontend
      - env : accept
  - dep6.yaml :
    - image : nginxdemos/hello
    - replica : 1
    - labels :
      - tier : backend
      - env : accept
  - Faites des requêtes à partir de kubectl afin d'afficher :
    - tous les pods du tier backend appartenant à l'environnement fonc
    - tous les pods du tier frontend appartenant n'appartenant pas à l'environnement fonc
    - tous les pods du tier backend appartenant aux environnements (fonc, unit)
    - tous les pods du tier backend
    - tous les pods de l'environnement unit
- Supprimez tous vos déploiements

## Partie 2 - Réplica

- Créez un déploiement avec les caractéristiques suivantes :
  - rep1.yaml :
    - réplica : 1
    - image : nginx-demos/hello
    - labels :
      - tier : frontend
      - env : unit
- Affichez les pods, vous devriez voir une seule réplique de votre pod
- Rentrez en mode intéractif dans un nouveau pod qui vous permet de faire des lignes de commandes comme l'image busybox.
- Avec wget / curl, affichez la page web de votre précédent déploiement avec l'adresse IP du pod
- À partir d'une autre fenêtre de commande, supprimez le pod (pas le déploiement !) qui contient votre nginx-demos/hello.
- Listez les pods et regardez ce qui se passe
- À partir de la précédente fenêtre faites de nouveau des requêtes au service web. Que s'est-il passé ? (une différence dans l'adresse IP ?)
- Faites une mise à l'échelle (scale) pour passer d'un réplica à 10.
- Affichez les pods avec une vue enrichie (wide)
- Dans le pod qui contient votre shell, essayez différentes adresses
- Amusez vous à supprimer des pods pour voir ce qui se passe

## Partie 3 - Service

- Créez le déploiement d'un service qui vous permet d'accéder à vos 10 réplica :
  - service :
    - nom : mon-site
    - port : 80
    - ne spécifiez pas de port pour le node et ne créez donc pas de propriété nodePort
    - type : nodePort
    - matchLabels : voir exercice précédent

- **Attention** faites des captures d'écran des étapes de la partie 3 et déposer celles-ci sur LÉA dans travaux.
- À partir d'un pod qui a un shell, essayez la commande suivante :

```bash
watch -n 1 wget -qO - mon-site | grep -o -E '[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+'
```

- Cherchez le port d'exposition sur le noeud en affichant la liste des services
- À partir de votre VM, naviguez l'adresse IP suivie du port

## Partie 4 - Livre d'or

- Suivez les instructions du site https://kubernetes.io/docs/tutorials/stateless-application/guestbook/ afin de créer un livre d'or avec un site PHP et le serveur de cache redis
