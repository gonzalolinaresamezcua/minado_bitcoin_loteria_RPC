# Python 比特币矿工 (RPC)

该脚本是一个基于 Python 的比特币矿工，通过 RPC (远程过程调用) 与比特币节点进行交互。它专为教育目的而设计，用于演示比特币挖矿过程，包括获取区块模板、构建 coinbase 交易、计算默克尔树根以及执行哈希 (工作量证明) 过程。

**免责声明：** 此矿工仅用于教育和测试目的 (例如，在 RegTest 或 Testnet 上)。由于比特币网络的难度极高，使用此脚本在主网上进行盈利性挖矿是不可行的。

## 功能

*   使用 RPC 连接到比特币节点。
*   从节点获取真实的区块模板。
*   构建有效的 coinbase 交易。
*   计算区块中交易的默克尔树根。
*   执行 SHA256 哈希运算以找到有效的 nonce (工作量证明)。
*   将有效区块提交回比特币节点。
*   连续挖矿模式：在 nonce 范围用尽或找到区块后，自动获取新模板并继续挖矿。
*   可配置的 nonce 范围和 RPC 连接参数。
*   定期向节点发送“检查”区块，以验证连接性和区块构建 (即使不是有效的 PoW 解决方案)。

## 要求

*   Python 3.6 或更高版本。
*   一个正在运行的比特币节点 (例如，Bitcoin Core)，可通过 RPC 访问。
    *   如果连接到 Testnet 或 Mainnet，请确保您的比特币节点已完全同步。对于 RegTest，这不是问题。
*   `requests` Python 库。

## 设置与安装

1.  **克隆存储库或下载文件。**
    ```bash
    # 如果您安装了 git
    # git clone <repository_url>
    # cd <repository_directory>
    ```

2.  **安装依赖项：**
    导航到包含 `requirements.txt` 文件的目录并运行：
    ```bash
    pip install -r requirements.txt
    ```

3.  **配置比特币节点 RPC：**
    您需要配置您的比特币节点以允许 RPC 连接。这通常涉及在您的 `bitcoin.conf` 文件中设置 `rpcuser` 和 `rpcpassword`。例如：
    ```
    rpcuser=your_rpc_user
    rpcpassword=your_strong_rpc_password
    server=1
    txindex=1 # 建议用于某些 RPC 调用，尽管对于此脚本的核心功能而言并非绝对必要。
    ```
    更改 `bitcoin.conf`后，请记住重新启动您的比特币节点。

4.  **配置矿工脚本：**
    打开 `bitcoin_miner.py` 文件，并修改脚本顶部的以下 RPC 配置变量：

    ```python
    # === Configuración ===
    RPC_USER = "usuario"  # 替换为您的比特币节点的 RPC 用户名
    RPC_PASSWORD = "constraseña"  # 替换为您的比特币节点的 RPC 密码
    RPC_PORT = 8332  # 主网默认值。测试网 (Testnet): 18332, 回归测试 (Regtest): 18443
    RPC_URL = f"http://{RPC_USER}:{RPC_PASSWORD}@127.0.0.1:{RPC_PORT}/"
    ```
    -   `RPC_USER`: 您在 `bitcoin.conf` 中设置的 RPC 用户名。
    -   `RPC_PASSWORD`: 您在 `bitcoin.conf` 中设置的 RPC 密码。
    -   `RPC_PORT`: 您的比特币节点的 RPC 端口。
        *   主网 (Mainnet): `8332`
        *   测试网 (Testnet): `18332`
        *   回归测试 (Regtest): `18443`
    -   `RPC_URL` 是根据这些值自动构建的。如果您的节点在不同的主机上运行，请确保 `127.0.0.1` 是正确的。

    您还可以调整 `ENVIO_CADA_NONCES` 变量：
    ```python
    # Configuración de minado
    ENVIO_CADA_NONCES = 1000000  # Enviar bloque de comprobación cada 1 millón de nonces
    ```
    这控制了在挖矿期间向节点提交“检查”区块的频率。

## 如何运行

1.  确保您的比特币节点正在运行，并且您已使用正确的 RPC 凭据和端口配置了 `bitcoin_miner.py`。

2.  打开您的终端或命令提示符，导航到 `bitcoin_miner.py` 所在的目录。

3.  使用 Python 运行脚本：
    ```bash
    python bitcoin_miner.py
    ```

4.  **初始配置：**
    脚本将以“连续模式”启动，并提示您进行初始 nonce 范围配置：
    ```
    💡 MODO CONTINUO ACTIVADO
    🔄 El minero reiniciará automáticamente al completar cada rango
    📝 Configuración por defecto:
       • Nonce inicio: 0
       • Nonce fin: 4294967295
       • Rango total: 4,294,967,296 nonces

    ❓ ¿Usar configuración por defecto? (Enter=sí, 'n'=configurar manualmente):
    ```
    *   按 `Enter` 使用默认的完整 nonce 范围 (0 到 2^32 - 1)。
    *   键入 `n` 并按 `Enter` 指定自定义的起始和结束 nonce。

5.  然后，矿工将尝试连接到您的比特币节点，获取区块模板，并开始挖矿过程。您将看到指示其进度、哈希率以及找到或提交的任何区块的输出消息。

6.  **停止矿工：**
    按 `Ctrl+C` 中断矿工。在连续模式下，它会要求确认停止：
    ```
    [⚠️] INTERRUPCIÓN DETECTADA EN CICLO #X
    🔄 El minero está en MODO CONTINUO

    ❓ ¿Realmente quieres DETENER el minado continuo? (escribe 'DETENER' para confirmar):
    ```
    键入 `DETENER` 并按 `Enter` 停止脚本。否则，它将继续挖矿。

## 重要说明

*   **仅用于教育目的：** 此脚本用于了解比特币挖矿过程。它没有针对性能进行优化，并且由于全球巨大的哈希算力，极不可能在比特币主网或公共测试网上找到任何区块。
*   **需要比特币节点：** 您必须拥有一个正在运行的、可通过 RPC 访问的比特币节点 (如 Bitcoin Core)。此脚本无法独立工作。
*   **建议使用 Regtest：** 对于测试和实验，强烈建议在 `regtest` (回归测试) 模式下针对比特币节点运行此矿工。在 `regtest` 模式下，您可以立即生成区块并创建私有测试环境。
*   **RPC 安全性：** 请谨慎对待对比特币节点的 RPC 访问，尤其是在节点持有真实资金的情况下。确保 `rpcpassword` 安全，并考虑使用防火墙规则限制对 RPC 端口的访问。
*   **错误处理：** 该脚本包含针对 RPC 调用和连接问题的基本错误处理，但可能无法涵盖所有可能的情况。

## 工作原理 (简要概述)

1.  **获取区块模板 (RPC `getblocktemplate`)：** 矿工从比特币节点请求区块模板。该模板包含有关前一个区块、要包含的交易、当前难度 (`bits`) 和其他必要数据的信息。
2.  **构建 Coinbase 交易：** 创建一个特殊的 "coinbase" 交易。此交易包括挖矿奖励和任何交易费用。它是新区块中的第一笔交易。
3.  **计算默克尔树根：** 将所有交易 (coinbase + 模板中的其他交易) 的哈希值排列成一个默克尔树，并计算该树的根。默克尔树根有效地总结了区块中的所有交易。
4.  **构建区块头：** 使用以下内容组装区块头：
    *   版本 (Version)
    *   前一个区块哈希 (Previous block hash)
    *   默克尔树根 (Merkle root)
    *   时间戳 (Timestamp)
    *   位数 (Bits) (难度目标)
    *   Nonce (矿工迭代的值)
5.  **工作量证明 (哈希)：** 矿工重复对区块头执行双 SHA256 哈希运算，每次都更改 `nonce` 值。
6.  **与目标比较：** 将结果哈希转换为整数，并与“目标”难度 (从 `bits`派生) 进行比较。如果哈希小于或等于目标，则找到有效区块。
7.  **提交区块 (RPC `submitblock`)：** 如果找到有效的 nonce，则将完整的区块 (头部 + 交易) 发送到比特币节点。如果节点接受它，则该区块将广播到网络。

该脚本在一个连续的循环中自动执行这些步骤。
