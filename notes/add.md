### Step 1: Pull the Docker Image (if not already locally available)

If the image is not already on your system, you can pull it from a Docker registry (e.g., Docker Hub). The command for pulling an image is:

```bash
docker pull <image_name>:<tag>
```

For example:

```bash
docker pull nginx:latest
```

If the image is already available on your system, you can skip this step.

### Step 2: Run a Container from the Image

The most straightforward command to create and run a container from an image is:

```bash
docker run <image_name>
```

However, for a typical setup, you'll want to specify additional options:

- **Container Name**: To easily identify and reference the container later.
- **Port Mapping**: To map container ports to the host.
- **Volume Mounting**: To persist data or share files.
- **Environment Variables**: To configure the container's runtime behavior.

A more comprehensive example of `docker run`:

```bash
docker run -d --name my-container -p 8080:80 -v /host/path:/container/path -e ENV_VAR=value <image_name>:<tag>
```

- **-d**: Run the container in detached mode (in the background).
- **--name**: Assign a custom name to the container.
- **-p 8080:80**: Map port 8080 on the host to port 80 on the container.
- **-v**: Mount a host directory or file to the container.
- **-e**: Set environment variables.

For example, to set up an Nginx container:

```bash
docker run -d --name my-nginx-container -p 8080:80 nginx:latest
```

This command does the following:

- Runs the container in the background (`-d`).
- Names the container `my-nginx-container`.
- Maps port `8080` on your host machine to port `80` on the container.
- Uses the `nginx:latest` image.

### Step 3: Verify the Container is Running

You can check if the container is running with:

```bash
docker ps
```

This command lists all running containers and shows their names, IDs, and other details.

### Step 4: Managing the Container

You can use additional commands to manage the container:

- **Start** an existing container:

  ```bash
  docker start my-container
  ```

- **Stop** the container:

  ```bash
  docker stop my-container
  ```

- **Remove** the container:

  ```bash
  docker rm my-container
  ```
