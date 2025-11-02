#  BIG DATA ENGINEERING – Lab1  
**Programmation avec l’API HDFS et MapReduce**

##  Objectifs du TP

Ce tp a pour but de :
- Manipuler **l’API HDFS** (lecture, écriture, métadonnées).
- Développer un **programme MapReduce** en Java (WordCount).
- Lancer un job **MapReduce Python** via Hadoop Streaming.
- Gérer le projet avec **Maven** et livrer le code sur **GitHub**.


##  Structure du projet

hadoop_lab/
├── pom.xml
├── src/
│ └── main/java/edu/ensias/hadoop/
│ ├── Main.java
│ ├── hdfslab/
│ │ ├── HadoopFileStatus.java
│ │ ├── ReadHDFS.java
│ │ └── WriteHDFS.java
│ └── mapreducelab/
│ ├── TokenizerMapper.java
│ ├── IntSumReducer.java
│ └── WordCount.java
└── target/
├── hadoop_lab-1.0-SNAPSHOT-HadoopFileStatus.jar
├── hadoop_lab-1.0-SNAPSHOT-ReadHDFS.jar
├── hadoop_lab-1.0-SNAPSHOT-WriteHDFS.jar
└── hadoop_lab-1.0-SNAPSHOT-WordCount.jar


##  Compilation Maven

À la racine du projet :
mvn clean package
Les JARs générés se trouvent dans le dossier target/.

 *Partie I – Programmation avec l’API HDFS*
1. Lecture des métadonnées
Classe : HadoopFileStatus.java
Affiche les informations sur un fichier (taille, permissions, date, réplication, etc.).

Exécution :
hadoop jar target/hadoop_lab-1.0-SNAPSHOT-HadoopFileStatus.jar /user/root/input/alice.txt

2. Lecture de fichier depuis HDFS
Classe : ReadHDFS.java
Lit et affiche le contenu d’un fichier HDFS dans la console.

Exécution :
hadoop jar target/hadoop_lab-1.0-SNAPSHOT-ReadHDFS.jar /user/root/input/alice.txt

3. Écriture de fichier dans HDFS
Classe : WriteHDFS.java
Écrit du texte ou des données dans un fichier HDFS.

Exécution :

hadoop jar target/hadoop_lab-1.0-SNAPSHOT-WriteHDFS.jar /user/root/input/bonjour.txt "Bonjour tout le monde !"

 *Partie II – Programmation MapReduce (Java)*
 Classes principales
TokenizerMapper.java → Transforme les lignes en couples (mot, 1).

IntSumReducer.java → Additionne les valeurs associées à chaque mot.

WordCount.java → Configure et exécute le job MapReduce.

 Commande d’exécution

hadoop jar target/hadoop_lab-1.0-SNAPSHOT-WordCount.jar /input/alice.txt /output/wordcount

 *Partie III – MapReduce avec Python (Hadoop Streaming)*
 
mapper.py
#!/usr/bin/env python3
import sys
for line in sys.stdin:
    for word in line.strip().split():
        print(f"{word}\t1")
        
reducer.py
#!/usr/bin/env python3
import sys
current_word = None
count = 0
for line in sys.stdin:
    word, value = line.strip().split("\t", 1)
    value = int(value)
    if current_word == word:
        count += value
    else:
        if current_word:
            print(f"{current_word}\t{count}")
        current_word = word
        count = value
if current_word == word:
    print(f"{current_word}\t{count}")
Exécution :

hadoop jar /opt/hadoop-3.2.1/share/hadoop/tools/lib/hadoop-streaming-3.2.1.jar \
-files mapper.py,reducer.py \
-mapper "python3 mapper.py" \
-reducer "python3 reducer.py" \
-input /input/alice.txt \
-output /output/python_wordcount

 *Partie IV – Gestion du projet avec Git et GitHub*
 
Commandes principales :

git init
git remote add origin https://github.com/Boutanfitsalma/BIGDATA_ENGINEERING_LABS.git
git add .
git commit -m "Ajout du Lab1 : Programmation API HDFS et MapReduce"
git branch -M main
git push -u origin main

Ce dépôt GitHub contient :
Le code source complet.
Les JARs générés.
Ce README.md explicatif.

 *Partie V – Démarrage du cluster Hadoop (Docker)*
Depuis le répertoire racine du projet Docker :

docker start hadoop-master hadoop-slave1 hadoop-slave2
docker exec -it hadoop-master bash
