fargate-deploy
=

Simplify deployment of Java Tasks to AWS Fargate

Overview
=

A bash shell script to easily deploy Java-based tasks to AWS Fargate cluster

Specifically, it does the following:

1) Checks for uncommitted Git changes, and ask for confirmation before proceeding
2) Builds a local Docker image, and by default, tags it with the current Git revision
3) Pushes the local Docker image to AWS ECR repository
4) Uses a CloudFormation template to deploy ECR image as a Fargate task to the selected "environment"

An named "environment" is defined by a .properties file in the scripts directory, which references pre-defined values 
stored in AWS System Manager Paramater Store

For example:

File `~/bin/fargate-deploy-dev.properties` defines the 'dev' environment and looks like this:

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

```sh
mvn clean package
fargate-deploy --task-name MyTask --development-environment alpha
```

For a full description of all available options:

```sh
fargate-deploy --help
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
 

