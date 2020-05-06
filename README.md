# ProtoDash

ProtoDash is a tool to aid the rapid development of prototype dashboards and enable data engineering and data science to deploy static sites without the need to engage with ops.

## Usage

You'll want to set the `GOOGLE_CLOUD_CREDENTIALS` to point at either the json keyfile for a service account, or your local application default credentials json keyfile (usually `~/.config/gcloud/application_default_credentials.json`).

```
go build -o protodash
./protodash
```

## Dashboard Config

The config for the dashboards is stored in `config.yml` and is a map of slugs (the path that the dashboard will serve from) and the config options for that specific dashboard.

A verbose example of the file with all available options is below.

```yaml
---
dashboard-slug:
  gcs_bucket: my-sandbox-bucket # required
  single_page_app: true         # optional
  prefix: sub-dir-in-gcs        # optional
```

| Key               | Description                                                                                                                 | Default | Required |
|-------------------|-----------------------------------------------------------------------------------------------------------------------------|---------|----------|
| `gcs_bucket`      | The bucket to serve the files from                                                                                          |         | `yes`    |
| `single_page_app` | Whether the app is an SPA, when this is set to `true` and a path would return a 404, we serve the root `index.html` instead | `false` | `no`     |
| `prefix`          | A prefix in the bucket to serve from, this would allow you to run multiple apps from the same bucket                        |         | `no`     |


## Environment Config

| Env Variable                  | Description                                                                                             | Default |
|-------------------------------|---------------------------------------------------------------------------------------------------------|---------|
| `PROTODASH_LISTEN`            | Address to bind the server                                                                              | `:8080` |
| `PROTODASH_LOG_LEVEL`         | Logging level                                                                                           | `debug` |
| `PROTODASH_PROXY_TIMEOUT`     | Defines the maximum time in serving the proxy requests, this is a hard timeout and includes retries     | `10s`   |
| `PROTODASH_CLIENT_TIMEOUT`    | Hard timeout on requests that protodash sends to the Google Storage API                                 | `2s`    |
| `PROTODASH_IDLE_CONN_TIMEOUT` | Maximum duration of idle connections between protodash and the Google Storage API                       | `120s`  |
| `PROTODASH_MAX_IDLE_CONNS`    | Maximum number of idle connections to keep open. This doesn't control the maximum number of connections | `10`    |

## Adding a dashboard

Once you have a dashboard ready to go, open a PR against `config.yml` with the required info. After it's approved and merged we auto-deploy the changes and from that point on you can edit the files in your GCS bucket and the changes should be instant.

## Thanks

* [nytimes/gcs-helper](https://github.com/nytimes/gcs-helper) - Portions of the code here were heavily inspired by the gcs-helper project from the NY Times, particularly the method of proxying requests to GCS without having to use the GCS storage APIs.