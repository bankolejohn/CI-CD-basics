# DevOps Project Documentation: Introduction to Jenkins for CI/CD

## 1\. Introduction

### 1.1 Project Overview

*   **Project Name:** Introduction to Jenkins for CI/CD
    
*   **Purpose/Goals:** This project aims to provide learners with a foundational understanding of Continuous Integration (CI) and Continuous Delivery (CD) principles, and to articulate their role in improving software development processes. It focuses on acquiring proficiency in using Jenkins, covering installation, configuration, navigation, and hands-on experience in creating and managing Jenkins jobs and pipelines. The ultimate goal is to enable learners to automate software builds, run automated tests, and deploy applications using Jenkins, while applying CI/CD best practices including parameterized builds, integration with external tools, and leveraging containerization technologies like Docker.
    
*   **Scope:** This documentation covers the theoretical introduction to CI/CD and Jenkins, practical steps for Jenkins installation and initial setup, creation and configuration of Jenkins Freestyle and Pipeline jobs, connecting Jenkins to Source Code Management, configuring build triggers, writing Jenkins Pipeline scripts, and integrating Docker into the pipeline.
    
*   **Key Stakeholders:**
    
    *   Development Teams
        
    *   Operations Teams
        
    *   QA Teams
        
    *   Project Managers
        
    *   New Team Members (specifically learners of this project)
        
*   **Document Version:** 1.1
    
*   **Last Updated:** 2025-07-10
    

### 1.2 Target Audience

This document is intended for:

*   Development Teams
    
*   Operations Teams
    
*   QA Teams
    
*   Project Managers
    
*   New Team Members (especially those completing "foundations Core programs 1 - 3")
    

## 2\. Architecture Overview

### 2.1 System Architecture Diagram

The core architecture for this project involves a single Jenkins instance deployed on a virtual machine or server. This Jenkins instance will interact with a Source Code Management (SCM) system (e.g., Git repository) to pull code. It will also interact with Docker for containerization aspects of the CI/CD pipeline.

    +-------------------+       +-------------------+
    |   Source Code     |       |                   |
    |   Management      |<----->|   Jenkins Server  |
    |   (e.g., Git)     |       |   (Port 8080)     |
    +-------------------+       |                   |
              ^                 +---------+---------+
              |                           |
              |                           |
              |                           v
              |                 +-------------------+
              |                 |                   |
              +---------------->|      Docker       |
                                |   (for builds/    |
                                |    deployments)   |
                                +-------------------+
    
    

### 2.2 Technology Stack

*   **Programming Languages:** Not explicitly specified for the application being built, but Java is required for Jenkins.
    
*   **Frameworks:** Not explicitly specified for the application being built.
    
*   **Databases:** Not applicable for the Jenkins setup itself within this project's scope.
    
*   **Cloud Provider:** Not explicitly specified, assumed to be a generic Linux instance (e.g., Debian-based) which could be on any cloud or on-premise.
    
*   **Other Key Technologies:**
    
    *   **Jenkins:** CI/CD Automation Server
        
    *   **Git:** For Source Code Management
        
    *   **Docker:** For containerization during builds and deployments
        
    *   **Java Development Kit (JDK):** Prerequisite for Jenkins
        

## 3\. Infrastructure

### 3.1 Infrastructure as Code (IaC)

*   **Tooling:** Not explicitly used for infrastructure provisioning in this project. Jenkins installation is performed via manual `apt` commands.
    
*   **Repository Location:** N/A (manual installation)
    
*   **Directory Structure:** N/A (manual installation)
    
*   **Key Modules/Resources:** N/A (manual installation)
    
*   **Environments:** This project primarily focuses on a single Jenkins instance setup, typically for a development or learning environment.
    
    *   **Development:** The Jenkins instance set up as per the instructions.
        
    *   **Staging:** Not covered in this project.
        
    *   **Production:** Not covered in this project.
        

### 3.2 Network Configuration

*   **VPC/VNet/Networking Details:** Assumed to be a standard network configuration where the Jenkins instance resides. Specific details are dependent on the underlying cloud provider or on-premise setup.
    
*   **Security Groups/Network ACLs:** An inbound rule must be created for **TCP port 8080** in the security group associated with the Jenkins instance. This allows web browser access to the Jenkins UI.
    
*   **Load Balancers:** Not applicable for this single-instance setup.
    
*   **DNS Management:** Access is via direct IP address (`http://public_ip_address:8080`). DNS management is not covered.
    

### 3.3 Compute Resources

*   **Virtual Machines/Instances:** A Linux-based virtual machine or server (e.g., Debian-based) is required to host the Jenkins installation. Specific instance types or sizes are not detailed but should be sufficient to run Jenkins and Docker.
    
*   **Containers/Orchestration:** Docker is installed on the Jenkins host to support containerized builds and deployments as part of the CI/CD pipeline. Kubernetes or other orchestrators are not part of this project's scope.
    
*   **Serverless Functions:** Not applicable.
    

### 3.4 Data Storage

*   **Database Instances:** Not applicable for the Jenkins setup itself. Jenkins stores its configuration and job data on the local filesystem of the instance, typically in `/var/lib/jenkins`.
    
*   **Object Storage:** Not applicable.
    
*   **File Storage:** Not applicable beyond the local filesystem.
    

### 3.5 Jenkins Installation Steps

Now that we have an idea what Jenkins is, let's dive into installing Jenkins.

1.  **Update package repositories:**
    
        sudo apt update
        
    
2.  **Install JDK (Java Development Kit):** Jenkins requires Java to run.
    
        sudo apt install default-jdk-headless
        
    
3.  **Install Jenkins:**
    
        wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
        sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > \
        /etc/apt/sources.list.d/jenkins.list'
        sudo apt update
        sudo apt-get install jenkins
        
    
    This command sequence installs Jenkins. It involves importing the Jenkins GPG key for package verification, adding the Jenkins repository to the system's sources, updating package lists, and finally, installing Jenkins through the package manager (`apt-get`).
    
4.  **Check if Jenkins has been installed and is up and running:**
    
        sudo systemctl status jenkins
        
    

### 3.6 Jenkins Web Console Setup

After installation, you need to configure Jenkins through its web interface.

1.  **Create new inbound rules for port 8080 in the security group:** By default, Jenkins listens on port 8080. You need to create an inbound rule for this port in the security group of your Jenkins instance (e.g., in your cloud provider's console or firewall settings) to allow external access.
    
2.  **Access Jenkins On The Web Console:** Input your Jenkins Instance IP address on your web browser: `http://public_ip_address:8080`
    
3.  **Retrieve Initial Admin Password:** On your Jenkins instance (via SSH), check the following path to find your initial administrator password:
    
        sudo cat /var/lib/jenkins/secrets/initialAdminPassword
        
    
    Copy this password and paste it into the Jenkins web interface to unlock Jenkins.
    
4.  **Install Suggested Plugins:** After unlocking, Jenkins will prompt you to install suggested plugins. It is recommended to proceed with this option.
    
5.  **Create a User Account:** Follow the prompts to create your first admin user account for Jenkins.
    
6.  **Log in to Jenkins Console:** Once the setup is complete and the user account is created, you will be redirected to the Jenkins dashboard.
    

## 4\. CI/CD Pipeline

### 4.1 Overview

*   **CI/CD Tool:** Jenkins
    
*   **Pipeline Stages:** The project highlights the creation of both Freestyle and Pipeline jobs. A typical pipeline would involve stages like:
    
    *   Source Code Checkout
        
    *   Build
        
    *   Test
        
    *   Deployment (conceptual within the project)
        
*   **Triggering Mechanisms:** Jenkins is configured to trigger builds automatically upon code changes detected in the connected version control system (e.g., Git pushes). Manual triggers are also possible.
    

### 4.2 Build Process

*   **Build Artifacts:** The project implies the generation of build artifacts, though specific types (e.g., JAR, WAR, Docker images) are not detailed. Artifacts would typically be stored by Jenkins or pushed to an artifact repository.
    
*   **Dependency Management:** Handled as part of the application's build process within Jenkins jobs (e.g., Maven, npm, pip).
    
*   **Unit Testing:** The project emphasizes running automated tests as part of the CI/CD process. Specific frameworks or reporting are not detailed.
    

### 4.3 Testing and Quality Gates

*   **Integration Testing:** Mentioned as a goal to be learned, but specific implementation details are not provided in the installation guide.
    
*   **End-to-End (E2E) Testing:** Mentioned as a goal to be learned, but specific implementation details are not provided.
    
*   **Security Scans:** Not covered in this project.
    
*   **Code Quality Checks:** Not covered in this project.
    

### 4.4 Deployment Process

*   **Deployment Strategy:** The project aims for learners to deploy applications using Jenkins. Specific strategies (e.g., Blue/Green, Canary) are not detailed, but the focus is on automating the deployment.
    
*   **Target Environments:** Deployment to various environments is a general concept of CD, but specific target environments are not configured within this project's setup guide.
    
*   **Rollback Procedure:** Not explicitly detailed in the provided project description.
    
*   **Secrets Management:** Initial Jenkins setup involves retrieving an initial admin password. For pipeline secrets (e.g., credentials for deploying), Jenkins' built-in credentials management or plugins would be used, though not explicitly detailed in the provided text.
    

### 4.5 Pipeline Scripting and Execution

This section details how to define and execute CI/CD pipelines using Jenkins.

*   **Creating a Freestyle Project:** Freestyle projects are a basic type of Jenkins job that allows you to configure build steps, SCM, and post-build actions through the Jenkins UI. This is a good starting point for simple tasks.
    
*   **Connecting Jenkins To Our Source Code Management:** To build code, Jenkins needs to connect to your version control system (e.g., Git). You will configure the SCM section in your Jenkins job to point to your repository URL and specify credentials if needed.
    
*   **Configuring Build Trigger (Freestyle):** Freestyle projects can be triggered manually, periodically, or by SCM changes (e.g., polling SCM or using webhooks).
    
*   **Creating a Pipeline Job:** Pipeline jobs allow you to define your entire CI/CD workflow as code using a `Jenkinsfile`. This provides version control, reusability, and better visibility into your pipeline.
    
*   **Configuring Build Trigger (Pipeline):** Similar to Freestyle projects, Pipeline jobs can be triggered manually, periodically, or by SCM changes. The `Jenkinsfile` itself can also define triggers.
    
*   **Writing Jenkins Pipeline Script:** A `Jenkinsfile` is a text file that defines your Jenkins Pipeline. It's typically written in Groovy syntax and committed to your project's source code repository. It defines stages like build, test, and deploy.
    
*   **Installing Docker (for Pipeline Builds):** To leverage containerization within your Jenkins pipelines (e.g., building Docker images, running tests in isolated containers), Docker needs to be installed on the Jenkins host.
    
        # (Assuming Docker installation steps would be provided here if available,
        # as per the project highlight "Installing Docker")
        
    
*   **Building Pipeline Script:** Once your `Jenkinsfile` is committed to your SCM, you configure a Jenkins Pipeline job to point to this `Jenkinsfile`. When the job runs, Jenkins executes the script, orchestrating your CI/CD workflow.
    

## 5\. Monitoring and Alerting

### 5.1 Monitoring Tools

*   **Metrics Collection:** Not explicitly covered. Jenkins itself provides build history and logs.
    
*   **Visualization:** Jenkins UI provides basic job status and history.
    
*   **Log Management:** Jenkins logs are available on the instance (e.g., `/var/log/jenkins/jenkins.log`) and via the Jenkins web UI for individual jobs.
    

### 5.2 Key Metrics

*   **Application Metrics:** Not covered in this project.
    
*   **Infrastructure Metrics:** Basic system health (e.g., `sudo systemctl status jenkins`) can be checked.
    
*   **Database Metrics:** Not applicable.
    

### 5.3 Alerting Strategy

*   **Alerting Channels:** Not covered in this project.
    
*   **Alerting Rules:** Not covered in this project.
    
*   **On-Call Rotation:** Not applicable.
    

## 6\. Logging

### 6.1 Log Sources

*   **Application Logs:** Jenkins system logs (e.g., `/var/log/jenkins/jenkins.log`).
    
*   **Server Logs:** Standard OS logs (e.g., `syslog`).
    
*   **Cloud Service Logs:** Not applicable for this project's scope.
    

### 6.2 Log Aggregation and Analysis

*   **Log Aggregator:** Not covered in this project.
    
*   **Log Storage:** Logs are stored locally on the Jenkins instance. Retention policies are not specified.
    
*   **Log Querying/Analysis:** Manual inspection of log files or via the Jenkins UI.
    

## 7\. Security

### 7.1 Access Control

*   **IAM/RBAC Strategy:** Jenkins provides its own user management and role-based access control (RBAC) through plugins. The project includes steps to "Create a user account" and "Log in to jenkins console".
    
*   **Principle of Least Privilege:** Implicitly encouraged by creating dedicated user accounts, but not detailed.
    

### 7.2 Secrets Management

*   **Tooling:** Initial admin password retrieval from `/var/lib/jenkins/secrets/initialAdminPassword`. For pipeline secrets, Jenkins' built-in credentials management is the primary tool, though not explicitly detailed in the provided text.
    
*   **Process:** Initial password retrieval is a manual step.
    

### 7.3 Network Security

*   **Firewalls/Security Groups:** Crucial step: "create new inbound rules for port 8080 in security group" to allow access to the Jenkins UI.
    
*   **VPN/Direct Connect:** Not applicable.
    

### 7.4 Compliance and Auditing

*   **Relevant Standards:** Not covered.
    
*   **Audit Trails:** Jenkins maintains a history of job executions and user actions, serving as a basic audit trail.
    

## 8\. Incident Management and Troubleshooting

### 8.1 Incident Response Plan

*   **Severity Levels:** Not covered.
    
*   **Communication Plan:** Not covered.
    
*   **Post-Mortem Process:** Not covered.
    

### 8.2 Runbooks/Troubleshooting Guides

*   **Common Issues:**
    
    *   **Jenkins Service Not Running:** Check `sudo systemctl status jenkins`.
        
    *   **Cannot Access Jenkins UI:** Verify port 8080 inbound rule in security group, check Jenkins service status.
        
    *   **Incorrect Initial Admin Password:** Double-check the path `/var/lib/jenkins/secrets/initialAdminPassword`.
        
*   **Diagnostic Steps:** Use `systemctl status jenkins`, check server logs, review Jenkins job console output.
    
*   **Resolution Steps:** Restart Jenkins service, adjust security group rules, re-enter password.
    
*   **Escalation Matrix:** Not covered.
    

## 9\. Cost Management

### 9.1 Cost Monitoring

*   **Tools:** Not covered; depends on the cloud provider's billing tools if hosted in the cloud.
    
*   **Key Cost Drivers:** The primary cost driver would be the virtual machine/server hosting Jenkins.
    

### 9.2 Cost Optimization Strategies

*   **Resource Sizing:** Not covered, but choosing an appropriately sized VM is important.
    
*   **Reserved Instances/Savings Plans:** Not applicable for this project's scope.
    
*   **Auto-Scaling:** Not applicable for a single Jenkins instance.
    

## 10\. Onboarding Guide

### 10.1 Getting Started

*   **Repository Access:** Learners will need access to the source code repository that their Jenkins jobs will build from.
    
*   **Local Development Setup:** Not explicitly detailed for the application code, but the Jenkins installation steps are provided.
    
*   **Key Contacts:** N/A (for a self-paced project).
    

### 10.2 Important Tools and Resources

*   **Links to Documentation:**
    
    *   Official Jenkins Documentation
        
    *   Docker Documentation
        
    *   Git Documentation
        
*   **Communication Channels:** N/A (for a self-paced project).
    

## 11\. Future Considerations / Roadmap

*   **Planned Improvements:**
    
    *   Implementing more advanced CI/CD patterns (e.g., Blue/Green deployments).
        
    *   Integrating with artifact repositories (e.g., Nexus, Artifactory).
        
    *   Setting up distributed builds with Jenkins agents.
        
    *   Implementing comprehensive monitoring and alerting for Jenkins and the applications it deploys.
        
    *   Automating Jenkins installation and configuration using IaC tools (e.g., Ansible, Terraform).
        
*   **Technical Debt:** N/A (for a learning project).


## Screenshots

<img width="1115" alt="Snipaste_2025-07-10_09-28-07" src="https://github.com/user-attachments/assets/915c0e01-b5b0-4152-b500-4879b3b7ddb3" />


<img width="700" alt="Snipaste_2025-07-10_09-25-50" src="https://github.com/user-attachments/assets/6d0e552e-2e58-4c34-ab32-ae5c9f12f095" />


<img width="491" alt="Snipaste_2025-07-10_09-25-04" src="https://github.com/user-attachments/assets/3104c541-ba9c-4a07-b3e1-b3f5ed9ee4b9" />

    

## 12\. Appendices

### 12.1 Glossary

*   **CI/CD:** Continuous Integration/Continuous Delivery
    
*   **CI:** Continuous Integration
    
*   **CD:** Continuous Delivery
    
*   **SCM:** Source Code Management
    
*   **JDK:** Java Development Kit
    
*   **IaC:** Infrastructure as Code
    

### 12.2 Acronyms

*   CI/CD
    
*   CI
    
*   CD
    
*   SCM
    
*   JDK
    
*   IaC
    

### 12.3 References

*   Jenkins Official Website: [https://www.jenkins.io/](https://www.jenkins.io/ "null")
    
*   Docker Official Website: [https://www.docker.com/](https://www.docker.com/ "null")
    
*   Git Official Website: [https://git-scm.com/](https://git-scm.com/ "null")
