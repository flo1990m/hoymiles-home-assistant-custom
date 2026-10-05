# Hoymiles S-Miles Home Custom

Personal Home Assistant integration based on Phil-0805/hoymiles-home-assistant.

This repository keeps the original `hoymiles_home` domain so an existing Home Assistant config entry can continue to be used. It adds a diagnostic request for Hoymiles inverter indicators (`type=6`) so the exact temperature field returned by the user's station can be identified before exposing it as a normal sensor.


## Custom additions

- v0.3.0-custom.3: adds inverter internal temperature from Hoymiles indicator `inv_tin`, removes temporary warning log spam, and fixes battery settings exception handling.
