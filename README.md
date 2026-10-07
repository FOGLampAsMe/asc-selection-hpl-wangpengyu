# HPL 基础题提交材料

姓名：王鹏宇  
学号：240809010506  
专业和年级：24届计算机科学与技术  
题目：HPL 基础题  

## 正式实验环境

- 硬件：2 × Intel Xeon Platinum 8255C；容器可用 12 个 CPU 核；约 43 GiB cgroup 内存
- 软件：HPL 2.3、OpenMPI 4.1.2、OpenBLAS 0.3.20
- 线程：`OMP_NUM_THREADS=1`、`OPENBLAS_NUM_THREADS=1`、`MKL_NUM_THREADS=1`
- 编译：`-O3 -march=native -mtune=native`
- GPU：未参与 HPL 计算

## 固定规模对照

正式加速比使用同一个 `N=32768`、12 个 MPI 进程和同一套 OpenBLAS。每组运行都通过 HPL 残差检查。

| 配置 | NB | P×Q | BCAST | GFLOPS | 正确性 |
| --- | ---: | ---: | ---: | ---: | --- |
| Baseline | 192 | 3×4 | 1 | 513.46 | PASSED |
| 仅调整 NB | 256 | 3×4 | 1 | 520.74 | PASSED |
| 调整进程网格 | 256 | 2×6 | 1 | 543.99 | PASSED |
| 最终优化 | 256 | 2×6 | 3 | 565.00 | PASSED |

同规模加速比为 `565.00 / 513.46 = 1.100×`，性能提升约 10.0%。参数链和结果见 [`results/formal_20261007/RESULTS.md`](results/formal_20261007/RESULTS.md)。

## 复现要点

```bash
export OMP_NUM_THREADS=1 OPENBLAS_NUM_THREADS=1 MKL_NUM_THREADS=1
mpirun --allow-run-as-root --map-by core --bind-to core -np 12 ./xhpl
```

正式优化的 `HPL.dat` 使用 `N=32768`、`NB=256`、`P=2`、`Q=6`、`BCAST=3`。服务器原始记录保存在 `/root/autodl-tmp/asc26-hpl/RESULTS.md`。更大 `N=61440、NB=288` 的 648.22 GFLOPS 是探索结果，未与不同规模基线计算加速比。

历史 Colab 小规模记录仍保留在仓库中，但不参与本次正式结论。
