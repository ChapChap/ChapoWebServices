# Install
echo "deb https://dl.bintray.com/sbt/debian /" | sudo tee -a /etc/apt/sources.list.d/sbt.list
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 2EE0EA64E40A89B84B2DF73499E82A75642AC823

wget https://d3kbcqa49mib13.cloudfront.net/spark-1.6.3-bin-hadoop2.6.tgz
tar -xf spark-1.6.3-bin-hadoop2.6.tgz
rm -f spark-1.6.3-bin-hadoop2.6.tgz

export SPARK_HOME=/home/ubuntu/workspace/spark-1.6.3-bin-hadoop2.6
export JAVA_HOME=/usr/lib/jvm/java-7-openjdk-amd64
export PATH=$PATH:$SPARK_HOME/bin

# TP
wget https://datanova.legroupe.laposte.fr/explore/dataset/laposte_hexasmal/download/?format=csv&timezone=Europe/Berlin&use_labels_for_header=true
docker run -it -v data:/data jayok/spark spark-shell


val d = sc.textFile("/data.csv")
d.count
d.take(10)
var arrayT = d.toArray()
echo "75010;MATTERS;75010;MATTERS;;48.1567685845,4.79470021531" >> data.csv
val da = d.map(_.split(";"))
#Communes du 77
val dsm = d.map(_.split(";")).filter(_(2).startsWith("77"))
dsm.count
#Nom le plus long
d.map(_.split(“;”)).map(_(1)).map(x =>(x.length,x)).sortByKey(false).take(1)
#Nom le plus long par reduce
d.map(_.split(";")).map(x => (x(1),x(1).length)).reduce((acc,value) => { if(acc._2 < value._2) value else acc})

#union
val dsm = d.map(_.split(";")).filter(_(2).startsWith("77"))
val dess = d.map(_.split(";")).filter(_(2).startsWith("91"))
dsm.union(dess).count # 751
#Nombre de communes par dpt
val dagg = da.map(x => (x(2).substring(0,2), 1)).reduceByKey((x,y) => x+y)
#Maps scala avec comme clef, le nom des champs
val smap =
#tableau de mots composants les noms de communes
val dword = d.map(_.split(";")).map(_(1).split(" ")).toArray().flatten


#Spark SQL
val profiles = sqlContext.read.json("/data/profiles.json")
#Schema
profiles.printSchema()
#Prints
profiles.show()
#nombre de profiles
profiles.count
#20 noms
profiles.map(_(4)).take(20)
#nombre de profiles masculain
profiles.filter("sex = 'M'").count()
#3 plus jeunes
profiles.sort($"dob".desc).show(3)
#profiles de chaque sexe
sqlContext.sql("SELECT sex, count('sex') FROM profiles  GROUP BY sex").show()
#5 prenoms les plus utilises
profiles.groupBy("name").count.sort($"count".desc).show(5)

#EnSql
sqlContext.sql("SELECT * FROM profiles").count
#20 noms
sqlContext.sql("SELECT lastname FROM profiles").show(20)

# STREAM
sbt package && spark-submit --class "SimpleApp" --master "local[4]"target/scala-2.10/simple-project_2.10-0.13.jar

Questions théoriques :
NB: Simply stated: an RDD is a distributed collections of (read-only) items.
1. Quelles sont les différences entre l’approche fonctionnelle et l’approche impérative ?

Source: https://blog.nouhoumtraore.com/2011/08/imperatif-vs-fonctionnel-ou-le-comment.html

Programmation fonctionnelle:
Peu d’affection
Immutaibilité des variables
Peut être utilisé dans du calcul distribué
Utilise beaucoup la récursion (rien à voir) récursivité != currying, fonction d’ordre supérieur
Lazy evaluation +++ (énorme économie de compute)
Fonctions d’ordre supérieur (prend en param un fonction ou retourne une fonction)

Programmation impérative
Beaucoup d’affectation donc la mutation est fortement présente
Effet de bord possible


2. Qu’est-ce que l’approche fonctionnelle apporte pour le calcul distribué?
-Limitation des effets de bords =  immuabilité
-Simplicité.
-Robustesse.
-Parallélisation. ++
- L’évaluation paresseuse permet d’éviter des calculs, jusqu’à ce qu’une action soit réalisée. Permet donc de traiter une énorme collection de données, sans avoir un seul calcul à faire jusqu’au dernier moment.
3. En quoi l’immutabilité joue-t-elle un rôle important dans le calcul distribué ?
Permet d’être Thread safe et de pouvoir paralléliser les opérations sans risquer des effets de bord
4. Décrivez l’utilité des opérations fonctionnelles de map, filter et reduce sur une collection
Map: Divise le volume de données en lots selon une association key-value
Filter: Trie les donnée désirées au travers de conditions
Reduce: Permet de faire une addition (des opérations, pas forcément la somme) sur les values pour une key donnée
5. Quelle est l’approche de spark pour aborder le calcul distribué ? En quoi cette approche est elle compatible avec le langage Scala ?
La couche d'abstraction que fournit spark permet de distribuer les calculs sans se soucier réellement de l'architecture :o

Scala est un langage scalable comme son nom ne l’indique pas. Et avec ses éléments immutable, la parallélisation de spark s’y colle idéalement.
(C’est aussi un langage fonctionnel qui permet d’être thread safe et parallélisable.)

6. Qu’est ce que spark sur hadoop apporte par rapport à map reduce ?
Du traitement sur du Pet(y)aByte.

(Spark est beaucoup plus optimisé que hadoop, il peut être jusqu’à 10x plus rapide sur un map reduce(https://www.lemondeinformatique.fr/actualites/lire-hadoop-vs-spark-apache-5-choses-a-savoir-63271.html))
7. Que permet spark SQL ?
Requêter les datasets structurés et les datasets distribués en langage SQL, HiveQL, ou encore Scala.
Spark SQL permet d'exécuter des requêtes en langages SQL pour charger et transformer des données. Le langage SQL est issu des bases de données relationnelles, mais dans Spark, il peut être utilisé pour traiter n’importe quelles données, quel que soit leur format d’origine
8. Que permet spark streaming ?
Traiter une quantité ininterrompu de donnée (flux) et lui appliquer des filtres et transformations désirées.
9. Quel type de streaming propose spark ? Quel sont ses caractéristiques ?

Kafka: Queue
Flume: Collecteur de log
HDFS: Stockage décentralisé de Hadoop
Kinesis Amazon traitement de données en temps réel
Twitter uknowwhat
Tout l’UDP du WWW quoi
10. Quelles sont les caractéristiques d’un RDD spark ? En quoi est-ce intéressant pour le calcul distribué ?
Resilient Distributed Datasets
Un RDD est une collection partitionnée d’enregistrements en lecture seule qui ne peut être créée que par des opérations déterministes :
soit à partir de données présentes dans un stockage stable,
soit à partir d’autres RDD

Enfin, les utilisateurs peuvent controler deux autres aspects des RDDs :
	La persistence : il est possible de préciser quels sont les RDDs réutilisés ainsi que la stratégie de stockage,
	Le partitionning : les éléments des RDDs peuvent être partitionnés entre les machines en se basant sur une clé dans chaque enregistrement.

Pour les personnes parlant la langue de Churchill:

Beside the above traits (that are directly embedded in the name of the data abstraction - RDD) it has the following additional traits:

In-Memory, i.e. data inside RDD is stored in memory as much (size) and long (time) as possible.
Immutable or Read-Only, i.e. it does not change once created and can only be transformed using transformations to new RDDs.
Lazy evaluated, i.e. the data inside RDD is not available or transformed until an action is executed that triggers the execution.
Cacheable, i.e. you can hold all the data in a persistent "storage" like memory (default and the most preferred) or disk (the least preferred due to access speed).
Parallel, i.e. process data in parallel.
Typed — RDD records have types, e.g. Long in RDD[Long] or (Int, String) in RDD[(Int, String)].
Partitioned — records are partitioned (split into logical partitions) and distributed across nodes in a cluster.
Location-Stickiness — RDD can define placement preferences to compute partitions (as close to the records as possible).

11. Quel est la structure de données principale de spark streaming ?
Spark abstrait les stream via les Discretized Stream (DStream) qui est un stream de RDD. On
retrouve sur les DStream, des transformations similaires à celles présentes sur les RDD.
(https://jaceklaskowski.gitbooks.io/spark-streaming/spark-streaming-streamingcontext.html)
12. Quel est l’avantage d’utiliser une telle structure ?
Appliquer les transfo des RDDs aux streams
13. Donnez un exemple d’utilisation de spark streaming
Filtrer un flux twitter et en saisir les posts contenant un certain mot
Être en veille sur un flux RSS et être notifié de l’apparition d’un mot clé (ex : Mehdi attentat)
Scanner des trames TCP à la main pour y lire un password WiFi :D
Website monitoring, network monitoring
Fraud detection
Web clicks
Advertising
Internet of Things sensors
14. Quel est l’équivalent en fonctionnel (sur spark) des opérations suivantes :

- Projection : map()
- Restriction : reduce()
- Union union()
- Jointure join()
- Count .count()
- Group by .groupBy()
Best of Slack

# Extraction du header
val dataAll = sc.textFile("data.csv")
#Extraction du header
var header = dataAll.first()
# Données sans le header du fichier csv
val data = dataAll.filter(row => row != header)

data.count()

data.first(10)

val dsm = data.map(_.split(";")).filter(_(2).startsWith("77"))
val dess = d.map(_.split(";")).filter(_(2).startsWith("91"))
dsm.union(dess).count

# Count by department

# Mot par village
dword.map(x => (x,1)).reduceByKey((x,y) => x + y).takeOrdered(10)(Ordering[Int].reverse.on(_._2))
||
da.flatMap(_(1).split(" ")).map(x=>(x,1)).reduceByKey(_ + _).sortBy(_._2,ascending=false). take(10)

SQL
sqlContext.sql("SELECT name FROM profiles LIMIT 20").show
profiles.filter(profiles(“sex”)===“M”).count
sqlContext.sql(“select name, lastname, dob from profiles order by dob DESC”).take(3)

sqlContext.sql("SELECT * FROM profiles WHERE SEX == 'M'").write.format("parquet").save("males.parquet")

sqlContext.read.format("parquet").load("males.parquet")
sqlContext.sql("SELECT count(*),name FROM profiles group by name order by count(*) desc").show(3)


