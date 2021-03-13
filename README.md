# Airflow on Docker - Just for learn purpose!!! 
<h1>Apache airflow on docker - Just for learn purpose!</h1>

# What is Apache Airflow?
<img align="left" src="docs/images/apache-airflow-logo.png" width="50" height="44"> Apache Airflow is a platform created by the community to programmatically author, schedule and monitor workflows.

It is:
* Scalable - Airflow has a modular architecture and uses a message queue to orchestrate an arbitrary number of workers. Airflow is ready to scale to infinity.
* Dynamic - Airflow pipelines are defined in Python, allowing for dynamic pipeline generation. This allows for writing code that instantiates pipelines dynamically.
* Extensible - Easily define your own operators and extend libraries to fit the level of abstraction that suits your environment.
* Elegant - Airflow pipelines are lean and explicit. Parametrization is built into its core using the powerful Jinja templating engine.

Source of information and image: https://airflow.apache.org/

In another words it is a great software that can help data engineers (and why not other devs?) to use the full potential of your data pipelines and orchestrate the public clouds in the best way!

# Motivation
<img align="left" src="docs/images/docker-logo.png" width="50" height="44">When we install airflow on virtual machines or clouds instances (ex: EC2) we can find some challenges and problems that containers and K8s can solve for us.

The way I decided to challenge myself to deploy my own version of airflow and try to use that on kubernetes and EKS (aws).

So if this project is not complete don't worry, it'll be soon!

Docker image: https://www.docker.com/company/newsroom/media-resources

# About the project
This project its divided in some folder. Please see below the objective of each one:
* single-node - This folder is responsible to store all the codes responsible to run a single container with all the services running in it.
  * Use the following command to start the container: docker run -d -p 8080:8080 subtilf/airflow
* cluster-mode - This folder is responsible to store all the codes responsible to run many containers each one running your own service.
* docs - This folder is responsible to store doc and auxiliar files (ex: images) for all repository.