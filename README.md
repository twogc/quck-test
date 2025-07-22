# 2GC CloudBridge QUICK testing

[🇷🇺 Read in Russian (Русская версия)](README_RU.md) 

Load tester and analyzer for QUIC + TLS 1.3

[![Watch Demo Video](https://customer-aedqzjrbponeadcg.cloudflarestream.com/d31af3803090bcb58597de9fe685a746/thumbnails/thumbnail.jpg)](https://customer-aedqzjrbponeadcg.cloudflarestream.com/d31af3803090bcb58597de9fe685a746/watch)

## Running

### Server

```
go run main.go --mode=server --addr=:9000
```

### Client

```
go run main.go --mode=client --addr=127.0.0.1:9000 --connections=2 --streams=4 --packet-size=1200 --rate=100 --report=report.md --report-format=md --pattern=random
```

### Test (server+client)

```
go run main.go --mode=test
```

## Flag Description

* `--mode` — operating mode: `server`, `client`, `test` (default `test`)
* `--addr` — address to connect or listen (default `:9000`)
* `--connections` — number of QUIC connections (default 1)
* `--streams` — number of streams per connection (default 1)
* `--duration` — test duration (0 means manual stop, default 0)
* `--packet-size` — packet size in bytes (default 1200)
* `--rate` — packet sending rate per second (default 100, supports ramp-up/ramp-down)
* `--report` — report file path (optional)
* `--report-format` — report format: `csv`, `md`, `json` (default `md`)
* `--cert` — TLS certificate path (optional)
* `--key` — TLS key path (optional)
* `--pattern` — data pattern: `random`, `zeroes`, `increment` (default `random`)
* `--no-tls` — disable TLS (for tests)
* `--prometheus` — export Prometheus metrics on `/metrics`
* `--emulate-loss` — packet loss probability (0..1, e.g., 0.05 for 5%)
* `--emulate-latency` — additional latency before sending packet (e.g., 20ms)
* `--emulate-dup` — packet duplication probability (0..1)

## Advanced Features

* **Detailed Metrics:**

  * Latency percentiles (p50, p95, p99), jitter, packet loss, retransmits, handshake time, session resumption, 0-RTT/1-RTT, flow control, key updates, out-of-order packets, error breakdown.
* **Time-Series Metrics:**

  * For latency, throughput, packet loss, retransmits, handshake time, etc.
* **ASCII Graphs:**

  * Included in Markdown reports for all key metrics.
* **Ramp-up/ramp-down:**

  * Dynamically adjust packet sending rate for stress testing.
* **Network Condition Emulation:**

  * Latency, packet loss, duplication (see flags above).
* **CI/CD Integration:**

  * JSON reports, SLA-based exit codes.
* **Prometheus:**

  * Export live metrics for monitoring.

## Example with Network Emulation

```
go run main.go --mode=client --addr=127.0.0.1:9000 --connections=2 --streams=4 --packet-size=1200 --rate=200 --emulate-loss=0.05 --emulate-latency=20ms --emulate-dup=0.01 --report=report.md
```

## Default Behavior

* If `--duration` is not set, the test runs until manually stopped (Ctrl+C).
* Upon completion, a report is automatically generated and saved in the selected format.

## Report Examples

* Markdown, CSV, JSON — include test parameters, aggregated metrics, time-series, ASCII graphs, errors.

## Dependencies

* [quic-go](https://github.com/lucas-clemente/quic-go)
* [tablewriter](https://github.com/olekukonko/tablewriter)
* [asciigraph](https://github.com/guptarohit/asciigraph)
* [prometheus/client\_golang](https://github.com/prometheus/client_golang)