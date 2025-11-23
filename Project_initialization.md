Configuration
---

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
    