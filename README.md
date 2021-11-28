# IBD Assignment

# Authors

**Small Data Team**

- *Andrey Palaev* (a.palaev@innopolis.university)
    
    Programming
    
- *Dinislam Gabitov* (d.gabitov@innopolis.university)
    
    Deployment
    
- Georgy Andryushchenko (g.andryushchenko@innopolis.university)
    
    Report Composing
    

# Introduction

In order to become more experienced in working with Hadoop, Spark, MLlib and Scala our team finalized the implementation of the movie recommender system running on Spark.

In this report we would like to present the solution with the achieved results and their corresponding analysis.

# Solution Description

## General System Description

The recommender system takes a directory as an input that contains 4 files: `movies2.csv`, `ratings2.csv`, `user_ratings.tsv`, `for_grading.tsv`. It also takes the flag to consider preferences while making a prediction.

At first it asks if the user would like to load the preferences from the file instead of interrogation. If the answer is positive than the system loads the preferences and append it to the training dataset. Otherwise, it interrogates about the movies from `for_grading.tsv` and also appends the data to the dataset. The model learns afterwards.

After the learning the system makes a prediction on the test predictors, calculates the test error and baseline error and outputs recommendations for the user. 

## Detailed System Description

- Within this part of the code we check whether there are quotes in the entry. If there are ones then we take everything between the quotes. Otherwise, we split the line on the commas and take the name of the movie.
    
    ![Untitled](pictures/Untitled.png)
    

- Baseline error: for each movie we find its baseline and calculate RMSE.
    
    ![Untitled](pictures/Untitled%201.png)
    

- Exclude from the recommendations the movies that have already been graded by the user.
    
    ![Untitled](pictures/Untitled%202.png)
    

- Get rid of the movies that has less than 75 reviews.
    
    ![Untitled](pictures/Untitled%203.png)
    
- Before interrogation we suggest the user to load preferences from a file
    
    ![Untitled](pictures/Untitled%204.png)
    

## Deployment on Private Network Hadoop Cluster

The recommender system has been run on the two laptops connected to the same Wi-Fi hotspot.

Here is the process of setting up and running:

1. In both hosts we create a new user `hadoop` with its password, home directory, and sudo rights
    
    `$sudo useradd -m hadoop`
    
    `$sudo passwd hadoop`
    
    `$sudo adduser hadoop sudo`
    
    `$su hadoop`
    
2. set up the hostnames in the `/etc/nosts` file
    
    On both hosts we deleted the line with 127.0.1.1 and added these lines:
    
    `192.168.31.42 andrey-G5-5590`
    
    `192.168.31.42 andr`
    
    `192.168.31.128 georgy`
    
    `192.168.31.128 datapaf-pc`
    
    ![Untitled](pictures/Untitled%205.png)
    
    ![Screenshot from 2021-11-27 23-18-08.png](pictures/Screenshot_from_2021-11-27_23-18-08.png)
    
3. One of the hosts wasn't able to ssh to another host, so we generate an ssh public key, installed the ssh-server and copied to another host
    
    ![Screenshot from 2021-11-27 23-27-48.png](pictures/Screenshot_from_2021-11-27_23-27-48.png)
    
    ![Screenshot from 2021-11-27 23-27-49.png](pictures/Screenshot_from_2021-11-27_23-27-49.png)
    
    ![Screenshot from 2021-11-27 23-27-52.png](pictures/Screenshot_from_2021-11-27_23-27-52.png)
    
    ![Untitled](pictures/Untitled%206.png)
    
    ![Untitled](pictures/Untitled%207.png)
    
4. On both hosts in `~/.bashrc` file add the following configuration for Spark and Hadoop
    
    ![Untitled](pictures/Untitled%208.png)
    
    ![Screenshot from 2021-11-27 23-32-03.png](pictures/Screenshot_from_2021-11-27_23-32-03.png)
    
5. Set up Hadoop `tmp` directory and replication factor in `hdfs-site.xml`
    
    ![Untitled](pictures/Untitled%209.png)
    
6. Set up the namenode in `core-site.xml`
    
    ![Untitled](pictures/Untitled%2010.png)
    
7. Set up YARN's node manager and resource manager in `yarn-site.xml`
    
    ![Untitled](pictures/Untitled%2011.png)
    
8. Set up the following settings for MapReduce
    
    ![Untitled](pictures/Untitled%2012.png)
    
9. Add the following workers
    
    ![Untitled](pictures/Untitled%2013.png)
    
10. Distribute the configurations to all the hosts
11. Run HDFS by `start-dfs.sh` and check the working nodes by `hdfs dfsadmin -report`
    
    ![Untitled](pictures/Untitled%2014.png)
    
    ![Untitled](pictures/Untitled%2015.png)
    
12. Start YARN by running `start-yarn.sh`
13. Check that the node really work for YARN
    
    ![Untitled](pictures/Untitled%2016.png)
    

## What Could Have Been Done Differently?

### Code

Movies filtering may be done differently. Instead of setting up the lower bound for the number of reviews equal to 75, it may be equal to another value (for example, 100).

The parameters of the model may also be different.

### Deployment

Whlie deploying on the private network cluster we could host Hadoop components on each host's virtual machine. We tried to do it but due to the problems with the proper setup of the bridged adapter it's not possible to successfully run the recommender system on such cluster.

# Results

## Achieved Results

### Running the System on a Local Hadoop Cluster

- Running the system without checking the user's preferences
    
    ![Untitled](pictures/Untitled%2017.png)
    
- Loading the user's preferences from the file
    
    ![Untitled](pictures/Untitled%2018.png)
    
    ![Untitled](pictures/Untitled%2019.png)
    
- Loading the preferences by interrogating the user
    
    ![Untitled](pictures/Untitled%2020.png)
    
    ![Untitled](pictures/Untitled%2021.png)
    

### Running the System on Private Network Hadoop Cluster

- Running the system without checking the user's preferences
    
    ![Untitled](pictures/Untitled%2022.png)
    
    ![Untitled](pictures/Untitled%2023.png)
    
1. Run the recommender system with loading the grades from the file
    
    ![Untitled](pictures/Untitled%2024.png)
    
    ![Untitled](pictures/Untitled%2025.png)
    
    ![Untitled](pictures/Untitled%2026.png)
    
2. Run the recommender system with getting the grades from keyboard input
    
    ![Untitled](pictures/Untitled%2027.png)
    
    ![Untitled](pictures/Untitled%2028.png)
    
    ![Untitled](pictures/Untitled%2029.png)
    

## Analysis of the Results

Discovering how rank affects the error of the model. All ranks were tested on the same `user_ratings.tsv` file

- Rank = 10

![Untitled](pictures/Untitled%2030.png)

- Rank = 20

![Untitled](pictures/Untitled%2031.png)

- Rank = 30

![Untitled](pictures/Untitled%2032.png)

- Rank = 40

![Untitled](pictures/Untitled%2033.png)

- Rank = 50

![Untitled](pictures/Untitled%2034.png)

- Rank = 60

![Untitled](pictures/Untitled%2035.png)

- Rank = 100

![Untitled](pictures/Untitled%2036.png)

So, in the results, we can see that baseline error in all cases is almost the same, while test errors (after training) are different. The minimum test error is on rank 40. That means all ranks < 40 - underfit and all ranks > 40 - overfit.

### Conclusion

The implementation of the system was successfully completed and deployed on both local and private network Hadoop clusters. Different configurations of the model were tested and compared to find the best configuration.
