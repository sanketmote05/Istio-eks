# Istio-eks

Exercise for setting up and using Istio in an EKS (Amazon Elastic Kubernetes Service) cluster. This will give students hands-on experience with Istio service mesh in EKS:

### Exercise: Deploying and Exploring Istio Service Mesh on EKS

#### Prerequisites:
- Basic knowledge of Kubernetes and EKS
- An EKS cluster with kubectl configured to interact with it
- AWS CLI configured with the necessary permissions
- Helm installed on the local machine

### Part 1: Setting up the EKS Cluster (if not already done)
1. **Create an EKS Cluster:**
   Use eksctl to create a cluster:
   ```bash
   eksctl create cluster --name istio-cluster --version 1.25 --region <region-name> --nodegroup-name linux-nodes --nodes 2 --nodes-min 1 --nodes-max 3 --node-type t3.medium --with-oidc --ssh-access --ssh-public-key <your-key-name> --managed
   ```
   
2. **Verify cluster:**
   ```bash
   kubectl get svc
   ```

### Part 2: Installing Istio on EKS

1. **Download Istio CLI:**
   Download and install Istioctl, which is the CLI tool for Istio.
   ```bash
   curl -L https://istio.io/downloadIstio | sh -
   cd istio-<version>
   export PATH=$PWD/bin:$PATH
   ```
   
2. **Install Istio on the cluster:**
   Use the demo profile for learning purposes:
   ```bash
   istioctl install --set profile=demo -y
   ```
   
3. **Label the default namespace for automatic sidecar injection:**
   ```bash
   kubectl label namespace default istio-injection=enabled
   ```

4. **Verify the installation:**
   ```bash
   kubectl get pods -n istio-system
   ```

### Part 3: Deploying a Sample Application on the Istio Mesh

1. **Deploy the Bookinfo Application:**
   Istio’s Bookinfo is a simple application consisting of four microservices. Deploy it as follows:
   ```bash
   kubectl apply -f samples/bookinfo/platform/kube/bookinfo.yaml
   ```
   
2. **Verify the services are deployed:**
   ```bash
   kubectl get services
   ```

3. **Apply the Gateway:**
   This exposes the application outside the service mesh.
   ```bash
   kubectl apply -f samples/bookinfo/networking/bookinfo-gateway.yaml
   ```

4. **Confirm the gateway is created:**
   ```bash
   kubectl get gateway
   ```

5. **Determine the ingress IP and Port:**
   Get the external IP of the ingress gateway to access the Bookinfo application.
   ```bash
   kubectl get svc istio-ingressgateway -n istio-system
   ```
   Then access the application in a browser using the external IP and port.

### Part 4: Observing Istio Features

1. **Traffic Routing:**
   Create a destination rule for one of the services, `reviews`, which has multiple versions:
   ```bash
   kubectl apply -f samples/bookinfo/networking/destination-rule-all.yaml
   ```

2. **Visualizing Service Mesh with Kiali:**
   Install Kiali (service graph visualization tool):
   ```bash
   kubectl apply -f samples/addons
   kubectl port-forward svc/kiali -n istio-system 20001:20001
   ```
   Then visit `http://localhost:20001` to view the Istio service mesh visualization.

3. **Monitoring with Prometheus & Grafana:**
   - Access Prometheus: `kubectl port-forward svc/prometheus -n istio-system 9090:9090`
   - Access Grafana: `kubectl port-forward svc/grafana -n istio-system 3000:3000`
   
   Use Grafana to view Istio dashboards for traffic metrics, error rates, etc.

### Part 5: Cleanup

1. **Remove the Bookinfo application:**
   ```bash
   kubectl delete -f samples/bookinfo/platform/kube/bookinfo.yaml
   ```

2. **Uninstall Istio:**
   ```bash
   istioctl uninstall --purge
   ```

3. **Delete the EKS Cluster (if necessary):**
   ```bash
   eksctl delete cluster --name istio-cluster
   ```

---

### Exercise Objectives:
- Understand the core components of Istio and how they integrate with Kubernetes.
- Deploy and expose a microservices application using Istio.
- Explore Istio’s observability tools such as Kiali, Grafana, and Prometheus.
- Learn how to configure traffic routing within the service mesh.

This exercise should provide a good foundation in setting up and managing Istio on EKS.
