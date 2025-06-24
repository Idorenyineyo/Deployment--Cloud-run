# 📦 Deploying a Node.js App on Google Cloud Run (GCP Lab Walkthrough)

This documentation walks through the full steps to build, containerize, and deploy a simple Express.js application on **Google Cloud Run** using **Cloud Shell and CLI**.

---

## ✅ Task 1: Enable Cloud Run API & Set Region

```bash
# Enable Cloud Run API
gcloud services enable run.googleapis.com

# Set compute region
gcloud config set compute/region "REGION"

# Set environment variable for region
export LOCATION="REGION"
```

---

## ✅ Task 2: Write the Sample Application

```bash
# Create app directory
mkdir helloworld && cd helloworld
```

### Create `package.json`

```json
{
  "name": "helloworld",
  "description": "Simple hello world sample in Node",
  "version": "1.0.0",
  "main": "index.js",
  "scripts": {
    "start": "node index.js"
  },
  "author": "Google LLC",
  "license": "Apache-2.0",
  "dependencies": {
    "express": "^4.17.1"
  }
}
```

### Create `index.js`

```js
const express = require('express');
const app = express();
const port = process.env.PORT || 8080;

app.get('/', (req, res) => {
  const name = process.env.NAME || 'World';
  res.send(`Hello ${name}!`);
});

app.listen(port, () => {
  console.log(`helloworld: listening on port ${port}`);
});
```


---

## ✅ Task 3: Containerize and Push to Artifact Registry

### Create `Dockerfile`

```dockerfile
FROM node:12-slim
WORKDIR /usr/src/app
COPY package*.json ./
RUN npm install --only=production
COPY . ./
CMD [ "npm", "start" ]
```

### Build and Push Container

```bash
# Submit build to Cloud Build
gcloud builds submit --tag gcr.io/$GOOGLE_CLOUD_PROJECT/helloworld

# (Optional) List container images
gcloud container images list

# Configure Docker credentials
gcloud auth configure-docker
```


### Test Locally (Cloud Shell)

```bash
# Run Docker container locally
docker run -d -p 8080:8080 gcr.io/$GOOGLE_CLOUD_PROJECT/helloworld
```


---

## ✅ Task 4: Deploy to Cloud Run

```bash
gcloud run deploy --image gcr.io/$GOOGLE_CLOUD_PROJECT/helloworld \
  --allow-unauthenticated \
  --region=$LOCATION
```

* You will receive a **Service URL** like: `https://helloworld-xxxxx.a.run.app`


---

## ✅ Task 5: Clean Up Resources

```bash
# Delete container image
gcloud container images delete gcr.io/$GOOGLE_CLOUD_PROJECT/helloworld

# Delete Cloud Run service
gcloud run services delete helloworld --region=$LOCATION
```

---

## 📌 Summary

This guide demonstrated how to:

* ✅ Build a Node.js web server
* ✅ Containerize it using Docker
* ✅ Deploy it using Google Cloud Build and Cloud Run
* ✅ Clean up cloud resources

⚡ Now your app runs **serverlessly** on Cloud Run and scales automatically with demand!

