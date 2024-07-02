# CVAT Setup Guide

## Setup and Deploy CVAT with Serverless Auto Annotations Using a Custom YOLO Model

This guide provides step-by-step instructions for installation, configuration, and deployment of CVAT (Computer Vision Annotation Tool) with serverless auto annotations using a custom YOLO model.

## Clone CVAT Source Code

1. Clone the CVAT repository from GitHub:
   ```sh
   git clone https://github.com/cvat-ai/cvat
   cd cvat
   ```

## Install `nuctl` Command Line Tool

2. To build and deploy serverless functions, you need to install the `nuctl` tool. Download version 1.13.0. Make sure the version matches the version specified in `docker-compose.serverless.yml`.

   Download using `wget`:
   ```sh
   wget https://github.com/nuclio/nuclio/releases/download/1.13.0/nuctl-1.13.0-linux-amd64
   ```

3. Grant execution permission and create a soft link:
   ```sh
   sudo chmod +x nuctl-1.13.0-linux-amd64
   sudo ln -sf $(pwd)/nuctl-1.13.0-linux-amd64 /usr/local/bin/nuctl
   ```

## Create a Nuclio Project

4. Create a project in Nuclio:
   ```sh
   nuctl create project cvat
   ```

## Bring Up CVAT with Auto Annotation Tool

5. From the CVAT root directory, run:
   ```sh
   docker compose -f docker-compose.yml -f docker-compose.dev.yml -f components/serverless/docker-compose.serverless.yml up -d --build
   ```

## Create a Superuser

6. Create a login/password in the console:
   ```sh
   docker exec -it cvat_server bash -ic 'python3 ~/manage.py createsuperuser'
   ```

## Access CVAT and Nuclio

7. Open CVAT and Nuclio in your browser:
   - CVAT: [http://localhost:8080/](http://localhost:8080/)
   - Nuclio: [http://localhost:8070/](http://localhost:8070/)

## Deploy a Function in Nuclio

8. Enter the CVAT project in Nuclio using Chrome.
9. Click on the **NEW FUNCTION** button.
10. IMPORT the [YAML](https://github.com/shahzaibkhan9/cvat-setup-with-auto-annotation/blob/main/function.yaml) file and then CREATE.
11. Copy all the code from [main.py](https://github.com/shahzaibkhan9/cvat-setup-with-auto-annotation/blob/main/main.py) and change the model path (if required) in the `line number 11` you want to deploy.

### Configure Volumes

12. Go to the **Configurations** tab, scroll down to the **Volumes** section:
    - Name: `custom-model`
    - Type: `Host path`
    - Mount Path: `/opt/nuclio/custom_model.pt`
    - Host Path: `<directory>/custom_model.pt`

13. Click **Deploy** and wait. After deployment, refresh the CVAT tab in Chrome. You should see the **Models Tab** with the YOLO model inside.

## Use CVAT with Your Custom Model

14. Open CVAT using [http://localhost:8080/](http://localhost:8080/).

15. Create a Project and upload a video or frames in a Task.

16. Go to [http://localhost:8080/tasks](http://localhost:8080/tasks).
    - Click 'Open' on your task.
    - Click 'Actions' on the top-right and select 'Automatic annotation'.
    - Select your custom model and click 'annotate'.

That's it!

## Stop the Server

17. To stop the server, run:
    ```sh
    docker compose -f docker-compose.yml -f docker-compose.dev.yml -f components/serverless/docker-compose.serverless.yml down
    ```