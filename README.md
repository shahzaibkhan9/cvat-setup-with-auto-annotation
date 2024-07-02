# CVAT Setup Guide

Setup and deploy CVAT with serverless auto annotations using a custom YOLO model.

Includes step-by-step instructions for installation, configuration, and deployment.

## Clone CVAT Source Code

Clone the CVAT repository from GitHub:

```sh
git clone https://github.com/cvat-ai/cvat
cd cvat
```

## Install `nuctl` Command Line Tool

To build and deploy serverless functions, you need to install the `nuctl` tool. Download version 1.13.0. Make sure the version matches the version specified in `docker-compose.serverless.yml`.

Download using `wget`:

```sh
wget https://github.com/nuclio/nuclio/releases/download/1.13.0/nuctl-1.13.0-linux-amd64
```

Grant execution permission and create a soft link:

```sh
sudo chmod +x nuctl-1.13.0-linux-amd64
sudo ln -sf $(pwd)/nuctl-1.13.0-linux-amd64 /usr/local/bin/nuctl
```

## Create a Nuclio Project

Create a project in Nuclio:

```sh
nuctl create project cvat
```

## Bring Up CVAT with Auto Annotation Tool

From the CVAT root directory, run:

```sh
docker compose -f docker-compose.yml -f docker-compose.dev.yml -f components/serverless/docker-compose.serverless.yml up -d --build
```

## Create a Superuser

Create a login/password in the console:

```sh
docker exec -it cvat_server bash -ic 'python3 ~/manage.py createsuperuser'
```

## Access CVAT and Nuclio

- Open CVAT at [http://localhost:8080/](http://localhost:8080/)
- Open Nuclio at [http://localhost:8070/](http://localhost:8070/)

## Deploy a Function in Nuclio

1. Enter the CVAT project in Nuclio using Chrome.
2. Click on the **NEW FUNCTION** button.
3. IMPORT the [YAML](https://github.com/shahzaibkhan9/cvat-setup-with-auto-annotation/blob/main/function.yaml) file and then CREATE.

Copy all the code from [main.py](https://github.com/shahzaibkhan9/cvat-setup-with-auto-annotation/blob/main/main.py) and change the model path in the line you want to deploy.

### Configure Volumes

Go to the **Configurations** tab, scroll down to the **Volumes** section:
- Name: `custom-model`
- Type: `Host path`
- Mount Path: `/opt/nuclio/custom_model.pt`
- Host Path: `<directory>/custom_model.pt`

Click **Deploy** and wait. After deployment, refresh the CVAT tab in Chrome. You should see the **Models Tab** with the YOLO model inside.