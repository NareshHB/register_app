# Introduction

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

## Pipeline Workflow
**The pipeline consists of multiple stages to ensure code quality, security, and successful deployment:**

1. Git Checkout: Jenkins checks out the code from the GitHub repository.

```
stage("Checkout from SCM"){
                steps {
                    git branch: 'main', credentialsId: 'github', url: 'https://github.com/NareshHB/register_app.git'
                }
        }

```        

2. Compile: Maven compiles the code.

3. Test: Maven runs unit tests.

```

stage("Test Application"){
           steps {
                 sh "mvn test"
           }
       }
```

4. File System Scan: Trivy scans the file system for vulnerabilities.

```
stage('File System Scan') {
            steps {
                sh "trivy fs --format table -o trivy-fs-report.html ."
            }
        }

```

5. SonarCloud Analysis: SonarQube performs code quality analysis.

```

       stage("SonarQube Analysis"){
           steps {
	           script {
		        withSonarQubeEnv(credentialsId: 'jenkins-sonarqube-token') { 
                        sh "mvn sonar:sonar"
		        }
	           }	
           }
       }

 ```    

6. Quality Gate: Jenkins waits for SonarQube’s quality gate results.

```
       stage("Quality Gate"){
           steps {
               script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'jenkins-sonarqube-token'
                }	
            }

        }
        
```

7. Build: Maven packages the application.

```
 stage("Build Application"){
            steps {
                sh "mvn clean package"
            }

       }
```      

8. Publish to Nexus: Jenkins publishes the build artifacts to the Nexus Repository.

```

		stage('Publish To Nexus') {
            steps {
               withMaven(globalMavenSettingsConfig: 'global-settings', jdk: 'jdk17', maven: 'maven3', mavenSettingsConfig: '', traceability: true) {
                    sh "mvn deploy"
                }
            }
        }
```

9. Build & Tag Docker Image: Docker builds and tags the application image.

```

        stage("Build & Push Docker Image") {
            steps {
                script {
                    docker.withRegistry('',DOCKER_PASS) {
                        docker_image = docker.build "${IMAGE_NAME}"
                    }
                    
                }
            }

       }
```

10. Docker Image Scan: Trivy scans the Docker image for vulnerabilities.

```
       stage("Trivy Scan") {
           steps {
               script {
	            sh ('docker run -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image naresh1989/register-app-pipeline:latest --no-progress --scanners vuln  --exit-code 0 --severity HIGH,CRITICAL --format table')
               }
           }
       }
```

11. Push Docker Image: Jenkins pushes the Docker image to the registry.

```
docker.withRegistry('',DOCKER_PASS) {
                        docker_image.push("${IMAGE_TAG}")
                        docker_image.push('latest')
                    }
```

12. Clean Artifacts and remove images

```
       stage ('Cleanup Artifacts') {
           steps {
               script {
                    sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG}"
                    sh "docker rmi ${IMAGE_NAME}:latest"
               }
          }
       }
```


13. Deploy to Kubernetes: Trigger CD Pipeline.

```

       stage("Trigger CD Pipeline") {
            steps {
                script {
                    sh "curl -v -k --user clouduser:${JENKINS_API_TOKEN} -X POST -H 'cache-control: no-cache' -H 'content-type: application/x-www-form-urlencoded' --data 'IMAGE_TAG=${IMAGE_TAG}' 'ec2-13-232-128-192.ap-south-1.compute.amazonaws.com:8080/job/gitops-register-app-cd/buildWithParameters?token=gitops-token'"
                }
            }
       }
    }
```

14. Verify the Deployment: Jenkins verifies the deployment by checking the pods and services.

```
stage('Verify the Deployment') {
            steps {
                withKubeConfig([credentialsId: 'gitops-token', serverUrl: 'https://11DF796EEE3966468F9CC68CC102D1BA.yl4.ap-south-1.eks.amazonaws.com']) {
                    sh "kubectl get pods -n argocd"
                    sh "kubectl get svc -n argocd"
                }
            }
        }
```

15. Send Mail Notification.

```
    post {
       failure {
             emailext body: '''${SCRIPT, template="groovy-html.template"}''', 
                      subject: "${env.JOB_NAME} - Build # ${env.BUILD_NUMBER} - Failed", 
                      mimeType: 'text/html',to: "hbnaresh@gmail.com"
      }
      success {
            emailext body: '''${SCRIPT, template="groovy-html.template"}''', 
                     subject: "${env.JOB_NAME} - Build # ${env.BUILD_NUMBER} - Successful", 
                     mimeType: 'text/html',to: "hbnaresh@gmail.com"
      }      
   }
}
```

**This detailed breakdown ensures each stage of the CI/CD pipeline is clearly understood and properly configured to maintain the integrity and quality of the application deployment process.**

## Conclusion

***In this project, we've set up a comprehensive CI/CD pipeline using Jenkins, integrating essential tools such as Maven, Docker, SonarQube, Trivy, Kubernetes, & ArgoCD Each stage of the pipeline—from code checkout to deployment—has been meticulously configured to ensure code quality, security, and seamless deployment. By leveraging SonarQube for code analysis, Trivy for security scanning, and Prometheus and Grafana for monitoring, we have embedded DevSecOps practices throughout the pipeline. This approach not only automates the build and deployment process but also ensures that our application is robust, secure, and reliable.***

***By following this structured CI/CD pipeline, teams can achieve faster and more reliable software delivery, maintain high standards of code quality, and ensure continuous monitoring and improvement of their applications. This setup is a testament to the power of integrating various DevOps tools to create an efficient and secure software delivery lifecycle.***
