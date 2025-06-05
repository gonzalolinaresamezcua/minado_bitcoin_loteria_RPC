# Python Bitcoin Miner (RPC)

---

**Translations:** [Español](README.es.md) | [中文 (Simplified Chinese)](README.zh.md) | [Deutsch](README.de.md) | [Français](README.fr.md) | [日本語 (Japanese)](README.ja.md) | [Italiano](README.it.md)

---

This script is a Python-based Bitcoin miner that interacts with a Bitcoin node via RPC (Remote Procedure Call). It's designed for educational purposes to demonstrate the process of Bitcoin mining, including fetching block templates, constructing coinbase transactions, calculating Merkle roots, and performing the hashing (proof-of-work) process.

**Disclaimer:** This miner is intended for educational and testing purposes (e.g., on RegTest or Testnet). Due to the extremely high difficulty of the Bitcoin network, it is not feasible to mine Bitcoin profitably with this script on the mainnet.

## Features

*   Connects to a Bitcoin node using RPC.
*   Fetches real block templates from the node.
*   Constructs valid coinbase transactions.
*   Calculates Merkle roots for the transactions in a block.
*   Performs SHA256 hashing to find a valid nonce (Proof-of-Work).
*   Submits valid blocks back to the Bitcoin node.
*   Continuous mining mode: Automatically fetches new templates and continues mining after a range of nonces is exhausted or a block is found.
*   Configurable nonce range and RPC connection parameters.
*   Sends periodic "check" blocks to the node to verify connectivity and block construction (even if not a valid PoW solution).

## Requirements

*   Python 3.6 or higher.
*   A running Bitcoin node (e.g., Bitcoin Core) accessible via RPC.
    *   Ensure your Bitcoin node is fully synced if connecting to Testnet or Mainnet. For RegTest, this is not an issue.
*   The `requests` Python library.

## Setup and Installation

1.  **Clone the repository or download the files.**
    ```bash
    # If you have git installed
    # git clone <repository_url>
    # cd <repository_directory>
    ```

2.  **Install dependencies:**
    Navigate to the directory containing the `requirements.txt` file and run:
    ```bash
    pip install -r requirements.txt
    ```

3.  **Configure Bitcoin Node RPC:**
    You need to configure your Bitcoin node to allow RPC connections. This usually involves setting up `rpcuser` and `rpcpassword` in your `bitcoin.conf` file. For example:
    ```
    rpcuser=your_rpc_user
    rpcpassword=your_strong_rpc_password
    server=1
    txindex=1 # Recommended for some RPC calls, though not strictly necessary for this script's core functions.
    ```
    Remember to restart your Bitcoin node after changing `bitcoin.conf`.

4.  **Configure Miner Script:**
    Open the `bitcoin_miner.py` file and modify the following RPC configuration variables at the top of the script:

    ```python
    # === Configuración ===
    RPC_USER = "usuario"  # Replace with your Bitcoin node's RPC username
    RPC_PASSWORD = "constraseña"  # Replace with your Bitcoin node's RPC password
    RPC_PORT = 8332  # Default for Mainnet. Testnet: 18332, Regtest: 18443
    RPC_URL = f"http://{RPC_USER}:{RPC_PASSWORD}@127.0.0.1:{RPC_PORT}/"
    ```
    -   `RPC_USER`: The RPC username you set in `bitcoin.conf`.
    -   `RPC_PASSWORD`: The RPC password you set in `bitcoin.conf`.
    -   `RPC_PORT`: The RPC port for your Bitcoin node.
        *   Mainnet: `8332`
        *   Testnet: `18332`
        *   Regtest: `18443`
    -   The `RPC_URL` is constructed automatically from these values. Ensure `127.0.0.1` is correct if your node is running on a different host.

    You can also adjust the `ENVIO_CADA_NONCES` variable:
    ```python
    # Configuración de minado
    ENVIO_CADA_NONCES = 1000000  # Enviar bloque de comprobación cada 1 millón de nonces
    ```
    This controls how often a "check" block is submitted to the node during mining.

## How to Run

1.  Ensure your Bitcoin node is running and you have configured `bitcoin_miner.py` with the correct RPC credentials and port.

2.  Open your terminal or command prompt, navigate to the directory where `bitcoin_miner.py` is located.

3.  Run the script using Python:
    ```bash
    python bitcoin_miner.py
    ```

4.  **Initial Configuration:**
    The script will start in "Continuous Mode" and prompt you for initial nonce range configuration:
    ```
    💡 MODO CONTINUO ACTIVADO
    🔄 El minero reiniciará automáticamente al completar cada rango
    📝 Configuración por defecto:
       • Nonce inicio: 0
       • Nonce fin: 4294967295
       • Rango total: 4,294,967,296 nonces

    ❓ ¿Usar configuración por defecto? (Enter=sí, 'n'=configurar manualmente):
    ```
    *   Press `Enter` to use the default full nonce range (0 to 2^32 - 1).
    *   Type `n` and press `Enter` to specify a custom start and end nonce.

5.  The miner will then attempt to connect to your Bitcoin node, fetch a block template, and start the mining process. You will see output messages indicating its progress, hash rate, and any blocks found or submitted.

6.  **Stopping the Miner:**
    Press `Ctrl+C` to interrupt the miner. In continuous mode, it will ask for confirmation to stop:
    ```
    [⚠️] INTERRUPCIÓN DETECTADA EN CICLO #X
    🔄 El minero está en MODO CONTINUO

    ❓ ¿Realmente quieres DETENER el minado continuo? (escribe 'DETENER' para confirmar):
    ```
    Type `DETENER` and press `Enter` to stop the script. Otherwise, it will resume mining.

## Important Notes

*   **Educational Use Only:** This script is for learning about the Bitcoin mining process. It is not optimized for performance and is highly unlikely to find any blocks on the Bitcoin mainnet or a public testnet due to the immense global hashing power.
*   **Bitcoin Node Required:** You MUST have a running Bitcoin node (like Bitcoin Core) that is accessible via RPC. This script does not work standalone.
*   **Regtest Recommended:** For testing and experimentation, it is highly recommended to run this miner against a Bitcoin node in `regtest` (regression test) mode. In `regtest` mode, you can generate blocks instantly and create a private testing environment.
*   **RPC Security:** Be cautious with RPC access to your Bitcoin node, especially if it holds real funds. Ensure `rpcpassword` is strong and consider firewall rules to limit access to the RPC port.
*   **Error Handling:** The script includes basic error handling for RPC calls and connection issues, but it may not cover all possible scenarios.

## How it Works (Brief Overview)

1.  **Get Block Template (`getblocktemplate` RPC):** The miner requests a block template from the Bitcoin node. This template contains information about the previous block, transactions to include, the current difficulty (`bits`), and other necessary data.
2.  **Build Coinbase Transaction:** A special "coinbase" transaction is created. This transaction includes the mining reward and any transaction fees. It's the first transaction in a new block.
3.  **Calculate Merkle Root:** The hashes of all transactions (coinbase + others from the template) are arranged in a Merkle tree, and the root of this tree is calculated. The Merkle root efficiently summarizes all transactions in the block.
4.  **Construct Block Header:** The block header is assembled using:
    *   Version
    *   Previous block hash
    *   Merkle root
    *   Timestamp
    *   Bits (difficulty target)
    *   Nonce (this is the value the miner iterates)
5.  **Proof-of-Work (Hashing):** The miner repeatedly performs a double SHA256 hash on the block header, changing the `nonce` value each time.
6.  **Check Against Target:** The resulting hash is converted to an integer and compared against the "target" difficulty (derived from `bits`). If the hash is less than or equal to the target, a valid block is found.
7.  **Submit Block (`submitblock` RPC):** If a valid nonce is found, the complete block (header + transactions) is sent to the Bitcoin node. If the node accepts it, the block is broadcast to the network.

This script automates these steps in a continuous loop.
