# Minero de Bitcoin en Python (RPC)

Este script es un minero de Bitcoin basado en Python que interactúa con un nodo de Bitcoin a través de RPC (Llamada a Procedimiento Remoto). Está diseñado con fines educativos para demostrar el proceso de minería de Bitcoin, incluyendo la obtención de plantillas de bloques, la construcción de transacciones coinbase, el cálculo de raíces de Merkle y la realización del proceso de hashing (prueba de trabajo).

**Descargo de responsabilidad:** Este minero está destinado a fines educativos y de prueba (por ejemplo, en RegTest o Testnet). Debido a la dificultad extremadamente alta de la red Bitcoin, no es factible minar Bitcoin de manera rentable con este script en la red principal (mainnet).

## Características

*   Se conecta a un nodo de Bitcoin usando RPC.
*   Obtiene plantillas de bloques reales del nodo.
*   Construye transacciones coinbase válidas.
*   Calcula las raíces de Merkle para las transacciones en un bloque.
*   Realiza hashing SHA256 para encontrar un nonce válido (Prueba de Trabajo).
*   Envía bloques válidos de vuelta al nodo de Bitcoin.
*   Modo de minería continua: Obtiene automáticamente nuevas plantillas y continúa minando después de que se agota un rango de nonces o se encuentra un bloque.
*   Rango de nonce configurable y parámetros de conexión RPC.
*   Envía bloques de "verificación" periódicos al nodo para verificar la conectividad y la construcción de bloques (incluso si no es una solución de PoW válida).

## Requisitos

*   Python 3.6 o superior.
*   Un nodo de Bitcoin en ejecución (por ejemplo, Bitcoin Core) accesible a través de RPC.
    *   Asegúrate de que tu nodo de Bitcoin esté completamente sincronizado si te conectas a Testnet o Mainnet. Para RegTest, esto no es un problema.
*   La biblioteca `requests` de Python.

## Configuración e Instalación

1.  **Clona el repositorio o descarga los archivos.**
    ```bash
    # Si tienes git instalado
    # git clone <url_del_repositorio>
    # cd <directorio_del_repositorio>
    ```

2.  **Instala las dependencias:**
    Navega al directorio que contiene el archivo `requirements.txt` y ejecuta:
    ```bash
    pip install -r requirements.txt
    ```

3.  **Configura el RPC del Nodo Bitcoin:**
    Necesitas configurar tu nodo de Bitcoin para permitir conexiones RPC. Esto generalmente implica configurar `rpcuser` y `rpcpassword` en tu archivo `bitcoin.conf`. Por ejemplo:
    ```
    rpcuser=tu_usuario_rpc
    rpcpassword=tu_contraseña_rpc_segura
    server=1
    txindex=1 # Recomendado para algunas llamadas RPC, aunque no estrictamente necesario para las funciones principales de este script.
    ```
    Recuerda reiniciar tu nodo de Bitcoin después de cambiar `bitcoin.conf`.

4.  **Configura el Script del Minero:**
    Abre el archivo `bitcoin_miner.py` y modifica las siguientes variables de configuración RPC en la parte superior del script:

    ```python
    # === Configuración ===
    RPC_USER = "usuario"  # Reemplaza con el nombre de usuario RPC de tu nodo Bitcoin
    RPC_PASSWORD = "constraseña"  # Reemplaza con la contraseña RPC de tu nodo Bitcoin
    RPC_PORT = 8332  # Predeterminado para Mainnet. Testnet: 18332, Regtest: 18443
    RPC_URL = f"http://{RPC_USER}:{RPC_PASSWORD}@127.0.0.1:{RPC_PORT}/"
    ```
    -   `RPC_USER`: El nombre de usuario RPC que configuraste en `bitcoin.conf`.
    -   `RPC_PASSWORD`: La contraseña RPC que configuraste en `bitcoin.conf`.
    -   `RPC_PORT`: El puerto RPC para tu nodo de Bitcoin.
        *   Mainnet: `8332`
        *   Testnet: `18332`
        *   Regtest: `18443`
    -   La `RPC_URL` se construye automáticamente a partir de estos valores. Asegúrate de que `127.0.0.1` sea correcto si tu nodo se está ejecutando en un host diferente.

    También puedes ajustar la variable `ENVIO_CADA_NONCES`:
    ```python
    # Configuración de minado
    ENVIO_CADA_NONCES = 1000000  # Enviar bloque de comprobación cada 1 millón de nonces
    ```
    Esto controla la frecuencia con la que se envía un bloque de "verificación" al nodo durante la minería.

## Cómo Ejecutarlo

1.  Asegúrate de que tu nodo de Bitcoin esté en ejecución y que hayas configurado `bitcoin_miner.py` con las credenciales RPC y el puerto correctos.

2.  Abre tu terminal o símbolo del sistema, navega al directorio donde se encuentra `bitcoin_miner.py`.

3.  Ejecuta el script usando Python:
    ```bash
    python bitcoin_miner.py
    ```

4.  **Configuración Inicial:**
    El script comenzará en "Modo Continuo" y te pedirá la configuración inicial del rango de nonce:
    ```
    💡 MODO CONTINUO ACTIVADO
    🔄 El minero reiniciará automáticamente al completar cada rango
    📝 Configuración por defecto:
       • Nonce inicio: 0
       • Nonce fin: 4294967295
       • Rango total: 4,294,967,296 nonces

    ❓ ¿Usar configuración por defecto? (Enter=sí, 'n'=configurar manualmente):
    ```
    *   Presiona `Enter` para usar el rango de nonce completo por defecto (0 a 2^32 - 1).
    *   Escribe `n` y presiona `Enter` para especificar un nonce de inicio y fin personalizado.

5.  El minero intentará entonces conectarse a tu nodo de Bitcoin, obtener una plantilla de bloque y comenzar el proceso de minería. Verás mensajes de salida que indican su progreso, tasa de hash y cualquier bloque encontrado o enviado.

6.  **Detener el Minero:**
    Presiona `Ctrl+C` para interrumpir el minero. En modo continuo, te pedirá confirmación para detenerse:
    ```
    [⚠️] INTERRUPCIÓN DETECTADA EN CICLO #X
    🔄 El minero está en MODO CONTINUO

    ❓ ¿Realmente quieres DETENER el minado continuo? (escribe 'DETENER' para confirmar):
    ```
    Escribe `DETENER` y presiona `Enter` para detener el script. De lo contrario, reanudará la minería.

## Notas Importantes

*   **Solo para Uso Educativo:** Este script es para aprender sobre el proceso de minería de Bitcoin. No está optimizado para el rendimiento y es altamente improbable que encuentre bloques en la red principal de Bitcoin o en una testnet pública debido al inmenso poder de hashing global.
*   **Se Requiere un Nodo de Bitcoin:** DEBES tener un nodo de Bitcoin en ejecución (como Bitcoin Core) que sea accesible a través de RPC. Este script no funciona de forma independiente.
*   **Regtest Recomendado:** Para pruebas y experimentación, se recomienda encarecidamente ejecutar este minero contra un nodo de Bitcoin en modo `regtest` (prueba de regresión). En modo `regtest`, puedes generar bloques instantáneamente y crear un entorno de prueba privado.
*   **Seguridad RPC:** Ten cuidado con el acceso RPC a tu nodo de Bitcoin, especialmente si contiene fondos reales. Asegúrate de que `rpcpassword` sea segura y considera reglas de firewall para limitar el acceso al puerto RPC.
*   **Manejo de Errores:** El script incluye manejo básico de errores para llamadas RPC y problemas de conexión, pero puede que no cubra todos los escenarios posibles.

## Cómo Funciona (Resumen Breve)

1.  **Obtener Plantilla de Bloque (RPC `getblocktemplate`):** El minero solicita una plantilla de bloque al nodo de Bitcoin. Esta plantilla contiene información sobre el bloque anterior, transacciones a incluir, la dificultad actual (`bits`) y otros datos necesarios.
2.  **Construir Transacción Coinbase:** Se crea una transacción especial "coinbase". Esta transacción incluye la recompensa por minería y cualquier tarifa de transacción. Es la primera transacción en un nuevo bloque.
3.  **Calcular Raíz de Merkle:** Los hashes de todas las transacciones (coinbase + otras de la plantilla) se organizan en un árbol de Merkle, y se calcula la raíz de este árbol. La raíz de Merkle resume eficientemente todas las transacciones en el bloque.
4.  **Construir Encabezado del Bloque:** El encabezado del bloque se ensambla usando:
    *   Versión
    *   Hash del bloque anterior
    *   Raíz de Merkle
    *   Timestamp (marca de tiempo)
    *   Bits (objetivo de dificultad)
    *   Nonce (este es el valor que itera el minero)
5.  **Prueba de Trabajo (Hashing):** El minero realiza repetidamente un doble hash SHA256 en el encabezado del bloque, cambiando el valor del `nonce` cada vez.
6.  **Verificar Contra el Objetivo:** El hash resultante se convierte a un entero y se compara con el "objetivo" de dificultad (derivado de `bits`). Si el hash es menor o igual que el objetivo, se encuentra un bloque válido.
7.  **Enviar Bloque (RPC `submitblock`):** Si se encuentra un nonce válido, el bloque completo (encabezado + transacciones) se envía al nodo de Bitcoin. Si el nodo lo acepta, el bloque se transmite a la red.

Este script automatiza estos pasos en un bucle continuo.
