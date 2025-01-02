# Pub/Sub: Qwik Start - Python experiment

## Create a virtual environment

```

gcloud auth list
gcloud config list project
sudo apt-get install -y virtualenv
python3 -m venv venv
source venv/bin/activate
```

## Install the client library

```
pip install --upgrade google-cloud-pubsub
git clone https://github.com/googleapis/python-pubsub.git
cd python-pubsub/samples/snippets
```

## Pub/Sub - the Basics

Pub/Sub is an asynchronous global messaging service. There are three terms in Pub/Sub that appear often: topics, publishing, and subscribing.

A topic is a shared string that allows applications to connect with one another through a common thread.

Publishers push (or publish) a message to a Pub/Sub topic. Subscribers will then make a subscription to that thread, where they will either pull messages from the topic or configure webhooks for push subscriptions. Every subscriber must acknowledge each message within a configurable window of time.

In sum, a publisher creates and sends messages to a topic and a subscriber creates a subscription to a topic to receive messages from it.

## Pub/Sub in Google CLoud

Pub/Sub comes preinstalled in Cloud Shell, so there are no installations or configurations required to get started with this service. In this lab you use Python to create the topic, subscriber, and then view the message. You use a gcloud command to publish the message to the topic.

## Create a topic

```
echo $GOOGLE_CLOUD_PROJECT
cat publisher.py
python publisher.py -h
python publisher.py $GOOGLE_CLOUD_PROJECT create MyTopic
python publisher.py $GOOGLE_CLOUD_PROJECT list
```

## Create a subscription

```
python subscriber.py $GOOGLE_CLOUD_PROJECT create MyTopic MySub
```

```
Subscription created: name: "projects/qwiklabs-gcp-01-e3621f9af4ff/subscriptions/MySub"
topic: "projects/qwiklabs-gcp-01-e3621f9af4ff/topics/MyTopic"
push_config {
}
ack_deadline_seconds: 10
message_retention_duration {
  seconds: 604800
}
expiration_policy {
  ttl {
    seconds: 2678400
  }
}
state: ACTIVE
```

python subscriber.py $GOOGLE_CLOUD_PROJECT list-in-project
python subscriber.py -h


## Publish messages

```
gcloud pubsub topics publish MyTopic --message "Hello"
gcloud pubsub topics publish MyTopic --message "Publisher's name is <YOUR NAME>"
gcloud pubsub topics publish MyTopic --message "Publisher likes to eat <FOOD>"
gcloud pubsub topics publish MyTopic --message "Publisher thinks Pub/Sub is awesome"
```


## View Message 

`python subscriber.py $GOOGLE_CLOUD_PROJECT receive MySub`

Ref: [gcloud documentation](https://cloud.google.com/sdk/gcloud)
