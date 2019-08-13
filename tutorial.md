# Container Orchstration Demo
<walkthrough-directive-name name="Markus Keuter">
</walkthrough-directive-name>

**Duration**: 15 minutes

Goal of this demo is to publish a simpel web-application by:
- Building a container image,
- Run the container with Docker in the cloudshell,
- Connect to the preview,
- deploying it on a Kubernetes orchestration platform, 
- demonstrate how it scales, 
- demonstrate how to create a new version of your container afstand
- demonstrate how to deploy it in a green/blue deployment scenario.

To get started click on the shell icon [>_] to copy the text to your shell and press **ENTER**:
```bash
gcloud config set project quint-demo
```  
Press **ENTER**

Select the correct zone: 
```bash
gcloud config set compute/zone europe-west4-b
```  

Press **ENTER**


## Docker Tutorial
You'll start this exercise by testing if Docker is installed in your shell by cpying the following command to your cloudshell and press **ENTER**:
```bash
docker version
```
This shows you the version of the client engine and the version of the server engine, sometimes they can be different, but this should not cause any issues for this tutorial.

### The Dockerfile
First you'll check the content of the Dockerfile, which describes the container you'll be building:
<walkthrough-editor-open-file filePath="cloudshell-tutorials-summerschool/orchestration/Dockerfile" text="Open Dockerfile">
</walkthrough-editor-open-file>

You see two lines of configuration, one describing which base image you'll use for this build and one line which tells Docker to copy the contents of your public folder into a location in your container. This location will be used to get the web content by the webserver called Nginx, which is presently the most used webserver on the Internet.


## Creating the Docker image
To build the image



## Run the container
From the Docker Client start a container based on the webapp:v1 image you created in the previous step. For this you use the "docker run" client command, which will connect to Container engine server API on the host and issue the commands to run the container on the host.
```bash
docker run =p 8080:80 -d website:v1
```
This will collect the image and create a running container. To check if the container is up and running you type the following:
```bash
docker ps
```
This will give you an overview of the running containers. When looking at the PORTS section you'll see a refence of publishing port 80 of the container to port 8080 on the Host tcp ip address. It looks like this:

'0.0.0.0:8080->80/tcp'

### View the website
Next you preview the website which is running on the container. Click on the Web preview button at the top right of your console which looks something like this "[<>]" and select "Preview on port 8080". It will open up a new tab in your browser and will show you the content of the website, running in your very own container. 


## Orchestration

### Install Kubectl
Kubectl is the Kubernetes controller we'll be using to connect to the GKE hosted Kubernetes cluster. To install kubectl 
execute the following command in your shell by clicking the [>_] icon:
```bash
gcloud components install kubectl
```

## Kubernetes Cluster Bouwen
We geven middels gcloud de opdracht om een GKE container cluster te bouwen bestaande 
drie nodes. In de training setting van Summerschool heb je geen rechten om dit te bouwen, deze stap staat in de tutorial wanneer je dit wil bouwen in je eigen Google Cloud omgeving. 
We noemen het cluster het persistent-disk-tutorial cluster.
**Sla voor de training deze stap over, het cluster is al gebouwd!**
```bash  
gcloud container clusters create quint-kube-orchestration --num-nodes=3
```
Dit kan een paar minuten duren....

## Verbinden met bestaand cluster
Als eerste zorg je met onderstaande opdracht ervoor dat je geautoriseerd bent om
met het cluster te werken.

```bash
gcloud container clusters get-credentials quint-kube-orchestration
```
Verder hebben we nog een variabele nodig voor het instellen van het juiste project:
```bash
export PROJECT_ID="$(gcloud config get-value project -q)"
```


## Docker Container aanmaken
Zodra dit is gelukt, kan je naar folder met configuratie bestanden navigeren:
```bash
cd ~/cloudshell-tutorials-summerschool/orchestration
```
En daarna op je door onderstaande link te klikken het configuratie bestand voor het aanmaken van de webserver:

<walkthrough-editor-open-file filePath="cloudshell-tutorials-summerschool/orchestration/manifests/helloweb-deployment.yaml" text="Open configuratie bestand helloweb-deployment.yaml.yaml">
</walkthrough-editor-open-file>

In de console van Google heb je de beschikking over Docker. Docker pakt automatisch een bestand in de folder als input voor
het bouwen van de container:
```bash
docker build -t eu.gcr.io/quint-demo/hello-app:v1 .
```
De Docker installatie in je console moet nog wel rechten krijgen binnen de cloud omgeving:
```bash
gcloud auth configure-docker
```


Om de container te publiceren in de centrale opslag voor containers (de zgn Registry), gebruik je onderstaand commando,
**Sla deze stap over tijdens de training!**
```bash
docker push eu.gcr.io/quint-demo/hello-app:v1
```

Hierna voer je onderstaand commando uit vervang 'markus' door je eigen naam: 
```bash
kubectl create deployment markus --image=eu.gcr.io/quint-demo/hello-app:v1
```
Hier maak je LoadBalancer aan, vervang ook hier weer 'markus' door je eigen naam:
```bash
kubectl expose deployment markus --type=LoadBalancer --port 80 --target-port 8080
```
En om uiteindelijk te connecten controlleer je het EXTERNAL-IP door onderstaand commando meerdere keren uit te voeren
tot er een IP adres staat, dit adres copieër je in je browser:
```bash
kubectl get service
```


## Deployement aantallen verhogen
Door onderstaande commando kan je het huidige aantal replica's (1), verhogen naar 3, waardoor er 3 containers in jouw deployment draaien, vervang ook hier weer 'markus' door je eigen naam: 
```bash
kubectl scale deployment markus --replicas=3
```

Om te controlleren of de container 3 keer draait type je en vervang je 'markus' met je eigen naam:
```bash
kubectl get deployment markus
```
## Versie aanpassing
Je kan de applicatie aanpassen en daarbij 


## Opruimen
Voer de onderstaande opdrachten uit om de omgeving op te ruimen:

