# WordPress with Command Line!
Hello everyone, here are the steps for me to run the WordPress manually instead of using docker-comp. Hope you can contribute to guide my way, I can complete better.

**Step 1: Install Docker**
1. First, download and add the GPG key with the following command:
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -
2. Add the Docker repository with the following command:  
$ add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
3. Once the repository is added, you can install the Docker and Docker Compose using the following command:
$ apt-get install docker-ce docker-ce-cli containerd.io  -y
4. After installing both packages, check the installed version of Docker with the following command:
$ docker --version
