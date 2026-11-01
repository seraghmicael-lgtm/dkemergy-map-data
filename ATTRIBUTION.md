# Data attribution

## OpenStreetMap

`korea-map.sqlite` and the `transport.json` records whose `source` is `OpenStreetMap` are derived from OpenStreetMap data.

- Attribution: © OpenStreetMap contributors.
- Copyright and contributors: https://www.openstreetmap.org/copyright
- License: Open Data Commons Open Database License 1.0, https://opendatacommons.org/licenses/odbl/1-0/
- Exact license text distributed as `LICENSE`, downloaded from https://opendatacommons.org/licenses/odbl/odbl-10.txt
- Extract: https://tiles.osm.kr/download/south-korea-latest-non-military.osm.pbf
- Extract download date: 2026-10-30 UTC.
- Extract SHA256: `a7502ac0e74e74cff3cf330a2b0b01305459e43c2bac80265e9788c1b35d9813`.

The map keeps selected display layers and labels, quantizes coordinates to one millionth of a degree, and packs the selected features losslessly after that quantization. It does not retain all original OSM tags or identifiers. OSM port records retain source object links. The complete resulting map database and port records are supplied without an additional fee or technological restrictions.

## OurAirports

The 15 `transport.json` records whose `source` is `OurAirports` are selected passenger-airport records from OurAirports. They remain a distinct public-domain data component; the ODbL notice for the OSM component does not impose new conditions on these public-domain records.

- Source and public-domain terms: https://ourairports.com/data/
- Each airport record includes its OurAirports source URL.
- Source airport-file SHA256: `6c600b7821829c2bd672c97b0c1e25b51a12e1e281fbd6fb1b698156b17d6acb`.
- Transport collection date: 2026-10-31 UTC.

OurAirports does not guarantee accuracy or suitability. Localized names and descriptive display fields do not establish current airport operation or terminal access.
