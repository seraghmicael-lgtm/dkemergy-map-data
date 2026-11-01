# DKemergy public map data

Free, machine-readable copies of the South Korea map and transport datasets distributed with DKemergy build 9. Downloads require no account, app purchase, authentication, or additional agreement.

## Download

[Release v1.0.0-build9](https://github.com/seraghmicael-lgtm/dkemergy-map-data/releases/tag/v1.0.0-build9)

| Asset | Contents |
| --- | --- |
| [korea-map.sqlite](https://github.com/seraghmicael-lgtm/dkemergy-map-data/releases/download/v1.0.0-build9/korea-map.sqlite) | 89,788,416 bytes; 1,930,410 map features in 20,648 compressed blocks |
| [map-manifest.json](https://github.com/seraghmicael-lgtm/dkemergy-map-data/releases/download/v1.0.0-build9/map-manifest.json) | Source, collection date, feature counts, size, and SHA256 |
| [transport.json](https://github.com/seraghmicael-lgtm/dkemergy-map-data/releases/download/v1.0.0-build9/transport.json) | 440 OSM ports/ferry locations and 15 OurAirports passenger airports |
| [SHA256SUMS](https://github.com/seraghmicael-lgtm/dkemergy-map-data/releases/download/v1.0.0-build9/SHA256SUMS) | Checksums for the three data files and LICENSE |

After downloading the files and [LICENSE](https://github.com/seraghmicael-lgtm/dkemergy-map-data/releases/download/v1.0.0-build9/LICENSE) into one directory, verify them with `shasum -a 256 -c SHA256SUMS` or `sha256sum -c SHA256SUMS`.

## Sources and permissions

The Korea map database and the OSM port records contain information from [OpenStreetMap](https://www.openstreetmap.org/copyright), © OpenStreetMap contributors, made available here under the [Open Database License 1.0](LICENSE). The derived database is available for download without DRM or additional restrictions, including to recipients of the app. The license permits reuse and modification subject to its attribution and share-alike conditions.

The source was the [South Korea non-military OSM extract](https://tiles.osm.kr/download/south-korea-latest-non-military.osm.pbf), downloaded on 2026-10-30 UTC. The source file SHA256 is recorded in the manifest. Its upstream replication timestamp was unavailable; the download date is not a claim that every feature was verified that day.

Airport records are a separate [OurAirports](https://ourairports.com/data/) public-domain component. That public-domain status is unchanged by inclusion in `transport.json`. Each record identifies its source; use `source == "OpenStreetMap"` or `source == "OurAirports"` to separate the components. Transport data was assembled on 2026-10-31 UTC. See [ATTRIBUTION.md](ATTRIBUTION.md) for component details.

## Format and limits

The SQLite database stores vector coordinates in independent Zstandard blocks, using E6 integer coordinate deltas, ZigZag encoding, and unsigned varints. [FORMAT.md](FORMAT.md) documents the complete block format, SQLite tables, coordinate system, feature kinds, and transport JSON fields. The database is an extract for display rather than a complete OSM planet or a routing graph. Terrain elevation and contour rasters, shelter/water/medical directories, and application code are not part of this release.

This is a dated geographic snapshot. It does not establish safe routes, road access, evacuation availability, airport or port operation, passenger service, water quality, or emergency rescue access. Airport coordinates represent airports, not terminal entrances. OSM feature names and geometries may be incomplete or inaccurate.

## 한국어 안내

위 릴리스에서 대한민국 지도 DB와 항구·공항 자료를 로그인이나 결제 없이 내려받을 수 있습니다. OSM 지도와 항구 자료는 ODbL 1.0을 따르며, OurAirports 공항 자료는 퍼블릭 도메인입니다. 파일 형식과 SHA256을 공개합니다. 현재 통행·운영 상태나 안전한 대피 경로를 보장하는 자료는 아닙니다.
