# Travail pratique 2 - Kubernetes - Déploiement de WordPress dans K8S

Le but de se laboratoire est de monter un site Wordpress avec MySQL dans Kubernetes. Wordpress doit avoir 3 replica.

Le laboratoire s'effectue en groupe de 2. Vous devez créer vos objets dans un espace de nom qui contient la liste de vos matricules séparés par un tiret. Exemple "Tp2-1234567-2134567".

Le temps alloué est de 3 séances (29 avril, 2 mai et 6 mai). La date de la remise est indiqué sur LÉA.

Vous devez fournir (déposé sur LÉA) :

- Tous les fichiers YAML
- Un dessin qui illustre les noeuds, les services et les pods ainsi que leurs relations
- Une vidéo d'environ 10 min. (maximum 15 min.), lien ou source:
  - Explication de tous vos fichiers
  - Démonstration que le site fonctionne
  - Démonstration que vous êtes capable de faire une mise à l'échelle

À se rappeler :

- Vous aurez besoin d'un volume persistant pour la base de données
- Il faut passer les informations de la base de données à Wordpress et à MySQL par des variables d'environnement
- Si vous exposez votre MySQL avec un service, vous pouvez le contacter avec son adresse DNS

Pour que le montage du volume fonctionne bien pour MySQL, vous devez ajouter une propriété "securityContexte" dans la spec du pod ou du template et ajouter la clef "runAsUser: 2000"  :

```yaml
...
  spec:
    securityContext:
      runAsUser: 2000
```

Références à lire :

- https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/
- https://hub.docker.com/_/wordpress
- https://hub.docker.com/_/mysql
- Outils d'enregistrement : OBS, Screencast-o-matic, etc.


## Consignes :
- Ce travail pratique vaut pour 20 % de la note finale
- Ce travail est réalisé en équipe de 2 membres et seuls les membres de cette équipe y contribuent
- Tout le matériel est autorisé 
- Toute copie de code, de portion de code, d’algorithme ou de texte doit faire mention de sa source
- Tout constat de plagiat, tricherie ou fraude sera automatiquement déclaré à la Direction et les sanctions prévues seront appliquées.

## Évaluation :
|Item |Points  |
--- | --- | 
|Deployment / pod Wordpress|10|
|Service Wordpress|5|
|Deployment / pod MySQL|10|
|Volume MySQL|10|
|Service MySQL|5|
|Service MySQL - CusterIP|5|
|Site fonctionnel|30
|Mise à l'échelle|5
|Explication fichiers|10
|Diagramme|10|
|Total|100
