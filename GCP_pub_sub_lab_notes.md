# pub/sub - Command Line

```
gcloud auth list
gcloud config list project
```

```
gcloud pubsub topics create myTopic
gcloud pubsub topics create Test1
gcloud pubsub topics create Test2
```

```
gcloud pubsub topics list

gcloud pubsub topics delete Test1
gcloud pubsub topics delete Test2
gcloud pubsub topics list

gcloud  pubsub subscriptions create --topic myTopic mySubscription
gcloud  pubsub subscriptions create --topic myTopic Test1
gcloud  pubsub subscriptions create --topic myTopic Test2
gcloud pubsub topics list-subscriptions myTopic

gcloud pubsub subscriptions delete Test1
gcloud pubsub subscriptions delete Test2
gcloud pubsub topics list-subscriptions myTopic

gcloud pubsub topics publish myTopic --message "Hello"
gcloud pubsub topics publish myTopic --message "Publisher's name is <YOUR NAME>"
gcloud pubsub topics publish myTopic --message "Publisher likes to eat <FOOD>"
gcloud pubsub topics publish myTopic --message "Publisher thinks Pub/Sub is awesome"


gcloud pubsub subscriptions pull mySubscription --auto-ack
```

##Pub/Sub pulling all messages from subscriptions

```
gcloud pubsub topics publish myTopic --message "Publisher is starting to get the hang of Pub/Sub"
gcloud pubsub topics publish myTopic --message "Publisher wonders if all messages will be pulled"
gcloud pubsub topics publish myTopic --message "Publisher will have to test to find out"

gcloud pubsub subscriptions pull mySubscription --auto-ack --limit=3
```
