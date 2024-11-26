# Kong Gateway with Datakit (Flow Engine)

Datakit is a data flow engine built on top of WASM inside the Kong Gateway. This is a general purpose tool for interacting with third party apis and using response data from these requests for seeding information for subsequent calls - either upstream / back to the client or to other apis. It is still in **experimental** phase

## Setting Up

Create your own .env file and provide the required values. They are required to run the containers.


```
POSTGRES_PASSWORD=
```

From Kong 3.8, a version of Datakit ships with the Kong Gateway. It’s loaded into the system as a ‘filter’, which is the term we use for custom logic in WASM and is configurable at the /filters endpoint

Download the datakit.wasm and datakit.meta.json directly from the [release](https://github.com/Kong/datakit/releases) section of the Datakit repo and put them into a folder named **datakit**. This is similar to a custom plugin

Subsequently, mount the datakit artifacts when starting the container

```   
 volumes:
    - "./datakit:/usr/local/kong/wasm/"
```

### Enterprise License Loading (Optional)

For Kong enterprise users, you can load the license by sending a request Kong Admin API

```
http POST :9001/licenses \
  payload='LICENSE_KEY'
```

### Kong API and Datakit Configuration

Configuration can be found in ./deck/ folder

Run the following to sync the configurations

```
deck gateway sync --kong-addr http://localhost:9001  ./deck
```

## Starting Up

The docker compose will spin up Kong Gateway and Postgres

1. Start Docker Compose 
```
docker-compose up -d
```

2. Tear-down

```
docker-compose down
```


## Test The API

```
http localhost:9000/aggregate-facts 

HTTP/1.1 200 OK
Connection: keep-alive
Content-Length: 231
Content-Type: application/json

{
    "cat_fact": "Your cat's heart beats at a rate almost double that of yours, from 110-140 beats per minute.",
    "chuck_norris_fact": "Chuck Norris calculated the square root of negative one while eating a bowl full of rusty fishhooks."
}
```

