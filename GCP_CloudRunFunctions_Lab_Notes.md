# Cloud Run finctions - Command Line

```gcloud auth list
gcloud config list project
gcloud config set run/region us-central1
mkdir gcf_hello_world && cd $_
```
## Copy index.js

```
nano index.js

const functions = require('@google-cloud/functions-framework');

// Register a CloudEvent callback with the Functions Framework that will
// be executed when the Pub/Sub trigger topic receives a message.
functions.cloudEvent('helloPubSub', cloudEvent => {
  // The Pub/Sub message is passed as the CloudEvent's data payload.
  const base64name = cloudEvent.data.message.data;

  const name = base64name
    ? Buffer.from(base64name, 'base64').toString()
    : 'World';

  console.log(`Hello, ${name}!`);
});
```

## Copy package.json

```
nano package.json

{
  "name": "gcf_hello_world",
  "version": "1.0.0",
  "main": "index.js",
  "scripts": {
    "start": "node index.js",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "dependencies": {
    "@google-cloud/functions-framework": "^3.0.0"
  }
}
```
## Install Node.js

npm install

## Deploy the function 

```
gcloud functions deploy nodejs-pubsub-function \
  --gen2 \
  --runtime=nodejs20 \
  --region=us-central1 \
  --source=. \
  --entry-point=helloPubSub \
  --trigger-topic cf-demo \
  --stage-bucket qwiklabs-gcp-02-bb962e9476dd-bucket \
  --service-account cloudfunctionsa@qwiklabs-gcp-02-bb962e9476dd.iam.gserviceaccount.com \
  --allow-unauthenticated
```

## Check status 

```
  gcloud functions describe nodejs-pubsub-function \
  --region=us-central1 

  status : Active 

```

## Publish a msg to the topic and read the logs 

```
  gcloud pubsub topics publish cf-demo --message="Cloud Function Gen2"

  gcloud functions logs read nodejs-pubsub-function \
  --region=us-central1 
```

## Log viewer
  (resource.type = "cloud_function"
resource.labels.function_name = "nodejs-pubsub-function"
resource.labels.region = "us-central1")
 OR 
(resource.type = "cloud_run_revision"
resource.labels.service_name = "nodejs-pubsub-function"
resource.labels.location = "us-central1")
