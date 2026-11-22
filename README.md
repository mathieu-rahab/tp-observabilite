# tp-observabilite

## 12. Questions de compréhension

**1. Quelle est la différence entre Prometheus et Grafana ?**

Prometheus collecte les métriques et les stocke. Grafana ne stocke rien : il interroge Prometheus et affiche les données sous forme de dashboards.

**2. Quel est le rôle de Node Exporter ?**

Il expose les métriques système de la machine (CPU, RAM, disque, réseau…) sur le port 9100 pour que Prometheus puisse les récupérer.

**3. Que signifie le terme scraping ?**

C'est le fait que Prometheus vienne lui-même lire les métriques sur l'endpoint `/metrics` de chaque cible, à intervalle régulier (toutes les 15 s dans notre config).

**4. À quoi sert le fichier `prometheus.yml` ?**

C'est la configuration de Prometheus : on y indique notamment l'intervalle de scraping et la liste des cibles à surveiller.

**5. Pourquoi Grafana utilise-t-il l'adresse `prometheus:9090` ?**

Les conteneurs sont sur le même réseau Docker, donc ils peuvent se joindre par le nom du service. `localhost` ne marcherait pas, car dans le conteneur Grafana il désigne Grafana lui-même.

**6. À quoi sert PromQL ?**

C'est le langage de requête de Prometheus. Il sert à interroger les métriques, à les filtrer et à faire des calculs dessus, par exemple pour obtenir un pourcentage d'utilisation CPU.

**7. Pourquoi utilise-t-on des volumes Docker ?**

Pour garder les données quand un conteneur est supprimé ou recréé : l'historique des métriques pour Prometheus, les dashboards pour Grafana. On s'en sert aussi pour passer le fichier `prometheus.yml` au conteneur.

**8. Que se passe-t-il lorsqu'une target passe à l'état DOWN ?**

Prometheus n'arrive plus à la joindre, donc il ne récupère plus ses métriques et les graphes se retrouvent sans données. La métrique `up` passe à 0, et c'est ce qui permet de déclencher une alerte.

**9. Pourquoi est-il important de surveiller les ressources CPU, RAM et disque ?**

Si l'une de ces ressources sature, les services ralentissent ou plantent. En les surveillant, on voit le problème arriver avant qu'il ne touche les utilisateurs.

**10. Expliquer la différence entre les états UP et DOWN.**

UP veut dire que Prometheus a réussi à récupérer les métriques de la cible (`up = 1`). DOWN veut dire que la cible ne répond pas (`up = 0`).

**11. Pourquoi utilise-t-on la fonction `rate()` pour calculer l'utilisation CPU ?**

`node_cpu_seconds_total` est un compteur qui ne fait qu'augmenter depuis le démarrage, donc sa valeur brute ne dit pas grand-chose. `rate()` calcule sa vitesse d'augmentation, ce qui donne l'utilisation réelle sur la période choisie.

**12. Pourquoi exclut-on généralement l'interface `lo` ?**

`lo` est l'interface loopback : elle ne transporte que le trafic interne de la machine. Ce trafic n'est pas du vrai trafic réseau et il fausserait les graphes.
