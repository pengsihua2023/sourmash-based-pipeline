# Sourmash 安装和使用指南

## 简介

Sourmash 是一个用于快速搜索、比较和分析基因组和元基因组数据的工具。它使用 MinHash 算法来计算和比较序列签名。

## 系统要求

- Python 3.8 或更高版本
- 64位操作系统（Windows, macOS, 或 Linux）
- 至少 2GB RAM（推荐 4GB 或更多）

## 安装方法

### 方法一：使用 pip 安装（推荐）

这是最简单的安装方法：

```bash
pip install sourmash
```

或者安装最新版本：

```bash
pip install -U sourmash
```

### 方法二：使用 conda 安装

如果您使用 Anaconda 或 Miniconda：

```bash
conda install -c conda-forge -c bioconda sourmash
```

### 方法三：从源代码安装

```bash
git clone https://github.com/sourmash-bio/sourmash.git
cd sourmash
pip install -e .
```

### 验证安装

安装完成后，运行以下命令验证：

```bash
sourmash --version
```

如果显示版本号，说明安装成功。

## 基本使用

### 1. 计算序列签名

从 FASTA 文件计算 MinHash 签名：

```bash
sourmash sketch dna input.fasta -o output.sig
```

参数说明：
- `sketch dna`: 对 DNA 序列进行sketch计算
- `input.fasta`: 输入的 FASTA 文件
- `-o output.sig`: 输出的签名文件

### 2. 比较两个签名

```bash
sourmash compare signature1.sig signature2.sig
```

### 3. 搜索数据库

在数据库中搜索相似序列：

```bash
sourmash search query.sig database.sig
```

### 4. 创建和使用索引

对于大规模搜索，建议先创建索引：

```bash
# 创建索引
sourmash index -k 31 database.sbt.zip *.sig

# 使用索引搜索
sourmash search query.sig database.sbt.zip
```

## 常用命令示例

### 计算 k-mer 大小为 31 的签名

```bash
sourmash sketch dna -p k=31,scaled=1000 input.fasta -o output.sig
```

### 比较多个基因组

```bash
sourmash compare *.sig -o compare_matrix.mat
sourmash plot compare_matrix.mat
```

### 分类学分类

```bash
sourmash gather query.sig database.sbt.zip -o results.csv
```

## 高级选项

### 自定义参数

- `-p k=21,31,51`: 指定多个 k-mer 大小
- `--scaled 1000`: 设置缩放因子（越大越快但精度越低）
- `--track-abundance`: 跟踪 k-mer 丰度

### 示例：高质量签名计算

```bash
sourmash sketch dna input.fasta \
  -p k=21,k=31,k=51,scaled=1000,abund \
  -o high_quality.sig
```

## 获取测试数据

下载 sourmash 提供的测试数据：

```bash
curl -L https://osf.io/q4vms/download -o genome1.fasta
curl -L https://osf.io/tnhsr/download -o genome2.fasta
```

## 常见问题

### Q: 命令未找到
**A**: 请确保 Python 的 Scripts 目录在系统 PATH 中。Windows 用户可能需要：
```bash
python -m sourmash --version
```

### Q: 内存不足错误
**A**: 尝试增加 `--scaled` 参数值，减少内存使用。

### Q: 如何处理大型数据集？
**A**: 使用 `--scaled` 参数和索引功能：
```bash
sourmash sketch dna large_file.fasta -p scaled=10000 -o output.sig
```

## 输出文件说明

- `.sig`: 签名文件，包含计算的 MinHash 签名
- `.sbt.zip`: 索引文件，用于快速搜索
- `.csv`: 结果文件，包含搜索或分类结果
- `.mat`: 比较矩阵，用于可视化

## 性能优化建议

1. **使用适当的 scaled 值**: 对于大型基因组，使用 `scaled=1000` 或更大
2. **创建索引**: 对于频繁搜索的数据库，先创建 SBT 索引
3. **并行处理**: 使用多个进程处理多个文件
4. **选择合适的 k 值**: k=31 适用于大多数细菌基因组

## 相关资源

- 官方文档: https://sourmash.readthedocs.io/
- GitHub 仓库: https://github.com/sourmash-bio/sourmash
- 教程: https://sourmash.readthedocs.io/en/latest/tutorials.html
- 问题反馈: https://github.com/sourmash-bio/sourmash/issues

## 许可证

Sourmash 使用 BSD 3-Clause 许可证。

## 引用

如果您在研究中使用 sourmash，请引用：

```
Brown, C.T. and Irber, L. (2016) sourmash: a library for MinHash sketching of DNA.
Journal of Open Source Software, 1(5), 27, doi:10.21105/joss.00027
```

## 支持

如果遇到问题，可以：
1. 查看官方文档
2. 在 GitHub 上提交 issue
3. 加入 sourmash 社区讨论

---

**注意**: 本指南适用于 sourmash 独立工具。对于编程 API 的使用，请参考官方 API 文档。

