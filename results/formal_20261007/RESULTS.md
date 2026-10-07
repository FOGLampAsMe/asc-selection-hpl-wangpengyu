# HPL 正式结果

姓名：王鹏宇  
学号：240809010506  
题目：HPL 基础题

## 环境

- CPU：2 × Intel Xeon Platinum 8255C
- 容器可用：12 个 CPU 核，约 43 GiB cgroup memory
- HPL：2.3
- MPI：OpenMPI 4.1.2
- BLAS：OpenBLAS 0.3.20
- 线程：`OMP_NUM_THREADS=1`、`OPENBLAS_NUM_THREADS=1`、`MKL_NUM_THREADS=1`
- 编译：`-O3 -march=native -mtune=native`
- GPU：未参与计算

## 同规模参数链

四组实验均使用 `N=32768`、12 个 MPI 进程，并通过 HPL 残差检查。

| 配置 | NB | P×Q | BCAST | GFLOPS | 结果 |
| --- | ---: | ---: | ---: | ---: | --- |
| Baseline | 192 | 3×4 | 1 | 513.46 | PASSED |
| 仅调整 NB | 256 | 3×4 | 1 | 520.74 | PASSED |
| 调整进程网格 | 256 | 2×6 | 1 | 543.99 | PASSED |
| 最终优化 | 256 | 2×6 | 3 | 565.00 | PASSED |

正式同规模加速比：

```text
565.00 / 513.46 = 1.100×
```

相对性能提升约 10.0%。参数调整顺序是先改变块大小，再调整 MPI 进程网格，最后调整广播方式。这样可以分别观察块计算、进程映射和通信设置的影响。

## 探索结果

完成同规模对照后，测试了 `N=61440`、`NB=288`、`P×Q=2×6`、`PFACT=1`、`RFACT=2`、`BCAST=3`、`DEPTH=0`，单次结果为 648.22 GFLOPS，状态为 `PASSED`。该组使用了不同的 N，没有与同 N 的基线配对，因此只作为探索记录，不计算正式加速比。

## 复现

```bash
export OMP_NUM_THREADS=1 OPENBLAS_NUM_THREADS=1 MKL_NUM_THREADS=1
mpirun --allow-run-as-root --map-by core --bind-to core -np 12 ./xhpl
```

正式优化对应的 `HPL.dat` 参数为 `N=32768`、`NB=256`、`P=2`、`Q=6`、`BCAST=3`。完整原始记录来自服务器 `/root/autodl-tmp/asc26-hpl/RESULTS.md` 和 `runs/` 目录。
