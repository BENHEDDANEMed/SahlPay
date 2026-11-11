# SahlPay — Plateforme Big Data (EMSI, Écosystème Big Data 2)

## Versions
| Composant | Version | Image Docker |
|---|---|---|
| Spark | 3.5.8 (Scala 2.12, Java 17) | spark:3.5.8-scala2.12-java17-python3-ubuntu |
| Kafka | 3.9.1, mode KRaft | apache/kafka:3.9.1 |
| MinIO | dernière version communautaire | cgr.dev/chainguard/minio |
| Jupyter | JupyterLab 4 sur l'image Spark | ./jupyter/Dockerfile |
| hadoop-aws / spark-sql-kafka / delta-spark | 3.3.4 / 3.5.8 / 3.3.2 | jupyter/spark-defaults.conf |

## Lancement
```
docker compose up -d --build
docker compose ps
```

| Interface | Adresse |
|---|---|
| Spark Master UI | http://localhost:8080 |
| Spark UI de l'application | http://localhost:4040 |
| JupyterLab | http://localhost:8888 |
| Console MinIO | http://localhost:9001 (minioadmin / minioadmin123) |

Depuis les conteneurs : Kafka = kafka:9092, MinIO = http://minio:9000.
Depuis le PC : Kafka = localhost:9094.

## Commandes utiles
```
docker compose exec kafka /opt/kafka/bin/kafka-topics.sh --bootstrap-server localhost:9092 --describe --topic transactions
docker compose run --rm mc ls --recursive local/lake/
docker compose stop      # pause sans perdre les données
```
Attention : `docker compose down` supprime les messages Kafka.

## Avancement
| Partie | Responsable | État |
|---|---|---|
| 1. Cluster | A | terminé |
| 2. RDD | B | à faire |
| 3. DataFrames / SQL | C | à faire |
| 4. Streaming | A | à faire |
| 5. Production | B | à faire |
| 6. Graphes | C | à faire |