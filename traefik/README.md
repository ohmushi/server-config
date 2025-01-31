# TRAEFIK

La configuration de base pour un nouveau projet est dans le projet [whoami](./whoami/compose.yml).

Il faut **dans le compose.yml du projet** :
- Ajouter un sous domaine DNS chez le cloud provider ('sous-domaine.domain.com')
- Retirer la partie `ports` (xx:yy)
- Ajouter la partie `labels` contenant
  - "traefik.enable=true"
  - "traefik.http.routers.{NOM_ROUTEUR}.rule=Host(`sous-domaine.domain.com`)"
  - Pour du **httpS** :
    - "traefik.http.routers.{NOM_ROUTEUR}.tls=true"
    - "traefik.http.routers.{NOM_ROUTEUR}.tls.certresolver=resolver"
  - "traefik.http.services.{SERVICE}.loadbalancer.server.port={XX}"
  - Pour poller la dernière version de l'image (par son tag) :
    - "com.centurylinklabs.watchtower.enable=true"
- Préciser le réseau, ici 'traefik_network', et le déclarer, en tant qu'externe
- Redemarer le compose

>*NOM_ROUTEUR* est le "routeur" virtuel que l'on veut configurer, ici on en créé un qui se nomme "NOM_ROUTEUR" et qui possede la règle de cibler notre domaine. Dans mon cas il est plutôt utile de créer un routeur par projet, mais ça peut être utile si on a besoin de règles communes pour plusieurs projets d'avoir un même routeur.
>
>*SERVICE* est le nom du service déclaré dans traefik, il peut être remplacé par ce que l'on veut. Je recommande d'avoir le service identique au routeur pour moins de confusion.
>
>*XX* est le port exposé par le conteneur (lire la documentation de l'image en question).
>
> Le poll de la dernière version de l'image est effectuée grâce à l'outil [WatchTower](https://containrrr.dev/watchtower). Il faut donc préciser l'image avec le label qui est suseptible de changer par exemple `latest` ou `main`.
> /!\ Attention, cela peut être dangereux si la version récupérée n'est pas stable.

exemple pour une app APP:

```yml
compose.yml


services:
  APP:
    image: APP-image:latest
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.APP.rule=Host(`APP.domain.com`)"
      - "traefik.http.routers.APP.tls=true"
      - "traefik.http.routers.APP.tls.certresolver=resolver"
      - "traefik.http.services.APP.loadbalancer.server.port=????"
      - "com.centurylinklabs.watchtower.enable=true"

networks:
  traefik_network:
    external: true
```
> Changer **APP** par le nom du projet

 
On peut voir la configuration de Traefik dans le fichier `traefik.yml`, il déclare simplement les entry points, qui sont les ports sur lesquels Traefik écoute pour recevoir le trafic entrant et le resolver pour le https. Ils peuvent être configurés dans ce fichier.
Dans mon cas pour l'instant je ne déclare que l'entrypoint web qui gère le http (port 80) qui est redirigé automatiquement vers le websecure pour le https (port 443), et un resolver qui utilise mes certificats [Let's Encrypt](https://letsencrypt.org/fr/).
