# Uptime Kuma on Docker (Ubuntu VM Lab)

A hands-on lab where I deployed [Uptime Kuma](https://github.com/louislam/uptime-kuma), a self-hosted monitoring tool, as a Docker container on an Ubuntu Server VM, used it to monitor public websites, and set up Telegram alerts for downtime.

## Goal
Practice running containerized services on Linux and understand the basics of availability monitoring and alerting, which are core parts of security operations.

## Environment
- Host: MacBook Pro (M1 Pro) running UTM
- Guest: Ubuntu Server 26.04 LTS (ARM64)
- Docker, with remote access over SSH from the Mac
- Image: `louislam/uptime-kuma:2`

## Setup

1. Verify the Docker installation with a test container:

~~~
davidbarthy@ubuntu-vm:~$ docker run hello-world
~~~

2. Start Uptime Kuma:

~~~
davidbarthy@ubuntu-vm:~$ docker run -d --restart=always -p 3001:3001 -v uptime-kuma:/app/data --name uptime-kuma louislam/uptime-kuma:2
~~~

3. Confirm it is running and healthy:

~~~
davidbarthy@ubuntu-vm:~$ docker ps
~~~

4. Open `http://<vm-ip>:3001` in a browser and create the admin account.

5. Add HTTP monitors for Google and GitHub.

## What each option does
- `-d` runs the container in the background
- `--restart=always` starts it again after a reboot or crash
- `-p 3001:3001` exposes the web interface on port 3001
- `-v uptime-kuma:/app/data` keeps data in a named volume so it survives container restarts
- `--name uptime-kuma` gives the container a readable name

## Verification
I confirmed the container settings with `docker inspect`:

~~~
davidbarthy@ubuntu-vm:~$ docker inspect --format '{{.Config.Image}}' uptime-kuma
davidbarthy@ubuntu-vm:~$ docker inspect --format '{{.HostConfig.RestartPolicy.Name}}' uptime-kuma
davidbarthy@ubuntu-vm:~$ docker inspect --format '{{json .Mounts}}' uptime-kuma
~~~

These showed the image tag `louislam/uptime-kuma:2`, the restart policy `always`, and the `uptime-kuma` volume mounted at `/app/data`.

## Alerting
I connected Uptime Kuma to a Telegram bot so a monitor going down sends me a message. To test it, I added a temporary monitor pointing at a domain that doesn't exist and confirmed the alert arrived.




## What I learned
- How a container differs from a full VM
- How port mapping and named volumes work in Docker
- How to check container health with `docker ps` and inspect settings with `docker inspect`
- How to connect a monitoring tool to Telegram for alerts, and why bot tokens must be kept secret
- Why exposing a port to the network has security consequences
- Why membership of the `docker` group is effectively root access on the host
  ## screenshots

   <img width="1204" height="238" alt="telegram-alert" src="https://github.com/user-attachments/assets/996bfa92-059e-4bb4-82ac-d0c710769942" />
<img width="1884" height="1056" alt="docker-ps" src="https://github.com/user-attachments/assets/707dd153-6ab8-49ad-8925-a6ca1750f55e" />
<img width="1910" height="952" alt="dashboard" src="https://github.com/user-attachments/assets/3a9d9e9b-1432-4b05-9410-7b1b8b3cbdbb" />


