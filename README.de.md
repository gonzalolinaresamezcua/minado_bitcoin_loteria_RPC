# Python Bitcoin Miner (RPC)

Dieses Skript ist ein Python-basierter Bitcoin-Miner, der über RPC (Remote Procedure Call) mit einem Bitcoin-Node interagiert. Es dient zu Bildungszwecken, um den Prozess des Bitcoin-Minings zu demonstrieren, einschließlich des Abrufens von Blockvorlagen, des Erstellens von Coinbase-Transaktionen, des Berechnens von Merkle-Roots und des Durchführens des Hashing-Prozesses (Proof-of-Work).

**Haftungsausschluss:** Dieser Miner ist für Bildungs- und Testzwecke gedacht (z. B. auf RegTest oder Testnet). Aufgrund der extrem hohen Schwierigkeit des Bitcoin-Netzwerks ist es nicht möglich, mit diesem Skript auf dem Mainnet profitabel Bitcoin zu minen.

## Funktionen

*   Verbindet sich über RPC mit einem Bitcoin-Node.
*   Ruft echte Blockvorlagen vom Node ab.
*   Erstellt gültige Coinbase-Transaktionen.
*   Berechnet Merkle-Roots für die Transaktionen in einem Block.
*   Führt SHA256-Hashing durch, um eine gültige Nonce (Proof-of-Work) zu finden.
*   Sendet gültige Blöcke an den Bitcoin-Node zurück.
*   Kontinuierlicher Mining-Modus: Ruft automatisch neue Vorlagen ab und setzt das Mining fort, nachdem ein Nonce-Bereich erschöpft ist oder ein Block gefunden wurde.
*   Konfigurierbarer Nonce-Bereich und RPC-Verbindungsparameter.
*   Sendet periodische "Check"-Blöcke an den Node, um die Konnektivität und Blockkonstruktion zu überprüfen (auch wenn keine gültige PoW-Lösung vorliegt).

## Anforderungen

*   Python 3.6 oder höher.
*   Ein laufender Bitcoin-Node (z. B. Bitcoin Core), der über RPC zugänglich ist.
    *   Stellen Sie sicher, dass Ihr Bitcoin-Node vollständig synchronisiert ist, wenn Sie sich mit Testnet oder Mainnet verbinden. Bei RegTest ist dies kein Problem.
*   Die Python-Bibliothek `requests`.

## Einrichtung und Installation

1.  **Klonen Sie das Repository oder laden Sie die Dateien herunter.**
    ```bash
    # Wenn Sie Git installiert haben
    # git clone <repository_url>
    # cd <repository_verzeichnis>
    ```

2.  **Abhängigkeiten installieren:**
    Navigieren Sie zu dem Verzeichnis, das die Datei `requirements.txt` enthält, und führen Sie Folgendes aus:
    ```bash
    pip install -r requirements.txt
    ```

3.  **Bitcoin-Node-RPC konfigurieren:**
    Sie müssen Ihren Bitcoin-Node so konfigurieren, dass RPC-Verbindungen zugelassen werden. Dies beinhaltet normalerweise das Einrichten von `rpcuser` und `rpcpassword` in Ihrer `bitcoin.conf`-Datei. Zum Beispiel:
    ```
    rpcuser=ihr_rpc_benutzer
    rpcpassword=ihr_starkes_rpc_passwort
    server=1
    txindex=1 # Empfohlen für einige RPC-Aufrufe, obwohl für die Kernfunktionen dieses Skripts nicht unbedingt erforderlich.
    ```
    Denken Sie daran, Ihren Bitcoin-Node nach dem Ändern von `bitcoin.conf` neu zu starten.

4.  **Miner-Skript konfigurieren:**
    Öffnen Sie die Datei `bitcoin_miner.py` und ändern Sie die folgenden RPC-Konfigurationsvariablen am Anfang des Skripts:

    ```python
    # === Configuración ===
    RPC_USER = "usuario"  # Ersetzen Sie dies durch den RPC-Benutzernamen Ihres Bitcoin-Nodes
    RPC_PASSWORD = "constraseña"  # Ersetzen Sie dies durch das RPC-Passwort Ihres Bitcoin-Nodes
    RPC_PORT = 8332  # Standard für Mainnet. Testnet: 18332, Regtest: 18443
    RPC_URL = f"http://{RPC_USER}:{RPC_PASSWORD}@127.0.0.1:{RPC_PORT}/"
    ```
    -   `RPC_USER`: Der RPC-Benutzername, den Sie in `bitcoin.conf` festgelegt haben.
    -   `RPC_PASSWORD`: Das RPC-Passwort, das Sie in `bitcoin.conf` festgelegt haben.
    -   `RPC_PORT`: Der RPC-Port für Ihren Bitcoin-Node.
        *   Mainnet: `8332`
        *   Testnet: `18332`
        *   Regtest: `18443`
    -   Die `RPC_URL` wird automatisch aus diesen Werten erstellt. Stellen Sie sicher, dass `127.0.0.1` korrekt ist, wenn Ihr Node auf einem anderen Host ausgeführt wird.

    Sie können auch die Variable `ENVIO_CADA_NONCES` anpassen:
    ```python
    # Configuración de minado
    ENVIO_CADA_NONCES = 1000000  # Enviar bloque de comprobación cada 1 millón de nonces
    ```
    Dies steuert, wie oft während des Minings ein "Check"-Block an den Node gesendet wird.

## Ausführung

1.  Stellen Sie sicher, dass Ihr Bitcoin-Node ausgeführt wird und Sie `bitcoin_miner.py` mit den korrekten RPC-Anmeldeinformationen und dem richtigen Port konfiguriert haben.

2.  Öffnen Sie Ihr Terminal oder Ihre Eingabeaufforderung und navigieren Sie zu dem Verzeichnis, in dem sich `bitcoin_miner.py` befindet.

3.  Führen Sie das Skript mit Python aus:
    ```bash
    python bitcoin_miner.py
    ```

4.  **Erstkonfiguration:**
    Das Skript startet im "Continuous Mode" und fordert Sie zur Konfiguration des anfänglichen Nonce-Bereichs auf:
    ```
    💡 MODO CONTINUO ACTIVADO
    🔄 El minero reiniciará automáticamente al completar cada rango
    📝 Configuración por defecto:
       • Nonce inicio: 0
       • Nonce fin: 4294967295
       • Rango total: 4,294,967,296 nonces

    ❓ ¿Usar configuración por defecto? (Enter=sí, 'n'=configurar manualmente):
    ```
    *   Drücken Sie `Enter`, um den standardmäßigen vollen Nonce-Bereich (0 bis 2^32 - 1) zu verwenden.
    *   Geben Sie `n` ein und drücken Sie `Enter`, um einen benutzerdefinierten Start- und End-Nonce anzugeben.

5.  Der Miner versucht dann, sich mit Ihrem Bitcoin-Node zu verbinden, eine Blockvorlage abzurufen und den Mining-Prozess zu starten. Sie sehen Ausgabemeldungen, die den Fortschritt, die Hash-Rate und alle gefundenen oder übermittelten Blöcke anzeigen.

6.  **Miner stoppen:**
    Drücken Sie `Strg+C`, um den Miner zu unterbrechen. Im kontinuierlichen Modus werden Sie um Bestätigung zum Stoppen gebeten:
    ```
    [⚠️] INTERRUPCIÓN DETECTADA EN CICLO #X
    🔄 El minero está en MODO CONTINUO

    ❓ ¿Realmente quieres DETENER el minado continuo? (escribe 'DETENER' para confirmar):
    ```
    Geben Sie `DETENER` ein und drücken Sie `Enter`, um das Skript zu stoppen. Andernfalls wird das Mining fortgesetzt.

## Wichtige Hinweise

*   **Nur für Bildungszwecke:** Dieses Skript dient zum Erlernen des Bitcoin-Mining-Prozesses. Es ist nicht für Leistung optimiert und es ist höchst unwahrscheinlich, dass aufgrund der immensen globalen Hashing-Leistung Blöcke im Bitcoin-Mainnet oder einem öffentlichen Testnet gefunden werden.
*   **Bitcoin-Node erforderlich:** Sie MÜSSEN einen laufenden Bitcoin-Node (wie Bitcoin Core) haben, der über RPC zugänglich ist. Dieses Skript funktioniert nicht eigenständig.
*   **Regtest empfohlen:** Für Tests und Experimente wird dringend empfohlen, diesen Miner gegen einen Bitcoin-Node im `regtest`-Modus (Regressionstest) auszuführen. Im `regtest`-Modus können Sie Blöcke sofort generieren und eine private Testumgebung erstellen.
*   **RPC-Sicherheit:** Seien Sie vorsichtig mit dem RPC-Zugriff auf Ihren Bitcoin-Node, insbesondere wenn er echte Gelder enthält. Stellen Sie sicher, dass `rpcpassword` stark ist, und erwägen Sie Firewall-Regeln, um den Zugriff auf den RPC-Port einzuschränken.
*   **Fehlerbehandlung:** Das Skript enthält eine grundlegende Fehlerbehandlung für RPC-Aufrufe und Verbindungsprobleme, deckt jedoch möglicherweise nicht alle möglichen Szenarien ab.

## Funktionsweise (Kurzübersicht)

1.  **Blockvorlage abrufen (`getblocktemplate` RPC):** Der Miner fordert eine Blockvorlage vom Bitcoin-Node an. Diese Vorlage enthält Informationen über den vorherigen Block, einzuschließende Transaktionen, die aktuelle Schwierigkeit (`bits`) und andere notwendige Daten.
2.  **Coinbase-Transaktion erstellen:** Eine spezielle "Coinbase"-Transaktion wird erstellt. Diese Transaktion enthält die Mining-Belohnung und alle Transaktionsgebühren. Es ist die erste Transaktion in einem neuen Block.
3.  **Merkle-Root berechnen:** Die Hashes aller Transaktionen (Coinbase + andere aus der Vorlage) werden in einem Merkle-Baum angeordnet, und die Wurzel dieses Baums wird berechnet. Die Merkle-Root fasst alle Transaktionen im Block effizient zusammen.
4.  **Block-Header erstellen:** Der Block-Header wird zusammengestellt aus:
    *   Version
    *   Hash des vorherigen Blocks
    *   Merkle-Root
    *   Zeitstempel
    *   Bits (Schwierigkeitsziel)
    *   Nonce (dies ist der Wert, den der Miner iteriert)
5.  **Proof-of-Work (Hashing):** Der Miner führt wiederholt einen doppelten SHA256-Hash auf den Block-Header aus und ändert dabei jedes Mal den `nonce`-Wert.
6.  **Mit Ziel vergleichen:** Der resultierende Hash wird in eine Ganzzahl umgewandelt und mit dem "Ziel"-Schwierigkeitsgrad (abgeleitet von `bits`) verglichen. Wenn der Hash kleiner oder gleich dem Ziel ist, wird ein gültiger Block gefunden.
7.  **Block übermitteln (`submitblock` RPC):** Wenn eine gültige Nonce gefunden wird, wird der vollständige Block (Header + Transaktionen) an den Bitcoin-Node gesendet. Wenn der Node ihn akzeptiert, wird der Block an das Netzwerk gesendet.

Dieses Skript automatisiert diese Schritte in einer Endlosschleife.
