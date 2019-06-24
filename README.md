fargate-deploy
=

Simplify deployment of Spring Boot Tasks to AWS Fargate

Overview
=

A bash shell script to easily deploy Spring Boot-based tasks to AWS Fargate cluster

Specifically, it does the following:

1) Checks for uncommitted Git changes, and asks for confirmation before proceeding
2) Builds a local Docker image, and by default, tags it with the current Git revision
3) Pushes the local Docker image an AWS ECR repository
4) Uses a CloudFormation template to deploy ECR image as a Fargate task to the selected *deployment environment*

An *deployment environment* is defined by a **.properties** file in the scripts directory, which references pre-defined 
name-value pairs describing the characteristics of the environment.  The values are references to [AWS System Manager 
Parameter Store](https://aws.amazon.com/systems-manager/features/#Parameter_Store) entries:

For example:

The file *fargate-deploy-**dev**.properties* defines a deployment environment named **dev** and contains:

```sh
EcsClusterName=/dev/ecs-cluster-name
EcsExecutionRole=/dev/ecs-execution-role
EcsTaskRole=/dev/ecs-task-role
VpcSecurityGroups=/dev/vpc-security-groups
VpcSubnets=/dev/vpc-subnets
ServiceDiscoveryNamespaceId=/dev/service-discovery-namespace-id
SpringCloudConfigUri=/dev/spring-cloud-config-uri
SpringProfilesActive=/dev/spring-profiles-active
 ```
 
Usage Summary
=

Assuming you're in the root directory of the Spring Boot project you wish to deploy, just do:

```sh
mvn clean package
fargate-deploy --task-name <task-name> --development-environment <environment-name>
```

Where:
 
*<task-name>* = the name the task will be deployed under (e.g. *'MyImportantTask*)

*<development-environment>* = the pre-defined environment the task will be deployed to (e.g. *'alpha'*)  

For a full description of all available options:

```sh
fargate-deploy --help
``` 

Dockerfile
=

The script assumes that it's being called the project's root directory, containing a Dockerfile.  

A typical Dockerfile for 
deployment would contain:

```dockerfile
FROM openjdk:8-jdk-alpine
RUN apk add --no-cache curl
VOLUME /tmp
EXPOSE 8080
ARG DEPENDENCY=target/dependency
COPY ${DEPENDENCY}/BOOT-INF/lib /app/lib
COPY ${DEPENDENCY}/META-INF /app/META-INF
COPY ${DEPENDENCY}/BOOT-INF/classes /app
ENTRYPOINT ["java","-cp","app:app/lib/*","com.exworks.tms.compliance.Application"] 
```

Install
=

General:

1) Clone this repo to your local machine
1) Create a `~/bin` directory
2) Add `~/bin` to your PATH
3) Copy all the files from the repo's `/bin` directory to `~/bin`
4) Mark `fargate-deploy` as executable

For Linux specifically:
```sh
# Clone
git clone https://github.com/ExWorks/fargate-deploy.git

# Create scripts directory
mkdir $HOME/bin
cd $HOME/bin

# Add to path
echo export PATH="$HOME/bin:\$PATH" >> $HOME/.bashrc

# Copy files
cp ~/fargate-deploy/bin/* .

# Mark as executable
chmod 775 fargate-deploy
```

TODO
=

- Make parameter parsing a little more robust
 

