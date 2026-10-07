# StandGuard Pre-Flight Action

**Lint your HIL test-bench config before it reaches hardware.**

A transposed row in a bench spreadsheet once put 28 V into a 12 V rail and killed a six-week prototype. This action runs the [StandGuard](https://standguard.dev) linter — 20 checks across pin conflicts, interlocks, Nyquist timing, stimulus overdrive, sequence references — on every commit. Exit code 1 means *do not apply power*.

## Usage

```yaml
- uses: HanchoPOW/standguard-action@v1
  with:
    config: bench.json      # path to your bench config (default: bench.json)
    strict: "false"         # "true" treats warnings as errors
```

A failing config fails the build:

```
[ERROR  ] pin.conflict        channels['sense_line']: pin 'P1.3' already allocated to channel 'bus_power'
[ERROR  ] interlock.cuts      interlocks['estop_loop_1']: cuts unknown channel 'bus_pwr'
7 error(s), 1 warning(s) — BENCH UNSAFE TO POWER (exit 1)
```

## The bench config format

The configuration spec and the full check catalog are open (MIT): [github.com/HanchoPOW/standguard-spec](https://github.com/HanchoPOW/standguard-spec). Generate an annotated reference config:

```bash
python3 standguard.py example
```

## Try it without installing anything

The same linter runs in your browser (WebAssembly, nothing leaves your machine): [standguard.dev/play](https://standguard.dev/play/?src=action)

## Air-gapped benches

Mirror `standguard.py` (one file, zero dependencies, Python 3.9+) inside your network and point `version-url` at your mirror. No telemetry, no phone-home — the linter is one auditable file.
