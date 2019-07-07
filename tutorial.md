# Les 3 Orchstration Demo
<walkthrough-directive-name name="Markus Keuter">
</walkthrough-directive-name>

**Duur van de oefening**: ongeveer 15 minuten

Doel van deze demo is om te laten zien hoe je een simpele web-applicatie als:
- een container uitrolt op een Kubernetes orchestration platform, 
- hoe je de applicatie schaalt en 
- hoe je een nieuwe versie uitrolt van de applicatie.

Om te beginnen zet je eerst alle instellingen die noodzakelijk zijn op.

Deze tutorial gaat uit van het project quint-demo, mocht je deze tutorial 
onafhankelijk van de training volgen, selecteer dan een eigen project.

Klik op het shell icoontje en daarna op **ENTER**:

```bash
gcloud config set project quint-demo
```  

Stel de juiste compute zone in (Eemshaven NL) 
```bash
gcloud config set compute/zone europe-west4-b
```  

**ENTER**

## Opzetten Kubectl
Kubectl is de controller van Kubernetes, met deze controller kan je via de API van 
Kubernetes, op afstand, taken laten uitvoeren op het Kubernetes cluster

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

