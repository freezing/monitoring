# OS and Hardware monitoring

## Setup metrics collector on host

### node\_exporter

* Download node\_exporter binary for the platform [here](https://github.com/prometheus/node_exporter/releases)
* Run the binary on the host machine to collect metrics
* The binary exposes HTTP API on port 9100. Try it on [http://localhost:9100](http://localhost:9100)

### collectd

TBD - I managed to run collectd that collects metrics from host and exposes HTTP API for prometheus (there are plugins for other data sources too).
We probably don't need to run both node exporter and collectd yet, but it's good that we can do it.

## Start prometheus and grafana via docker

We are going to use docker-compose to start prometheus and grafana in the custom docker network. Make sure docker and docker-compose are installed.

* Make sure you are in the root folder of this repo.
* Run `docker-compose up`. This command uses the `docker-compose.yml` file by default. `docker-compose.yml` starts prometheus with `prometheus.yml` config and grafana with default config.

At this point you should have node\_exporter, prometheus and grafana up and running.

**TODO: I haven't configured persistent volumes yet, which enable us to store metrics even after the machine crashes**

## Configure grafana

As of this moment, grafana doesn't have any datasources and dashboards configured. 
I plan on doing this such that it has default dashboards preconfigured and it's connected to the above prometheus datasource.
Therefore, the below steps won't be necessary, but for the time being you have to do it manually.

### Login to grafana
Grafana docker container exposes port 3000 so you can access it via [http://localhost:3000](http://localhost:3000).
Use username: *admin* and password: *pass* to login in grafana.

### Add prometheus as datasource

* Go to [http://localhost:3000/datasources/new](http://localhost:3000/datasources/new)
* Select Prometheus
* Type: `http://prometheus:9090` in the URL input. `prometheus` is resolved to the actual IP because both grafana and prometheus run in the same docker network. This is a docker feature which resolves docker container name to an IP.
* Click `Save & Test`. You should see green box saying `Data source is working`.

### Add dashboard to grafana

You can create your own dashboards in grafana using any datasource. For now, we are going to add [pre-existing dashboard](https://grafana.com/dashboards/9096) for node exporter.
There are many pre-defined dashboards and you can find them [here](https://grafana.com/dashboards/9096).

* Go to: [http://localhost:3000/dashboard/import](http://localhost:3000/dashboard/import) to import pre-existing dashboard.
* There are two ways to import dashboards: using JSON or getting it directly from [grafana dashboards](https://grafana.com/dashboards). We are going to use the second approach by typing dashboard ID in the first input box: `9096` - note that you have to remove focus from the input box for grafana to pull it (lol); you can do this by pressing tab or clicking anywhere else in the UI.
* Click Import. This should add the dashboard and take you to the page that shows some metrics.

**Note that this dashboard doesn't show all the metrics and some of them are aggregated, but node exporter has more granular metrics which we can add manually if we need them.**
