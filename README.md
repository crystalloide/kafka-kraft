# Kafka en mode Kraft avec docker-compose :


[![Open in Gitpod](https://gitpod.io/button/open-in-gitpod.svg)](https://gitpod.io/#https://github.com/crystalloide/kafka-kraft
)

#### Rappel pour retrouver les environnements éventuellement précédemment instanciés dans Gitpod :
[# Pour afficher les workspaces déjà instanciés si besoin de faire du ménage :](https://gitpod.io/workspaces)

#### Affichage du répertoire courant dans Gitpod : 

    pwd

#### On modifie les droits pour que le conteneur ait accès :  

    sudo mkdir ClusterID
    sudo chmod 777 -Rf ClusterID
    sudo chmod 777 -Rf /workspace/kafka-kraft
    
#### On lance le docker compose (les étapes contenues sont expliquées juste après) : : 

    docker compose up -d

#### Les étapes réalisées : 

Avant de démarrer, le clusterId doit être généré et le stockage doit être formaté avec un outil spécial. La commande pour générer l'uuid :

    kafka-storage.sh random-uuid

La commande pour le formatage :

    kafka-storage.sh format

Le docker compose définit 4 conteneurs :

- **kafka-gen** - si le fichier clusterID/clusterID n'existe pas, ce script génère un nouveau fichier avec un clusterID
- **kafka1(2,3)** - conteneurs kafka, chacun d'eux sert de broker et de contrôleur. Ces conteneurs attendent le fichier clusterID, formatent le répertoire de stockage puis démarrent.


Après avoir démarré le cluster kafka, nous pouvons créer un topic :

    docker exec -ti kafka1 /usr/bin/kafka-topics --create  --bootstrap-server kafka1:19092,kafka2:19093,kafka3:19094 --replication-factor 2 --partitions 4 --topic topic1

Production des données dans le topic :

    docker exec -ti kafka1 /usr/bin/kafka-console-producer --bootstrap-server kafka1:19092,kafka2:19093,kafka3:19094 --topic topic1

Lecture des données contenues dans le topic :

    docker exec -ti kafka1 /usr/bin/kafka-console-consumer --bootstrap-server kafka1:19092,kafka2:19093,kafka3:19094 --topic topic1 --from-beginning
