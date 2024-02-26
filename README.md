


# CI/CD Pipeline Overview

A Continuous Integration and Continuous Deployment (CI/CD) pipeline, set up in a Jenkinsfile, handles a web app made with Flask and packaged in Docker. This setup automatically does a few key tasks: it builds the Docker image (which is like making a ready-to-run package of the app), runs this package to make sure everything's okay, and then moves this package first to a Docker registry (a place where Docker images are stored) and after that to Google Cloud Run. Google Cloud Run is a service that lets the app run on the internet. This whole process makes it easier and quicker to update the app and get those updates live for users to see.
## Rights Checks for Authorization and Authentication:

- **Google Cloud Service Account Key:** `Ensure the Jenkins user has the appropriate permissions to use the Google Cloud service account key specified by GOOGLE_CLOUD_SERVICE_ACCOUNT_KEY. This key is crucial for authenticating with Google Cloud services during deployment.`
- **Docker Hub Credentials:** `Verify that the Jenkins user has the correct rights to use Docker Hub credentials (DOCKER_CREDENTIALS_ID) for pushing images. This involves ensuring the credentials stored in Jenkins allow for authentication with Docker Hub to push the Docker image.`



## Environment Configuration:

Uses environment variables for configuration, such as PATH, BASE_IMAGE_NAME, BASE_CONTAINER_NAME, DOCKER_CREDENTIALS_ID, GOOGLE_REGION, GOOGLE_PROJECT_ID, and GOOGLE_CLOUD_SERVICE_ACCOUNT_KEY.


## Pipeline Configuration

### Environment Variables

The pipeline uses several environment variables for configuration:

- `PATH`: Includes the path to the Google Cloud SDK to ensure the `gcloud` command is available.
- `BASE_IMAGE_NAME`: The base name for the Docker image, set to 'appflask'.
- `BASE_CONTAINER_NAME`: The base name for the Docker container, set to 'appflask-container'.
- `DOCKER_CREDENTIALS_ID`: The ID for Docker Hub credentials, used for pushing the Docker image.
- `DOCKER_USERNAME_ID`: The ID for the Docker Hub username, used in naming the Docker image.
- `GOOGLE_REGION`: The Google Cloud region for deployment, set to 'europe-west3'.
- `GOOGLE_PROJECT_ID`: The Google Cloud project ID for the deployment.
- `GOOGLE_CLOUD_SERVICE_ACCOUNT_KEY`: The ID for the Google Cloud service account key, stored in Jenkins for authentication.

## Pipeline Stages ; CI/CD Pipeline Overview:

The pipeline consists of the following stages:

### 1. Checkout

- **Action**: Clones the source code from the GitHub repository specified by the URL, using the 'main' branch.
Check out the [Checkout stage](https://github.com/YavorMarkov/appflask/blob/main/Jenkinsfile#L16-L21) of the Jenkinsfile.
![Checkout Screenshot](screenshots/jenkins_pipeline/1.%20AppFlaskCICDView.JPG)
![Checkout Screenshot](screenshots/checkout/3.%20Checkout.JPG)

### 2. Build Docker Image

- **Action**: Builds a Docker image with a unique tag comprising the Docker username, a random number, and the Jenkins build ID.
- **Details**: Retrieves the Docker username from Jenkins credentials.

### 3. Run Container

- **Action**: Runs a Docker container from the built image, exposing it on port 5000.
- **Details**: Generates a unique name for the container based on the build ID.

### 4. Push Image

- **Action**: Pushes the built Docker image to Docker Hub.
- **Details**: Uses Docker Hub credentials stored in Jenkins for authentication.

### 5. Deploy to Google Cloud Run

- **Action**: Deploys the Docker image to Google Cloud Run as a managed service.
- **Details**: Authenticates with Google Cloud using a service account key, sets up Docker to use `gcloud` as a credential helper, and deploys the service with specified resources for cost optimization.

### Key Elements:
**Port Configuration:** 

The Docker container is configured to expose the Flask application on port 8080, reflecting the settings in the Flask application and Dockerfile. This alignment is essential for the application's accessibility from outside the container and compatibility with cloud deployment expectations.

**Flask and Docker Integration:**

The Flask application defaults to port 8080, ensuring it listens on the correct port when the PORT environment variable is not explicitly set.

The Dockerfile designates port 8080 as the external port and employs gunicorn to serve the Flask application, facilitating external access to the application.
Consistency and Compatibility:

`Ensuring that all project configurations (within the Jenkinsfile, Docker setups, and Flask application) consistently reference port 8080 is crucial for seamless operation.`

`The configuration of the Google Cloud Run service to recognize the application's use of port 8080 enhances deployment success, although Google Cloud Run generally auto-detects and accommodates the container's exposed port.`

## Outcome:
This configuration ensures the Flask application's smooth deployment and operation on Google Cloud Run by standardizing port 8080 across the project's Docker and Flask settings. This standardized approach aids in meeting cloud deployment criteria and enhancing the application's accessibility and functionality in the cloud environment.


## Deployment Details

The deployment to Google Cloud Run is configured to use minimal resources to optimize costs, with settings for memory, CPU, and maximum instances specified in the Jenkinsfile.

## Conclusion

The Jenkinsfile defines a comprehensive CI/CD pipeline for automating the build, run, and deployment processes of a Flask application to Google Cloud Run, ensuring efficient and consistent deployments. It emphasizes the importance of rights checks for the Jenkins user regarding the use of authorization and authentication files necessary for deploying the application.



 🚧 Under development 🚧 
 
 🔍 Actively seeking job opportunities to leverage my DevOps skills. Eager to contribute to a dynamic team 🔍
