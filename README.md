# WeatherBug uploader for WeeWX

Upload archive weather readings using the legacy WeatherBug Backyard publishing
API 1.4 query format. The mapping includes wind, temperature, humidity, pressure,
rain totals and selected additional sensors.

This repository mirrors [the upstream source](https://github.com/matthewwall/weewx-wbug). The maintained
catalog archive is **0.8.post1**, with Python 3 response decoding repaired. Install that fixed archive below;
the mirrored default branch can differ from its repaired payload.

The catalog archive targets WeeWX `>=5.5.2,<6` and Python `>=3.10,<3.15`.

## Install

Use `weectl` from the Python environment running WeeWX. With stock WeeWX 5,
install the published archive:

```sh
weectl extension install https://github.com/weewx-packages/catalog/releases/download/wbug/v0.8.post1/wbug-0.8.post1.tar.gz --config /path/to/weewx.conf
```

Replace `/path/to/weewx.conf` with your station configuration. You can also
download that URL and pass the local archive to the same command. Use elevated
permissions only when your station files require them.

## Connect an existing publishing account

You need a WeatherBug publisher ID, station number and publishing password
accepted by that legacy service. Check access with the provider before relying
on it; this archive's local protocol checks do not establish current account
registration or endpoint availability. There are no extra Python dependencies.

```ini
[StdRESTful]
    [[WeatherBug]]
        publisher_id = YOUR_PUBLISHER_ID
        station_number = YOUR_STATION_NUMBER
        password = YOUR_PUBLISHING_PASSWORD
```

The installer registers `user.wbug.WeatherBug`. Restart WeeWX yourself, then
check its log after the next archive record. The implementation targets
`http://data.backyard2.weatherbug.com/data/livedata.aspx` and expects replies
starting with `Successfully Received`.

## Limits

The default endpoint sends credentials in an HTTP query. A `server_url`
override is available, but use another endpoint only if your provider confirms
its protocol. The service reads `wx_binding` for rain totals and daily high/low
temperatures. Its day, month and year aggregation helpers use UTC boundaries.
Weather values use the US conversion for Fahrenheit, mph, inches and inHg.

The default map also forwards radiation and soil observations without
specialized provider-specific scaling. Check those units before depending on
them. Missing readings are omitted; the service does not validate that every
field required by the provider exists. See [the protocol map and legacy API
reference](bin/user/wbug.py) for the complete mapping.

Copyright 2014-2020 Matthew Wall. GNU GPLv3, as stated in [install.py](install.py).
The catalog archive includes the full license text.
