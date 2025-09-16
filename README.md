# Homomorphic-Encryption
A repository that brings together various homomorphic encryption algorithms.

Homomorphic-Encryption

一个聚合多种同态加密/密码学库的集合仓（monorepo）。
通过 git subtree 将常用库纳入 external/ 子目录，便于统一管理、统一示例、统一依赖。

目标

🌐 一站式：在一个仓库内找到主流 FHE/密码库的可用代码与示例。

🧭 导航清晰：用哪个算法 → 该用哪个库，一目了然。

🔁 易维护：通过 git subtree 定期同步上游，保持更新。

仓库结构
Homomorphic-Encryption/
├─ external/
│  ├─ SEAL-repo/                # Microsoft SEAL（BFV、CKKS）
│  ├─ tfhe-repo/                # TFHE（boolean-level / gate-level FHE）
│  ├─ openfhe-repo/             # OpenFHE（BFV/BGV/CKKS 等）
│  ├─ bfv-py/                   # bfv-py（Python 封装的 BFV 示例/工具）
│  ├─ helib-repo/               # HElib（BGV/CKKS）
│  ├─ pailliercryptolib/        # Intel Paillier（加法同态）
│  ├─ libgm/                    # Goldwasser–Micali（乘法同态于比特层/加法同态于明文位）
│  └─ cryptopp/                 # Crypto++（广泛通用密码学原语）
├─ examples/                    # 本仓统一的示例（推荐你逐步补充）
│  ├─ seal_bfv_demo.cpp
│  ├─ openfhe_ckks_demo.cpp
│  ├─ tfhe_boolean_demo.cpp
│  └─ paillier_sum_demo.py
├─ scripts/                     # 子树同步/构建脚本
│  ├─ subtree_import.sh
│  └─ subtree_update_all.sh
└─ README.md


目录名以你真实合入时的命名为准（上面按你之前的命名举例：SEAL-repo、tfhe-repo 等）。

我该用哪个库？（算法 → 库 对照表）
需求 / 算法场景	推荐库	子目录	说明
BFV（整数同态，性能好，常用于计数/聚合）	Microsoft SEAL, OpenFHE, HElib, bfv-py	external/SEAL-repo, external/openfhe-repo, external/helib-repo, external/bfv-py	SEAL/HElib/OpenFHE 都支持 BFV；bfv-py 便于快速原型（Python）。
BGV（整数同态，支持模切换/编码灵活）	HElib, OpenFHE	external/helib-repo, external/openfhe-repo	学术/工程两头都比较成熟。
CKKS（近似数值同态，浮点/ML 推理常用）	SEAL, HElib, OpenFHE	external/SEAL-repo, external/helib-repo, external/openfhe-repo	适合统计/ML 场景的近似计算。
TFHE（bit/gate-level FHE，布尔门操作）	TFHE	external/tfhe-repo	精细到门级的操作，适合布尔电路。
Paillier（加法同态，轻量/易用）	pailliercryptolib（Intel）	external/pailliercryptolib	常用于安全求和/聚合（联邦学习等）。
Goldwasser–Micali (GM)	libgm	external/libgm	比特级别的同态特性（历史经典算法）。
通用密码原语/工具箱	Crypto++	external/cryptopp	哈希/对称/非对称等通用组件。

选择建议：

需要浮点/近似 → CKKS（SEAL/OpenFHE/HElib）；

需要精确整数 → BFV/BGV（SEAL/HElib/OpenFHE）；

只需加法同态且实现简单 → Paillier；

要在比特/门级做运算 → TFHE。

快速开始

以下示例假定你已经在各子库的官方说明里装好了必需的编译依赖（CMake、C++ 编译器、Python 环境等）。

1) 使用 SEAL（BFV/CKKS）
cd external/SEAL-repo
# 参考其官方 README 构建，如：
cmake -S . -B build && cmake --build build -j
# 运行你在 examples/ 下的示例（建议你在本仓 examples/ 统一放 Demo）

2) 使用 OpenFHE（BFV/BGV/CKKS）
cd external/openfhe-repo
# 参考其官方 README 构建/安装

3) 使用 TFHE（布尔门级）
cd external/tfhe-repo
# 参考其官方 README 构建/运行 test/demo

4) 使用 Paillier（加法同态）
cd external/pailliercryptolib
# 按官方说明构建；或直接在本仓 examples/ 里调用其 API 做加法聚合 demo


建议：在本仓根的 examples/ 里放统一风格的 Demo（输入→加密→同态运算→解密对比），并在 README 里逐个链接，方便新人直接上手。

如何同步上游子库（git subtree）

本仓用 git subtree 把外部库放入 external/ 子目录。下面是常用命令（以 SEAL 为例）：

首次导入（你已完成）
git remote add seal https://github.com/microsoft/SEAL.git
git fetch seal
git subtree add --prefix=external/SEAL-repo seal main --squash

后续更新
git fetch seal
git subtree pull --prefix=external/SEAL-repo seal main --squash
git push origin main


其他库同理：

TFHE：openfhe/tfhe 等你设置的远程别名，分支对应 main 或 master；

HElib：分支多为 master；

Crypto++ / Paillier / libgm / bfv-py：按各自默认分支（main/master）更新。

你可以把所有库的更新写到一个脚本里（本仓 scripts/subtree_update_all.sh），一键同步。

依赖与编译（建议）

C++: 建议使用 CMake ≥ 3.16，GCC/Clang 的近年版本。

Python: conda/venv 管理环境，按示例安装 requirements（如 pybind11、numpy 等）。

平台：Linux/macOS/WSL，Windows（MSVC）按各子库官方说明。

FAQ

为什么不用 submodule？
为了把代码真正放到一个仓里，便于统一搜索/示例/重构，并减少协作者对子模块命令的学习成本。subtree 也支持后续同步上游。

会不会冲突？
每个子库都在 external/<name> 独立目录下，互不影响。构建与依赖按各自官方流程进行。

许可证怎么处理？
各子库 保留各自的 LICENSE（位于它们各自目录下）。根目录可新增 THIRD_PARTY_NOTICES.md 说明来源与许可证类型。

贡献

欢迎提交：

新的 Demo（examples/），展示不同库在相同任务上的用法对比；

构建脚本改进（scripts/）；

子库同步脚本；

文档补充（算法背景、选型建议）。

提交前请先开 Issue 讨论一下方案。

参考与上游

Microsoft SEAL → external/SEAL-repo（上游：https://github.com/microsoft/SEAL）

TFHE → external/tfhe-repo（上游：https://github.com/tfhe/tfhe）

OpenFHE → external/openfhe-repo（上游：https://github.com/openfheorg/openfhe-development）

HElib → external/helib-repo（上游：https://github.com/homenc/HElib）

Paillier → external/pailliercryptolib（上游：https://github.com/intel/pailliercryptolib）

GM → external/libgm（上游：https://github.com/prvnkumar/libgm）

Crypto++ → external/cryptopp（上游：https://github.com/weidai11/cryptopp）

BFV-Python → external/bfv-py（上游：https://github.com/yuriko627/bfv-py）
