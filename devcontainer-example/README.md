# Configuration for VS Code development

## .Devcontainer Configuration

### Adding environment variables

 You can set environment variables in your container without altering the container image by using one of the options below. However, you should verify Terminal > Integrated: *Inherit Env* is checked in settings or the variables you set may *not appear* in the Integrated Terminal.

01. Option 1: Add individual variables

- Dockerfile or image:

 Add the containerEnv property to devcontainer.json to set variables that should apply to the entire container or remoteEnv to set variables for VS Code and related sub-processes (terminals, tasks, debugging, etc.):

 ```json
"containerEnv": {
    "MY_CONTAINER_VAR": "some-value-here",
    "MY_CONTAINER_VAR2": "${localEnv:SOME_LOCAL_VAR}"
},
"remoteEnv": {
    "PATH": "${containerEnv:PATH}:/some/other/path",
    "MY_REMOTE_VARIABLE": "some-other-value-here",
    "MY_REMOTE_VARIABLE2": "${localEnv:SOME_LOCAL_VAR}"
}
 ```

 As this example illustrates, containerEnv can reference local variables and remoteEnv can reference both local and existing container variables.

- Docker Compose: 
 Since Docker Compose has built-in support for updating container-wide variables, only remoteEnv is supported in devcontainer.json:

 ```json
 "remoteEnv": {
    "PATH": "${containerEnv:PATH}:/some/other/path",
    "MY_REMOTE_VARIABLE": "some-other-value-here",
    "MY_REMOTE_VARIABLE2": "${localEnv:SOME_LOCAL_VAR}"
}
 ```

 As this example illustrates, remoteEnv can reference both local and existing container variables.

 To update variables that apply to the entire container, update (or extend) your `docker-compose.yml` with the following for the appropriate service:

 ```yml
 version: '3'
 services:
  your-service-name-here:
    environment:
      - YOUR_ENV_VAR_NAME=your-value-goes-here
      - ANOTHER_VAR=another-value
     # ...
 ```

02. Option 2: Use an env file

 If you have a large number of environment variables that you need to set, you can use a .env file instead. VS Code will automatically pick up a file called .env in your workspace root, but you can also create one in another location.

 First, create an environment file somewhere in your source tree. Consider this `.devcontainer/devcontainer.env` file:

 ```env
 YOUR_ENV_VAR_NAME=your-value-goes-here
 ANOTHER_ENV_VAR_NAME=your-value-goes-here
 ```

 Next, depending on what you reference in devcontainer.json:
 
- Dockerfile or image: Edit devcontainer.json and add a path to the devcontainer.env

 ```json
"runArgs": ["--env-file",".devcontainer/devcontainer.env"]
 ```

- Docker Compose: Edit docker-compose.yml and add a path to the devcontainer.env file relative to the Docker Compose file:

 ```json
 version: '3'
 services:
  your-service-name-here:
    env_file: devcontainer.env
    # ...
```

--------

### Starting a process when the container starts

When you are working in a development container, you may want to execute a command or start something each time the container starts.The easiest way to do this is using the postStartCommand property in devcontainer.json.

- Option 1: `postStartCommand` (Easy way)
 This is the easiest way to do this is using the `postStartCommand` property in devcontainer.json

 For example, if you wanted to run yarn install every time you connected to the container to keep dependencies up to date, you could add the following:

 ```json
 "postStartCommand": "yarn install"
 ```

 In other cases, you may want to start up a process and leave it running. This can be accomplished by using nohup and putting the process into the background using &. For example:

 ```json
 "postStartCommand": "nohup bash -c 'your-command-here &'"
 ```

 Those familiar with Linux may expect to be able to use the systemctl command to start and stop background services managed by something called `systemd`. Unfortunately, `systemd` has overhead and is generally not used in containers as a result.

 In many cases, there is a command you can run instead (for example, `sshd`). And on Debian/Ubuntu, there are often scripts under /etc/init.d that you can run directly.

 ```json
 "postStartCommand": "/etc/init.d/ssh start"
 ```

 These systems also include a service command that will use systemctl or /etc/init.d scripts based on what is installed.

 ```json
 "postStartCommand": "service ssh start"
 ```

- Option 2: Adding startup commands to the Docker image instead using `postStartCommand`

 While postStartCommand is convenient and allows you to execute commands in your source tree, you can also add these steps instead to a Dockerfile using a custom ENTRYPOINT or CMD.

 When referencing a Dockerfile in devcontainer.json, the default entrypoint and command is overridden. First,  disable this behavior using the overrrideCommand property.

 ```json
 "overrideCommand": false
 ```

 The overrideCommand property defaults to true because many images will immediately exit if a command is not  specified. Instead, we will need to handle this in our Dockerfile.

 Next, consider this Dockerfile:

 ```dockerfile
 FROM mcr.microsoft.com/vscode/devcontainers/base:0-focal

 COPY docker-entrypoint.sh /
 RUN chmod +x /docker-entrypoint.sh
 ENTRYPOINT [ "/docker-entrypoint.sh" ]
 CMD [ "sleep", "infinity"' ]
 ```

 The CMD here makes sure the container stays running by default. Keeping your startup steps in the ENTRYPOINT allows you to safely override the command when using docker run with your image or using Docker Compose. This resolves to  the following:

 ```/docker-entrypoint.sh sleep infinity```

 Next, create a docker-entrypoint.sh script:

 ```shell
 #!/usr/env bash

 echo "Hello from our entrypoint!"

 exec "$@"
 ```

 Anything you execute in this file will then fire each time the container starts. However, it's important to include the last exec "$@" line since this is what will cause the command sleep infinity in our example to fire.

 Finally, if you are using Docker Compose, be sure that neither the entrypoint nor command properties are set for your container.

 That's it!

--------

### Adding another local file mount

***Note:
Mounting the local file system is not supported in GitHub Codespaces. See developing inside a container on a remote Docker host for information on mounting remote folders in this scenario.

You can add a volume bound to any local folder by using the following appropriate steps, based on what you reference in `devcontainer.json`:

- Dockerfile or image: Add the following to the mounts property

 ```json
 "mounts": [
  "source=/local/source/path/goes/here,target=/target/path/in/container/goes/here,type=bind,consistency=cached"
 ]
 ```

 You can also reference local environment variables or the local path of the workspace. For example, this will bind mount ~ ($HOME) on macOS/Linux and the user's folder (%USERPROFILE%) on Windows and a sub-folder in the workspace to a different location:

```json
"mounts": [
    "source=${localEnv:HOME}${localEnv:USERPROFILE},target=/host-home-folder,type=bind,consistency=cached",
    "source=${localWorkspaceFolder}/app-data,target=/data,type=bind,consistency=cached"
]
```

- Docker Compose: Update (or extend) your docker-compose.yml with the following for the appropriate service:

```yml
version: '3'
services:
  your-service-name-here:
    volumes:
      - /local/source/path/goes/here:/target/path/in/container/goes/here:cached
      - ~:/host-home-folder:cached
      - ./data-subfolder:/data:cached
     # ...
```

If you've already built the container and connected to it, run Remote-Containers: Rebuild Container from the Command Palette (F1) to pick up the change. Otherwise run Remote-Containers: Open Folder in Container... to connect to the container

--------



## Docker-compose

--------

## DEBUG in vscode

--------

### Python debug


[python debug for frappe development](https://github.com/frappe/frappe/wiki/Using-the-VSCode-Debugger-with-Frappe)

--------

### nodejs debug

--------

## References

### Container development



[containers-tutorial](https://code.visualstudio.com/docs/remote/containers-tutorial)
[devcontainer property](https://code.visualstudio.com/docs/remote/devcontainerjson-reference)
[Advance devContainer](https://code.visualstudio.com/docs/remote/containers-advanced)
[More about Developing inside a Container](https://code.visualstudio.com/docs/remote/containers)

### Python development

[python debug for frappe development](https://github.com/frappe/frappe/wiki/Using-the-VSCode-Debugger-with-Frappe)

### WSL Development

[More about Developing in WSL](https://code.visualstudio.com/docs/remote/wsl)

[Remote Development Tips and Tricks](https://code.visualstudio.com/docs/remote/troubleshooting)