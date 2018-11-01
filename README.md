# heroku-fluentd-datadog
[![FOSSA Status](https://app.fossa.io/api/projects/git%2Bgithub.com%2FApplauseOSS%2Fheroku-fluentd-datadog.svg?type=shield)](https://app.fossa.io/projects/git%2Bgithub.com%2FApplauseOSS%2Fheroku-fluentd-datadog?ref=badge_shield)


This is a [Heroku](https://heroku.com) application which contains everything
to run [fluentd](https://fluentd.org) on Heroku to accept logs from [HTTPS
drains](https://devcenter.heroku.com/articles/log-drains#https-drains) using
the [in_heroku_http](https://github.com/ApplauseOSS/fluent-plugin-heroku-http)
plugin to fluentd. The
[filter_record_transformer](https://docs.fluentd.org/v1.0/articles/filter_record_transformer)
plugin is used to inject the source application's hostname, service name,
and fluent tag into the record. The
[out_datadog](https://github.com/DataDog/fluent-plugin-datadog) plugin is
used to stream the records to DataDog Logging.

This application uses the request PATH to specify the fluentd tag to use for
routing. This matches the behavior of the
[in_http](https://docs.fluentd.org/v1.0/articles/in_http) plugin. The value
of the `hostname` attribute of the record can be set by specifying the
desired value as the second (or more) part of the tag, using a `.`
separator, with `.herokuapp.com` automatically appended to the value. To set
the hostname to `my-example-app.herokuapp.com` and route to DataDog, you
would send requests to `/datadog.my-example-app` of this application.

## Setup

Check out this repository and create a Heroku application from it.
```
# Login to Heroku, then create
$ heroku apps:create YOUR-LOGGING-APP-NAME

# Next, configure your API key
$ heroku config:set DD_API_KEY=YOUR-DATADOG-API-KEY -a YOUR-LOGGING-APP-NAME

# Push your application
$ git push heroku master
```

Next, you configure Heroku HTTPS log drains to publish logs to this
application.

```
$ heroku drains:add https://YOUR-LOGGING-APP-NAME/datadog.YOUR-SOURCE-APP-NAME -a YOUR-SOURCE-APP-NAME
```

Logs should now be flowing from your service to DataDog.

## Testing

This application supports sending incoming events to STDOUT using a `debug`
tag prefix.
```
# debug using curl
$ curl "https://YOUR-LOGGING-APP-NAME/debug.YOUR-SOURCE-APP-NAME" -d "60 <13>1 2014-01-29T06:25:52.589365+00:00 host app web.1 - foo"
```

This should show up in the logging application's logs on Heroku.


## License
[![FOSSA Status](https://app.fossa.io/api/projects/git%2Bgithub.com%2FApplauseOSS%2Fheroku-fluentd-datadog.svg?type=large)](https://app.fossa.io/projects/git%2Bgithub.com%2FApplauseOSS%2Fheroku-fluentd-datadog?ref=badge_large)