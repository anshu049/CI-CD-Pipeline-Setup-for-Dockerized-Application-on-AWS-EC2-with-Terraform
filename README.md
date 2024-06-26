## Launch an Amazon Linux EC2 (add port 8080 in inbound rules)
**Install Docker**
```
yum update && yum install docker
```

**start Docker**
```
service docker start
```


## Install Jenkins
```
docker run -d -p 8080:8080 -p 50000:50000 \
-v jenkins_home:/var/jenkins_home \
-v /var/run/docker.sock:/var/run/docker.sock \
-v $(which docker):/usr/bin/docker \
jenkins/jenkins:lts
```


## Initial admin password
```
docker exec -it <container-id> /bin/bash
```
```
cat /var/jenkins_home/secrets/initialAdminPassword
```
**Install plugin `SSH Agent`**


## Enter as root into Jenkins container and modify docker.sock permission
```
docker exec -u 0 -it <container-id> /bin/bash
```
```
chmod 666 /var/run/docker.sock
```


## Enter as root into Jenkins container and [install Terraform](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli)
```
docker exec -u 0 -it <container-id> /bin/bash
```
```
apt install wget
```
```
apt-get update && apt-get install -y gnupg software-properties-common
```
```
wget -O- https://apt.releases.hashicorp.com/gpg | \
gpg --dearmor | \
tee /usr/share/keyrings/hashicorp-archive-keyring.gpg > /dev/null
```
```
gpg --no-default-keyring \
--keyring /usr/share/keyrings/hashicorp-archive-keyring.gpg \
--fingerprint
```
```
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] \
https://apt.releases.hashicorp.com $(lsb_release -cs) main" | \
tee /etc/apt/sources.list.d/hashicorp.list
```
```
apt update
```
```
apt-get install terraform
```


## Create key-pair for EC2 which will be used by Terraform to connect to created instance for our


## Add Credentials in Jenkins
- **As Secret text for docker-hub and AWS Credentials, SSH username with private key for key-pair (username as `ec2-user`)**
![credentials in jenkins](https://github.com/anshu049/CI-CD-Pipeline-Setup-for-Dockerized-Application-on-AWS-EC2-with-Terraform/assets/95365748/2e75a587-07e8-4d17-9fbb-4b8937d24948)


## Create job as Pipeline and start build
![job successful](https://github.com/anshu049/CI-CD-Pipeline-Setup-for-Dockerized-Application-on-AWS-EC2-with-Terraform/assets/95365748/05facbf0-fe4f-42a4-a8a1-dc4c006a31bb)


## Add webhook
![webhook](https://github.com/anshu049/CI-CD-Pipeline-Setup-for-Dockerized-Application-on-AWS-EC2-with-Terraform/assets/95365748/df9abd62-da13-4159-9e24-eee37bafa160)

- **After new commit to github it will go into pending state**
![pending build](https://github.com/anshu049/CI-CD-Pipeline-Setup-for-Dockerized-Application-on-AWS-EC2-with-Terraform/assets/95365748/0f3d68dc-0b01-439b-bcca-52138563827d)

- **Few second later it will be triigerd for push**
![build from push](https://github.com/anshu049/CI-CD-Pipeline-Setup-for-Dockerized-Application-on-AWS-EC2-with-Terraform/assets/95365748/c2f2f91d-ac2c-42ef-9ecf-92cc8dc965c9)


## AWS resources created from Terrafrom
![AWS resources](https://github.com/anshu049/CI-CD-Pipeline-Setup-for-Dockerized-Application-on-AWS-EC2-with-Terraform/assets/95365748/d91be25c-cb32-4e78-ba77-1f4dc09efd28)


## App
![App](https://github.com/anshu049/CI-CD-Pipeline-Setup-for-Dockerized-Application-on-AWS-EC2-with-Terraform/assets/95365748/443236b1-54f4-4d9f-83a8-271dc209af34)

