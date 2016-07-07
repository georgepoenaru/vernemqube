# docker-vernemq

## What is VerneMQ?

VerneMQ is a high-performance, distributed MQTT message broker. It scales
horizontally and vertically on commodity hardware to support a high number of
concurrent publishers and consumers while maintaining low latency and fault
tolerance. VerneMQ is the reliable message hub for your IoT platform or smart
products.

VerneMQ is an Apache2 licensed distributed MQTT broker, developed in Erlang.

## How to use this image

### Start a VerneMQ cluster node

    docker run --name vernemq1 -d erlio/docker-vernemq

This starts a new node that listens on 1883 for MQTT connections and on 8080 for MQTT over websocket connections. However, at this moment the broker won't be able to authenticate the connecting clients. To allow anonymous clients use the ```DOCKER_VERNEMQ_ALLOW_ANONYMOUS=on``` environment variable.

    docker run -e "DOCKER_VERNEMQ_ALLOW_ANONYMOUS=on" --name vernemq1 -d erlio/docker-vernemq

### Autojoining a VerneMQ cluster in docker environment

This allows a newly started container to automatically join a VerneMQ cluster. Assuming you started your first node like the example above you could autojoin the cluster (which currently consists of a single container 'vernemq1') like the following:

    docker run -e "DOCKER_VERNEMQ_DISCOVERY_NODE=<IP-OF-VERNEMQ1>" --name vernemq2 -d erlio/docker-vernemq

(Note, you can find the IP of a docker container using `docker inspect <containername/cid> | grep \"IPAddress\"`).

### Autojoining a VerneMQ cluster in Kubernetes environment

This allows a replica controller pod to automatically join a VerneMQ cluster. You need to have a kuberentes pod ip finder running on your kuberenetes cluster. More details [here](https://github.com/thesandlord/kubernetes-pod-ip-finder/)

Make sure you setup the node discovery URL in your Replica Controller, Deployment, or Replica Set

    env:
      - name: KUBE_VERNEMQ_DISCOVERY_URL
      #Considering namespace = default. Change according  your namespace Eg: kubernetes-pod-ip-finder.dev.svc.cluster.local/?app=broker
      value: http://kubernetes-pod-ip-finder.default.svc.cluster.local/?label=value


### Checking cluster status

To check if the bove containers have successfully clustered you can issue the ```vmq-admin``` command:

    docker exec vernemq1 vmq-admin cluster status
    +--------------------+-------+
    |        Node        |Running|
    +--------------------+-------+
    |VerneMQ@172.17.0.151| true  |
    |VerneMQ@172.17.0.152| true  |
    +--------------------+-------+

All ```vmq-admin``` commands are available. See https://vernemq.com/docs/administration/ for more information.
