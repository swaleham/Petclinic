# Petclinic DevSecOps CICD

<h3>Petclinic is a Java based application where the application is deployed in two ways.</h3>
1. Docker Container. 2. Tomcat Server.
<h4>Manual approval process is also integrated with the CI/CD process.</h4>

## Project Architecture
<img src="https://github.com/swaleham/Petclinic/blob/main/Petclinic (2).svg">

## Steps:
1. **Create an Ubuntu instance(t2.large,RAM=10GB).**
2. **Install Jenkins,Docker and Trivy on instance.**
3. **Create a sonarqube container using docker on port 9000.**
4. **Install Plugins like JDK,Sonarqube Scanner,Maven,OWASP Dependency Check,Email Extension Template,Eclipse Temurin Installer,Docker(Commons,Pipeline,API,build-step)etc.**
5. **Create a Pipeline project in jenkins using a declarative pipeline.**
6. **Email Integration with Jenkins and setup.**
7. **Write script to build docker image using dockerfile and push to dockerHub.**
8. **Deploy image using docker.**
9. **Install Tomcat on port 8083 and deplou on Apache Tomcat.**
10. **Access Real World App.**
11. **Terminate AWS EC2 instance.**

## Petclinic DevSecOps CI/CD stages

<img src="https://github.com/swaleham/Petclinic/blob/main/PetclinicCICD.png">

## Exposing Tomcat server on port 8083

<img src="https://github.com/swaleham/Petclinic/blob/main/Tomcat-Server.png">

## Sonarqube results for code quality checks

<img src= "https://github.com/swaleham/Petclinic/blob/main/SonarqubeResults.png">

## Accessing Real World Petclinic Application

<img src="https://github.com/swaleham/Petclinic/blob/main/RealWorldApp.png">
