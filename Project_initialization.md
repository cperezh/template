# Configuration

### Using only Dockerfile (no docker compose)

To initilize the project for Docker configuration do this:

1. Check **Container Tools** extension is installed.
1. Press F1 to open command palette.
1. Use **Containers: Add Docker Files to Workspace** to create docker environment files.
    - This will create:
        - Dockerfile
        - tasks.json: build and run taks to create and run docker image based on Dockerfile
        - launch.json: debug configuration
    - If you already have a Dockerfile file, you can use **Containers: Initialize for container debugging** command.

Tasks.json
---

The important thing is that it takes into account de WORKDIR statement in the Dockerfile. Therefore, the path to the entrypoint must take it into account too.

```python
{
    "type": "docker-run",
    "label": "docker-run: debug",
    "dependsOn": [
        "docker-build"
    ],
    "python": {
        "file": "main.py" # This is the entrypoint thant overrides the one in Dockerfile
    }
}
```

Launch.json
---

This one is the configuration for launching on debug the docker container. Using F5, VSC launches the debug. If no breakpoints
are set on VSC, then is like running the app.

**Important**: it doesn't take into account the WORKDIR on Dockerfile when linking the code at VSC and the code at the container. 
You must use absolute path on the pathmappings setting.

```python
{
    "configurations": [
        {
            "name": "Containers: Python - General",
            "type": "docker",
            "request": "launch",
            "preLaunchTask": "docker-run: debug",
            "python": {
                "pathMappings": [
                    {
                        "localRoot": "${workspaceFolder}",
                        "remoteRoot": "/" # Absolute path at the container, not using WORKDIR statement.
                    }
                ],
                "projectType": "general"
            }
        }
    ]
}
```

### Using Docker Compose

The debugger behaves differently on using docker compose. With docker, the debugger will use "tasks.json" to build
and run the specific container needed. This is done because on "launch.json" we include a ```preLaunchTask``` pointing to
the docker run execution. The same way, the docker run task "depends on" the build task, so everytime we launch
the debugger (F5), the build and run docker tasts are launched.

When working with docker compose is not the same behaviour. 

1. Press F1
1. Press **Containers: Add Compose File to Workspace". This will add a compose file and it's debug file, which will be used
to start services on debug mode.
1. Double click on the compose debug file and press **compose up**. Thant way services will start.
1. Create an **attach** task on **launch.json** so the debugger can attach the debug session to the running services you
started on the compose up commnand. Check that the debug "type" is "debugpy".

```
 {
            "name": "Python Debugger: Remote Attach",
            "type": "debugpy",
            "request": "attach",
            "connect": {
                "host": "localhost",
                "port": 5678
            },
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}",
                    "remoteRoot": "/"
                }
            ]
        },
```

1. Press F5 to launch debugging session.