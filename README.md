# CI-CD_pipeline_with_githubactions-
Creating a CI/CD pipeline using GitHub Actions, Docker, and Kubernetes.

## Getting Started

### Prerequisites

Software and tools that need to be installed to run the project locally.

- [Java JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html)
- [Gradle](https://gradle.org/install/)
- [Docker](https://www.docker.com/products/docker-desktop)
- [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

### Installation

1. Clone the repository:
    ```bash
    git clone https://github.com/your-username/your-repo.git
    cd your-repo
    ```

2. Build the project using Gradle:
    ```bash
    ./gradlew build
    ```

3. Run the application locally:
    ```bash
    java -jar build/libs/your-application.jar
    ```
## CI/CD Pipeline

### Continuous Integration

This project uses [GitHub Actions](https://github.com/features/actions) for continuous integration.

- The CI pipeline is defined in `.github/workflows/ci.yml`.
- On every push and pull request to the `main` branch, the workflow:
  - Checks out the code.
  - Sets up JDK 11.
  - Builds the project with Gradle.
  - Runs tests.
  - Publishes test results.

### Dockerization

The project is containerized using Docker.

- The Docker image is defined by the `Dockerfile` in the root directory.
- The CI workflow builds the Docker image and pushes it to Docker Hub.

### Continuous Deployment

Deployment is managed using Kubernetes.

- Deployment and service configurations are defined in `k8s-deployment.yml` and `k8s-service.yml`.
- The CI workflow deploys the application to a Kubernetes cluster.

#### Example Workflow Configuration

```yaml
name: CI Pipeline

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout code
      uses: actions/checkout@v2

    - name: Set up JDK 11
      uses: actions/setup-java@v2
      with:
        java-version: '11'

    - name: Build with Gradle
      run: ./gradlew build

    - name: Run tests
      run: ./gradlew test

    - name: Build Docker image
      run: docker build -t your-dockerhub-username/your-repo:latest .

    - name: Log in to Docker Hub
      run: echo "${{ secrets.DOCKERHUB_TOKEN }}" | docker login -u "${{ secrets.DOCKERHUB_USERNAME }}" --password-stdin

    - name: Push Docker image
      run: docker push your-dockerhub-username/your-repo:latest

    - name: Set up kubectl
      run: |
        curl -LO "https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl"
        chmod +x ./kubectl
        sudo mv ./kubectl /usr/local/bin/kubectl

    - name: Deploy to Kubernetes
      run: |
        kubectl apply -f k8s-deployment.yml
        kubectl apply -f k8s-service.yml
