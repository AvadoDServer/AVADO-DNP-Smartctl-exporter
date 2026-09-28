# AVADO-DNP-Smartctl-exporter

**SMART disk stats** for AVADO: shows your disk's health, temperature and wear in Grafana.

It is installed together with the [Grafana package](https://github.com/AvadoDServer/AVADO-DNP-Grafana)
(open Grafana and pick the **Disk health** dashboard). There is nothing to set up.

## What it does

It runs the [Prometheus smartctl exporter](https://github.com/prometheus-community/smartctl_exporter)
(v0.14.0, with smartmontools 7.4), which reads the SMART data of the AVADO's drives every minute
and publishes it at `http://smartctl-exporter.my.ava.do:9633/metrics` on the AVADO's internal
network. The [Prometheus package](https://github.com/AvadoDServer/AVADO-DNP-Prometheus) collects it
(job `smartctl`) and Grafana's **Disk health** dashboard shows it: SMART status, temperature, wear
(percentage used), data written, power-on hours, spare capacity, media errors, critical warnings
and the NVMe error log.

No ports are published on the host: the metrics are only reachable from other AVADO packages and
from browsers connected to the AVADO network.

## Why the container is privileged and runs as root

`smartctl` can only read SMART data as root, from a container that can open the host's drives.
The AVADO package manager does not pass `devices` or `user` settings to packages, so the package
runs with `privileged: true` and the image switches to `USER root` (the upstream image runs as
`nobody`). The exporter only reads SMART data (`smartctl --info --health --attributes --log=error`).
It does not run self-tests or change drive settings, and it does not wake drives that are in
standby.

## Drives

All drives found by `smartctl --scan` are read: NVMe drives and SATA drives, including USB
drives that are plugged in (a USB stick or enclosure that does not pass SMART through shows up
with little or no data). Drives added or removed are picked up within 10 minutes.
