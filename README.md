# TextMining
# Documentation Pipeline de Traitement de Texte

Vue d’Ensemble
Le pipeline de traitement de texte repose sur une architecture en cinq couches principales :

Ingestion des Données (Kafka)
Traitement (Spark)
Stockage (AWS S3)
Service de Modèles (TensorFlow Serving)
Monitoring (Prometheus/Grafana)
Flux de Données
1. Ingestion des Données
Les textes sont injectés dans le pipeline via Kafka.
Trois topics principaux sont utilisés : raw-text-fr, raw-text-en, et raw-text-ar.
Exemple de format des messages :
json
Copier
Modifier
{
    "text": "contenu du texte",
    "language": "fr/en/ar",
    "metadata": {},
    "timestamp": "2025-01-18T20:00:00Z"
}
2. Traitement des Données (Spark)
Lecture des données en streaming depuis Kafka.
Traitement par lots toutes les minutes.
Résultats enregistrés dans S3, avec un partitionnement par langue.
3. Stockage dans S3
Organisation des buckets :
sql
Copier
Modifier
s3://raw-bucket/
    ├── raw/
    │   ├── fr/
    │   ├── en/
    │   └── ar/
s3://processed-bucket/
    ├── processed/
    │   ├── language=fr/
    │   ├── language=en/
    │   └── language=ar/
4. Service des Modèles
API REST exposée sur http://localhost:8501.
Principaux endpoints :
Prédiction : /v1/models/text_classifier/predict
Statut : /v1/models/text_classifier
Métadonnées : /v1/models/text_classifier/metadata
Guide de Démarrage
1. Lancement des Services
bash
Copier
Modifier
docker-compose up -d
2. Vérification des Services
Kafka UI : http://localhost:8080
Spark UI : http://localhost:8081
TensorFlow Serving : http://localhost:8501
Grafana : http://localhost:3000
Points d'Accès des Services
1. Kafka
Bootstrap Server : kafka:29092
Topics :
raw-text-fr : Textes en français
raw-text-en : Textes en anglais
raw-text-ar : Textes en arabe
processed-text : Textes après traitement
2. Spark
Master : spark://spark-master:7077
Interface Web : http://localhost:8081
Deux workers configurés.
3. TensorFlow Serving
REST API : http://localhost:8501
gRPC : localhost:8500
Proxy NGINX : http://localhost:8502
4. Monitoring
Prometheus : http://localhost:9090
Grafana : http://localhost:3000
Surveillance et Maintenance
1. Logs des Conteneurs
bash
Copier
Modifier
# Afficher les logs d’un service spécifique
docker-compose logs -f [service]
2. Indicateurs Clés
Kafka : Latence de traitement.
Spark : Utilisation mémoire.
Modèles : Temps de réponse et taux de succès.
S3 : Disponibilité de l’espace de stockage.
3. Points de Contrôle
Vérification des topics Kafka.
Surveillance de l’état des workers Spark.
Disponibilité des modèles TensorFlow.
Suivi de l’espace disque sur S3.
Procédures de Reprise
1. Panne Kafka
bash
Copier
Modifier
docker-compose restart kafka
# Vérification des topics
docker-compose exec kafka kafka-topics --list --bootstrap-server localhost:9092
2. Problème Spark
bash
Copier
Modifier
docker-compose restart spark-master spark-worker-1 spark-worker-2
# Relancer les jobs
docker exec spark-master /opt/spark-apps/submit-job.sh
