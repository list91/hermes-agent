# CoPostly: Hermes Agent as the single brain of Buzzy (board chat + Telegram).
# A thin layer over the official image of the SAME release as this fork's code (v0.22.6), pinned by digest:
# building Hermes from source (Chromium, frontends) on the 1-CPU / 2.5 GB build box is not viable.
# Only our managed config is added; ENTRYPOINT/CMD stay as upstream (entrypoint-dispatch.sh -> /init -> gateway).
FROM nousresearch/hermes-agent:v0.22.6@sha256:d27689bc6e8581ec884a7227c9101fdaebf6c853cff8859020dac14f21452b4d

# Managed scope: wins over /opt/data/config.yaml for every key set here and cannot be rewritten at runtime.
COPY --chmod=0644 config.yaml /etc/hermes/config.yaml
# Do not stamp/migrate the seeded user config in the volume on start: managed scope pins what matters.
ENV HERMES_SKIP_CONFIG_MIGRATION=1

CMD ["gateway", "run"]
