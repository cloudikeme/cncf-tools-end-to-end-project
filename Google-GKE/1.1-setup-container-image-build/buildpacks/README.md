# Buildpack

### **Building Container Images with Cloud Native Buildpacks**

The Cloud Native Buildpacks project offers an easy way to convert source code into Open Container Initiative (OCI)-compliant container images. By using Cloud Native Buildpacks, we streamline the development experience while providing platform teams with control over how container images are built and regulated.

### **Why Use Cloud Native Buildpacks?**

Cloud Native Buildpacks simplify the process of containerizing applications. They abstract much of the complexity by automating image creation. This allows developers to focus on writing code, while platform teams can ensure that the images adhere to best practices for security, efficiency, and compliance.

### **Using the pack CLI**

The pack CLI is one of the most common ways to implement Cloud Native Buildpacks. It’s maintained by the Cloud Native Buildpacks project and provides a simple interface for building container images directly from source code.

### **Setup**

Before we begin, we need to ensure that the following prerequisites are met:

1. **Install the pack CLI**: The pack CLI is required to build images using Cloud Native Buildpacks.
2. **Ensure Docker is Running**: Docker must be up and running since pack uses Docker to create and manage container images.

### **Building the Container Image**

Once we have pack and Docker set up, we can proceed with building our image. Follow the steps below to build a container image using Cloud Native Buildpacks:

1. **Set the Image Tag**: First, we set the version tag for our container image. In this case, we’ll use `v0.0.1`:

   ```bash
   export TAG=v0.0.1
   ```

2. **Update the Configuration File**: We’ll also update the `settings.yaml` file to include the image tag:

   ```bash
   yq --inplace ".tag = \"v0.0.1\"" settings.yaml
   ```

3. **Configure the Default Builder**: We need to set the default builder that Cloud Native Buildpacks will use. Here, we’re using the `paketobuildpacks/builder:tiny`, which is a minimal builder that’s optimized for small image sizes:

   ```bash
   pack config default-builder paketobuildpacks/builder:tiny
   ```

4. **Build the Image**: Now we can build the container image from our source code. The `pack build` command takes the name of the image (`cncf-demo`) and the tag (`$TAG`):

   ```bash
   pack build cncf-demo:$TAG
   ```

   This command packages our source code into an OCI-compliant image using the configured builder.

5. **List Docker Images**: After the build process completes, we can verify that the image has been created by listing the available Docker images:

   ```bash
   docker image ls
   ```

   We should see `cncf-demo:v0.0.1` in the output, confirming that our image was successfully built.

### **Conclusion**

By using Cloud Native Buildpacks and the pack CLI, we have a streamlined process for building OCI-compliant container images directly from source code. This approach allows developers to focus on code while giving platform teams control over how images are built and ensuring that they meet operational standards.