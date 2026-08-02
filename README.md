# Experiments Data

This repository contains the benchmark datasets, executable, and experimental
results used in our paper. The result data can be validated using the binary
checker provided by the industrial vendor, ensuring both correctness and
authenticity.

## Contents

- `TestCase/`  
  Benchmark testcases from the Die-Level Routing Contest 2023 and the
  industrial vendor.

- `with_rerouting_result/`  
  Outputs of our algorithm with the delay-constrained rerouting module enabled.
  These results demonstrate the routing-quality improvement provided by
  rerouting and TDM-ratio assignment.

- `without_rerouting_result/`  
  Results produced without the rerouting module. The `design.route.out` file in
  each testcase directory is also used as the initial routing topology when
  reproducing the experiments with `Routing`.

- `Routing`  
  A statically linked Linux x86-64 executable of our router. 

- `route_checker_1201`  
  Binary tool provided by the industrial vendor for result validation.

## An Initial Route Is Required

`Routing` takes an existing routing topology as its starting point. This design
allows the same initial route to be used for both the baseline and the enhanced
flow, isolating the effects of the delay-constrained rerouting module and the
subsequent TDM-ratio assignment and legalization stages.

For each testcase, `Routing` reads only `design.route.out` from the directory
specified by `--init-route-dir`. In our experiments, the corresponding directory
under `without_rerouting_result/` provides this initial route. The executable
then performs rerouting and TDM-ratio optimization and writes a new
`design.route.out` and `design.tdm.out` to `--output-dir`.

## Running Routing

First, grant execute permission to the binaries:

```bash
chmod +x Routing route_checker_1201
```

The three directory arguments refer directly to one testcase directory:

- `--input-dir`: directory containing the benchmark design files.
- `--init-route-dir`: directory containing the initial `design.route.out`.
- `--output-dir`: directory in which the generated `design.route.out` and
  `design.tdm.out` will be written.

For example, reproduce testcase1 without overwriting the published results:

```bash
mkdir -p reproduced_result/testcase1

./Routing \
  --input-dir ./TestCase/testcase1 \
  --init-route-dir ./without_rerouting_result/testcase1 \
  --output-dir ./reproduced_result/testcase1 \
  --no-memory
```

The testcase number is inferred automatically from the final component of
`--input-dir` (for example, `testcase1`). 

Use `--memory` instead of `--no-memory` to print the process memory statistics.

> **Note:** `--output-dir` may be the same as another directory, but using a
> separate output directory is recommended to avoid overwriting the benchmark
> data or the published reference results.

## Validating Results

To validate newly reproduced results, run the vendor-provided checker:

```bash
./route_checker_1201 \
  -input_design_dir ./TestCase/testcase1 \
  -output_route_out_dir ./reproduced_result/testcase1 \
  -output_tdm_out_dir ./reproduced_result/testcase1
```

The published results can be checked in the same way. For example:

```bash
./route_checker_1201 \
  -input_design_dir ./TestCase/testcase1 \
  -output_route_out_dir ./with_rerouting_result/testcase1 \
  -output_tdm_out_dir ./with_rerouting_result/testcase1
```
