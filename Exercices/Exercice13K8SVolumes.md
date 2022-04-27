# Exercice 13 - Kubernetes - Volumes

Dans kubernetes, la persistance est généré à partir des volumes persistants (PersistantVolume)

Pour utiliser un stockage persistant, vous devez créer un objet PersistantVolume (PV) pour réserver de l'espace. Cette opération est généralement faite par un administrateur système qui peut en créer avec différentes caractéristiques : vitesse, type de support, redondance, etc. Une fois le volume persistant créé, les développeurs font un objet de type PersistantVolumeClaim (PVC).

Dans notre configuration, nous avons créé un partage NFS sur le serveur maître qui se trouve à l'adresse 10.100.2.90. Le répertoire du partage est "/srv/exports"

Pour la suite des exercices, vos PV auront un seule PVC. Vos PV doivent pointer vers le répertoire suivant : /srv/exports/<numéro_matricule>/<nom_voulu_pour_partage>.

Exemples :

- /srv/exports/1234567/mon_site
- /srv/exports/1234567/ma_bd01

Voici un exemple de configuration de PV qui utilise notre serveur NFS et se lie au chemin jpd/nginx en réservant 200 Mo :

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: monpvnfs
spec:
  storageClassName: manual
  capacity:
    storage: 200Mi
  accessModes:
  - ReadWriteMany
  nfs:
    server: 10.100.2.90
    path: "/srv/exports/jpd/nginx"
```

Pour faire une réclamation de volume persistant, il suffit de spécifier le nom du PVC et du PV :

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mon-pvc-nfs
spec:
  storageClassName: manual
  accessModes:
  - ReadWriteMany
  resources:
    requests:
      storage: 100Mi
  volumeName: monpvnfs
```

Une fois le PVC créé, vous pouvez l'utiliser et le monter dans un conteneur. Ici on monte le PVC "mon-pvc-nfs" vers le chemin /usr/share/nginx/html.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-deploy-test-volume
spec:
  containers:
  - image: nginx
    name: nginx
    volumeMounts:
    - mountPath: /usr/share/nginx/html
      name: www
  volumes:
  - name: www
    persistentVolumeClaim:
      claimName: mon-pvc-nfs
```

## Exercice 0 - Préparation

Nous allons installer un client NFS afin de vous laisser accéder aux systèmes de fichiers et de pouvoir y créer vos répertoires (<numéro_matricule> et <nom_voulu_pour_partage>)

### Windows

Activez le client pour NFS en allant dans les fonctionnalités Windows :

[Activer le client pour NFS](img/windows_install_nfs_client.png)

Pour monter le partage dans le lecteur x (Utiliser cmd et non PowerShell) :

```bash
mount -o fileaccess=777 \\10.100.2.90:\srv\exports x:  
```

### Linux

Installez le package nfs-common.

Pour monter le partage sur le répertoire "/tmp/nfs" :

```bash
mount 10.100.2.90:/srv/exports /tmp/nfs
```

## Exercice 2 - Nginx

Le but est de créer un déploiement avec 3 replica d'une image nginx et de servir un fichier html de votre création.

- Créez un PV qui pointe sur le partage "/srv/exports/<numéro_matricule>/nginx_01" de 100Mo. Assurez-vous d'avoir créé le répertoire
- Créez un PVC pour le PV : quand vous créez votre PV et PVC ajoutez votre numéro de matricule dans le nom
- Créez un déploiement pour 3 réplica de l'image nginx qui monte le PVC créé précédemment vers le répertoire "/usr/share/nginx/html"
- Créez un service qui expose les réplica sur les noeuds
- Dans le répertoire pointé par le PV du partage, créez un fichier index.html avec un contenu HTML
- Validez que le tout fonctionne
- Faite une capture d'écran de la commande *kubectl get all* et déposer celle-ci sur LÉA comme preuve de réalisation de l'exercice.