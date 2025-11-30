- create the file `docker-compose.yml` using the content of https://tm-en.doc.squashtest.com/v12/install-guide/installation/install-squash/docker-install.html
- `docker compose up`
- log in http://localhost:8090/squash

in another window
- ```sh
  docker exec -it tmp-squash-tm-1 sh
  cd /opt/squash-tm/plugin-files/squash-tm-premium/squash.tm.premium-12.0.0.RELEASE/plugins
  cp squash.tm.premium-12.0.0.RELEASE.jar /opt/squash-tm/plugins
  mkdir /opt/squash-tm/plugins/license
  exit
  ```
- `docker cp /mnt/c/Users/lmazure/Documents/docs/running/licence\ Squash\ TM/1751615543316_Ultimate.l4j tmp-squash-tm-1:/opt/squash-tm/plugins/license/squash-tm.lic`

in the first window
- type Ctrl-C
- `docker compose start`
- `docker compose logs --follow`
