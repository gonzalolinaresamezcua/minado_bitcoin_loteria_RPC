# Miner di Bitcoin in Python (RPC)

Questo script è un miner di Bitcoin basato su Python che interagisce con un nodo Bitcoin tramite RPC (Remote Procedure Call). È progettato per scopi didattici per dimostrare il processo di mining di Bitcoin, incluso il recupero di modelli di blocco, la costruzione di transazioni coinbase, il calcolo delle radici di Merkle e l'esecuzione del processo di hashing (proof-of-work).

**Disclaimer:** Questo miner è destinato a scopi didattici e di test (ad esempio, su RegTest o Testnet). A causa dell'estrema difficoltà della rete Bitcoin, non è fattibile estrarre Bitcoin in modo redditizio con questo script sulla mainnet.

## Caratteristiche

*   Si connette a un nodo Bitcoin utilizzando RPC.
*   Recupera modelli di blocchi reali dal nodo.
*   Costruisce transazioni coinbase valide.
*   Calcola le radici di Merkle per le transazioni in un blocco.
*   Esegue l'hashing SHA256 per trovare un nonce valido (Proof-of-Work).
*   Invia blocchi validi al nodo Bitcoin.
*   Modalità di mining continuo: recupera automaticamente nuovi modelli e continua il mining dopo l'esaurimento di un intervallo di nonce o il ritrovamento di un blocco.
*   Intervallo di nonce e parametri di connessione RPC configurabili.
*   Invia periodicamente blocchi di "controllo" al nodo per verificare la connettività e la costruzione dei blocchi (anche se non si tratta di una soluzione PoW valida).

## Requisiti

*   Python 3.6 o versioni successive.
*   Un nodo Bitcoin in esecuzione (ad esempio, Bitcoin Core) accessibile tramite RPC.
    *   Assicurati che il tuo nodo Bitcoin sia completamente sincronizzato se ti connetti a Testnet o Mainnet. Per RegTest, questo non è un problema.
*   La libreria Python `requests`.

## Configurazione e Installazione

1.  **Clona il repository o scarica i file.**
    ```bash
    # Se hai git installato
    # git clone <url_repository>
    # cd <directory_repository>
    ```

2.  **Installa le dipendenze:**
    Naviga nella directory contenente il file `requirements.txt` ed esegui:
    ```bash
    pip install -r requirements.txt
    ```

3.  **Configura l'RPC del nodo Bitcoin:**
    Devi configurare il tuo nodo Bitcoin per consentire connessioni RPC. Questo di solito comporta l'impostazione di `rpcuser` e `rpcpassword` nel tuo file `bitcoin.conf`. Ad esempio:
    ```
    rpcuser=il_tuo_utente_rpc
    rpcpassword=la_tua_password_rpc_robusta
    server=1
    txindex=1 # Consigliato per alcune chiamate RPC, sebbene non strettamente necessario per le funzioni principali di questo script.
    ```
    Ricorda di riavviare il tuo nodo Bitcoin dopo aver modificato `bitcoin.conf`.

4.  **Configura lo script del miner:**
    Apri il file `bitcoin_miner.py` e modifica le seguenti variabili di configurazione RPC all'inizio dello script:

    ```python
    # === Configuración ===
    RPC_USER = "usuario"  # Sostituisci con il nome utente RPC del tuo nodo Bitcoin
    RPC_PASSWORD = "constraseña"  # Sostituisci con la password RPC del tuo nodo Bitcoin
    RPC_PORT = 8332  # Predefinito per Mainnet. Testnet: 18332, Regtest: 18443
    RPC_URL = f"http://{RPC_USER}:{RPC_PASSWORD}@127.0.0.1:{RPC_PORT}/"
    ```
    -   `RPC_USER`: Il nome utente RPC impostato in `bitcoin.conf`.
    -   `RPC_PASSWORD`: La password RPC impostata in `bitcoin.conf`.
    -   `RPC_PORT`: La porta RPC per il tuo nodo Bitcoin.
        *   Mainnet: `8332`
        *   Testnet: `18332`
        *   Regtest: `18443`
    -   L'`RPC_URL` viene costruito automaticamente da questi valori. Assicurati che `127.0.0.1` sia corretto se il tuo nodo è in esecuzione su un host diverso.

    Puoi anche regolare la variabile `ENVIO_CADA_NONCES`:
    ```python
    # Configuración de minado
    ENVIO_CADA_NONCES = 1000000  # Enviar bloque de comprobación cada 1 millón de nonces
    ```
    Questo controlla la frequenza con cui un blocco di "controllo" viene inviato al nodo durante il mining.

## Come Eseguire

1.  Assicurati che il tuo nodo Bitcoin sia in esecuzione e di aver configurato `bitcoin_miner.py` con le credenziali RPC e la porta corrette.

2.  Apri il terminale o il prompt dei comandi, naviga nella directory in cui si trova `bitcoin_miner.py`.

3.  Esegui lo script usando Python:
    ```bash
    python bitcoin_miner.py
    ```

4.  **Configurazione Iniziale:**
    Lo script si avvierà in "Modalità Continua" e ti chiederà la configurazione iniziale dell'intervallo di nonce:
    ```
    💡 MODO CONTINUO ACTIVADO
    🔄 El minero reiniciará automáticamente al completar cada rango
    📝 Configuración por defecto:
       • Nonce inicio: 0
       • Nonce fin: 4294967295
       • Rango total: 4,294,967,296 nonces

    ❓ ¿Usar configuración por defecto? (Enter=sí, 'n'=configurar manualmente):
    ```
    *   Premi `Invio` per utilizzare l'intervallo di nonce completo predefinito (da 0 a 2^32 - 1).
    *   Digita `n` e premi `Invio` per specificare un nonce di inizio e fine personalizzato.

5.  Il miner tenterà quindi di connettersi al tuo nodo Bitcoin, recuperare un modello di blocco e avviare il processo di mining. Vedrai messaggi di output che indicano il suo avanzamento, l'hash rate e gli eventuali blocchi trovati o inviati.

6.  **Arrestare il Miner:**
    Premi `Ctrl+C` per interrompere il miner. In modalità continua, ti chiederà conferma per l'arresto:
    ```
    [⚠️] INTERRUPCIÓN DETECTADA EN CICLO #X
    🔄 El minero está en MODO CONTINUO

    ❓ ¿Realmente quieres DETENER el minado continuo? (escribe 'DETENER' para confirmar):
    ```
    Digita `DETENER` e premi `Invio` per arrestare lo script. Altrimenti, riprenderà il mining.

## Note Importanti

*   **Solo per Uso Didattico:** Questo script serve per apprendere il processo di mining di Bitcoin. Non è ottimizzato per le prestazioni ed è altamente improbabile che trovi blocchi sulla mainnet di Bitcoin o su una testnet pubblica a causa dell'immensa potenza di hashing globale.
*   **Nodo Bitcoin Richiesto:** DEVI avere un nodo Bitcoin in esecuzione (come Bitcoin Core) accessibile tramite RPC. Questo script non funziona autonomamente.
*   **Regtest Consigliato:** Per test e sperimentazione, si consiglia vivamente di eseguire questo miner su un nodo Bitcoin in modalità `regtest` (regression test). In modalità `regtest`, puoi generare blocchi istantaneamente e creare un ambiente di test privato.
*   **Sicurezza RPC:** Fai attenzione all'accesso RPC al tuo nodo Bitcoin, specialmente se detiene fondi reali. Assicurati che `rpcpassword` sia robusta e considera regole firewall per limitare l'accesso alla porta RPC.
*   **Gestione degli Errori:** Lo script include una gestione di base degli errori per le chiamate RPC e i problemi di connessione, ma potrebbe non coprire tutti i possibili scenari.

## Come Funziona (Breve Panoramica)

1.  **Ottieni Modello di Blocco (RPC `getblocktemplate`):** Il miner richiede un modello di blocco al nodo Bitcoin. Questo modello contiene informazioni sul blocco precedente, le transazioni da includere, la difficoltà corrente (`bits`) e altri dati necessari.
2.  **Costruisci Transazione Coinbase:** Viene creata una transazione speciale "coinbase". Questa transazione include la ricompensa per il mining e le eventuali commissioni di transazione. È la prima transazione in un nuovo blocco.
3.  **Calcola Radice di Merkle:** Gli hash di tutte le transazioni (coinbase + altre dal modello) sono disposti in un albero di Merkle e viene calcolata la radice di questo albero. La radice di Merkle riassume in modo efficiente tutte le transazioni nel blocco.
4.  **Costruisci Intestazione del Blocco:** L'intestazione del blocco viene assemblata utilizzando:
    *   Versione
    *   Hash del blocco precedente
    *   Radice di Merkle
    *   Timestamp
    *   Bits (target di difficoltà)
    *   Nonce (questo è il valore che il miner itera)
5.  **Proof-of-Work (Hashing):** Il miner esegue ripetutamente un doppio hash SHA256 sull'intestazione del blocco, modificando ogni volta il valore del `nonce`.
6.  **Verifica Rispetto al Target:** L'hash risultante viene convertito in un intero e confrontato con il "target" di difficoltà (derivato da `bits`). Se l'hash è inferiore o uguale al target, viene trovato un blocco valido.
7.  **Invia Blocco (RPC `submitblock`):** Se viene trovato un nonce valido, il blocco completo (intestazione + transazioni) viene inviato al nodo Bitcoin. Se il nodo lo accetta, il blocco viene trasmesso alla rete.

Questo script automatizza questi passaggi in un ciclo continuo.
