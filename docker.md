# Start SquashTM
- create the file `docker-compose.yml` using the content of https://tm-en.doc.squashtest.com/v13/install-guide/installation/install-squash/docker-install.html#quick-start
- `docker compose up`
- log in http://localhost:8090/squash/login (admin/admin)

# install some plugins and the license file
in another window
- ```sh
  docker exec -it squashtm sh
  cd /opt/squash-tm/plugin-files/squash-tm-premium/squash.tm.premium-13.0.1.RELEASE/plugins
  cp squash.tm.premium-13.0.1.RELEASE.jar /opt/squash-tm/plugins
  cd /opt/squash-tm/plugin-files/api-rest-admin/api.rest.admin-13.0.0.RELEASE/plugins
  cp api.rest.admin-13.0.0.RELEASE.jar /opt/squash-tm/plugins
  mkdir /opt/squash-tm/plugins/license
  exit
  ```
- `docker cp /mnt/c/Users/lmazure/Documents/docs/running/licence\ Squash\ TM/1751615543316_Ultimate.l4j squashtm:/opt/squash-tm/plugins/license/squash-tm.lic`

#  Set the secret for JWT tokens
- ```sh
  docker exec -it squashtm sh
  cd /opt/squash-tm/conf
  vi squash.tm.cfg.properties
  ```
- uncomment and set the value for the line `#squash.rest-api.jwt.secret =` (this must really be a real base-64 encoded long string, e.g. using https://www.base64encode.org/)
- save the file, exit `vi`
- exit the shell

other option, if you have a copy of the `squash.tm.cfg.properties` file (corresponding to the 13.0.1 release!) in the current folder:
```sh
docker cp squash.tm.cfg.properties squashtm://opt/squash-tm/conf/squash.tm.cfg.properties
```

# Restart SquashTM
in the first window
- type Ctrl-C
- ```sh
  docker compose start
  docker compose logs --follow
  ```

# Login SquashTM
- log on http://localhost:8090/squash/login
