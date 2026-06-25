# Analysis of Covid 19 papers - Paralelized 

Analysis of COVID-19 research papers using distributed computing frameworks

This readme file contains the commands and instructions we hvae been implementing over the project development , as a guide to the Cloud Veneto conection over the Master and slave machines, the installation and activation of the libraries andd the virtual enviroments, and the seting up of the machines for the code working part.


## Location of the files in the Master virtual machine 
1) PDF PATH:
/mnt/vol2/.cache/kagglehub/datasets/allen-institute-for-ai/CORD-19-research-challenge/versions/50/document_parses/pmc_json 

2) PMC PATH:
 /mnt/vol2/.cache/kagglehub/datasets/allen-institute-for-ai/CORD-19-research-challenge/versions/50/document_parses/pdf_json   


## MUST DO for SPARK and JUPYTER

**From the master covid19-1:**


1) Enter and acctivate the Jupyter environment (all the libraries should be installed here):
source /mnt/vol2/env/jup_env/bin/activate   

2) Enter spark directory:
cd $SPARK_HOME

3) Start a spark session on the cluster:
./sbin/start-all.sh

4) Go to the directory you want to open jupyter and open it with:
jupyter notebook --no-browser  --ip 0.0.0.0  --port 8888 --NotebookApp. token=


5) FROM A NEW TERMINAL OUTSIDE VM AND CLOUD VENETO open the tunnel THIS FOR THE JUPYTER:
ssh -L 4444:10.67.22.xyz:8888 username@gate.cloudveneto.it

ps instead of xyz put the number of VM u want to use

6)  FROM A NEW TERMINAL OUTSIDE VM AND CLOUD VENETO open the tunnel THIS FOR THE SPARK :
ssh -L 8080:10.67.22.xyz:8080 username@gate.cloudveneto.it

6) In your browser open the localhost :
localhost:4444/

6) In your browser in a new tab open the localhost :
localhost:8080/



7) Work on the jupyter and REMEMBER to save

8) **Stop the jupyter notebook** where it is running with ctrl+C.

9) **Stop the spark session** doing:
cd $SPARK_HOME
./sbin/stop-all.sh