---
layout: page
title: IV. Simulations en détection de changement
menu:
  main:
    weight: 4
bibFile: content/bibliography.json
toc: True
---

Cette page présente la planification de simulations d'exécution d'algorithmes de détection de changement via le programme présenté sur la page **Détection de changement**, ainsi que les résultats obtenus en termes de performances, temps de calcul, cosommation CPU et mémoire, température au sein de la machine...

<!--more-->

## Plan de simulation

### Hyperparamètres

| Hyperparamètres                                   | Valeur                         |
|---------------------------------------------------|--------------------------------|
| Taille des images (px)                            | 500x500 - 500x1000 - 1000x1000 |
| Nombre d'images                                   | 4 - 17 - 68                    |
| Taille de la fenêtre de calcul de covariance (px) | 5x5 - 11x11 - 21x21            |
| Nombre de threads pour calcul de covariance       | 5 - 10 - 12                    |

Nombre de types de simulations : $81$ ($\times 10$ afin d'obtenir des descriptions statistiques des résultats)

### Format du résultat

* Temps d'exécution (ns)
* Données sur la période d'exécution (format csv)

### Fichier d'exécution

```shell
#!/bin/bash

while getopts 'f:p:P' OPTION; do
  case "$OPTION" in
    f)
      name_file="$OPTARG" #récupération du nom du fichier python à exécuter
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

: ${name_file:?Missing -f} #vérification de l'argument du nom du fichier python

for name_image in "500x500x4" "1000x1000x4" "500x500x17" "1000x1000x17" "500x500x68" "1000x1000x68"; do  #nom du repository avec les images redimensionnées
  for window_size in 5 11 21; do 
    for n_jobs_cov in 5 10 12; do
      for i in {1..10}; do  #réplication de l'expérience 10 fois
        t1=$(date -u +%Y-%m-%dT%T.%9NZ)
        echo "*************************************************"
        echo "Time start: $t1"
        echo "*************************************************"
        echo "Running python script: $name_file"
        echo "*************************************************"

        python3 $name_file $name_image $window_size $n_jobs_cov  #exécution du fichier python avec les hyperparamètres
        t2=$(date -u +%Y-%m-%dT%T.%9NZ)

        echo "*************************************************"
        echo "Time stop: $t2"
        echo "*************************************************"

        echo "$name_image,$window_size,$n_jobs_cov,$(($(date -d "$t2" +%s%9N) - $(date -d "$t1" +%s%9N)))" >> execution_time  #sauvegarde du temps d'exécution

        if [ "$by_pid" = true ]; then
          if [ "$get_pid" = true ]; then
            npid=$(cat python_process.pid)
          fi
          echo "Process ID: ${npid}"
          query="data=from(bucket: \"telegraf_bucket\")
            |> range(start: ${t1}, stop: ${t2})
            |> filter(fn: (r) => r[\"_measurement\"] == \"procstat\")
            |> filter(fn: (r) => r[\"pid\"] == \"${npid}\")
            |> aggregateWindow(every: 1s, fn: mean, createEmpty: false)
            |> yield(name: \"mean\")"
        else
          query="data=from(bucket: \"telegraf_bucket\")
            |> range(start: ${t1}, stop: ${t2})
            |> aggregateWindow(every: 1s, fn: mean, createEmpty: false)
            |> yield(name: \"mean\")"
        fi

        echo $query > query

        sudo docker cp query influxdb:/query
        sudo docker exec -it influxdb sh -c 'influx query -f query -r' > metrics_output
        sudo docker exec -it influxdb sh -c 'rm query'
        sudo rm query

        mv metrics_output $name_image.$window_size.$n_jobs_cov.$i.metrics_output
        echo "*************************************************"
        echo "File $name_image.$window_size.$n_jobs_cov.$i.metrics_output created"
        echo "*************************************************"
        mv $name_image.$window_size.$n_jobs_cov.$i.metrics_output results_simulation/  #sauvegarde du résultat
      done
    done
  done
done
```

```shell
bash [nom du script bash] [nom du script python] [éventuelle option sur le process]
```


## Résultats

<!---
1000x1000x4_11_12  41.806750  0.659514
1000x1000x4_21_12  55.804168  0.884749
1000x1000x4_5_12   38.160634  0.420911
500x500x17_5_12    51.393487  0.455210
500x500x4_11_12    14.030667  0.250084
500x500x4_21_12    16.680907  0.202637
500x500x4_5_12     12.813025  0.307787
500x1000x17_11_12   95.213890  0.885555
500x1000x17_21_12  123.228465  0.868575
500x1000x17_5_12    86.993663  1.249665
500x1000x4_11_12    23.294776  0.248905
500x1000x4_21_12    29.507388  0.378102
500x1000x4_5_12     21.270526  0.270534
--->

Temps d'exécution des algorithmes de détection de changement en fonction de la taille de l'échantillon, et de la taille de la fenêtre de calcul de la covariance (longueur du côté de la fenêtre carrée).
<iframe src="../simulation/result_simulation_time.html"
width="1400" height="800" style="border: none;"></iframe>


Temps d'exécution des algorithmes de détection de changement en fonction de la taille de la fenêtre de calcul de la covariance (longueur du côté de la fenêtre carrée), et de la taille de l'image, ici avec 4 images.
<iframe src="../simulation/result_simulation_time_window.html"
width="1400" height="800" style="border: none;"></iframe>


Temps d'exécution des algorithmes de détection de changement en fonction de la taille de l'image, et de la taille de la fenêtre de calcul de la covariance (longueur du côté de la fenêtre carrée), ici avec 4 images.
<iframe src="../simulation/result_simulation_time_imagesize.html"
width="1400" height="800" style="border: none;"></iframe>

<iframe src="../simulation/cpu_usage_500x500x4.html"
width="1400" height="800" style="border: none;"></iframe>

<iframe src="../simulation/cpu_usage_500x1000x4.html"
width="1400" height="800" style="border: none;"></iframe>

<iframe src="../simulation/cpu_usage_1000x1000x4.html"
width="1400" height="800" style="border: none;"></iframe>

<!---
| Taille - Nombre - Fenêtre - Cores                 |Durée d'exécution (s)|
|---------------------------------------------------|:-------------------:|
| 500x500 - 4 - 5x5 - 5                             |                     |
| 500x500 - 4 - 5x5 - 10                            |                     |
| 500x500 - 4 - 5x5 - 12                            | $12.813 \pm 0.308$  |
| 500x500 - 4 - 11x11 - 5                           |                     |
| 500x500 - 4 - 11x11 - 10                          |                     |
| 500x500 - 4 - 11x11 - 12                          | $14.031 \pm 0.250$  |
| 500x500 - 4 - 21x21 - 5                           |                     |
| 500x500 - 4 - 21x21 - 10                          |                     |
| 500x500 - 4 - 21x21 - 12                          | $16.681 \pm 0.203$  |
| 500x500 - 17 - 5x5 - 5                            |                     |
| 500x500 - 17 - 5x5 - 10                           |                     |
| 500x500 - 17 - 5x5 - 12                           | $51.393 \pm 0.455$  |
| 500x500 - 17 - 11x11 - 5                          |                     |
| 500x500 - 17 - 11x11 - 10                         |                     |
| 500x500 - 17 - 11x11 - 12                         |                     |
| 500x500 - 17 - 21x21 - 5                          |                     |
| 500x500 - 17 - 21x21 - 10                         |                     |
| 500x500 - 17 - 21x21 - 12                         |                     |
| 500x500 - 68 - 5x5 - 5                            |                     |
| 500x500 - 68 - 5x5 - 10                           |                     |
| 500x500 - 68 - 5x5 - 12                           |                     |
| 500x500 - 68 - 11x11 - 5                          |                     |
| 500x500 - 68 - 11x11 - 10                         |                     |
| 500x500 - 68 - 11x11 - 12                         |                     |
| 500x500 - 68 - 21x21 - 5                          |                     |
| 500x500 - 68 - 21x21 - 10                         |                     |
| 500x500 - 68 - 21x21 - 12                         |                     |
| 500x1000 - 4 - 5x5 - 5                            |                     |
| 500x1000 - 4 - 5x5 - 10                           |                     |
| 500x1000 - 4 - 5x5 - 12                           | $21.276 \pm 0.271$  |
| 500x1000 - 4 - 11x11 - 5                          |                     |
| 500x1000 - 4 - 11x11 - 10                         |                     |
| 500x1000 - 4 - 11x11 - 12                         | $23.295 \pm 0.249$  |
| 500x1000 - 4 - 21x21 - 5                          |                     |
| 500x1000 - 4 - 21x21 - 10                         |                     |
| 500x1000 - 4 - 21x21 - 12                         | $29.507 \pm 0.378$  |
| 500x1000 - 17 - 5x5 - 5                           |                     |
| 500x1000 - 17 - 5x5 - 10                          |                     |
| 500x1000 - 17 - 5x5 - 12                          | $86.994 \pm 1.250$  |
| 500x1000 - 17 - 11x11 - 5                         |                     |
| 500x1000 - 17 - 11x11 - 10                        |                     |
| 500x1000 - 17 - 11x11 - 12                        | $95.214 \pm 0.886$  |
| 500x1000 - 17 - 21x21 - 5                         |                     |
| 500x1000 - 17 - 21x21 - 10                        |                     |
| 500x1000 - 17 - 21x21 - 12                        | $123.228 \pm 0.869$ |
| 500x1000 - 68 - 5x5 - 5                           |                     |
| 500x1000 - 68 - 5x5 - 10                          |                     |
| 500x1000 - 68 - 5x5 - 12                          |                     |
| 500x1000 - 68 - 11x11 - 5                         |                     |
| 500x1000 - 68 - 11x11 - 10                        |                     |
| 500x1000 - 68 - 11x11 - 12                        |                     |
| 500x1000 - 68 - 21x21 - 5                         |                     |
| 500x1000 - 68 - 21x21 - 10                        |                     |
| 500x1000 - 68 - 21x21 - 12                        |                     |
| 1000x1000 - 4 - 5x5 - 5                           |                     |
| 1000x1000 - 4 - 5x5 - 10                          |                     |
| 1000x1000 - 4 - 5x5 - 12                          | $38.161 \pm 0.421$  |
| 1000x1000 - 4 - 11x11 - 5                         |                     |
| 1000x1000 - 4 - 11x11 - 10                        |                     |
| 1000x1000 - 4 - 11x11 - 12                        | $41.807 \pm 0.660$  |
| 1000x1000 - 4 - 21x21 - 5                         |                     |
| 1000x1000 - 4 - 21x21 - 10                        |                     |
| 1000x1000 - 4 - 21x21 - 12                        | $55.804 \pm 0.885$  |
| 1000x1000 - 17 - 5x5 - 5                          |                     |
| 1000x1000 - 17 - 5x5 - 10                         |                     |
| 1000x1000 - 17 - 5x5 - 12                         |                     |
| 1000x1000 - 17 - 11x11 - 5                        |                     |
| 1000x1000 - 17 - 11x11 - 10                       |                     |
| 1000x1000 - 17 - 11x11 - 12                       |                     |
| 1000x1000 - 17 - 21x21 - 5                        |                     |
| 1000x1000 - 17 - 21x21 - 10                       |                     |
| 1000x1000 - 17 - 21x21 - 12                       |                     |
| 1000x1000 - 68 - 5x5 - 5                          |                     |
| 1000x1000 - 68 - 5x5 - 10                         |                     |
| 1000x1000 - 68 - 5x5 - 12                         |                     |
| 1000x1000 - 68 - 11x11 - 5                        |                     |
| 1000x1000 - 68 - 11x11 - 10                       |                     |
| 1000x1000 - 68 - 11x11 - 12                       |                     |
| 1000x1000 - 68 - 21x21 - 5                        |                     |
| 1000x1000 - 68 - 21x21 - 10                       |                     |
| 1000x1000 - 68 - 21x21 - 12                       |                     |

--->