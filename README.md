# Microservices, Observability and Chaos on Digital Ocean

![image](https://user-images.githubusercontent.com/18049790/43352583-0b37edda-9269-11e8-9695-1e8de81acb76.png)

## Agenda
* Deploy a Kubernetes cluster on Digital Ocean with Observability software pre-configured
* Deploy the Socks Shop microservices application onto the Kubernetes cluster on Digital Ocean
* Verify operation of the Socks Shop microservice
* Observe the Socks Shop microservice with the Observability software
* Perform Chaos Engineering on the Socks Shop microservice

## Requirements
* A Digital Ocean Account
* A terminal to interact with the cluster 

## Buzz Words
* Digital Ocean - Developer focused Cloud Provider.
* Microservice - Collection of **loosely coupled services** that are **independently deployable and scalable**.
* Kubernetes - Open-source self-healing platform to deploy, scale and operate containers.
* Prometheus - Prometheus is an open source toolkit to monitor and alert.
* Grafana - Grafana offers data visualization & Monitoring with support for Graphite, InfluxDB, Prometheus.
* Prometheus Operator Helm Chart - Installer for Prometheus Operator.    
* Prometheus Operator - The Prometheus Operator provides easy monitoring for k8s services and deployments besides managing Prometheus, Alertmanager and Grafana configuration. 
* Kube State Metrics - Kube-state-metrics is a simple service that listens to the Kubernetes API server and generates metrics about the state of the objects. 
* Prometheus NodeExporter - The Prometheus Node Exporter exposes a wide variety of hardware- and kernel-related metrics.
* Kubernetes Metrics Server - Starting from Kubernetes 1.8, resource usage metrics, such as container CPU and memory usage, are available in Kubernetes through the Metrics API.
* Kube Monkey - Kube-Monkey periodically kills pods in your Kubernetes cluster,that are opt-in based on their own rules.

## Documentation 
* [Kubernetes](https://kubernetes.io)
* [Prometheus](https://prometheus.io)
* [Grafana](https://grafana.com)
* [Prometheus NodeExporter](https://github.com/prometheus/node_exporter/blob/master/README.md)
* [kube-state-metrics](https://github.com/kubernetes/kube-state-metrics/blob/master/README.md)
* [metrics-server](https://github.com/kubernetes-incubator/metrics-server/blob/master/README.md)

## Description

The Kubernetes Monitoring Stack distills operational knowledge of integrating Prometheus, Grafana, and metrics-server for deployment onto DigitalOcean Kubernetes clusters.

This stack provides core metrics configured with cluster specific graphs tested to ensure that they function properly on DigitalOcean Kubernetes. 

Use this Stack to monitor your Kubernetes cluster and Socks Shop application.

Note: This stack requires a minimum configuration of
* 2 Nodes at the $10/month plan (2GB memory / 1 vCPU) 
* 1 $10/month DigitalOcean Load Balancer

### Prometheus
![logo_prom](https://user-images.githubusercontent.com/18049790/64942965-faa02900-d859-11e9-8f2b-730b9851c763.png)
#### What is Prometheus?
* Prometheus is an open-source *systems monitoring and alerting* toolkit originally built at SoundCloud. 
* It is now a standalone open source project and maintained independently of any company. 
* Prometheus joined the Cloud Native Computing Foundation in 2016 as the second hosted project, after Kubernetes.

#### Prometheus's main features are:
* a multi-dimensional data model with **time series data** identified by metric name and key/value pairs
* PromQL, a flexible query language to leverage this dimensionality
* no reliance on distributed storage; single server nodes are autonomous
* time series collection happens via a **pull model over HTTP**
* pushing time series is supported via an intermediary gateway
* targets are discovered via service discovery or static configuration
* multiple modes of graphing and dashboarding support

#### Components
* the main Prometheus server which scrapes and stores time series data
* client libraries for instrumenting application code
* a push gateway for supporting short-lived jobs
* special-purpose exporters for services like HAProxy, StatsD, Graphite, etc.
* an alertmanager to handle alerts
* various support tools

Most Prometheus components are written in Go, making them easy to build and deploy as static binaries.

#### Architecture

This diagram illustrates the architecture of Prometheus and some of its ecosystem components:

![prom-architecture](https://user-images.githubusercontent.com/18049790/64942969-fd028300-d859-11e9-9b13-20b7d6f14069.png)

## Digital Ocean

![image](https://user-images.githubusercontent.com/18049790/43352593-2dbb84de-9269-11e8-9ae9-374690064767.png)

Installation:
* Go to [Digital Ocean](https://www.digitalocean.com) and sign up or login.
  * Use this [referral link](https://m.do.co/c/ac62c560d54a) to get $10 in credit 
* Go to "Marketplace" on the left tab.
* Under "Find a Solution" click the "Kubernetes" tab.
* Click the "[Kubernetes Monitoring Stack](https://cloud.digitalocean.com/marketplace/5d163fdd29a6ab0d4c7d5274?i=9ca3ac)"
* Select "Create Cluster"
* Choose a datacentre region : Singapore
* Go to bottom of page and select "Create Cluster"
  * Cluster build usually takes four minutes
* On Getting Started Panel go to "3. Download the config file"
* Make the .kube directory : `mkdir ~/.kube`
* Under "Quick connect with manual certificate management" select "download the cluster configuration file"
* Download the `kubeconfig.yaml` to the `~/.kube` directory.
 * The authentication certificate in kubeconfig.yaml expires seven days after download

### Accessing the Digital Ocean Kubernetes cluster 

DigitalOcean Kubernetes clusters are typically managed from a local machine or sometimes from a remote management server. 

The management machine needs:

#### kubectl

* [kubectl](https://kubernetes.io/docs/reference/kubectl/overview), the official Kubernetes command-line tool, which you’ll use to connect to and interact with the cluster.
* The Kubernetes project provides [installation instructions](https://kubernetes.io/docs/tasks/tools/install-kubectl) for kubectl on a variety of platforms. 

Linux install instructions for kubectl
```
curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
alias k='cd ~/.kube && kubectl --kubeconfig="digital-ocean-cluster-kubeconfig.yaml"'
k version
```
* Use 'k version' to make sure that your installation is working and within one minor version of your cluster.

## Socks Shop

tl;dr - [Example microservices application](https://microservices-demo.github.io) 

Create a namespace for sock shop.

`k create namespace sock-shop`

Install the sock shop microservice application from this URL:

`k apply -n sock-shop -f "https://github.com/microservices-demo/microservices-demo/blob/master/deploy/kubernetes/complete-demo.yaml?raw=true"`

## Grafana

![image](https://user-images.githubusercontent.com/18049790/65003256-3c2ae580-d8e7-11e9-992d-30358d52e731.png)

Grafana is exposed via a DigitalOcean Load Balancer. 

You can get the IP address to access your Grafana instance either by looking for the IP within the Load Balancer dashboard, or by running the following in a terminal shell and copying the EXTERNAL-IP.

`k -n prometheus-operator get svc prometheus-operator-grafana`

Paste the EXTERNAL-IP or IP address found in the Load Balancer dashboard into your web browser.

The default username and password are `admin` and `changeme` respectively.

To change the password:
* Log into to Grafana with default username and password
* Click on the admin avatar in the bottom left in Grafana. (above the question mark)
* Click on preferences
* Click on Change password on middle tab
* Follow the instructions and click “Change password”

Once you have logged in the default Grafana Home dashboard will be displayed. 

To see cluster specific graphs enabled in this stack go to the “Home” menu in the upper left hand corner of your Grafana web browser page. 

Pull down the Home Menu and select a dashboard.
* Kubernetes/Compute Resources/Namespace (Pods)
* namespace: sock-shop

Explore other Prometheus datasource based Kubernetes dashboards at: https://grafana.com/dashboards?dataSource=prometheus&search=kubernetes

For more information on how to build your own dashboard check out: https://grafana.com/docs/guides/getting_started/

## metrics-server

The metrics-server provides cluster metrics, such as container CPU and memory usage via the Kubernetes Metrics API.

To view the metrics made available by metrics server, run the following command in a terminal shell:

`k top nodes`

or for all Kubernetes Namespaces enter:

`k top pods --all-namespaces`

As metrics server is running on your cluster you can also see metrics in the DigitalOcean Kubernetes Dashboard. 

To see your cluster metrics 
* go to https://cloud.digitalocean.com/kubernetes/clusters
* click on your cluster 
* click on “Insights” tab

For additional information on metrics-server see https://github.com/kubernetes-incubator/metrics-server.

## Socks Shop

Login to Grafana

Top left click on `Home`

Under `General` select `Kubernetes Pods`
* datasource: Prometheus
* Namespace: sock-shop
* Pod : front-end-xxxxxxxxxx (random numbers)
* Top Right click Clock Icon with text `Last 1 hour`
* Select `Last 5 minutes`
* Top Right click last icon that looks like Recycle Icon
* In drop down select `5s`

### Apache Benchmark (optional)

Now install Apache Benchmark to stress the micro-services application.

`sudo apt install apache2-utils`

Obtain the external IP address of Socks Shop.

`k -n sock-shop get svc front-end`

The IP address under EXTERNAL-IPis the external IP address of Socks Shop.

Use that address to stress the micro-services application.

Run the stress command a few times and observe the Grafana Dashboard.
`ab -n 1000 -c 5 -C "somecookie=rawr" http://<EXTERNAL-IP>/`

After a few minutes you should see something similar to this.

![grafana-ab](https://user-images.githubusercontent.com/18049790/65480844-829db880-de82-11e9-8b8c-e2b33605e3e1.png)

## Kube Monkey

Clone [Kube Monkey](https://github.com/asobti/kube-monkey/tree/master/helm/kubemonkey)

```
git clone https://github.com/asobti/kube-monkey
cd kube-monkey/examples
```
Update the Sock Shop Front End with Kube Monkey Labels: 

`vi front-end.yml`
```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: front-end
  namespace: sock-shop
  labels:
    kube-monkey/enabled: enabled
    kube-monkey/identifier: front-end
    kube-monkey/mtbf: '1'
    kube-monkey/kill-mode: "fixed"
    kube-monkey/kill-value: '2'
spec:
  replicas: 4
  template:
    metadata:
      labels:
        name: front-end
        kube-monkey/enabled: enabled
        kube-monkey/identifier: front-end
        kube-monkey/mtbf: '1'
        kube-monkey/kill-mode: "fixed"
        kube-monkey/kill-value: '2'
    spec:
      containers:
      - name: front-end
        image: weaveworksdemos/front-end:0.3.12
        resources:
          requests:
            cpu: 100m
            memory: 100Mi
        ports:
        - containerPort: 8079
        securityContext:
          runAsNonRoot: true
          runAsUser: 10001
          capabilities:
            drop:
              - all
          readOnlyRootFilesystem: true
      nodeSelector:
        beta.kubernetes.io/os: linux
```
Create Configuration Map for Kube Monkey for Socks Shop: 

`vi debug-mode-configmap.yaml`
```
apiVersion: v1
kind: ConfigMap
metadata:
  name: kube-monkey-config-map
  namespace: sock-shop
data:
  config.toml: |
    [kubemonkey]
    run_hour = 8                              # Run scheduling at 8am on weekdays
    start_hour = 10                           # Don't schedule any pod deaths before 10am
    end_hour = 16                             # Don't schedule any pod deaths after 4pm
    blacklisted_namespaces = ["kube-system"]  # Critical apps live here
    whitelisted_namespaces = ["sock-shop"]    # Target Namespace
    time_zone = "Singapore/Singapore"
    graceperiod_sec= 10
    [debug]
    enabled= true
    schedule_immediate_kill= true
```
Create the Kube Monkey Deployment for Socks Shop: 

`vi deployment.yaml`
```
  apiVersion: extensions/v1beta1
  kind: Deployment
  metadata:
    name: kube-monkey
    namespace: sock-shop
  spec:
    replicas: 1
    template:
      metadata:
        labels:
          app: kube-monkey
      spec:
        containers:
          -  name: kube-monkey
             command:
               - "/kube-monkey"
             args: ["-v=5", "-log_dir=/var/log/kube-monkey"]
             image: ayushsobti/kube-monkey:v0.3.0
             volumeMounts:
               - name: config-volume
                 mountPath: "/etc/kube-monkey"
        volumes:
          - name: config-volume
            configMap:
              name: kube-monkey-config-map
```
Create the file responsible for Role Base Access: 

`vi rbac.yaml`
```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: default
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: default
    namespace: sock-shop
```

Apply the mainifest files to install Kube Monkey.
```
k apply -f rbac.yaml
k apply -f debug-mode-configmap.yaml 
k apply -f deployment.yaml
k get deployments -n kube-system
```

## Locust

Install [Locust](https://locust.io/)

`python -m pip install locustio`

Restart terminal for install to complete

Configure Locust: 
```
mkdir locust
cd locust
vi locustfile.py
```

vi locustfile.py
```
from locust import HttpLocust, TaskSet, task

class WebsiteTasks(TaskSet):
    @task
    def index(self):
        self.client.get("/")

class WebsiteUser(HttpLocust):
    task_set = WebsiteTasks
    min_wait = 5000
    max_wait = 15000
```

Obtain the external IP address of Socks Shop.

`k -n sock-shop get svc front-end`

The IP address under EXTERNAL-IPis the external IP address of Socks Shop.

Use that address to stress the micro-services application.

Start locust : `locust --host=http://<EXTERNAL-IP>`

Browse to : `http://127.0.0.1:8089/`
* Number of Users to Simulate: 500
* Hatch Rate: 10

On main panel select `Charts`
Top Right note Failures are 0%

From the terminal kill front-end pods to simulate chaos.

```
k get pods 
k delete pod front-end-xxxxxxx
```

Observe in the Locust page that Failures are still 0%

*End of Section*
