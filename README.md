# HPL 基础题提交材料

姓名：王鹏宇  
学号：240809010506  
专业和年级：24届计算机科学与技术  
题目：HPL 基础题  
资源：Google Colab（OpenMPI + OpenBLAS，2 个 MPI 进程）

## 完成情况

在 Colab 中编译 HPL 2.3，使用相同的 `mpirun --allow-run-as-root --oversubscribe -np 2 ./xhpl` 命令测试 3 组 `N/NB` 参数。三组运行均 `returncode=0`，残差校验为 `PASSED`。完整数值见 `results/hpl_results.json`。

## 结果

| N | NB | P x Q | 时间 / s | GFLOPS | 正确性 |
|---:|---:|:---:|---:|---:|:---|
| 256 | 32 | 1 x 2 | 0.922635 | 0.012123 | PASSED |
| 384 | 32 | 1 x 2 | 0.638987 | 0.059076 | PASSED |
| 512 | 64 | 1 x 2 | 0.722432 | 0.123857 | PASSED |

综合本次小规模 Colab 测试，选择 `N=512, NB=64` 作为最好配置，GFLOPS 最高且残差校验通过。不同 N 的绝对时间受小规模启动和 MPI 开销影响，不能只按时间排序。

## 复现

```bash
cd /content/hpl-2.3/bin/Colab
mpirun --allow-run-as-root --oversubscribe -np 2 ./xhpl
```

每次测试只修改 `HPL.dat` 中的 N 和 NB；HPL 源码、编译产物和 Colab 系统包不放入本轻量仓库。
