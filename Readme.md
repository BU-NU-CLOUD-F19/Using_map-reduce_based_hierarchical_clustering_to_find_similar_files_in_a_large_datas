** **

## Using map-reduce based hierarchical clustering to find similar files in a large dataset

## 1.   Vision and Goals Of The Project:

The current demands for datacenters are huge and storing backups can become a tedious task. Moving massive data in the production environment is both computationally and spatially expensive. In Dell’s  DDFS architecture, there is a need to find similar tags (files) in the entire system. The memory requirement for a dissimilarity matrix is N^2, which presents a great challenge when considering files in the order of millions. Implement an algorithm for this problem such that it can be scaled to multiple nodes.
Our short term goal in this project is to develop a clustering algorithm that can potentially find similar files on a single node. Our efforts would be focused on finding techniques that can predict similarity in files using minhash estimation of the Jaccard distance as it reduces space requirent.

The goals of the project:
* Report performance related findings on various file similarity calculation algorithms-minhash and jaccard.
* Implement an algorithm for this problem such that it can be scaled to multiple nodes.
* Deploy the algorithm to a cloud provider like AWs.

## 2. Users/Personas Of The Project:
Researchers working on DDFS (Data Domain File System by Dell)

## 3.   Scope and Features Of The Project:

The main features we are aiming to implement were elucidated by our mentor:

* Create python programs that can implement a basic clustering algorithm for the datasets on a single node.
* Report findings on various file similarity calculation algorithms( min-hash, Jaccard index) based on their performance for different parameters like dataset size.
* Extend this solution to develop programs that can run on multiple nodes to solve the clustering algorithm (using map reduce) and   produce an end result that looks like:

           	ClusterID  |  Dissimilarity Level  |   File IDs

** **

## 4. Solution Concept
_Overview_

* Fingerprint Computation: A fingerprinting algorithm is a procedure that maps an arbitrarily large data item (such as a computer file) to a much shorter bit string, its fingerprint, that uniquely identifies the original data for all practical purposes. Fingerprints are typically used to avoid the comparison and transmission of bulky data. For instance, a web browser or proxy server can efficiently check whether a remote file has been modified, by fetching only its fingerprint and comparing it with that of the previously fetched copy. In our project we will be computing fingerprints of a dataset of 100K-100TB file sizes for 100M-1B files. We are generating our dataset files by using random 32-64 bit integers as fingerprints and by keeping a certain percentage of similar fingerprints in some of the dataset files.
<img src="/images/Fingerprint.svg.png" width="400" height="300">

* Deduplication-Data deduplication is a technique for eliminating duplicate copies of repeating data. A related and somewhat synonymous term is single-instance (data) storage. This technique is used to improve storage utilization and can also be applied to network data transfers to reduce the number of bytes that must be sent. In the deduplication process, unique chunks of data, or byte patterns, are identified and stored during a process of analysis. In our project we will be globalizing deduplication by locating all similar files in the same node.

* Distance Matrix Computation: Similarity between two files is measured by the Jaccard index J(A, B) = |A ∩ B| / |A U B|. The distance would be 1 – J(A, B). We want to identify clusters of similar files. Most clustering algorithm takes a distance matrix and just combine the most similar files two at a time (or some variations). Computation of the distance matrix can be expensive. Most algorithms use approximation to update the matrix. The size of the matrix is O(n^2) and we can run out of memory quickly.

* Minhash- Check locality sensitive hashing.
Minhash for a file-Apply a uniformly distributed hash function to the fingerprints in a file. Hash function maps keys to numbers, thus providing an order. Minhash is the smallest hash number for the file. Yes – we represent a file by only one number. If two files have Jaccard Index J(A, B), the probability that they have the same minhash is J(A, B). Now generate n hash functions, compute the minhash for each function. We now have a minhash signature of n numbers
Given the minhash signature of 2 files, #same entries/#total entries = J(A, B)
Given the minhash signature of A and B, {a1,a2,a3,… an} and {b1, b2, b3, … bn}  the minhash of the union A U B is
{min(a1,b1), min(a2,b2), … min(an, bn)}
If we know |A| and |B| and J(A, B), we can estimate
           |A U B| and |A ∩ B|
           

<img src="/images/minhash.PNG" width="750" height="300">

Minhash reduces the space requirement from N X N to N X number of hash functions, where N is the number of files in the system. 
We can apply hash functions of the form (ax+b)% c where x is fingerprint, a and b are constants less than x and c is a prime number greater than x.

<img src="/images/matrix.PNG" width="300" height="300">

 * Hierarchical clustering - It is one of the popular and easy to understand clustering technique.
For this project, initially each data point is considered as an individual cluster. At each iteration, similar clusters will merge with other clusters until one cluster or K clusters are formed.The basic algorithm is :  
Agglomerative Hierarchical Clustering      
In this technique, initially each data point is considered as an individual cluster. At each iteration, the similar clusters merge with other clusters until one cluster or K clusters are formed.
The basic algorithm of Agglomerative is straight forward.
* Compute the proximity matrix.
* Let each data point be a cluster.
* Repeat: Merge the two closest clusters and update the proximity matrix until only a single cluster remains.
  
  <img src="/images/hierarchical_clustering.PNG" width="750" height="350">
  
## Learnings
* Finding similarity using minhash algorithm.
* Spark/Scala for iterative hierarchical clustering algorithm.
* AWS S3 for data storage and AWS EMR.
## 5. Acceptance criteria

*The minimum acceptance criteria for the project is as follows :*

* Develop a  clustering algorithm that finds similar files on a single node using minhash estimation of Jaccard.
* Report possible solutions for a scaled-out architecture of the clustering algorithm that performs clustering on multiple nodes using map-reduce.
* Deploy this algorithm to a cloud service provider like AWS.

## 6.  Release Planning:

Release #1 (due by Week 2): 

File Data Set Generation  : For this project, files contain 32 or 64 bit integers – each represents a chunk of data.

For example, file A = { 1203, 402392, 2300, 23, 102393822, …. }
             file B = { 32393000, 103032923, 29393, 123002, 123, 2300, … }
             
We need to create files with similar data. There are a few “canonical” building blocks:
* Files that are unique
* Chain of files with p% common data between adjacent files:
F1, F2, F3, … such that Fi and Fj share p% of common data
* A binary hierarchy relationship like this:

<img src="/images/Hierarchy.PNG" width="300" height="300">

Generate datsset with different similarity levels.
Understand jaccard and minhash.

Release # 2 (due by week 4)

* Implement minhash to optimize space complexity from N X N to N X number of hash functions. 

Release # 3 : (due by week 6)

* Implement hierarchical clustering algorithm using Python for single node.

Release # 4 : (due by week 8)

* Implement iterative hierarchical clustering algorithm.
* Research on mapReduce Hadoop and Spark to determine better algorithm for scaling current algorithm to multiple nodes.

Release # 5 : (due by week 10)

* Scale hierarchical clustering algorithm to multiple nodes using Spark/Scala.

Release # 6 : (due by week 12)
* Deploy algorithm on AWS and perform experiments with different machine configurations and number of files.

## Project Installation
Required Softwares and Assemblies
Maven, Scala Installer, Git, Spark, Python, aws-cli
Instructions
* Install maven from here : https://maven.apache.org/download.cgi
* Install Scala from here : https://www.scala-lang.org/download/
* Install Git from here : https://git-scm.com/book/en/v2/Getting-Started-Installing-Git
* Install spark from here : https://spark.apache.org/downloads.html
* Install python from here : https://www.python.org/downloads/
* Install aws-cli from here : https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html
* once all the required softwares are installed, clone the repository.
* run mvn clean install


## Open Questions?
* Apart from researchers at Dell Data Domain File System team, who else will be the users and personas for this project?

## Presentations:
* Demo 1:
https://docs.google.com/presentation/d/1QKXyxbTsCa_3MybXeT2vbhxHfIkL4UOI8EqA_8rBoV8/edit?usp=sharing

   * Demo 1 Video:
https://youtu.be/xMPnpYLJiG0

* Demo 2 :
https://docs.google.com/presentation/d/1Ezn64y_rod80Z9sNUsZNpL3KUEsxELNCFtvAdeZBqhA/edit?usp=sharing
           
   * Demo 2 Video:
https://www.youtube.com/watch?v=RRxVqCf8IT4&feature=youtu.be

* Demo 3 :
https://docs.google.com/presentation/d/1GHy-XMVLtc0iKHnHkkAv0IWuj4MZS7w9VitBBCiH9Yc/

   * Demo 3 Video : 
https://youtu.be/ksQER1UrrzQ

* Demo 4:
https://docs.google.com/presentation/d/1YZRqfU9g47Esq7O9m7mFGzf1OXJydVNFjp_d3Aa4fnY/edit?usp=sharing
           
   * Demo 4 Video:
https://www.youtube.com/watch?v=faLUXh1TB40&feature=youtu.be

* Demo 5:
https://docs.google.com/presentation/d/1e61itiK3FSfFck-eEj0vPj13foz7sxIwOhH3HKn7VQw/edit?usp=sharing
           
   * Demo 5 Video:




* DynamoDb Presentation
https://docs.google.com/presentation/d/1SsaQcF3r3qiJ5VydNwtSlKEdVpC8x4hzlnz1vtIg76w/edit?ts=5d9fd1a1#slide=id.g6402340200_4_51



* Final Presentation:
https://youtu.be/NBuytAo5oAk

## References :
https://pdfs.semanticscholar.org/7b12/f6ef8d620bcc54e71da13df4291bcc8d0679.pdf
** **
