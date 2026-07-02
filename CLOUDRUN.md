# Build an AI Agent and Configure an MCP Server on Cloud Run

## Why deploy to Cloud Run?

Why deploy to Cloud Run?
## Key Features of Cloud Run

**Runs containers anywhere**

* You bring a container (Docker image) that has your app inside.
* Cloud Run runs it on Google's infrastructure.
* No OS patching, VM setup, or scaling headaches.

**Automatic scaling**
- If 0 people are using your app → 0 instances run (you pay $0 when idle).
- If 1,000 requests hit it → it spins up as many copies as needed.

**Stateless by default**
- Each request can go to a different instance.
- If you need to store state, use an external service like Cloud SQL, Firestore, or Redis.

**Supports any language or framework**
- As long as it runs in a Linux container, Cloud Run doesn't care if it's Python, Go, Node.js, Java, or .Net.

**Pay for what you use**
- Billed per request + compute time (down to 100 ms).
- You're not paying for idle resources like with a traditional VM.

## Setup 

### Activate Cloud Shell

Cloud Shell is a virtual machine that is loaded with development tools. It offers a persistent 5GB home directory and runs on the Google Cloud. Cloud Shell provides command-line access to your Google Cloud resources.

1. Click Activate Cloud Shell Activate Cloud Shell icon at the top of the Google Cloud console.

Click through the following windows:

- Continue through the Cloud Shell information window.
- Authorize Cloud Shell to use your credentials to make Google Cloud API calls.

`gcloud auth list`

`gcloud config list project`


### Grant the "Agent Platform User" role to your service account

- [ ] Return to Cloud Shell and run:

```
gcloud projects add-iam-policy-binding $PROJECT_ID \
  --member="serviceAccount:$SERVICE_ACCOUNT" \
  --role="roles/aiplatform.user"
```

# Task 3. Prepare the application for deployment

With your local environment ready, the next step is to prepare your Google Cloud project for the ***your agent here*** deployment.

This involves a final check of your agent's file structure to ensure it's compatible with the deployment command. **More importantly, you configure a critical IAM permission** that allows your deployed Cloud Run service to act on your behalf and call the Agent Platform models. 
***Completing this step ensures the cloud environment is ready to run your agent successfully.***

1. Return to the Cloud Shell terminal and run the following command to load the variables into your shell session:

```
source .env
```

2. Run the following command to grant the service account the Agent Platform User role, which gives it permission to make predictions and call Google's models:

```
# Grant the "Agent Platform User" role to your service account
gcloud projects add-iam-policy-binding $PROJECT_ID \
  --member="serviceAccount:$SERVICE_ACCOUNT" \
  --role="roles/aiplatform.user"
```

# Task 4. Deploy the agent using the ADK CLI

With your local code ready and your Google Cloud project prepared, it's time to deploy the agent.

In this task, you use the `adk deploy cloud_run` command, which is a convenient tool that automates the entire deployment workflow. This single command packages your code, builds a container image, pushes it to Artifact Registry, and launches the service on Cloud Run to make it accessible on the web.

