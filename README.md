# Spatial Queries using Apache Spark

User-defined functions ST\_Contains and ST\_Within written in SparkSQL to perform four spatial queries:

* Range query: It uses ST_Contains. Given a query rectangle R and a set of points P, it finds all the points within R.
* Range join query: It uses ST_Contains. Given a set of Rectangles R and a set of Points S, it finds all (Point, Rectangle) pairs such that the point is within the rectangle.
* Distance query: It uses ST_Within. Given a point location P and distance D in km, it finds all points that lie within a distance D from P.
* Distance join query: It uses ST_Within. Given a set of Points S1 and a set of Points S2 and a distance D in km, it finds all (s1, s2) pairs such that s1 is within a distance D from s2 (i.e., s1 belongs to S1 and s2 belongs to S2).

### 1. ST_Contains

Input: pointString:String, queryRectangle:String

Output: Boolean (true or false)

Definition: Parses the pointString (e.g., "-88.331492,32.324142") and queryRectangle (e.g., "-155.940114,19.081331,-155.618917,19.5307"), then checks whether the queryRectangle fully contains the point (including on-boundary point).

### 2. ST_Within

Input: pointString1:String, pointString2:String, distance:Double

Output: Boolean (true or false)

Definition: Parses the pointString1 (e.g., "-88.331492,32.324142") and pointString2 (e.g., "-88.331492,32.324142"), then checks whether the two points are within the given distance (including on-boundary point).

Range query:
```
select * 
from point 
where ST_Contains(point._c0,'-155.940114,19.081331,-155.618917,19.5307')
```

Range join query:
```
select * 
from rectangle,point 
where ST_Contains(rectangle._c0,point._c0)
```

Distance query:
```
select * 
from point 
where ST_Within(point._c0,'-88.331492,32.324142',10)
```

Distance join query:
```
select * 
from point p1, point p2 
where ST_Within(p1._c0, p2._c0, 10)
```

### 3. How to run the code on Apache Spark using "spark-submit"

1. The main function takes **dynamic length of parameters** as follows:
	* Output file path: ```/Users/ubuntu/Downloads/output```
	* Range query data file path, query window: ```rangequery /Users/ubuntu/Downloads/arealm.csv -155.940114,19.081331,-155.618917,19.5307```
	* Range join query data file path, range join query window data file path: ```rangejoinquery /Users/ubuntu/Downloads/arealm.csv /Users/ubuntu/Downloads/zcta510.csv```
	* Distance query data file path, query point, distance: ```distancequery /Users/ubuntu/Downloads/arealm.csv -88.331492,32.324142 10```
	* Distance join query data A file path, distance join query data B file path, distance: ```distancejoinquery /Users/ubuntu/Downloads/arealm.csv /Users/ubuntu/Downloads/arealm.csv 10```
2. The number of queries and the order of queries in the input **do not matter**. The code will detect the corresponding query and call it!
3. Two example datasets are put in "src/resources" folder. arealm10000 is a point dataset and zcta10000 is a rectangle dataset. The code can be tested using them, or the actual NYC taxi trip dataset.
4. Here is an example that tells how to submit jar using "spark-submit".
```
./bin/spark-submit CSE512-Project-Phase2-Template-assembly-0.1.0.jar result/output rangequery src/resources/arealm10000.csv -93.63173,33.0183,-93.359203,33.219456 rangejoinquery src/resources/arealm10000.csv src/resources/zcta10000.csv distancequery src/resources/arealm10000.csv -88.331492,32.324142 1 distancejoinquery src/resources/arealm10000.csv src/resources/arealm10000.csv 0.1
```
5. A test case file is given: ``exampleinput``. A correct answer is given: ``exampleanswer``.