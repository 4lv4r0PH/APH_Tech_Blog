# Using the official Nessus Docker Image on Kali Linux

I have been using Kali Linux as a student Pen-tester for a couple of years, and I always run into the same issue of having to reinstall some of my favourite tools every time a new version of Kali is released. One of those tools is Tenable Nessus vulnerability scanner, which I enjoy working with because it is easy to understand and use. Fortunately, I discovered Docker containers. Docker has allowed me to deploy Nessus in its own containerised environment in the Kali Virtual Machine. Doing this facilitates me re-deploying Nessus on every new version of Kali's VM.

This blog post is a quick tutorial on how to pull the Nessus Docker Image into Kali, create a container and export this container as a file.

## Update Kali

First, we need to update Kali. We use the following commands for that:

```bash
sudo apt update
```

```
sudo apt full-upgrade -y
```

<figure><img src=".gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>

## Install Docker

For Docker, I am using the docker.io package as it is already in the Kali repository. Use the following commands to install it:

```bash
sudo apt install docker.io
```

<figure><img src=".gitbook/assets/image (26).png" alt=""><figcaption></figcaption></figure>

## Start, test, and configure Docker

To test that Docker has been installed correctly, use:

```bash
sudo docker run hello-world
```

<figure><img src=".gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

To enable Docker so it starts every time Kali boots, use:

```bash
sudo systemctl enable docker --now
```

Otherwise, use the command below if you do not want docker to start on every reboot:

```bash
sudo systemctl start docker
```

If you want to add yourself to the docker group to avoid typing sudo every time, use the following command:

```bash
sudo usermod -aG docker $USER
```

## Pull Tenable Nessus Image and Create a Container

Now, we pull the Docker Nessus Image using the command below. This can take several minutes as the image will be downloaded from the repository:

```bash
docker pull tenableofficial/nessus
```

After the download finishes, we have two options on how we want to create a container. These are Detached mode and Attached mode. Detached mode means the container will run in the background, and Attached mode means that the container will run attached or on the Terminal session. We can also use the option "--name" to give our container a name, and the option "-p 8834:8834" to bind the computer port 8834 to the container port 8834:

```bash
sudo docker run -d --name official_nessus -p 8834:8834 tenableofficial/nessus
```

Then we confirm that our container is running with the command:

```bash
sudo docker ps
```

<figure><img src=".gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

ps without options will show all the running containers, if we want to see every container in the machine use the option -a

```bash
sudo docker ps -a
```

<figure><img src=".gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

Now that our container is running we can use any web browser to access Nessus web interface, typing the localhost address and the port we set up before "https://127.0.0.1:8834". Then Click "Advanced..." and "Accept the risk", and we will see the Nessus web interface.

<figure><img src=".gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

Click "Continue"

<figure><img src=".gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

Select "Register for Nessus Essentials" as this is the license used for educational purposes, and is free of charge.

<figure><img src=".gitbook/assets/image (25).png" alt=""><figcaption></figcaption></figure>

Fill up your details and click "Register"

<figure><img src=".gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

You will get an activation code. Click "Continue"

<figure><img src=".gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

Create an Administrator account, and click "Submit".

<figure><img src=".gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>

Nessus will update and download all necessary plugins. This can take several minutes.

<figure><img src=".gitbook/assets/image (22).png" alt=""><figcaption></figcaption></figure>

Finally, the main page opens. It will show an advertisement window for Nessus Expert. Here, we can start a trial to test additional functionality, or close the advertisement window and stick to Nessus Essentials.

<figure><img src=".gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

Nessus will then compile the plugins previously downloaded. This can take a long time. Hovering the mouse pointer over the rotating arrows will show the compilation percentage.

<figure><img src=".gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

## Exporting Nessus' Docker container as an image

Now that we have our container configured and working, we can export it as a .tar file. This will allow us to make a backup or deploy it in a different VM or machine running docker.&#x20;

First, we stop the container with the command below. We can use the name that we gave the container when we created it or the container ID.

```bash
sudo docker stop official_nessus
```

Then we use the command below to create a new image from our ready-to-use container. We use the container's ID after "commit" followed by the name of the new image:

```bash
sudo docker commit 8efa3d307b66 current/nessus
```

After that, we can verify if the new image has been created

<figure><img src=".gitbook/assets/image (23).png" alt=""><figcaption></figcaption></figure>

Then we use the following command to save (export) our Nessus container:

```bash
sudo docker save current/nessus > current_nessus.tar
```

<figure><img src=".gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>

Now that we have a .tar file, we can move it to another Kali VM or to another Linux Distribution. In the image below I used Linux Mint. The command is "sudo docker" with the option "load".

```bash
sudo docker load < current_nessus.tar
```

<figure><img src=".gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

We can verify that there is a new image named current/nessus as when we ran the "commit" command.

<figure><img src=".gitbook/assets/image (21).png" alt=""><figcaption></figcaption></figure>

Now we can create a new container with that image, using the command we used in the Kali VM.

```
sudo docker run -d --name ready_nessus -p 8834:8834 current/nessus
```

<figure><img src=".gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

Open the web browser and type https://127.0.0.1:8834, and follow the same procedure as before.

<figure><img src=".gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

Useful links:

[https://hub.docker.com/r/tenableofficial/nessus](https://hub.docker.com/r/tenableofficial/nessus)

[https://docs.tenable.com/nessus/Content/DeployNessusDocker.htm](https://docs.tenable.com/nessus/Content/DeployNessusDocker.htm)
