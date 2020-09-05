# Lab: Google Cloud Fundamentals: Getting Started with Cloud Storage and Cloud SQL

> This is a lab from Quicklabs that has been translated from actions on the cloud console to cloud shell commands.

## Overview

In this lab, you create a Cloud Storage bucket and place an image in it. You'll also configure an application running in Compute Engine to use a database managed by Cloud SQL. For this lab, you will configure a web server with PHP, a web development environment that is the basis for popular blogging software. Outside this lab, you will use analogous techniques to configure these packages.
You also configure the web server to reference the image in the Cloud Storage bucket.

## Objectives

In this lab, you learn how to perform the following tasks:

- Create a Cloud Storage bucket and place an image into it.
- Create a Cloud SQL instance and configure it.
- Connect to the Cloud SQL instance from a web server.
- Use the image in the Cloud Storage bucket on a web page.

## Task 1: Sign in to the Google Cloud Platform (GCP) Console

## Task 2: Deploy a web server VM instance

1. get a list of zones in us-central 1:

        gcloud compute zones list | grep us-central1

You will get something like this:

        us-central1-c              us-central1              UP
        us-central1-a              us-central1              UP
        us-central1-f              us-central1              UP
        us-central1-b              us-central1              UP

in this case we wil select us-central1-a

2. Create a VM instance and specify 'my-vm-1'for the VM name:

- For zone specify 'us-central1-a'
- Use the dafult machine type
- For the boot disk specify Debian GNU/Linux 9 (stretch)
- For the firewall speicfy allow HTTP traffic
- Specify debian-9 for the image-family and debian cloud for the image-project
- Specify the tag http-server to be used with a firewall rule

        gcloud compute instances create my-vm-1 --zone us-central1-a\
        --image debian-9-stretch-v20200902 --image-project debian-cloud\
        --tags http-server --subnet default

3. Create a firewall rule to allow http traffic

        gcloud compute firewall-rules create default-allow-http\ 
        --direction INGRESS --priority 1000 --network default --action ALLOW \
        --rules tcp:80 --source-ranges 0.0.0.0/0 --target-tags http-server

## Task 3: Create a virtual machine using the gcloud command line

1. get a list of zones in us-central 1:

        gcloud compute zones list | grep us-central1

2. Set your default zone as us-central1-b:

        gcloud config set compute/zone us-central1-b

3. create instance my-vm-2:

        gcloud compute instances create "my-vm-2" \
        --machine-type "n1-standard-1" \
        --image-project "debian-cloud" \
        --image "debian-9-stretch-v20190213" \
        --subnet "default"

## Task 4: Connect between VM instances

1. ssh to 'my-vm-2':

        gcloud compute ssh --zone us-central1-b my-vm-2

- when asked for a passphrase click enter

2. Try to ping 'my-vm-1':

        ping -c 4 my-vm-1

3. Exit from the command prompt on my-vm-2 back to the cloud shell:

        exit

4. ssh to 'my-vm-1':

        gcloud compute ssh --zone us-central1-a my-vm-1

5. At the command prompt on my-vm-1, install the Nginx web server:

        sudo apt-get install nginx-light -y

- If you get an error try runing 'sudo apt-get update' first then try installing nginx again

6. Use the nano text editor to add a custom message to the home page of the web server:

        sudo nano /var/www/html/index.nginx-debian.html

5. Use the arrow keys to move the cursor to the line just below the h1 header. Add text like this, and replace YOUR_NAME with your name:

        Hi from YOUR_NAME

- Press Ctrl+O and then press Enter to save your edited file, and then press Ctrl+X to exit the nano text editor.

6. Confirm that the web server is serving your new page. At the command prompt on my-vm-1, execute this command:

        curl http://localhost/

7. The response will be the HTML source of the web server's home page, including your line of custom text.

8. To exit the command prompt on my-vm-1, execute this command:

        exit

- You will then return to the command prompt on my-vm-2

7. To confirm that my-vm-2 can reach the web server on my-vm-1, at the command prompt on my-vm-2, execute this command:

        curl http://my-vm-1/

- The response will again be the HTML source of the web server's home page, including your line of custom text.

8. Exit from 'my-vm-1':

        exit

9. Acquire the external IP address (natIP) of 'my-vm-1':

        gcloud compute instances describe my-vm-1 --zone us-central1-a

10. copy the external IP adress and paste it into a browser window and confirm that you see the server's home page.
