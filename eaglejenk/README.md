Eagleworld.net Jenkins Server Deployment
========================================

This deployment is designed to work on a Kubernetes cluster (either
with MiniKube or a standard cluster) for eagleworld.net services.
It uses the following:

- jenkins:lts base image
- getintodevops/jenkins-withdocker addons to use docker-ce
- sharing of /var/run/docker.sock to allow docker to be used
- custom plugin install for kubernetes/docker continuous deployment
- Kubernetes persistent volume claim of 2gb for /home/jenkins_home

Usage
-----

- Clone the repository onto the your kubectl-enabled workstation
- Enter the eaglejenk directory:

  $ cd eaglejenk

- Create the Persistent Volume Claim:

  $ kubectl create -f eaglejenk-pvclaim.yml

- Create the Jenkins Deployment:

  $ kubectl create --save-config=true -f eaglejenk-kube.yml

- You can get the initial admin password for Jenkins by executing:

  $ export POD_NAME=$(kubectl get pods -l "app=eaglejenk" | tail -1 | cut -f1 -d" ")
  $ kubectl exec -it $POD_NAME /bin/ls /var/jenkins_home/secrets/initialAdminPassword

Docker Build
------------

To build the Docker image for use with your own Docker Hub repo,
execute the following in the eaglejenk directory:

  $ docker login
  $ docker build -f Dockerfile -t <docker_id>/eaglejenk .
  $ docker push <docker_id>/eaglejenk:latest

To use your personal Docker image instead of mine, change the name
of the image referenced in eaglejenk-kube.yml:

  $ sed -i 's/eaglerock/<docker_id>/g' eaglejenk-kube.yml 

If you wish to customize the pre-installed plugins, include them
in the plugins.txt file (one per line).
