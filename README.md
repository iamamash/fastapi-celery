# Project Overview

This project serves as a comprehensive example demonstrating the integration of FastAPI, Celery with RabbitMQ as the task queue, Redis as the Celery backend, and Flower for monitoring Celery tasks. By combining these technologies, this project showcases a powerful asynchronous task processing system with a robust monitoring solution.

## Features

### FastAPI
FastAPI is a modern, fast (high-performance), web framework for building APIs with Python 3.7+. It offers tremendous speed and efficiency in handling requests and responses, making it an ideal choice for developing RESTful APIs.

### Celery
Celery is an asynchronous task queue/job queue based on distributed message passing. It allows for the execution of tasks asynchronously, making it perfect for handling time-consuming operations in the background while keeping your application responsive.

### RabbitMQ
RabbitMQ serves as the message broker in this setup, facilitating communication between FastAPI and Celery. It ensures reliable message delivery and enables efficient communication between different parts of the application.

### Redis
Redis is utilized as the Celery backend, providing a robust storage solution for task results, states, and other essential metadata. It plays a crucial role in maintaining the state of the asynchronous tasks managed by Celery.

### Flower
Flower is a real-time web-based monitoring tool for Celery. It provides a user-friendly dashboard to monitor task progress, success rates, failure rates, and other essential metrics, offering insights into the performance of Celery workers.

## Getting Started

### Prerequisites for running application/worker
- [Docker](https://docs.docker.com/engine/install/)
- [Docker-compose](https://docs.docker.com/compose/install/)

### Running the Project with Docker

1. **Start Services:**
   Run `docker-compose up` in your terminal to launch RabbitMQ, Redis, Flower, and the application/worker instances.

2. **Access API Documentation:**
   Visit [http://localhost:8000/docs](http://localhost:8000/docs) in your browser to explore available endpoints and their functionalities.

3. **Execute Test API Calls:**
   Utilize the API documentation to perform test API calls and interact with the application.

4. **Monitor Celery Tasks:**
   - **Console Logs:** Keep an eye on the console logs to track Celery task execution in real-time.
   - **Flower Monitoring App:** Access [http://localhost:5555](http://localhost:5555) in your browser, using:
     - **Username:** user
     - **Password:** test
   
   This dashboard provides insights into Celery task progress, success rates, and failures.

### Run the project without Docker

#### Requirements/Dependencies

- **Python >= 3.7**
  - Utilize [poetry](https://python-poetry.org/docs/#installation) for dependency management.
- **RabbitMQ instance**
- **Redis instance**

> Start RabbitMQ, Redis, and Flower services using ```docker-compose -f docker-compose-services.yml up```.

#### Install Dependencies

Execute the following command: ```poetry install --dev```

#### Run FastAPI App and Celery Worker

1. **Start the FastAPI Web Application:**
   Execute ```poetry run hypercorn app/main:app --reload``` to launch the FastAPI web server.

2. **Start the Celery Worker:**
   Run the command ```poetry run celery worker -A app.worker.celery_worker -l info -Q test-queue -c 1``` to initiate the Celery worker.

3. **Access API Documentation:**
   Open [http://localhost:8000/docs](http://localhost:8000/docs) in your browser to explore and test API endpoints.

4. **Monitor Celery Tasks:**
   - **Console Logs:** Observe the console logs to track real-time Celery task execution.
   - **Flower Monitoring App:** Visit [http://localhost:5555](http://localhost:5555) in your browser, using:
     - **Username:** user
     - **Password:** test
   Access the Flower monitoring dashboard to monitor Celery task progress and status.

---

## Deployment of FastAPI-Celery

### Setup Implementation Overview

1. **GitHub Actions for Continuous Deployment (CD):**
   Leveraged GitHub Actions to establish a robust Continuous Deployment pipeline. Changes automatically deploy to a Kubernetes cluster upon any push to the master branch or merging of a pull request (PR).

2. **AWS EKS Integration:**
   Implemented a Kubernetes (k8s) cluster on an AWS EC2 instance using AWS EKS. This setup facilitates the deployment of the FastAPI-Celery application and enables autoscaling based on incoming traffic requests.

#### AWS EKS Configuration Details

- **Services Utilized:**
  - AWS CloudFormation
  - AWS CloudWatch
  - AWS EC2
  - Other AWS services integrated for a secure deployment environment.

This deployment setup utilizing GitHub Actions for CD and AWS EKS for Kubernetes cluster management ensures efficient deployment, scalability, and a secure environment for the FastAPI-Celery application.

### Implementation Steps

1. **Clone GitHub Repository:**
   Use the command `git clone https://github.com/iamamash/fastapi-celery.git` to clone the project repository.

2. **Install Docker and Docker-Compose:**
   Install Docker and Docker-Compose, granting necessary permissions to Docker for operation:
   ```bash
   sudo apt update
   sudo apt install docker.io
   sudo apt install docker-compose
   sudo chown $USER /var/run/docker.sock  # Grant permissions to Docker for the user
   ```

3. **AWS IAM Setup:**
   - Create an "eks-admin" user in the AWS IAM service with AdministratorAccess permissions.
   - Generate Security Credentials (Access Key and Secret access key).

4. **Install AWS CLI:**
   Configure AWS CLI on your server to access AWS services:
   ```bash
   curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
   sudo apt install unzip
   unzip awscliv2.zip
   sudo ./aws/install -i /usr/local/aws-cli -b /usr/local/bin --update
   aws configure  # Provide access key, secret access key, and region
   ```

5. **Install Kubectl:**
   Fetch and install Kubectl for managing Kubernetes clusters:
   ```bash
   curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.19.6/2021-01-05/bin/linux/amd64/kubectl
   chmod +x ./kubectl
   sudo mv ./kubectl /usr/local/bin
   kubectl version  # Check version
   ```

6. **Install eksctl:**
   Obtain and install eksctl for managing Amazon EKS clusters:
   ```bash
   curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
   sudo mv /tmp/eksctl /usr/local/bin
   eksctl version  # Check version
   ```

7. **Cluster Setup:**
   - Create and update the cluster using eksctl and AWS CLI:
   ```bash
   eksctl create cluster --name <cluster-name> --region <region-name> --node-type <instance-type> --nodes-min <minimum-nodes> --nodes-max <maximum-nodes>
   aws eks update-kubeconfig --region <region-name> --name <cluster-name>
   kubectl get nodes  # View all nodes
   ```

8. **Apply Kubernetes Manifests:**
   - Apply configurations and check status using kubectl:
   ```bash
   kubectl apply -f k8s/
   kubectl get all  # View nodes, services, deployments, and pods
   kubectl delete -f k8s/  # Delete configurations
   ```

9. **Cluster Deletion:**
   - Delete the cluster using eksctl:
   ```bash
   eksctl delete cluster --name <cluster-name> --region <region-name>
   ```
Following these steps will guide you through setting up Docker, AWS IAM, AWS CLI, Kubernetes tools, cluster creation, applying manifests, managing configurations, and deleting the cluster for the FastAPI-Celery project.

---

### Challenges Faced

1. **Understanding the Application Architecture:**
   The initial challenge involved comprehending the intricacies of the application architecture. Being new to technologies like Celery, RabbitMQ, FastAPI, and Flower posed a learning curve to quickly grasp the entire setup and apply it effectively within this project.

2. **Container Initialization Error:**
   Upon cloning the project and attempting to run it following the README.md instructions, encountered errors with the `fastapi-celery` and `celery-worker` containers. Investigation via Docker container logs revealed issues within the application code, requiring modifications to enable container execution.

   <img width="550" alt="Screenshot 2024-01-07 134100" src="https://github.com/iamamash/fastapi-celery/assets/42666741/b2bcd8e1-70e8-48d7-9b2b-ba8aed201b9e">


3. **Transitioning from DevOps to Development Perspective:**
   While having proficiency in Python from a DevOps standpoint for scripting and automation, adapting to a developer's role within this project demanded a shift in approach. Identifying code issues and debugging became essential.

4. **Implementing Horizontal Pod Autoscaler (HPA):**
   Configuring HPA for the application wasn't inherently challenging, but synchronizing it with real-time incoming traffic requests posed complexity and required substantial time investment to resolve.

5. **Pods Stuck in Pending State in Kubernetes Cluster:**
   Post applying Kubernetes configurations, encountered pods stuck in a pending state. Resolving this issue consumed significant time and effort before achieving resolution.

These challenges encompassed various aspects, from learning new technologies to debugging application code, implementing scalable solutions, and troubleshooting Kubernetes cluster issues during the project's development cycle.

---

### Confronting the Challenges

1. **Understanding Application's Architecture:**
   Leveraged insights from blog posts shared by other developers who had worked on similar tech stacks. These resources significantly aided in comprehending the project's architecture, enabling a clearer understanding of the utilized technologies. [Link to Blog Post](https://markgituma.medium.com/kubernetes-local-to-production-with-django-1-introduction-d73adc9ce4b4)

2. **Bug Fixing and Troubleshooting:**
   Utilized the unparalleled resource, Stack Overflow, to resolve the container error encountered. This exploration not only addressed the immediate issue but also introduced new concepts and solutions to enhance debugging skills.

3. **Adapting to Python Development:**
   Transitioning to Python development posed a challenge, but a GitHub repository from a contributor of the owner's repository served as an invaluable resource. This repository offered guidance and support, facilitating familiarity and proficiency with Python within the project's context.

4. **Learning Horizontal Pod Autoscaler (HPA):**
   Acquired knowledge on Horizontal Pod Autoscaler (HPA) from diverse sources including the official Kubernetes documentation and supplementary blog posts and YouTube tutorials. These resources provided comprehensive insights into HPA implementation. [Kubernetes Documentation](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) | [Additional Blog](https://medium.com/@danielepolencic/request-based-autoscaling-in-kubernetes-scaling-to-zero-f61e64bc4a52)

5. **Resolving Pending Pods Issue in Kubernetes:**
   Identified that the root cause behind the pods' pending state was attributed to using only one node in the Kubernetes cluster, resulting in exhausted memory. Addressed this by expanding knowledge through Stack Overflow solutions, ultimately resolving the issue.

These strategies helped tackle challenges encountered during the project, leveraging external resources, community insights, and targeted learning to overcome obstacles in understanding, debugging, skill adaptation, and troubleshooting within the GitHub project environment.

---

### Pros and Cons of Approach

#### Pros

1. **Structured Development Workflow:**
   Adopted a structured approach by creating a separate development branch (`dev`) instead of directly pushing changes to the master branch. This practice adheres to best practices, ensuring a systematic development and deployment process.

2. **Secure Continuous Deployment Pipeline:**
   Utilized Secret variables within GitHub Actions to securely manage critical information, such as passwords, ensuring a robust and secure Continuous Deployment (CD) pipeline.

3. **Implementation of Best Practices:**
   Incorporated best practices essential for real-time projects, including encrypted sharing of sensitive information via config files using Secret config files in Kubernetes.

4. **Security and High Availability Measures:**
   Ensured security and high availability by deploying the Kubernetes cluster within a Virtual Private Cloud (VPC), mitigating unauthorized access to the cluster.

5. **Enhanced Security with AWS IAM Roles:**
   Leveraged AWS IAM roles to fortify security measures, adding an additional layer of security in the AWS environment.

#### Cons

1. **Overly Permissive AWS IAM Access:**
   Granted AWS IAM Administrator Access for expedited project delivery. However, in a production environment, this practice poses security risks and is not recommended due to its extensive permissions.

2. **Limited Branching Strategy:**
   While implementing a separate development branch (`dev`) is commendable, adopting a more comprehensive branching strategy, such as feature branches or release branches, could enhance collaboration and version control in larger-scale projects.
