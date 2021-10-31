# Using Portainer in development

## Pre-Request

- docker lastest version

## How to install Portainer in Ubuntu?

```shell
# update your machine and upgrade dependency
sudo apt update
sudo apt upgrade
# Install Docker
sudo apt install docker.io
```

## Create volume for portainer & install Portainer

```shell
sudo docker volume create portainer_data
sudo docker run -d -p 8000:8000 -p 9000:9000 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer
```

## Connect Portainer Docker With Remote Docker Environment

It was simple to connect to the local docker environment. However, before we can connect to our remote docker environment, we must first configure a few things on the remote docker server.

- Expose Remote Docker Api

    We need to expose our remote docker API in order to connect to it. Using SSH, connect to your remote server.
    Once logged in, create the configuration at:

    ```shell
    sudo nano /etc/systemd/system/docker.service.d/remote-api.conf
    ```

    Now paste the following in the file.

    ```shell
    [Service]
    ExecStart=/usr/bin/dockerd -H tcp://0.0.0.0:2375 -H unix://var/run/docker.sock
    ```

    Now press Ctrl + O to save the file and Ctrl + X to exit. Take note of the port number **2375**, which will be used to connect to this docker from portainer.

- Reload service using command:

    ```shell
    sudo systemctl daemon-reload
    sudo service docker restart

    # or 

    sudo systemctl restart docker 
    ```

    Now the configuration part is done.

- Add Remote Environment to portainer

    Open portainer in the web browser, click Endpoints from the sidebar, and Add environment. Select Docker from the Environment type.
    > If you want to use TLS, then toggle it on and upload the certificates. Rest of the fields are optional. Finally, click Add environment.
    > Important: If you’re using a host like Google Cloud, you’ll need to open port 2375 on your firewall. If you see any errors, make sure port 2375 is open. Only allow whitelisted clients to connect via port 2375 for security reasons.

Check this [document for More detail](https://www.linuxandubuntu.com/home/portainer-docker-manage-docker-containers)

To Setup Portainer with SSL [follow  this document](https://docs.portainer.io/v/ce-2.9/advanced/ssl#docker-standalone)
