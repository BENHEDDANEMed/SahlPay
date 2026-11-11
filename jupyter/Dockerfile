ARG SPARK_IMAGE=spark:3.5.8-scala2.12-java17-python3-ubuntu
FROM ${SPARK_IMAGE}

USER root

# JupyterLab + clients Kafka (pour producer.py) + visualisation
RUN pip3 install --no-cache-dir \
        "jupyterlab>=4,<5" \
        kafka-python confluent-kafka \
        pandas matplotlib networkx

# Rendre le PySpark de l'image importable (meme version que le cluster)
RUN ln -s "$(ls /opt/spark/python/lib/py4j-*-src.zip | head -n 1)" /opt/spark/python/lib/py4j-src.zip
ENV PYTHONPATH=/opt/spark/python:/opt/spark/python/lib/py4j-src.zip \
    PYSPARK_PYTHON=python3 \
    PYSPARK_DRIVER_PYTHON=python3

# Configuration commune a toutes les SparkSession
COPY spark-defaults.conf /opt/spark/conf/spark-defaults.conf

WORKDIR /workspace
ENTRYPOINT []
CMD ["jupyter", "lab", "--ip=0.0.0.0", "--port=8888", "--no-browser", "--allow-root", \
     "--ServerApp.root_dir=/workspace", "--IdentityProvider.token="]