# Custom apps management for Production

To add your own Frappe/ERPNext apps to the image, we'll need to create a custom image with the help of a unique wrapper script

## Create Custom_app docker file for Install applictaion to bench

> For the sake of simplicity, in this example, we'll be using a place holder called `[custom]`, and we'll be building off the edge image.

- Step 1: Create two directories called `[custom]-worker` and `[custom]-nginx` in the `build` directory.

```shell
cd frappe_docker
mkdir ./build/[custom]-worker ./build/[custom]-nginx
```

- Step 2: Create a `Dockerfile` in `./build/[custom]-worker` with the following content:

```Dockerfile
FROM frappe/erpnext-worker:edge

RUN install_app [custom] https://github.com/[username]/[custom] [branch]
# Only add the branch if you are using a specific tag or branch.
```

>**Note:** Replace `https://github.com/[username]/[custom]` above with your custom app's Git repository URL (may include credentials if needed). Your custom app Git repository **must** be named exactly as the custom app's name, and use the same branch name as Frappe/ERPNext branch name that you use.

- Step 3: Create a `Dockerfile` in `./build/[custom]-nginx` with the following content:

```Dockerfile

ARG NODE_IMAGE_TAG=14-buster-slim
ARG DOCKER_REGISTRY_PREFIX=frappe
ARG IMAGE_TAG=develop

FROM node:${NODE_IMAGE_TAG}

ARG GIT_REPO=https://github.com/frappe/erpnext
ARG GIT_BRANCH=develop
ARG FRAPPE_BRANCH=${GIT_BRANCH}

COPY build/[custom]-nginx/install_app.sh /install_app
RUN chmod +x /install_app && \
  apt-get update -y && \
  apt-get install build-essential git python2 -y && \
  apt-get clean && \
  rm -rf /var/lib/apt/lists/*
RUN /install_app [custom] https://github.com/[username]/[custom] [branch]

# Install required apps with following command
  # /install_app <app_name> <repo> <app_branch>
 # && /install_app posawesome https://github.com/yrestom/POS-Awesome master && \
 # /install_app frappe_s3_attachment https://github.com/zerodha/frappe-attachments-s3 master && \
 # /install_app metabase_integration https://github.com/pipech/frappe-metabase master && \
 # /install_app bookings https://github.com/franknyarkoh/bookings master && \
 # /install_app bench_manager https://github.com/frappe/bench_manager develop

FROM frappe/erpnext-nginx:edge

COPY --from=0 /home/frappe/frappe-bench/sites/ /var/www/html/
COPY --from=0 /rsync /rsync
# Add Run command to add application in to apps.txt file as below for the need of more require app to install
 # RUN echo -n "posawesome\nfrappe_s3_attachment\nmetabase_integration\nbookings\nbench_manager" >> /var/www/html/apps.txt
RUN echo -n "\n[custom]" >> /var/www/html/apps.txt


VOLUME [ "/assets" ]

ENTRYPOINT ["/docker-entrypoint.sh"]
CMD ["nginx", "-g", "daemon off;"]
```

- Step 4: Copy over the `install_app.sh` file from `./build/erpnext-nginx`

    Add Install_app.sh to [custom]-nginx folder for [custom]-nginx Dockerfile to use for install app and sent environment value to install_app.sh

```shell
cp ./build/erpnext-nginx/install_app.sh ./build/[custom]-nginx
```

-----

## Build Image form your dockerfile and push to registory

to install you application when deploy frappe docker to production server, Using Docker `build` command to build docker Image (to install custom app to apps folder in bench env) and push docker images (Custom_app-nginx & Custom_app-worker) to your Repository to do that private registory(docker hub in your organization name space or Private Container registory) is required.

more detail: [Set up docker registory for development](https://docs.docker.com/registry/)

- build docker image(Custom app)

    ```shell
    # Docker pull for fetch lastest version of frappe and Erpnext(Optional) Image file
    # To use frappe and/or erpnext get-app command to install app
    # [Image tags]-->To specify which custom app image to used (version)

    docker pull frappe/frappe-nginx:[Image tag]
    docker pull frappe/erpnext-worker:[Image tag]

    # Run command in frappe_docker directory
    # [branch]--> To Specify which custom_app code version will be use when this Image is used.
    # build Custom_app-nginx image

    docker build --no-cache --build-arg GIT_BRANCH=[branch] -t [custom]-nginx:[Image tag] -f ./build/[cuttom]-nginx/dockerfile .

    # build Custom_app-worker image

    docker build --no-cache --build-arg GIT_BRANCH=[branch] -t [custom]-worker:[Image tag] -f ./build/[custom]-worker/dockerfile .
    ```

>> Note: `--no-cache` option to tell docker not to use in memory cache to build the Image

- Push Custom_app's docker image to Container Registory

    ```shell
    # Login to container registory from your development machine
    # docker login [your-registory-domain]

    docker login registry.git.arterypartner.com
    
    # use `docker tag` to tag your image
    
    docker tag [custom]-worker registry.git.arterypartner.com/[custom]/frappe_docker/[custom]-worker:[Image tag]
    docker tag [custom]-nginx registry.git.arterypartner.com/[custom]/frappe_docker/[custom]-nginx:[Image tag]
    
    # push your image to container registory
    
    docker push registry.git.arterypartner.com/[custom]/frappe_docker/[custom]-worker:[Image tag]
    docker push registry.git.arterypartner.com/[custom]/frappe_docker/[custom]-nginx:[Image tag]
    ```

Install custom app to production cluster(k8s) --> [reference](https://about.lovia.life/docs/infrastructure/erpnext/developing-custom-apps/)

-----

## Install Custom app (Production Env.)

To install Custom app in docker production environment. It is quite tricky due to type of deployment(single or multi-server) and no bench command to install other application after deploy the container with docker-compose[single bench](./single-bench.md) or `docker-compose -f` command for [multi-bench environment](./multi-bench.md).

### For single-bench

Customize `./docker-compose.yml` in frappe_docker directory and add your component to compose file

### For multi-bench

Customize `./installation/docker-compose-[custom].yml`  and replace all instances of `[app]` with the name of your app. With command:

```shell
sed -i "s#\[app\]#[custom]#" ./installation/docker-compose-custom.yml
```

>*Note* this `docker-compost-[custom].yml` need to create per applications and need to specify in `docker-compose -f` command to combined into installtion process

Install like usual, except that when you set the `INSTALL_APPS` variable in .env file to `erpnext,[custom]` to add custom app to installation process.

refer to single-bench install [here](./single-bench.md)
refer to single-bench install [here](./multi-bench.md)
