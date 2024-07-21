# FastAPI Application Deployment to Google Cloud Run
This guide walks you through deploying a FastAPI application to Google Cloud Run and setting up CI/CD with GitHub Actions for automated deployments.

## Step 1: Create a FastAPI Application
First, let's create a simple FastAPI application with a single endpoint that returns "Hello, gcp Web Apps!".

### Set up your Python environment:
1. Ensure Python is installed (preferably Python 3.7+).
2. Set up a virtual environment (optional but recommended).

### Install FastAPI and Uvicorn:

```
pip install fastapi uvicorn
```
### Create your FastAPI app:
Create a file named main.py with the following content:

```
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def read_root():
    return {"message": "Hello, gcp Web Apps!"}
```

### Run the FastAPI app:
You can test your app locally using Uvicorn:

```
uvicorn main:app --reload
```
This will start a local server, and you can access your endpoint at http://localhost:8000.

## Step 2: Deploy to Google Cloud Run
Next, deploy your FastAPI application to Google Cloud Run.

### Install Google Cloud SDK:
Follow the instructions here to install the Google Cloud SDK.

### Authenticate with Google Cloud:
```
gcloud auth login
```

### Set your Google Cloud project:
```
gcloud config set project YOUR_PROJECT_ID
```

### Build and deploy to Cloud Run:
Replace YOUR_PROJECT_ID with your actual GCP project ID:

```
gcloud builds submit --tag gcr.io/YOUR_PROJECT_ID/fastapi-app
gcloud run deploy --image gcr.io/YOUR_PROJECT_ID/fastapi-app --platform managed
```
Follow the prompts to deploy the container to Cloud Run.

### Access your deployed app:
After deployment, you'll get a URL. Access it in your browser to see "Hello, gcp Web Apps!".

## Step 3: Set up CI/CD with GitHub Actions
Now, let's set up GitHub Actions to automatically deploy changes to Cloud Run whenever you push to your repository.

### Create a GitHub repository:
If you haven't already, create a new repository on GitHub.

### Add a GitHub Actions workflow:
Inside your repository, create a directory named .github/workflows and create a YAML file (e.g., deploy.yml) with the following content:

```
name: Deploy to Google Cloud Run

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v2

      - name: Set up Google Cloud SDK
        uses: google-github-actions/setup-gcloud@v0.3.0
        with:
          project_id: ${{ secrets.GCP_PROJECT_ID }}
          service_account_key: ${{ secrets.GCP_SA_KEY }}
          export_default_credentials: true

      - name: Build and Deploy to Cloud Run
        run: |
          gcloud builds submit --tag gcr.io/${{ secrets.GCP_PROJECT_ID }}/fastapi-app
          gcloud run deploy --image gcr.io/${{ secrets.GCP_PROJECT_ID }}/fastapi-app --platform managed --quiet
```
Make sure to replace ${{ secrets.GCP_PROJECT_ID }} with your actual GCP project ID.

### Set up GitHub Secrets:
In your GitHub repository settings, go to "Secrets" and add the following secrets:

- GCP_PROJECT_ID: Your Google Cloud project ID.
-GCP_SA_KEY: Your Google Cloud service account key JSON (create a service account with necessary permissions and download the JSON key).

## Final Steps:
1. Commit and push your changes to GitHub. This will trigger the GitHub Action to build and deploy your FastAPI app to Cloud Run.

2. Check the GitHub Actions tab to monitor the deployment process.

3. Once deployed, access your application via the Cloud Run URL.
   
This setup ensures that changes pushed to your main branch will automatically trigger a build and deployment process to keep your application up-to-date on Google Cloud Run.
