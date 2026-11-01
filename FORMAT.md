# File format, version 2

This specification describes the complete released database without requiring DKemergy application code. The file is ordinary SQLite 3, with one independent Zstandard frame per vector block. Standard SQLite and Zstandard implementations can read it.

## SQLite tables

```sql
CREATE TABLE metadata(key TEXT PRIMARY KEY, value TEXT);
CREATE TABLE blocks(id INTEGER PRIMARY KEY, z INTEGER, kind INTEGER, data BLOB);
CREATE VIRTUAL TABLE bounds USING rtree(id, minlon, maxlon, minlat, maxlat);
```

`bounds_rowid`, `bounds_node`, and `bounds_parent` are SQLite R-tree implementation tables. `bounds.id` joins `blocks.id`. Bounds are WGS84 longitude/latitude decimal degrees, stored as outward-rounded R-tree bounds. `blocks.z` is the minimum display zoom, not a tile coordinate. `blocks.kind` is the kind code below. `data` is a Zstandard frame compressed at level 19.

The release groups features by minimum zoom, kind, and a 0.1-degree grid cell containing the middle stored vertex. A feature can extend outside that grid cell; use the R-tree bounds for spatial selection. Groups are split into blocks of at most 256 features. The release has 20,648 blocks and 1,930,410 features.

Example block selection for a rectangle, using longitude west/east and latitude south/north:

```sql
SELECT blocks.id, blocks.z, blocks.kind, blocks.data
FROM blocks JOIN bounds ON bounds.id = blocks.id
WHERE bounds.maxlon >= :west AND bounds.minlon <= :east
  AND bounds.maxlat >= :south AND bounds.minlat <= :north
ORDER BY blocks.id;
```

This selects intersecting block bounds, not exact feature intersections. Reading all rows of `blocks`, ordered by `id`, yields the complete released feature set.

## Decompressed block layout

1. Five magic bytes: hexadecimal `44 4b 4d 56 01` (`DKMV` followed by byte 1).
2. Feature count, as an unsigned base-128 varint.
3. For each feature, in order:
   - One byte: kind code.
   - Unsigned varint: UTF-8 label length in bytes.
   - That many label bytes. Empty labels are allowed.
   - Unsigned varint: number of coordinate pairs.
   - For each pair, one ZigZag-encoded longitude delta varint, followed by one ZigZag-encoded latitude delta varint.

For each feature, initialize longitude and latitude accumulators to zero. Decode each signed delta and add it to its accumulator. Divide each accumulated integer by 1,000,000 to obtain WGS84 degrees. Coordinate order is always longitude, latitude. Accumulators reset for every feature, not only every block.

An unsigned varint stores seven low-order bits per byte, least significant group first. Bit 7 indicates that another byte follows. A 64-bit value uses at most ten bytes. Decode ZigZag unsigned integer `u` as `(u >> 1) XOR -(u & 1)`. A block ends immediately after its last coordinate pair; there is no padding or trailing section.

The format permits labels up to 4,096 bytes and feature point counts up to 200,000. Decompressed blocks are at most 4 MiB. Validate lengths, coordinate ranges, and final byte consumption before using arbitrary downloaded files. The published SHA256 values identify this release.

## Kind codes

| Code | Layer |
| --- | --- |
| 0 | place |
| 1 | major road |
| 2 | road |
| 3 | path |
| 4 | water area |
| 5 | coastline |
| 6 | railway |
| 7 | green area |
| 8 | river or stream |
| 9 | boundary fragment |
| 10 | contour, reserved in this release |
| 11 | index contour, reserved in this release |

Place features are single points. Other features contain ordered coordinate sequences. Closed water/green sequences can be drawn as areas; open fragments must remain lines. The file does not encode full OSM relation topology, routing connectivity, one-way restrictions, or turn restrictions. It contains no terrain raster or contour blocks.

## Metadata caveat

The SQLite `metadata.coordinateFormat` value is inherited from the intermediate uncompressed database and says `little-endian int32 lon,lat, degrees * 1e6`. That describes the intermediate file only. In this schema-2 release, use the Zstandard/delta/varint format above. `metadata.schema`, `metadata.compression`, and the separate `map-manifest.json` identify the released encoding. The original file is distributed byte-for-byte rather than silently rewriting that inherited field.

## Transport JSON

The root object contains provenance, `coverage`, and `facilities`. `schemaVersion` is 1. Each facility has `id`, `kind`, `name`, `address`, `latitude`, `longitude`, `entrance`, `drinkingStatus`, `source`, `sourceURL`, and `registrationStatus`; an optional `phone` may be null. Strings are UTF-8. Latitude and longitude are WGS84 decimal degrees.

`kind` is `port` or `airport`. `source` is `OpenStreetMap` or `OurAirports`, preserving the separately licensed components. `entrance: false` means the coordinate is a representative registered location. `drinkingStatus: "unknown"` is a generic schema field and makes no claim about drinking water. Descriptive status text does not confirm live operation. The release contains 440 OSM port records and 15 public-domain OurAirports airport records.
