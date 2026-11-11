# Partie 1 — Architecture Spark et positionnement

## 1. Les composants Spark dans notre déploiement

Notre cluster tourne en mode standalone avec Docker Compose : un conteneur spark-master,
deux conteneurs spark-worker-1 et spark-worker-2 (2 cœurs et 2 Go chacun), et un
conteneur jupyter où l'on écrit le code PySpark.

- Driver : le processus qui exécute notre programme PySpark. Chez nous, il tourne dans
  le conteneur jupyter. Il transforme le code en DAG, le découpe en stages et en tasks,
  envoie les tasks aux executors et récupère les résultats (collect(), show()).
  Son interface web est sur le port 4040.
- Cluster Manager : c'est le spark-master (port 7077, interface sur le port 8080). Il
  connaît les ressources des workers et attribue des cœurs et de la mémoire à chaque
  application. Il ne calcule rien sur les données.
- Workers et Executors : chaque worker met ses ressources à disposition. Au démarrage
  de l'application, chaque worker lance un executor, un processus JVM qui exécute les
  tasks sur une partie des données. Avec 2 workers × 2 cœurs, sc.defaultParallelism
  vaut 4 : 4 tasks peuvent tourner en même temps.
- SparkContext (sc) : le point d'entrée du driver vers le cluster. Il se connecte au
  master (spark://spark-master:7077), demande des executors et crée les RDD. La
  SparkSession (spark) l'enveloppe et ajoute les DataFrames, le SQL et le streaming.

Chemin d'une requête : notebook → Driver (jupyter) → Cluster Manager (spark-master)
→ Executors (spark-worker-1/2) → résultat renvoyé au Driver.

## 2. Deux limites de MapReduce corrigées par Spark

Limite 1 : les écritures sur disque entre chaque étape. MapReduce écrit le résultat
de chaque job sur HDFS, puis le job suivant le relit. Spark garde les résultats
intermédiaires en mémoire et enchaîne les opérations dans un même DAG.
Lien avec SahlPay : pour calculer le total, la moyenne et le top des commerçants sur le
même historique, Spark peut mettre les données en cache une seule fois et les réutiliser.

Limite 2 : MapReduce ne fait que du batch. Il traite un fichier complet puis s'arrête.
Spark propose Structured Streaming : un traitement presque en temps réel
(micro-batches), avec le même moteur et la même API que le batch.
Lien avec SahlPay : les transactions arrivent en continu depuis Kafka (20 événements/s)
et on calcule le taux de rejet par ville sur des fenêtres de 10 minutes. Une seule
requête Spark met à jour ces indicateurs au fil de l'eau.