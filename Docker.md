# Kali Linux and Metasploitable in Docker

## List of Contents

1. [Install Docker](#install-docker)

2. [Setup Docker Network](#setup-docker-network)

3. [Install Kali Linux](#install-kali-linux)

4. [How to start and use Kali Linux](#how-to-start-and-use-kali-linux)

5. [Install Metasploitable](#install-metasploitable)

6. [How to start and use Metasploitable](#how-to-start-and-use-metasploitable)

7. [How to copy image files to and from Kali](#how-to-copy-image-files-to-and-from-kali)

8. [When you are done](#when-you-are-done)

## Install Docker

Note: Before you start, if a command starts with the word "docker" it needs to be run in outside of the container's interactive terminal. If it does not start with "docker", chances are it needs to be run in the container's interactive terminal.

Please follow the instructions on the official Docker website to install Docker on your system.

<https://docs.docker.com/desktop/install/mac-install/>

Make sure you choose the version for your architecture. (Intel or Apple Silicon). Most likely you will need the Apple Silicon version, if you had issues with VirtualBox.

## Setup Docker Network

Once Docker is installed, you need to create a network for the containers to safely communicate with each other. This is done with the following command in a terminal app of your choice (e.g. Terminal, iTerm, Kitty, etc.):

```bash
docker network create pentest --internal
```

Note the "internal"-flag which disables external access to the network. This is important for security reasons (If you are on a public network).

You will therefore also need to create a second network for the Kali container to communicate with the outside world. This is done with the following command:

```bash
docker network create apt
```

In both commands "pentest" and "apt" are the names of the networks. You can choose any name you like.

Once done you can check the networks with the following command:

```bash
docker network ls
```

There will likely be more networks than just the two you created. You can ignore those.

## Install Kali Linux

You now need to download a Kali Linux image from the Docker Hub. This is done with the following command:

```bash
docker pull kalilinux/kali-rolling
```

You can also do this in the Docker Desktop app by searching in the top bar for "kalilinux/kali-rolling" and then clicking on "Pull".

You can check if the image was downloaded with the following command:

```bash
docker images
```

Now you can create a container from the image with the following command:

```bash
docker run --network=pentest -h attacker -it --name kalibox kalilinux/kali-rolling
```

This will create the container and start it. The container will be named "kalibox" and the hostname will be "attacker". You can choose any name if you like.

## How to start and use Kali Linux

At this point the container should already be running. You can at any point stop it with the following command:
`
exit
` in the container's terminal, or in another terminal with the following command:
`
docker stop kalibox
`.

Now you will need to change the network of the container to the "apt" network. This is done with the following command once the container is stopped:

```bash
docker network connect apt kalibox
```

This is necessary to install all the tools you will need for the labs.

When you want to start the container again, you can do so with the following command:

```bash
docker start -i kalibox
```

Please install the following tools in the container:

- nano
- steghide
- openssl

You can install them with the following command:

First run:

```bash
apt update
```

Then run the following command changing the name of the tool to the one you want to install:

```bash
apt install <toolname>
```

Press "y" then enter or just enter when asked if you want to install the tool.

You can also chain multiple tool names together (seperated by a space) to install them all at once.

```bash
apt install <toolname1> <toolname2> <toolname3>
```

**Note:** toolnames are written without <>. It is just to indicate that you need to change the name.

When that is done, shutdown the container and then change the network back to "pentest" with the following command:

```bash
docker network connect pentest kalibox
```

You can now start the container again and you are ready to install Metasploitable.

## Install Metasploitable

First make sure you are in the Macbook terminal and not the Kali interactive terminal. This can usualy be seen by looking to the left of the cursor. If it says something like "root@attacker" you are in the Kali interactive terminal. Otherwise you are most likely in the Macbook terminal.

You now need to download a Metasploitable image from the Docker Hub. This is done with the following command:

```bash
docker pull tleemcjr/metasploitable2
```

Now create the container with the following command:

```bash
docker run --network=pentest -h victim -it --name metasploitable2 tleemcjr/metasploitable2
```

## How to start and use Metasploitable

Just like with Kali Linux, you can stop the container with either
`exit` in the container termainal or `docker stop metasploitable2` in another terminal.

You can start the container again with the following command:

```bash
docker start -i metasploitable2
```

## How to copy image files to and from Kali

During the labs you will need to copy image files to and from the Kali Linux container.

To do this you can use the following commands:

To the container:

```bash
docker cp <source> <container>:<destination>
```

**Note:** again the <> are not part of the command. They are just there to indicate that you need to change them to the correct values/inputs.

From the container:

```bash
docker cp <container>:<source> <destination>
```

An example for copying a file from the container to the host:

```bash
docker cp kalibox:/example.png ./Downloads
```

This would copy the file "example.png" from the container "kalibox" root folder ('/') to the folder "Downloads" in your home directory, provided the terminal is in your home directory. A common symbol for the home directory is "~".

Note: this needs to be done in a separate terminal, not in the container's interactive terminal.

---

You should now be able to do the labs without virtual machines on M1 and M2 macs.

## When you are done

When you are done with the labs for the day you should stop the containers as explained earlier, as they will use up resources on your computer.

You should also shutdown the docker desktop app, as it will also use up resources. This can be done through the docker icon in the top bar of your mac's display (the one that kind of looks like a whale).

When you are done with the semester you can delete the containers and images using the docker desktop user interface.
