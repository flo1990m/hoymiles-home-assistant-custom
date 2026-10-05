# Hoymiles S-Miles Home – Florian custom build

Custom Home Assistant build based on `Phil-0805/hoymiles-home-assistant`.

## custom.6

- Reads the microinverter temperature from the S-Miles Home Protobuf chart endpoint.
- Uses the official Hoymiles micro-inverter quota `MI_TEMPERATURE`.
- Keeps the existing `Temperature` entity attached to the Hoymiles microinverter device.
- Removes the temporary JSON indicator and microinverter-detail diagnostics used in earlier test builds.
- Keeps the battery settings fix from the previous custom builds.

Temperature is refreshed with the same chart interval as PV1–PV4 values.
