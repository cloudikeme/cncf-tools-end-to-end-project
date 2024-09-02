# **Building Container Images with kbld in Dev Environments**

In development environments, kbld proves to be an invaluable command-line tool that automates the process of building container images, pushing them, and updating deployment configurations. Let’s walk through how kbld accomplishes this in four steps:

1. **Finding Image References**: kbld scans our deployment configuration files to locate image references. It identifies these references using specific image keys, which point to the container images our application relies on.

2. **Building Images**: Once kbld finds these references, it builds the images from the source code specified in the kbld configuration file. This configuration directs kbld to the appropriate directories containing our source code.

3. **Pushing Images to a Registry**: After building the images, kbld can optionally push them to a remote registry. Although this step is optional, it is particularly useful in production environments where images need to be available in a centralized location.

4. **Recording Image References**: Finally, kbld records the newly built image references in a YAML file. These references are crucial as they ensure our deployment configurations point to the correct images.

### **Unique Tagging for Local Development**

kbld automatically assigns each image a unique tag during the build process. This is especially useful in local development environments where we might not immediately push images to a remote registry. When we eventually push these images to a remote registry, kbld converts the tags into digest references, ensuring consistency and immutability.

**Fun Fact:** kbld can handle multiple changing applications simultaneously, making it particularly effective in dynamic development environments where several components may be under active development.

### **Prerequisites**

Before we begin, we need to ensure a few prerequisites are met:

- **[Install kbld CLI](https://carvel.dev/kbld/docs/v0.36.0/install/)**: We start by installing the kbld command-line interface.
- **[Install the pack CLI](https://buildpacks.io/docs/tools/pack/#pack-cli)**: The pack CLI is necessary for building container images.
- **Ensure Docker is Running**: Docker must be up and running, as kbld relies on it to build and manage images.

### **Implementation**

In our deployment definition, the container image is referenced as 'cncf-demo'. The kbld configuration file maps this image reference to our current directory, where our source code resides.

To inspect this configuration, we can review the deployment file:

```bash
cat kbld/ .yaml
```

Next, we build the image from the referenced source code and record the resulting image reference in a new file called `deployment-kbld.yaml`:

```bash
kbld --file kbld/deployment.yaml | tee kbld/deployment-kbld.yaml
```

At this point, the deployment definition now points to our newly built container image. To extract the image tag from the output, we follow these steps:

1. Identify the tag from the kbld output (everything after 'kbld:').
2. Replace `[...]` with the identified tag and store it in an environment variable:

   ```bash
   export TAG=[...]
   ```

3. Update the `settings.yaml` file to include this tag:

   ```bash
   yq --inplace ".tag = \"$TAG\"" settings.yaml
   ```

4. Tag the Docker image accordingly:

   ```bash
   docker image tag kbld:$TAG cncf-demo:$TAG
   ```

### **Next Steps**

At this point, we have successfully built and tagged our container image using kbld. Depending on our future choices, we may decide to push these images to a registry or allow kbld to find and update image references within our configuration files.

This process not only streamlines image management in development environments but also ensures that our deployments are consistent and reliable as we transition from local development to production.