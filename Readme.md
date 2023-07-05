# Description:

Implemented an automated process for building and pushing a Docker image to Docker Hub, which is later utilized by Kubernetes during deployment.
I used a simple [python-app](https://github.com/eslamkhaled560/simple-app/tree/dev) in dev branch.

-----------------------------------------
# Steps:

## 1- Create Jenkins Shared Library

- Configure ```shared-lib``` on Jenkins

![1](https://github.com/eslamkhaled560/shared-lib-pipeline/assets/54172897/a393f8f5-91e9-460b-bea9-3886dac3f4ba)
![2](https://github.com/eslamkhaled560/shared-lib-pipeline/assets/54172897/b2c8e32f-6ef7-4c78-97a5-4efffbe04bbe)

- Creating groovy files on this repo: ```jenkins-shared-lib```

Files Links: [buildImage.groovy](https://github.com/eslamkhaled560/shared-lib-pipeline/blob/main/vars/buildImage.groovy)     
```
def call() {
    echo "Building docker image..."
    sh "docker build -t islamdevops/pvt-repo:${BRANCH_NAME} ."
    echo "Building is successful"
}
```
[pushImage.groovy](https://github.com/eslamkhaled560/shared-lib-pipeline/blob/main/vars/pushImage.groovy)           
```
def call() {
    echo "Pushing docker image..."
    withCredentials([usernamePassword(credentialsId: 'docker-hub', usernameVariable: 'USER', passwordVariable: 'PWD')]) {
        sh "echo $PWD | docker login -u $USER --password-stdin" 
    }
    sh "docker push islamdevops/pvt-repo:${BRANCH_NAME}"
    echo "Pushing is successful"
}
```
[cleanUp.groovy](https://github.com/eslamkhaled560/shared-lib-pipeline/blob/main/vars/cleanUp.groovy)
```
def call() {
    echo "Deleting local docker image..."
    sh "docker rmi islamdevops/pvt-repo:${BRANCH_NAME}"
    echo "Deleting is successful"
}
```

## 2- AWS Agent Configuration

- Connect to ```aws-agent``` instance

![1](https://github.com/eslamkhaled560/shared-lib-pipeline/assets/54172897/e8774b54-344d-4435-9c9c-e31115f323b1)

- Configure __Java, Docker, Git__ on ```aws-agent```, and create __jenkins_home__ directory

![2](https://github.com/eslamkhaled560/shared-lib-pipeline/assets/54172897/0cbde0ca-40f5-4ce2-a69b-491474993090)

- Configure Jenkins-Agent ssh connection using ssh public and private key

![3](https://github.com/eslamkhaled560/shared-lib-pipeline/assets/54172897/4ce35b7b-47e3-4f37-8673-77cc3175abe6)

## 3- Jenkins Configuration

- Add ssh credentials to Jenkins

![4](https://github.com/eslamkhaled560/shared-lib-pipeline/assets/54172897/fec7e3d6-bdf8-43bb-8d70-6adaa780fd78)

- Configure ```aws-agent``` on Jenkins

![5](https://github.com/eslamkhaled560/shared-lib-pipeline/assets/54172897/4daba2c0-4a17-47c4-a6c2-0a340afb19f8)
![6](https://github.com/eslamkhaled560/shared-lib-pipeline/assets/54172897/bd8e0255-0076-47ae-b030-2952fa1ed44d)

- I made ```Jenkinsfile``` changes on the ```dev``` branch.

File Link: [Jenkinsfile-dev](https://github.com/eslamkhaled560/simple-app/blob/dev/Jenkinsfile)

![7](https://github.com/eslamkhaled560/shared-lib-pipeline/assets/54172897/2e54157c-073a-47ca-a40f-88ef14b62a4b)

## 4- Output

- Building ```dev``` branch in ```multibranch-simple-app```

![8](https://github.com/eslamkhaled560/shared-lib-pipeline/assets/54172897/5b241550-3971-43ab-a42a-90c76330dd60)

- Console and docker hub output
 
![Studio_Project_V11](https://github.com/eslamkhaled560/shared-lib-pipeline/assets/54172897/195fed31-d72e-43d4-b486-b4be1a4d8806)

- Check ```~/jenkins_home/workspace``` on ```aws-agent```

![image](https://github.com/eslamkhaled560/shared-lib-pipeline/assets/54172897/4af0ca46-4cfa-42b8-b903-c3ba0e290b4d)

-----------------------------------------
