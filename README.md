# Easy Peasy Network Monitor

Multi-container Docker app built from the following services:

- [InfluxDB](https://github.com/influxdata/influxdb) - time series database
- [Chronograf](https://github.com/influxdata/chronograf) - admin UI for InfluxDB
- [Telegraf](https://github.com/influxdata/telegraf) - monitor general network and device stats and store in InfluxDB
- Speedtest - Periodically runs a speedtest and logs to both InfluxDB and (if configured in `config.ini`) can also send to Google Sheets via IFTTT maker webhooks.
- [Grafana](https://github.com/grafana/grafana) - visualization UI for InfluxDB

Useful for quickly setting up a network monitor in a matter of minutes, even able to run on a Raspberry Pi.

## Quick Start

To start the app:

1. Install [docker-compose](https://docs.docker.com/compose/install/) on the docker host.
2. Clone this repo on the docker host.
3. Optionally, change default credentials or Grafana provisioning.
4. Run the following command from the root of the cloned repo:

```bash
docker-compose build
docker-compose up -d
open http://localhost:3000
```

To stop the app:

1. Run the following command from the root of the cloned repo:

```bash
docker-compose down
```

## Ports

The services in the app run on the following ports:

| Host Port | Service    |
| --------- | ---------- |
| 3000      | Grafana    |
| 8086      | InfluxDB    |
| 8888      | Chronograf |

Note that Chronograf does not support username/password authentication. Anyone who can connect to the service has full admin access.

## Volumes

The app creates the following named volumes (one for each service) so data is not lost when the app is stopped:

- influxdb-storage
- chronograf-storage
- grafana-storage

## Users

The app creates two admin users - one for InfluxDB and one for Grafana. By default, the username and password of both accounts is `admin`. To override the default credentials, set the following environment variables before starting the app:

- `INFLUXDB_USERNAME`
- `INFLUXDB_PASSWORD`
- `GRAFANA_USERNAME`
- `GRAFANA_PASSWORD`

## Database

The app creates a default InfluxDB database called `db0`, with additional `speedtests` and `telegraf` databases for those specific records.

## Data Sources

The app creates a Grafana data source called `InfluxDB` that's connected to the default IndfluxDB database.

To provision additional data sources, see the Grafana [documentation](http://docs.grafana.org/administration/provisioning/#datasources) and add a config file to `./grafana-provisioning/datasources/` before starting the app.

## Dashboards

By default, this configuration has a default "Network Monitoring dashboard with panels for internet dropout, ping durations to some websites (configurable in `telegraf.conf`), CPU usage and periodic speedtest monitoring.

To provision additional dashboards, see the Grafana [documentation](http://docs.grafana.org/administration/provisioning/#dashboards) and add a config file to `./grafana-provisioning/dashboards/` before starting the app.

## Thanks

Thanks to [@jkehres](https://github.com/jkehres/docker-compose-influxdb-grafana) and [@barrycarey](https://github.com/barrycarey/Speedtest-for-InfluxDB-and-Grafana) for some of their code for this project.

Also thanks to [@aallen](https://makezine.com/projects/send-ticket-isp-when-your-internet-drops/) for the idea to use IFTTT maker as a webhook client to Google Sheets for speedtest monitoring.
