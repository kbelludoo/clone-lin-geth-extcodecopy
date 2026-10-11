# clone-lin-geth-extcodecopy

LIN clone of [ethereum/go-ethereum](https://github.com/ethereum/go-ethereum) tag `v1.16.9` `gasExtCodeCopyEIP2929` (EXTCODECOPY Berlin cold-warm, LGPL-3.0).

This repository holds **only the LIN copy**. Auditable proofs live in [kbelludoo/lin-open](https://github.com/kbelludoo/lin-open):

- Kernel: `src/lin_geth_extcodecopy.lin`
- Proofs: `examples/geth_extcodecopy/`
- Event: `docs/events/EVENT_GETH_EXTCODECOPY_CLONE_LIN.rulel`
- Harness: `python3 test/prove_geth_extcodecopy_external.py`

## Class

**EXPERIMENTAL** i64-scale. Not a Geth node. Geth `gasExtCodeCopyEIP2929` reads `StateDB` + stack and calls `gasExtCodeCopy` (`memoryCopierGas(3)`). LIN lifts that to integers `size` and `is_cold`. Quadratic `memoryGasCost` (Cmem) is out of scope. Jump-table `constantGas` (`WarmStorageReadCostEIP2929=100`) is not returned by the dynamic function.

## Goldens

Dynamic = `CopyGas * ((size+31)/32)` + `2500` if cold. Opcode total adds the 100 warm constant.

| size | warm dynamic | cold dynamic | warm total | cold total |
|---|---:|---:|---:|---:|
| 0 | 0 | 2500 | 100 | 2600 |
| 1 | 3 | 2503 | 103 | 2603 |
| 32 | 3 | 2503 | 103 | 2603 |
| 33 | 6 | 2506 | 106 | 2606 |
| 1024 | 96 | 2596 | 196 | 2696 |
| 49152 | 4608 | 7108 | 4708 | 7208 |

Berlin delta (cold − warm) is **2500** at every in-range size. Negative size or `is_cold` outside `{0,1}` fail-closes to 0.

## License

Algorithm: LGPL-3.0 (go-ethereum). This LIN transcription is published so auditors can hash the clone independently of lin-open proofs.
