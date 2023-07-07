---
description: Your Dream MLOps Setup by running just one command
---

# ✨ Docker-Compose Magic

Note: This is a ‘how-to’ blog for taking your first step in creating a beginner-friendly MLOps platform setup using docker-compose. \
&#x20;

If you are an individual with a passion for MLOps or have a new budding MLOps team, it might be difficult for you to come up with your own MLOps platform.&#x20;

&#x20;\
But, you still would be using a wide set of tools for your day-to-day ML operations. In my [last blog](https://intuitive.cloud/blog/props-to-mlops-go-dev-to-prod-the-smart-way), I shared an overview of how a MLOps architecture should look like.&#x20;

<figure><img src="../.gitbook/assets/Untitled Diagram.drawio (4).png" alt=""><figcaption></figcaption></figure>

You would find tools for each step in this demonstrated architecture. Let me give you an example. Just for Data Analysis, we have a lot of options like:&#x20;

* [Pandas Profiling](https://github.com/ydataai/pandas-profiling) - Create HTML profiling reports from pandas DataFrame objects.&#x20;
* [Apache Zeppelin](https://zeppelin.apache.org/) - Enables data-driven, interactive data analytics and collaborative documents.&#x20;
* [BambooLib](https://github.com/tkrabel/bamboolib) - An intuitive GUI for Pandas DataFrames.&#x20;
* [DataPrep](https://github.com/sfu-db/dataprep) - Collect, clean and visualize your data in Python.&#x20;
* [Google Colab](https://colab.research.google.com/) - Hosted Jupyter notebook service that requires no setup to use.&#x20;
* [Jupyter Notebook](https://jupyter.org/) - Web-based notebook environment for interactive computing.&#x20;
* [JupyterLab](https://jupyterlab.readthedocs.io/) - The next-generation user interface for Project Jupyter.&#x20;
* [Polynote](https://polynote.org/) - The polyglot notebook with first-class Scala support.&#x20;

Same goes for each and every step of the ML processes like Model workflow orchestration, training & experimentation, versioning & registry, Serving and Monitoring. &#x20;

Since this blog is focused on how to create your dream MLOps stack setup and not any particular services, I will get into the ‘how-to' now. \
&#x20;

I have two words for you. **Docker Compose**.  Docker Compose is the magic wand which can run multiple containers as a single service. How will that be useful to us? When you imagine yourself using a MLOps platform, you would be performing different activities like experimentation, model versioning, model serving, performance monitoring etc. And you would be using different products for each of these requirements.&#x20;

Essentially, you can think of these products as microservices. We can assign each of these microservices to separate containers. Because every product can have a different set of dependencies like programming languages, databases etc. &#x20;

So, when we have a different container for each of these microservices, the setup becomes easier to build and manage. Then you combine all these containers using docker-compose and voila, you have an application ready to use!&#x20;

Okay, so how do we do it?&#x20;

<figure><img src="../.gitbook/assets/Untitled Diagram.jpg" alt=""><figcaption></figcaption></figure>

Creating a MLOps (Machine Learning Operations) platform using Docker Compose can greatly simplify the deployment and management of your machine learning workflows. A Docker Compose file is written in YAML (Yet Another Markup Language) format. It allows you to define and configure multiple containers and their associated services in a single file. Here's a step-by-step guide on how to use Docker Compose to build an MLOps platform:&#x20;

&#x20;\
Consider that, we are using Prefect for ML workflow orchestration, MLflow for experiment tracking & model registry, Streamlit for frontend, FastAPI for API performance testing, Prometheus & Grafana for monitoring and SQLite as MLflow database. &#x20;

**Step 1: Define the Services**&#x20;

Let’s start by defining the services required for our MLOps platform.&#x20;

&#x20;\
**1. Model Training + Workflow Orchestration Service :** The Model training service runs the machine learning training jobs. It can be based on a specific machine learning framework such as Sci-kit Learn, TensorFlow or PyTorch. And the workflow orchestration service handles the orchestration and scheduling of machine learning workflows.&#x20;

**2. Model Serving Service:** This service serves the trained models via REST APIs or other protocols. It allows for easy integration with other applications.&#x20;

**3. Data Storage Service:** This service manages the storage of training and inference data. You can use a database like PostgreSQL or a distributed storage system like Apache Hadoop HDFS.&#x20;

**4. Monitoring Service:** This service collects and analyzes metrics, logs, and performance data from the training and serving components.  \
&#x20;

**Step 2: Define Networks and Volumes**&#x20;

Next, define the networks and volumes required for communication and data storage within the MLOps platform. Consider creating separate networks for different services to isolate their communication and ensure security. Define volumes to persist data across container restarts and updates.&#x20;

**Step 3: Configure Environment Variables**&#x20;

Configure environment variables for each service to customize their behavior. For example, you might set environment variables to specify the data storage location, model serving port, or training parameters.&#x20;

**Step 4: Define Dependencies**&#x20;

Specify the dependencies between services. For example, the model serving service may depend on the model training service to ensure that the latest trained model is available for serving. Use the \`depends\_on\` attribute in the Docker Compose file to define these dependencies.&#x20;

&#x20;\
Here is a sample docker-compose file&#x20;

```yaml
version: "3.9" 

services: 

  api: 
    build: api 
    ports: 
      - 8086:8086 
    volumes: 
      - runs:/mlruns 
 
   app: 
    build: app 
    ports: 
      - 8501:8501 

  train: 
    build: training 
    ports: 
      - 4200:4200 
    volumes: 
      -  runs:/mlruns 

  mlflow: 
    build: mlflow 
    ports: 
      - 5000:5000 
    volumes: 
      -  runs:/mlruns 

  prometheus: 
    image: prom/prometheus:latest 
    ports: 
      - 9090:9090 
    volumes: 
      - ./monitoring/prometheus.yml:/etc/prometheus/prometheus.yml 

    command: 
      - "--config.file=/etc/prometheus/prometheus.yml" 

  grafana: 
    image: grafana/grafana:latest 
    user: "472" 
    depends_on: 
      - prometheus 
    ports: 
      - 3000:3000 

    volumes: 
      - ./monitoring/datasource.yml:/etc/grafana/provisioning/datasources/datasource.yml 
      - ./monitoring/dashboard.json:/etc/grafana/provisioning/dashboards/dashboard.json 
      - ./monitoring/dashboard.yml:/etc/grafana/provisioning/dashboards/default.yml 

volumes: 
  runs: 
```

Note that the build component creates Docker images for each service in your MLOps platform. Dockerfiles define the instructions to build each image, including the required dependencies and configurations. I suggest creating a different directory for each service with their respective Dockerfiles in order to create individual containers dedicated to a specific service. These directories shall have the scripts, requirements etc. for that service. &#x20;

A Dockerfile should look like this:&#x20;

```docker
# Use a base image from Docker Hub  
FROM base_image:tag  

# Set the working directory inside the container  
WORKDIR /app (e.g WORKDIR /mlflow) 

# Copy the application files to the container  
COPY . /app (e.g COPY requirements.txt ./)

# Install dependencies or run any necessary commands  
RUN command1 (e.g RUN pip install -r requirements.txt) 
RUN command2  

# Expose a port (if required)  
EXPOSE port_number (e.g EXPOSE 5000) 

# Define environment variables (if required)
ENV key=value (e.g ENV MLFLOW_TRACKING_URI=http://localhost:5000) 

# Define the default command to run when the container starts
CMD [ "command_to_run" ] (e.g CMD ["sh", "-c", "mlflow server --backend-store-uri sqlite:////runs/mlflow.db --default-artifact-root file:///runs --host=0.0.0.0"]) 
```

The directory structure should look like this.

<figure><img src="../.gitbook/assets/Screenshot 2023-07-07 181105.png" alt=""><figcaption></figcaption></figure>

**Start the MLOps Platform**&#x20;

Use the magic  \`docker-compose up\` command to start the MLOps platform. Docker Compose will automatically create and manage the containers for each service based on the configuration in the Docker Compose file.&#x20;

&#x20;

**Scale and Manage the Platform**&#x20;

You can scale the services in your MLOps platform using the \`docker-compose up --scale \<service\_name>=\<num\_instances>\` command. This allows you to increase the capacity of a particular service as needed. \
&#x20;

**Conclusion**&#x20;

Using Docker Compose to create an MLOps platform provides a consistent and reproducible environment for your machine learning workflows. By defining services, networks, volumes, and dependencies in a Docker Compose file, you can easily deploy and manage your MLOps platform. Docker Compose simplifies the process of scaling and managing containers, enabling you to focus on developing and deploying machine learning models effectively.&#x20;
