# INTRODUCTION

This project leverages a variety of tools and technologies to create an end-to-end pipeline, starting from codecommit to deployment and monitoring. By incorporating DevSecOps practices, we ensure that security is a key component at every stage of the pipeline. Key tools used in this project include Jenkins for orchestration, Maven for build management, SonarQube for code quality checks, Trivy for security scans, Nexus Repository for artifact management, Docker for containerization, Kubernetes for deployment, and Prometheus and Grafana for monitoring.

<img width="938" height="637" alt="image" src="https://github.com/user-attachments/assets/7d3e4b68-467c-4bc9-8020-e692c7211d8e" />

**The architecture of this CI/CD pipeline integrates various tools and technologies to facilitate seamless development, testing, deployment, and monitoring. Here's a condensed overview of the architecture and tools used:**

1. **GitHub:** Source code repository where developers push their code changes. [https://github.com/NareshHB/register_app]
2. **Jenkins:** CI/CD orchestrator that automates the building, testing, and deployment of the application.
3. **Maven:** Used for compiling the code, running unit tests, and packaging the application.
4. **SonarQube:** Performs static code analysis to identify code smells, bugs, and security vulnerabilities.
5. **Trivy:** Scans the file system and Docker images for security vulnerabilities.
6. **Nexus:** Repository: Stores the built artifacts (JAR files) for managing and distributing the build outputs.
7. **Docker:** Containerizes the application to ensure it runs consistently across different environments.
8. **Kubernetes:** (EKS on AWS): Orchestrates the deployment, management, and scaling of the containerized applications.
9. **Prometheus and Grafana:** Collects metrics and provides visualization dashboards for continuous monitoring and alerting of the system's health and performance.

# Pipeline Workflow
**The pipeline consists of multiple stages to ensure code quality, security, and successful deployment:**

1. Git Checkout: Jenkins checks out the code from the GitHub repository.

2. Compile: Maven compiles the code.

3. Test: Maven runs unit tests.

4. File System Scan: Trivy scans the file system for vulnerabilities.

5. SonarCloud Analysis: SonarQube performs code quality analysis.

6. Quality Gate: Jenkins waits for SonarQube’s quality gate results.

7. Build: Maven packages the application.

8. Publish to Nexus: Jenkins publishes the build artifacts to the Nexus Repository.

9. Build & Tag Docker Image: Docker builds and tags the application image.

10. Docker Image Scan: Trivy scans the Docker image for vulnerabilities.

11. Push Docker Image: Jenkins pushes the Docker image to the registry.

12. Deploy to Kubernetes: Jenkins deploys the application to the Kubernetes cluster.

13. Verify the Deployment: Jenkins verifies the deployment by checking the pods and services.

