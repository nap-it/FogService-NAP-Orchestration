# FogService-NAP-Orchestration
## Motivation
Service deployment with Kubernetes can be a complex process with a steep learning curve, which becomes even more challenging in a heterogeneous and limited environment such as the edge. Moreover, the Kubernetes platform, despite its extensive community support and high popularity, is not prepared for the edge environment, as it is designed for the cloud computing ecosystem, which contains much more powerful hardware, and is not well suited for use in low-powered devices.


## Orchestration Strategies for Time-Constrained Smart City Services
To address this issue, a set of Kubernetes extensions was developed and tested to enable the deployment of services optimized for edge environments within the research group Network Architectures and Protocols (NAP). These extensions provide capabilities such as support for both legacy and real-time services, continuous service monitoring to identify suboptimal conditions, and an improved load-balancing system that considers service-specific metrics and edge-node latency to achieve faster response times.

## Repositories
The following list of Kubernetes extensions that implement the proposed time-constrained smart city orchestration is introduced according to their specific purpose.

### Pre-Requisites
All of the following components assume the existence of a Kubernetes cluster running the lightweight edge-oriented distribution K3s, with a Prometheus server installed via a Helm chart to ensure observability of both the cluster and deployed applications.

### Latency Monitor
This represents the key component desgined to measure the latency between the master node and worker nodes in an edge time-constrained environment. It performs latency measurements from the master node to the other nodes and publishes such latency values in a Prometheus server. It was enviosioned to expose these latency metrics with the goal of them being used in orchestration decisions such as routing of requests, replica scheduling, etc.
* https://github.com/nap-it/latency-monitor

### Fog Operator
Includes the FogService CRD definition that allows the instantiation of FogService resources and the corresponding FogService Controller responsible for performing state reconciliation of the FogService objects. This new type of FogService resource, enable the possibility to define the deployment of services optimized for the edge environment by providing capabilities such as higher grained control over service deployment behavior (e.g., instantiation locations, number of replicas, instantiation based on node's capabilities) and the possibility to define service-specific metrics that can be used to assess the status of a certain replica and influence user requests and the replica's scheduling.
* https://github.com/nap-it/fog-operator

### Load Balancer
Kubernetes creates the necessary Iptables network rules through the Kube-Proxy that runs on each cluster node in the appropriate chains and tables to ensure that the user's requests reach their correct destination. However, the default Kube-Proxy load-balancing algorithm ensures that each replica receives the same amount of network requests as the other ones. In edge road environments, such logic is inadequate because of the dynamic nature of the ecosystem, and factors such as user proximity to the replicas, user mobility, service, and network performance are ignored. To address this issue, this custom Load Balancer service desgined. This service runs in each node alongside the Kube-Proxy, and periodically iterates over the list of Service resources to overwrite the probabilities of each replica to receive a request, taking into account the service-specific metrics and the network latency reflected on the cluster.
* [https://github.com/nap-it/fog-operator](https://github.com/nap-it/load-balancer)

### Descheduler
In the edge-cloud environments, node conditions can change quickly and unpredictably. Instead of relying only on static balancing policies, this component called the Descheduler introduces strategies that react to degraded nodes and to real scheduling pressure observed in the cluster.The project combines custom descheduling logic, including BetterNode and DegradedNode strategies, with a modified cluster-capacity simulation flow to estimate placement effects before large-scale changes.
* https://github.com/nap-it/descheduler

### Scheduler-Plugins
This project extends the Kubernetes scheduling pipeline with custom plugins designed for service placement in edge-cloud infrastructures. Instead of treating all nodes as equivalent, it introduces decision logic that accounts for degraded performance, runtime node metrics, service dependencies, and realtime constraints. The project is packaged to run as a dedicated secondary scheduler through Helm, making it practical to evaluate advanced policies without replacing the cluster's default scheduler behavior. It allows critical and latency-sensitive workloads to be placed with more context-aware criteria, particularly in smart-city and distributed computing environments.
* https://github.com/nap-it/scheduler-plugins


### Multi-Cri
Multi-cri acts as an integration layer between Kubernetes and runtime environments that do not fit a single standard backend model. It exposes a CRI-compatible interface to the kubelet while internally translating requests through adapter logic, allowing one control path to orchestrate different execution mechanisms. Beyond basic request forwarding, the project includes systemd-oriented container operations, automated image handling workflows, and support for realtime scheduling configuration when services require strict latency behavior.
* https://github.com/nap-it/multi-cri

## Citation

Text format:
```sh
R. Rosmaninho, D. Raposo, P. Rito and S. Sargento, "Edge-Cloud Continuum Orchestration of Critical Services: A Smart-City Approach," in IEEE Transactions on Services Computing, vol. 18, no. 3, pp. 1381-1396, May-June 2025, doi: 10.1109/TSC.2025.3568251. keywords: {Resource management;Real-time systems;Computer architecture;Monitoring;Training;Quality of service;Network topology;Load management;5G mobile communication;Topology;Edge computing;smart-city;real-time;Kubernetes;MEC;5G},
```
BibText format:
```
@ARTICLE{10994362,
  author={Rosmaninho, Rodrigo and Raposo, Duarte and Rito, Pedro and Sargento, Susana},
  journal={IEEE Transactions on Services Computing}, 
  title={Edge-Cloud Continuum Orchestration of Critical Services: A Smart-City Approach}, 
  year={2025},
  volume={18},
  number={3},
  pages={1381-1396},
  keywords={Resource management;Real-time systems;Computer architecture;Monitoring;Training;Quality of service;Network topology;Load management;5G mobile communication;Topology;Edge computing;smart-city;real-time;Kubernetes;MEC;5G},
  doi={10.1109/TSC.2025.3568251}}
```

## License

Latency Monitor is under [LGPL v3](./LICENSE) and [GPL v3](./LICENSE) licenses.


