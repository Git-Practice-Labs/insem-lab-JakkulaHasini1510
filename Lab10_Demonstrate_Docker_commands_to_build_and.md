# Lab 10: Demonstrate Docker commands to build and push a sample image to DockerHub

## Objective

The objective of this lab is to demonstrate the complete Docker workflow for containerizing a simple application, building a Docker image, and pushing it to DockerHub. Students will learn how to:
- Create a simple application and Dockerfile
- Build a Docker image from the Dockerfile
- Tag Docker images appropriately
- Authenticate with DockerHub
- Push the built image to DockerHub repository
- Verify the image is available on DockerHub

## Tools / Technologies

- **Docker Desktop** or **Docker Engine** (v20.10 or higher)
- **DockerHub Account** (free account at https://hub.docker.com)
- **Text Editor** (VS Code, Nano, Vim, or any preferred editor)
- **Terminal/Command Prompt** (Bash, PowerShell, or CMD)
- **Git** (for version control and pushing lab files)

## Prerequisites

1. **Docker Installed**: Verify Docker is installed and running:
   ```bash
   docker --version
   docker info
   ```

2. **DockerHub Account**: Create a free account at https://hub.docker.com if you don't have one already.

3. **Basic Command Line Knowledge**: Familiarity with terminal/command prompt navigation.

4. **Internet Connection**: Required for pushing images to DockerHub.

5. **Sufficient Disk Space**: At least 2GB free space for Docker images.

## Steps / Commands

### Step 1: Create Project Directory and Application Files

Create a new directory for your Docker project:

```bash
mkdir hello-docker
cd hello-docker
```

**Explanation**: This creates a dedicated workspace for our Docker project to keep files organized.

---

### Step 2: Create a Simple Application

Create a simple Python application file named `app.py`:

```python
# app.py
print("Hello from Docker!")
print("This is a sample containerized application.")
print("Lab 10: Docker Build and Push Demonstration")
```

Or create an HTML file for a web-based example:

```html
<!-- index.html -->
<!DOCTYPE html>
<html>
<head>
    <title>Hello Docker</title>
</head>
<body>
    <h1>Hello from Docker!</h1>
    <p>This is a sample containerized application.</p>
    <p>Lab 10: Docker Build and Push Demonstration</p>
</body>
</html>
```

**Explanation**: This creates a simple application that will be containerized. You can choose either Python or HTML version.

---

### Step 3: Create a Dockerfile

Create a file named `Dockerfile` (no extension) in the same directory:

**For Python application:**
```dockerfile
# Use official Python runtime as base image
FROM python:3.9-slim

# Set working directory in container
WORKDIR /app

# Copy application file to container
COPY app.py .

# Define command to run the application
CMD ["python", "app.py"]
```

**For HTML application (using Nginx):**
```dockerfile
# Use official Nginx image as base
FROM nginx:alpine

# Copy HTML file to Nginx default directory
COPY index.html /usr/share/nginx/html/

# Expose port 80
EXPOSE 80

# Start Nginx (default CMD from base image)
```

**Explanation**: 
- `FROM` specifies the base image to build upon
- `WORKDIR` sets the working directory inside the container
- `COPY` transfers files from host to container
- `CMD` or `EXPOSE` defines the container's runtime behavior

---

### Step 4: Build the Docker Image

Build the Docker image with a tag:

```bash
docker build -t hello-docker:v1.0 .
```

**Explanation**: 
- `docker build` initiates the image building process
- `-t hello-docker:v1.0` tags the image with name "hello-docker" and version "v1.0"
- `.` specifies the build context (current directory containing Dockerfile)

---

### Step 5: Verify the Built Image

List all Docker images to confirm the build:

```bash
docker images
```

Or filter for your specific image:

```bash
docker images hello-docker
```

**Explanation**: This command displays all locally stored Docker images, showing repository name, tag, image ID, creation time, and size.

---

### Step 6: Test the Image Locally (Optional but Recommended)

**For Python application:**
```bash
docker run hello-docker:v1.0
```

**For HTML/Nginx application:**
```bash
docker run -d -p 8080:80 hello-docker:v1.0
# Visit http://localhost:8080 in browser
# Stop container after testing:
docker ps
docker stop <container_id>
```

**Explanation**: This runs a container from the image to verify it works correctly before pushing to DockerHub.

---

### Step 7: Login to DockerHub

Authenticate with DockerHub using your credentials:

```bash
docker login
```

Enter your DockerHub username and password when prompted.

Alternatively, use:
```bash
docker login -u <your-dockerhub-username>
```

**Explanation**: This authenticates your local Docker client with DockerHub, enabling you to push images to your repositories.

---

### Step 8: Tag the Image for DockerHub

Tag your image with your DockerHub username:

```bash
docker tag hello-docker:v1.0 <your-dockerhub-username>/hello-docker:v1.0
```

Also create a 'latest' tag:
```bash
docker tag hello-docker:v1.0 <your-dockerhub-username>/hello-docker:latest
```

**Explanation**: 
- DockerHub requires images to be tagged with format: `username/repository:tag`
- This creates a new tag pointing to the same image
- The `latest` tag is a convention for the most recent version

---

### Step 9: Push the Image to DockerHub

Push the tagged image to DockerHub:

```bash
docker push <your-dockerhub-username>/hello-docker:v1.0
docker push <your-dockerhub-username>/hello-docker:latest
```

**Explanation**: 
- `docker push` uploads the image layers to DockerHub
- Push both version-specific and latest tags
- Docker only uploads layers that don't already exist (efficient bandwidth usage)

---

### Step 10: Verify on DockerHub

1. Open browser and navigate to: `https://hub.docker.com/r/<your-dockerhub-username>/hello-docker`
2. Verify the repository exists and shows both tags (v1.0 and latest)
3. Check the image size and push timestamp

**Explanation**: This confirms the image was successfully pushed and is publicly accessible.

---

### Step 11: Pull and Test the Image from DockerHub (Verification)

Optionally, remove local image and pull from DockerHub to verify:

```bash
# Remove local images
docker rmi hello-docker:v1.0
docker rmi <your-dockerhub-username>/hello-docker:v1.0
docker rmi <your-dockerhub-username>/hello-docker:latest

# Pull from DockerHub
docker pull <your-dockerhub-username>/hello-docker:latest

# Run the pulled image
docker run <your-dockerhub-username>/hello-docker:latest
```

**Explanation**: This verifies that the image can be successfully pulled from DockerHub and runs correctly.

---

## Expected Output / Result

### During Build (Step 4):
```
[+] Building 5.3s (8/8) FINISHED
 => [internal] load build definition from Dockerfile
 => => transferring dockerfile: 200B
 => [internal] load .dockerignore
 => [internal] load metadata for docker.io/library/python:3.9-slim
 => [1/2] FROM docker.io/library/python:3.9-slim
 => [internal] load build context
 => => transferring context: 150B
 => [2/2] COPY app.py .
 => exporting to image
 => => exporting layers
 => => writing image sha256:abc123...
 => => naming to docker.io/library/hello-docker:v1.0
```

### During Push (Step 9):
```
The push refers to repository [docker.io/<username>/hello-docker]
v1.0: digest: sha256:def456... size: 1234
```

### Verification Screenshots to Capture:
1. **Terminal output** showing successful `docker build` command
2. **Terminal output** showing `docker images` with your built image
3. **Terminal output** showing successful `docker login`
4. **Terminal output** showing successful `docker push` with progress bars
5. **Browser screenshot** of your DockerHub repository page showing the pushed image
6. **Terminal output** showing `docker run` executing successfully

### Expected Files Structure:
```
hello-docker/
├── Dockerfile
├── app.py (or index.html)
└── README.md (optional)
```

---

## Deliverables (what to push)

- `Labs/Lab10_Demonstrate_Docker_commands_to_build_and.md` (this file, completed with all steps)
- `Lab10_files/` folder containing:
  - `Dockerfile` (the Dockerfile used in the lab)
  - `app.py` or `index.html` (the application file)
  - `screenshot_build.png` (screenshot of docker build output)
  - `screenshot_images.png` (screenshot of docker images output)
  - `screenshot_push.png` (screenshot of docker push output)
  - `screenshot_dockerhub.png` (screenshot of DockerHub repository page)
  - `screenshot_run.png` (screenshot of docker run output)
  - `commands.txt` (text file with all commands executed)

---

## Notes / Tips

### Troubleshooting Common Issues:

1. **"docker: command not found"**
   - Verify Docker is installed and running
   - Check Docker Desktop is started (on Windows/Mac)
   - Add Docker to system PATH if needed

2. **"denied: requested access to the resource is denied"**
   - Ensure you're logged in: `docker login`
   - Verify the image tag includes your DockerHub username
   - Check DockerHub username spelling

3. **"Cannot connect to Docker daemon"**
   - Start Docker Desktop/service: `sudo systemctl start docker` (Linux)
   - Check Docker is running: `docker info`

4. **Build fails with "no such file or directory"**
   - Verify Dockerfile and application files are in the same directory
   - Check file names match exactly (case-sensitive)

5. **Push is very slow**
   - Docker pushes layers; first push takes longer
   - Subsequent pushes only upload changed layers
   - Check internet connection speed

### Best Practices:

- **Use specific version tags** (v1.0, v1.1) instead of only 'latest' for better version control
- **Keep Dockerfiles simple** and well-commented for maintainability
- **Use `.dockerignore` file** to exclude unnecessary files (node_modules, .git, etc.)
- **Minimize image size** by using slim/alpine base images when possible
- **One process per container** - follow Docker best practices
- **Use multi-stage builds** for production applications to reduce final image size
- **Never commit sensitive data** (passwords, API keys) in Dockerfiles or images

### Additional Commands:

```bash
# View Docker build history
docker history hello-docker:v1.0

# Inspect image details
docker inspect hello-docker:v1.0

# Remove unused images
docker image prune

# View container logs
docker logs <container_id>

# Execute commands inside running container
docker exec -it <container_id> /bin/sh

# Save image to tar file (for offline sharing)
docker save -o hello-docker.tar hello-docker:v1.0

# Load image from tar file
docker load -i hello-docker.tar
```

### Grading Checklist:
- ✅ Dockerfile is properly structured and documented
- ✅ Image builds successfully without errors
- ✅ Image is properly tagged with version
- ✅ Image is successfully pushed to DockerHub
- ✅ DockerHub repository is accessible and shows correct tags
- ✅ All screenshots clearly show command execution and results
- ✅ Commands file includes all steps with explanations
- ✅ Application runs correctly when container is executed

**Remember**: Replace `<your-dockerhub-username>` with your actual DockerHub username throughout all commands!
