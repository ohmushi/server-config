# WHOAMI

Image de test utile pour la mise en place de [Traefik](https://traefik.io/traefik/) qui est un reverse proxy, je l'utilise notamment pour lier un sous-domaine à un service docker compose.

Image docker : 
- [traefik/whoami](https://hub.docker.com/r/traefik/whoami/)

Voir le fichier `docker-compose.yml` pour voir la configuration de base.
Ici ce que j'ai fait :
- Sous domaine DNS 'test.domain.com'
- Retirer la partie `ports`
- Partie `labels` contenant 
  - "traefik.enable=true"
  - router 'whoami' -> rule=Host(`test.domain.com`)"
  - loadbalancer port -> 80 (port exposé par le conteneur de whoami)
- Réseau externe 'traefik_network'


