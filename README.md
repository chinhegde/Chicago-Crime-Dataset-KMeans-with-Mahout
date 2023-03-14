# Chicago-Crime-Dataset-KMeans-with-Mahout
Performing data analysis using K-Means clustering on Apache Mahout for Chicago Crime Dataset

## Downloading the dataset
Dataset: https://data.cityofchicago.org/Public-Safety/Crimes-2022/9hwr-2zxp/data
1. Click on “View Data”
2. Click on “Filter”
3. Select “Date” filter 
4. Click on “Export” -> Export as CSV file.

## Cleaning the data using OpenRefine

1. Load the dataset to OpenRefine
2. All rows were checked for blanks. Only X/Y coordinate, Location had blank columns.
3. Blanks were removed from the dataset.

Removing blanks in X/Y coordinates:
1. Editing cells and transforming them to numerical value
2. Click on “Facet” -> “Numerical Facet”
3. Use facet to match only blanks and remove them. Click on “Remove matching rows”
4. Save the data.

## Steps to execute K-Means clustering
The default input for Mahout machine learning algorithms is a sequence file. A vector
representation is created from the sequence file. In order for a machine learning algorithm to process text, it must first be transformed into a vector representation.
1. Make directories for input, sequence files, vector files & clusters.
 ```hdfs dfs -mkdir /input
  hdfs dfs -mkdir /sequence
  hdfs dfs -mkdir /vector
  hdfs dfs -mkdir /cluster
 ```
2. Copy input to the respective folder
```
hdfs dfs -put crime.csv /input
```

3. Command to convert CSV to sequence file:
```
mahout seqdirectory -c UTF08 -i /input -o /sequence
```
    -c: character set (sometimes it might throw an error wrt encoding)
    -i: input crime data directory
    -o: output directory (for sequence files)
    
4. Command to convert sequence files to vector:
```
mahout seq2sparse -nv -i /sequence -o /vector -ow -n 2 -ml 50
```
    -i: input directory (of sequence files)
    -o: output directory (for vector files)
    -ow: overwrite output folder (in case we run the command multiple times with the
    same output folder name)
    
5. Running K-Means clustering algorithm using mahout command:
  ```
mahout kmeans -i /vector/tfidf-vectors -o /output –maxIter 20 –clusters /cluster -dm org.apache.common.distance.CosineDistanceMeasure –method mapreduce -cd 0.1 -k 4 -ow 
```
  
    -i: input directory (of vector files - tfidf vectors here)
    -o: output directory (for clusters)
    –maxIter: maximum number of iterations
    –clusters: directory for clusters
    -dm: distance measure - here, we are using Cosine Distance Measure
    -cd: convergence delta - in case the centroid doesn’t change
    -k: number of clusters (4)

6. Cluster dump:
  ```
  mahout clusterdump --input output/clusters-20-final --pointsDir output/clusteredPoints --output cluster_final.txt
  ```
  The output cluster_final.txt is present in the local machine
