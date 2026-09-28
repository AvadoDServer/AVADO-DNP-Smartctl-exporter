# smartctl_exporter v0.14.0 (smartmontools 7.4), pinned by digest.
FROM --platform=linux/amd64 prometheuscommunity/smartctl-exporter:v0.14.0@sha256:cfe22c36d7d2fac48ebf619707305acb65eb0fb670656eb80f356e606d782bc1

# smartctl can only read SMART data as root, and only from a privileged
# container (the package runs with `privileged: true`, see the manifest).
# The upstream image runs as `nobody`, which sees no drives at all.
USER root

# Defaults: scan all drives, read them every 60 s (drives in standby are not
# woken up), rescan for added/removed drives every 10 min, serve the metrics
# on :9633 (reachable only on the AVADO network, no host port).
ENTRYPOINT ["/bin/smartctl_exporter"]
CMD []
