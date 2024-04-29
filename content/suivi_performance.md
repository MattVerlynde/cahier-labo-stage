---
layout: page
title: III. Mise en place d'outils de suivi de performance
menu:
  main:
    weight: 3
mermaid: true
bibFile: content/bibliography.json
toc: True
---

Cette page résume la procédure d'installation du pipeline de lecture, écriture, sauvegarde et visualisation des données internes du hardware. Celle-ci est basée sur le pipeline **Telegraf-InfluxDB-Grafana** (TIG). Elle s'inspire largement sur le tutoriel accessible en ligne à [cette adresse](https://domopi.eu/tig-le-trio-telegraf-influxdb-grafana-pour-surveiller-vos-equipements/).
Cette page présente égamement la procédure d'interrogation de la base de données **InfluxDB** après exécution d'un programme python, via l'exécution d'un script bash.

<!--more-->

## Présentation du pipeline


{{<mermaid>}}
flowchart LR
    a1[Container metrics] --> b{{Telegraf}}
    a2[CPU metrics] --> b{{Telegraf}}
    a3[GPU metrics] --> b{{Telegraf}}
    subgraph A[Docker containers]
    b{{Telegraf}} --> c[(InfluxDB)]
    c[(InfluxDB)] --> d((Grafana))
    end
{{</mermaid>}}

Le plugin Telegraf, produit par InfluxDB permet la collection des données du hardware de l'ordinateur en temps réel, ainsi que son formatage. 
InfluxDB permet le stockage de ces données en séries temporelles, et constitue la base de donnée qui est interrogée au sein du pipeleine; Grafana est un outil de visualisation et d'analyse des données lues la base de données de InfluxDB.

## Téléchargement de TIG

{{<warning>}}
Ce tutoriel se concentre sur l'installation de pipeline via docker, et nécessite donc son installation préalable.
{{</warning>}}

Commençons par télécharger les images docker de trois plugins constituant le pipeline.

```shell
docker pull telegraf
docker pull influxdb
docker pull grafana/grafana-oss
```
Nous allons utiliser la commande `docker compose` afin de réaliser notre pipeline. Construisons alors le fichier de construction `docker-compose.yml`. Configurons le port d'entrée de Grafana selon notre choix. Dans cet exemple, nous avons choisi le port `9090`.

```yaml
version: "3.8"
services:
  influxdb:
    image: influxdb
    container_name: influxdb
    restart: always
    ports:
      - 8086:8086
    hostname: influxdb
    environment:
      INFLUX_DB: $INFLUX_DB  # nom de la base de données créée à l'initialisation d'InfluxDB
      INFLUXDB_USER: $INFLUXDB_USER  # nom de l'utilisateur pour gérer cette base de données
      INFLUXDB_USER_PASSWORD: $INFLUXDB_USER_PASSWORD  # mot de passe de l'utilisateur pour gérer cette base de données
      DOCKER_INFLUXDB_INIT_MODE: $DOCKER_INFLUXDB_INIT_MODE
      DOCKER_INFLUXDB_INIT_USERNAME: $DOCKER_INFLUXDB_INIT_USERNAME
      DOCKER_INFLUXDB_INIT_PASSWORD: $DOCKER_INFLUXDB_INIT_PASSWORD
      DOCKER_INFLUXDB_INIT_ORG: $DOCKER_INFLUXDB_INIT_ORG
      DOCKER_INFLUXDB_INIT_BUCKET: $DOCKER_INFLUXDB_INIT_BUCKET
      DOCKER_INFLUXDB_INIT_RETENTION: $DOCKER_INFLUXDB_INIT_RETENTION
      DOCKER_INFLUXDB_INIT_ADMIN_TOKEN: $DOCKER_INFLUXDB_INIT_ADMIN_TOKEN
    volumes:
      - ./influxdb:/var/lib/influxdb  # volume pour stocker la base de données InfluxDB

  telegraf:
    image: telegraf
    depends_on:
      - influxdb  # indique que le service influxdb est nécessaire
    container_name: telegraf
    restart: always
    links:
      - influxdb:influxdb
    tty: true
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock  # nécessaire pour remonter les données du démon Docker
      - ./telegraf/telegraf.conf:/etc/telegraf/telegraf.conf  # fichier de configuration de Telegraf
      - /:/host:ro # nécessaire pour remonter les données de l'host (processes, threads...)

  grafana:
    image: grafana/grafana-oss
    depends_on:
      - influxdb  # indique que le service influxdb est nécessaire
    container_name: grafana
    restart: always
    ports:
      - 9090:3000  # port pour accéder à l'interface web de Grafana
    links:
      - influxdb:influxdb
    environment:
      GF_INSTALL_PLUGINS: "grafana-clock-panel,\
                          grafana-influxdb-08-datasource,\
                          grafana-kairosdb-datasource,\
                          grafana-piechart-panel,\
                          grafana-simple-json-datasource,\
                          grafana-worldmap-panel"
      GF_SECURITY_ADMIN_USER: $GF_SECURITY_ADMIN_USER  # nom de l'utilisateur créé par défaut pour accéder à Grafana
      GF_SECURITY_ADMIN_PASSWORD: $GF_SECURITY_ADMIN_PASSWORD  # mot de passe de l'utilisateur créé par défaut pour accéder à Grafana
    volumes:
      - ./grafana:/var/lib/grafana-oss
```

{{<info>}}
Ce fichier est disponible machine `lst-pa33` au chemin: `verlyndem/static/config_suivi/tig/docker-compose.yml`.
{{</info>}}


Ce fichier est construit en dépendance d'un fichier de contenance des variables d'environnement. Construisonsce fichier `.env` avec les valeurs de ces variables dans le même dossier.

```yaml
INFLUX_DB=telegraf
INFLUXDB_USER=telegraf_user
INFLUXDB_USER_PASSWORD=telegraf_password
GF_SECURITY_ADMIN_USER=grafana_user
GF_SECURITY_ADMIN_PASSWORD=grafana_password
DOCKER_INFLUXDB_INIT_MODE=setup
DOCKER_INFLUXDB_INIT_USERNAME=telegraf_user
DOCKER_INFLUXDB_INIT_PASSWORD=telegraf_password
DOCKER_INFLUXDB_INIT_ORG=telegraf_org
DOCKER_INFLUXDB_INIT_BUCKET=telegraf_bucket
DOCKER_INFLUXDB_INIT_RETENTION=365d
DOCKER_INFLUXDB_INIT_ADMIN_TOKEN=telegraf_token
```

{{<info>}}
Ce fichier est disponible machine `lst-pa33` au chemin: `verlyndem/static/config_suivi/tig/.env`.
{{</info>}}

Nous allons maintenant configurer les paramètres de Telegraf. Dans le shell, exécutez la commane suivante.

```shell
mkdir telegraf
docker run --rm telegraf telegraf config > telegraf/telegraf.conf
```

Cette commande nous a permi de créer un fichier de configuration par défaut de Telegraf, que nous alons alors modifier pour notre projet.

```squidconf

# Configuration for telegraf agent
[agent]
  
  [...]

  ## Override default hostname, if empty use os.Hostname()
  hostname = "telegraf"
  ## If set to true, do no set the "host" tag in the telegraf agent.
  omit_hostname = false

[...]

###############################################################################
#                            OUTPUT PLUGINS                                   #
###############################################################################


# # Configuration for sending metrics to InfluxDB 2.0
[[outputs.influxdb_v2]]
#   ## The URLs of the InfluxDB cluster nodes.
#   ##
#   ## Multiple URLs can be specified for a single cluster, only ONE of the
#   ## urls will be written to each interval.
#   ##   ex: urls = ["https://us-west-2-1.aws.cloud2.influxdata.com"]
   urls = ["http://influxdb:8086"]
#
#   ## Token for authentication.
   token = "telegraf_token"
#
#   ## Organization is the name of the organization you wish to write to.
   organization = "telegraf_org"
#
#   ## Destination bucket to write into.
   bucket = "telegraf_bucket"

   [...]

[...]

[[outputs.influxdb]]
#   ## The full HTTP or UDP URL for your InfluxDB instance.
#   ##
#   ## Multiple URLs can be specified for a single cluster, only ONE of the
#   ## urls will be written to each interval.
#   # urls = ["unix:///var/run/influxdb.sock"]
#   # urls = ["udp://127.0.0.1:8089"]
#   # urls = ["http://127.0.0.1:8086"]
   urls = ["http://influxdb:8086"]

   [...]

#   ## HTTP Basic Auth
   username = "telegraf_user"
   password = "telegraf_password"

   [...]

[...]

[[inputs.docker]]
#   ## Docker Endpoint
#   ##   To use TCP, set endpoint = "tcp://[ip]:[port]"
#   ##   To use environment variables (ie, docker-machine), set endpoint = "ENV"
   endpoint = "unix:///var/run/docker.sock"

   [...]

[...]

# # Monitor process cpu and memory usage
[[inputs.procstat]]
   pattern = ".*"
   fieldpass = ["cpu_time_system", "cpu_time_user", "cpu_usage", "memory_*", "num_threads", "*pid"]
   pid_finder = "native"
   pid_tag = true

   [...]

[...]

# # Read metrics about temperature
[[inputs.temp]]

[...]

```
Effectuez alors les modifications suivantes :
* Dans `[agent]`, la variable `hostname` comme la variable d'environnement `INFLUX_DB`, ici `"telegraf"`

* Décommenter `[[outputs.influxdb_v2]]`, la variable `urls` comme `["http://influxdb:8086"]`, et les variables `token`, `organization` et `bucket` comme `DOCKER_INFLUXDB_INIT_ADMIN_TOKEN`, `DOCKER_INFLUXDB_INIT_ORG` et `DOCKER_INFLUXDB_INIT_BUCKET`, ici de valeur `"telegraf_token"`, `"telegraf_org"` et `"telegraf_bucket"`.

* Décommenter `[[outputs.influxdb]]`, la variable `urls` comme `["http://influxdb:8086"]`, et les variables `username` et `password` comme `DOCKER_INFLUXDB_INIT_USERNAME` et `DOCKER_INFLUXDB_INIT_PASSWORD`, ici de valeur `"telegraf_user"` et `"telegraf_password"`.

* Décommenter `[[inputs.docker]]`, la variable `endpoint` comme `"unix:///var/run/docker.sock"`.

* Décommenter `[[inputs.procstat]]`, définir la variable `pattern` comme `".*"` pour récupérer les données de tous les processes, `fieldpass` comme les variables que l'on souhaite récupérer, ici `["cpu_time_system", "cpu_time_user", "cpu_usage", "memory_*", "num_threads", "*pid"]`, `pid_finder` comme `"native"` afin d'accéder aux données de l'hôte hors du container, et `pid_tag` comme `true` afin de conserver l'identifiant des processes,

* Décommenter `[[inputs.temp]]` pour obtenir les données de températures du CPU et de la NVME.

{{<info>}}
Ce fichier est disponible machine `lst-pa33` au chemin: `verlyndem/static/config_suivi/tig/telegraf/telegraf.conf`.
{{</info>}}

Nous pouvons alors ensuite créer les conteneurs.

```shell
docker compose up -d
```

Vérifiez que les conteneurs ont bien été créés avec la commande suivante:

```shell
docker ps
```

Si nous avons bien créé les conteneurs, vous pouvons accéder à l'interface de Grafana sur le port choisi, ici `http://localhost:9090`.

{{<info>}}
Nous pouvons également accéder à l'interface de InfluxDB via le port configuré dans le fichier `docker-compose.yml`, ici à l'adresse `http://localhost:8086`.
{{</info>}}

Nous pouvons alors passer à la configuration de Grafana.

## Configuration de Grafana

Sur la page d'accueil de Grafana, connectons nous avec l'identifiant et le mot de passe configuré dans le fichier `.env`. Dans notre exemple, nous avons `grafana_user` et `grafana_password`.

![Page d'accueil de Grafana](/config_suivi/screenshots_config/grafana_welcome.png)

Configurons la source des données dans l'onger Data source, et choisissons InfluxDB comme tyope de source.

![Sélection de la source des données (InfluxDB)](/config_suivi/screenshots_config/grafana_select_influx.png)

Configurons maintenant la source des données avec le port de InfluxDB, et choisissons `FLUX` comme langage d'interrogation de la base.

![Sélection du nom et du port](/config_suivi/screenshots_config/grafana_set_datasource1.png)

Ajoutons ensuite les identifiants de connexion à la base de données avec ceux choisis dans le fichier `.env`.

![Sélection ajout des identifiant](/config_suivi/screenshots_config/grafana_set_datasource2.png)

Importons ensuite un dashboard de visualisation des données compatible avec nos configurations. Nous pouvons choisir un dashboard compatible en ligne, mais le dashboard correspondant à l'identifiant `15650` convient à notre exemple.

![Importation du dashboard](/config_suivi/screenshots_config/grafana_import_dashb1.png)

Choisissons la source que nous avons configuré avant d'importer.

![Sélection de la source](/config_suivi/screenshots_config/grafana_import_dashb2.png)

Enfin, choisissons les paramètres du dashboard correspondant à nos données, ici le nom du bucket que nous avons configuré.

![Sélection des paramètres](/config_suivi/screenshots_config/grafana_import_dashb3.png)

Nous pouvons ensuite modifier plus finement les affichages du dashboard selon nos objectifs, en modifiant leurs paramètres ou les queries associées (en respectant le langage d'écriture Flux).

Une autre possibilité pour importer un dashboard est d'importer le fichier associé au forma `.json`. Le fichier configuré que nous pouvons importer est disponible sur la machine `lst-pa33` au chemin: `verlyndem/static/config_suivi/grafana_dashboard_template.json`.

Le dashboard créé lors de notre projet est accessible sur [ce lien](http://localhost:9090/d/edh1jtjp0b4lcb/38860f63-1c6f-5143-a2a7-cfc431003966?orgId=1&var-datasource=edh4qbg5qdm9sb&var-bucket=telegraf_bucket&var-host=telegraf&var-inter=1s&var-cpu=All&var-disk=All&var-interface=All&var-PID=All&var-Process=python).

## Interrogation de la base

Afin d'interrogée la base de données **InfluxDB** créée précédemment, nous utilisons un script bash exécute un fichier python présenté en argument, puis interroge la base de donnée afin de récolter les données enregistrée sur la période d'exécution du fichier python.

Ce fichier est disponible sur la machine `lst-pa33` au chemin: `verlyndem/static/config_suivi/get_metrics.sh`.

L'exécution de ce fichier est réalisée selon la commande suivante :

```bash
bash get_metrics.sh -f [python.file] (-p) (-P [pid])
```
* le drapeau `-f` est obligatoire et précède le nom du fichier python à exécuter
* le drapeau `-P` est facultatif : si renseigné, les données récoltées seront celles associées au process dont l'identifiant est disponible dans un fichier `python_process.pid` sur la période d'exécution du fichier python
* le drapeau `-p` est facultatif : si renseigné, les données récoltées seront celles associées au process dont l'identifiant est renseigné comme argument dans la commande,
* si les drapeaux `-p` et `-P` ne sont pas renseignés, l'ensemble des données de la base sur la période d'exécution du fichier python sera récoltée.

Les données récoltées sont enregistrées dans un fichier `metrics_output`.

Le fichier de récolte de donnée s'organise ainsi :

```bash
#!/bin/bash

#Récolte des arguments en entrée
while getopts 'f:p:P:' OPTION; do
  case "$OPTION" in
    f)
      name_file="$OPTARG"
      ;;
    P) 
      by_pid=true
      get_pid=true
      ;;
    p) 
      by_pid=true
      get_pid=false
      npid="$OPTARG"
      ;;
    \?)
      echo "Invalid option: $OPTARG" 1>&2
      exit 1
      ;;
  esac
done
: ${name_file:?Missing -f}

#Relevé du premier temps avant exécution du fichier python
t1=$(date -u +%Y-%m-%dT%T.%9NZ)
echo "*************************************************"
echo "Time start: $t1"
echo "*************************************************"
echo "Running python script: $name_file"
echo "*************************************************"

#Exécution du fichier python
python3 $name_file

#Relevé du second temps après exécution du fichier python
t2=$(date -u +%Y-%m-%dT%T.%9NZ)

echo "*************************************************"
echo "Time stop: $t2"
echo "*************************************************"


if [ "$by_pid" = true ]; then
  if [ "$get_pid" = true ]; then
    # Récolte du pid si non renseigné en entrée 
    npid=$(cat python_process.pid)
  fi
  echo "Process ID: ${npid}"
  # Construction de la query sur le process
  query="data=from(bucket: \"telegraf_bucket\")
    |> range(start: ${t1}, stop: ${t2})
    |> filter(fn: (r) => r[\"_measurement\"] == \"procstat\")
    |> filter(fn: (r) => r[\"pid\"] == \"${npid}\")
    |> aggregateWindow(every: 1s, fn: mean, createEmpty: false)
    |> yield(name: \"mean\")"
else
  # Construction de la query sur l'ensemble des données
  query="data=from(bucket: \"telegraf_bucket\")
    |> range(start: ${t1}, stop: ${t2})
    |> aggregateWindow(every: 1s, fn: mean, createEmpty: false)
    |> yield(name: \"mean\")"
fi

# Ecriture de la query
echo $query > query

# Copie de la query dans le conteneur d'InfluxDB
sudo docker cp query influxdb:/query

# Exécution de la query dans le conteneur, et enregistrement de la sortie dans metrics_output
sudo docker exec -it influxdb sh -c 'influx query -f query -r' > metrics_output

echo "*************************************************"
echo "File metrics_output created"
echo "*************************************************"
head metrics_output
echo "*************************************************"
```

Format de sortie dans `metrics_output` :

```text
#group,false,false,true,true,false,false,true,true,true,true,true,true

#datatype,string,long,dateTime:RFC3339,dateTime:RFC3339,dateTime:RFC3339,double,string,string,string,string,string,string

#default,mean,,,,,,,,,,,

,result,table,_start,_stop,_time,_value,_field,_measurement,host,pattern,pid,process_name

,,0,2024-03-27T16:15:21.073488201Z,2024-03-27T16:15:57.890192557Z,2024-03-27T16:15:31Z,6.46,cpu_time_system,procstat,telegraf,.*,1601463,python3

,,0,2024-03-27T16:15:21.073488201Z,2024-03-27T16:15:57.890192557Z,2024-03-27T16:15:41Z,6.87,cpu_time_system,procstat,telegraf,.*,1601463,python3

,,0,2024-03-27T16:15:21.073488201Z,2024-03-27T16:15:57.890192557Z,2024-03-27T16:15:51Z,7.46,cpu_time_system,procstat,telegraf,.*,1601463,python3

[...]
```

## Suivre les performances énergétiques

```bash
sudo docker run -d \
  --name homeassistant \
  --privileged \
  --restart=unless-stopped \
  -e TZ=Europe/Paris \
  -v /home/verlyndem/homeassistant:/config \
  -v /run/dbus:/run/dbus:ro \
  --network=host \
  ghcr.io/home-assistant/home-assistant:stable
```

```bash
sudo docker run -d \
  --restart=always \
  -p 8091:8091 \
  -p 5000:3000 \
  --device=/dev/ttyUSB0 \
  --name="zwave-js" \
  -e "TZ=Europe/Paris" \
  -v ~/homeassistant/docker/zwave-js:/usr/src/app/store zwavejs/zwavejs2mqtt:latest
```