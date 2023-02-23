# InfluxDB Cloud token lease management

### Prerequisites

* [Ockam Command](../../#install)
* [InfluxDB Cloud account](https://cloud2.influxdata.com/signup)

### The setup

#### Retrieve your InfluxDB Cloud configuration settings

Log in to your [InfluxDB Cloud Dashboard](https://cloud2.influxdata.com/) and get the `Cluster URL (Host Name)` and `Organization ID` from your Organization settings page, then generate a new All Access API token (`Load Data > API Tokens > Generate API Token > All Access API Token`). Take those three values and insert them into the appropriate environment variables below so we can use them later:

```bash
export INFLUXDB_ADMIN_TOKEN="infludb-cloud-token-here"
export INFLUXDB_ORG_ID="influxdb-org-id-here"
export INFLUXDB_ENDPOINT_URL="https://your-cluser-url-here"
```

#### Configure your Ockam project

We'll start by enrolling with the Orchestrator and ensuring the default project is setup for us to use:

```bash
ockam enroll
```

Next we'll save project information to a JSON file so that we can use it later when registering our client nodes:

```bash
ockam project information --output json default > project.json
```

Now it's time to integrate Ockam and InfluxDB Cloud! We'll do that by configuring the add-on to use the configuration settings we exported early, in addition to defining a default set of permissions each future lease request should be granted. We'll come back and adjust this later, so to make it easier we'll export it to an environment variable too:

```bash
export INFLUXDB_LEASE_PERMISSIONS="[{\"action\":  \"read\", \"resource\": {\"type\": \"authorizations\", \"orgID\": \"$INFLUXDB_ORG_ID\"}}]"
```

To save you having to unpack all of that: it's an InfluxDB permission definition (in JSON format) that only grants the ability to read authorizations that have been granted on the Org ID you exported earlier. Clients with this permission will be unable to do anything useful, but we'll fix that later.

We can pull all this together with the following command:

```bash
ockam project addon configure influx-db \
  --endpoint-url $INFLUXDB_ENDPOINT_URL \
  --token $INFLUXDB_ADMIN_TOKEN \
  --org-id $INFLUXDB_ORG_ID \
  --permissions "$INFLUXDB_LEASE_PERMISSIONS" \
  --max-ttl 900
```

### Connecting a client

Before a client can connect to a project, an enrollment token must be generated for it by an authorized entity:

```bash
SENSOR_TOKEN=$(ockam project enroll --attribute service=iot-sensor)
```

Next we need to create a new identity for our client, as it's to this identity that the leased InfluxDB token will be issued:

```bash
ockam identity create iot-sensor
```

Identity created, we can authenticate to our project (using the JSON configuration file we created earlier) and the enrolment token `SENSOR_TOKEN` we saved at the start of this section:

```bash
ockam project authenticate \
  --identity iot-sensor \
  --project-path project.json \
  --token $SENSOR_TOKEN
```

We've arrived at the big moment, time to request a new lease:

```
ockam lease create \
  --identity iot-sensor \
  --project-path project.json 
```

In the output you'll see not just the token you created but when it expires, which should be 5 minutes from the time it was created (we set the max TTL to 900 seconds in the original `ockam addon configure` command).&#x20;

Log into your InfluxDB Cloud dashboard and you'll see this new token listed with the rest of your API keys. It's a real token that will work like any other InfluxDB token you might use, just with the added security of not being permanently issues. You can use it directly or via an Ockam secure channel as outlined in our [Connect distributed clients to InfluxDB](../use-cases/connecting-distributed-clients-to-time-series-backends.md) use case.
