# 🚀 Enterprise CI/CD & Zero-Downtime Deployments (Hybrid Architecture)

## 📌 Project Overview
This project demonstrates an end-to-end, fully automated Continuous Integration and Continuous Deployment (CI/CD) pipeline for a Python/gRPC microservice (`emailservice`). 

**The Engineering Challenge:** Architect an enterprise-grade CI/CD workflow and Kubernetes cluster on a local environment. 
**The Solution:** A custom **Hybrid Cloud-Local Architecture**. By offloading heavy compute tasks (like static code analysis and artifact storage) to Cloud SaaS providers, I successfully orchestrated Jenkins, Docker, Minikube, and Argo Rollouts locally without exceeding hardware limits.

This project showcases expertise in CI/CD automation, DevSecOps principles, container orchestration, and advanced Kubernetes deployment strategies.

---

## 🏗️ Architecture: The "8GB RAM Survival Stack"

To prevent Out-Of-Memory (OOM) crashes while simulating an enterprise environment, the architecture is split between resource-restricted local containers and free-tier cloud services:

* **Local Compute (WSL/Ubuntu):** * **Minikube:** Capped at 2GB RAM / 2 CPUs.
*  * **Jenkins:** Containerized and strictly constrained to 512MB RAM using Java environment variables (`-Xmx512m`).
* **Cloud Offloading (SaaS):**
    * **SonarCloud:** Replaced local SonarQube to handle heavy static code analysis over the internet.
    * **Docker Hub:** Replaced local Nexus registry for lightweight artifact storage.

---

## ⚙️ The CI/CD Pipeline Lifecycle

The pipeline is defined entirely as code (`Jenkinsfile`) and executes the following stages automatically upon a Git push:

1.  **Checkout Code:** Pulls the latest source code from the `main` branch.
2.  **Code Quality & Security Scan (DevSecOps):** Injects the SonarScanner CLI to analyze the Python codebase for bugs, vulnerabilities, and code smells, sending the report to SonarCloud.
3.  **Build Container:** Uses the host PC's Docker daemon to build a highly optimized Docker image of the `emailservice`.
4.  **Push Artifact:** Authenticates securely via Jenkins credentials and pushes the versioned Docker image to Docker Hub.
5.  **Deploy to Kubernetes:** Dynamically updates the Kubernetes manifests with the new image tag and applies the configuration to the local Minikube cluster.

---

## 🔄 Zero-Downtime Deployments (Argo Rollouts)



Standard Kubernetes rolling updates can still result in dropped requests. To guarantee **Zero-Downtime**, this project implements a **Blue-Green Deployment Strategy** using **Argo Rollouts**.

* **Active Service:** Routes 100% of live traffic to the stable (Blue) version.
* **Preview Service:** Routes to the newly deployed (Green) version, allowing for isolated integration testing.
* **Controlled Promotion:** The pipeline automatically pauses the rollout. Once the Green version is verified via the preview service, a manual promotion (`kubectl argo rollouts promote`) instantly flips the traffic router to the new version and safely spins down the old pods.

---

## 🛠️ Technologies & Tools Used

* **Version Control:** Git, GitHub
* **CI/CD Orchestration:** Jenkins (Pipeline as Code)
* **Containerization:** Docker, Docker Hub
* **Code Quality/Security:** SonarCloud
* **Container Orchestration:** Kubernetes, Minikube
* **Advanced Deployment:** Argo Rollouts (Blue-Green)
* **Infrastructure:** Windows Subsystem for Linux (WSL), Ubuntu

---

## 🧠 Key Learnings & Engineering Workarounds

* **Resource Management:** Successfully ran a heavy Java/Kubernetes stack on 8GB RAM by mapping the host's Docker socket into a heavily restricted Jenkins container, proving an understanding of container limits and OS resources.
* **Network Integration:** Configured a Dockerized Jenkins instance on a `--network host` to seamlessly communicate with the host's Minikube cluster and local `.kube` configurations.
* **GitOps Fundamentals:** Separated the application code from the deployment state by templating Kubernetes manifests and manipulating them dynamically within the pipeline.

---

## 👤 Author

**Lucky Philip (Phil)** DevOps Engineer

* **Brand:** Phil's Tech Hub
* **Content Creation:** Exploring Cloud, DevOps, and Tech Discoveries on [Lucky TubeTips](#) & [Lucky Phil's Discoveries](#).
