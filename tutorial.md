# Container Orchstration Demo
<walkthrough-directive-name name="Markus Keuter">
</walkthrough-directive-name>

**Duration**: 15 minutes

Goal of this demo is to publish a simpel web-application by:
- Building a container image,
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

## Install Kubectl
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

