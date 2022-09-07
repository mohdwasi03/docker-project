# docker-project

# Problem-Statement
 Setup Postgres in a docker container and imported  dataset  into it.
 Setup Jupyter notebook in another docker container.
 Impelement SQL queries from Jupyter notebook container, connecting to the Postgres container.
 
 
 # Steps
 
1) create a ec2-instance and enable password authentication
Here  I am using Amazon-linux-machine, so we use yum repository .run the following commands to install docker:


                                   "yum install docker -y"
                                   
                                   "docker service start"
                                   
It installs docker and start the docker service.
 

2) Make a data directory in /tmp/project/  which  contains dataset in info.sql
run the command being in the location of info.sql :

                    "scp ./info.sql root@<public_ip-of-instance>:/tmp/project/data/"

It shall copy info.sql from local machine to our file structure in ec2-instance




3) login to ec2-instance and run the following cammands:
         
                    "vi /tmp/project/docker-compose.yml"


It shall create a docker-compose.yml file and then copy the content and save it.

docker-compose.yml
------------------------------------------------------

       version: '3.7'

       services:
           postgres-container:
               restart: unless-stopped
               image: postgres:latest
               environment:
                   POSTGRES_USER: ${POSTGRES_USER:-wasi}
                   POSTGRES_PASSWORD: ${POSTGRES_PASSWORD:-12345}
                   POSTGRES_DB: ${POSTGRES_DB:-data}
               ports:
                   - 5432:5432
               volumes:
                   - /var/lib/postgresql/data
                   - ./data/info.sql:/docker-entrypoint-initdb.d/info.sql

           jupyter-notebook-container:
               restart: unless-stopped
               image: jupyter/datascience-notebook:latest
               environment:
                   JUPYTER_TOKEN: 12345
               volumes:
                   - /home/joyves/work
               ports:
                   - 8080:8888


-------------------------------------------------------------
It all the required information to create containers from their respective images, volumes, environment variables.  

4) Being in the same directory (cd /tmp/project/)
run the following command :
                     "docker-compose up -d "
It executes the .yml file  and create the container (by docker-compose method). Since port no 8080 is for jupyter-notebook-container it can be accessed by 
   <public_ip>:8080 on the browser. 
Then provided the token as "12345" since we assigned in jupyter-notebook-container.

5) Access the jupyter-notebook install it dependencies and executes the queries.
The results are provided in the images directory (#1.png , #2.png).
