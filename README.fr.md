# Mineur Bitcoin en Python (RPC)

Ce script est un mineur de Bitcoin basé sur Python qui interagit avec un nœud Bitcoin via RPC (Remote Procedure Call). Il est conçu à des fins éducatives pour démontrer le processus de minage de Bitcoin, y compris la récupération de modèles de blocs, la construction de transactions coinbase, le calcul des racines de Merkle et l'exécution du processus de hachage (preuve de travail).

**Avertissement :** Ce mineur est destiné à des fins éducatives et de test (par exemple, sur RegTest ou Testnet). En raison de la difficulté extrêmement élevée du réseau Bitcoin, il n'est pas possible de miner du Bitcoin de manière rentable avec ce script sur le mainnet.

## Fonctionnalités

*   Se connecte à un nœud Bitcoin en utilisant RPC.
*   Récupère les modèles de blocs réels du nœud.
*   Construit des transactions coinbase valides.
*   Calcule les racines de Merkle pour les transactions d'un bloc.
*   Effectue un hachage SHA256 pour trouver un nonce valide (Preuve de Travail).
*   Soumet les blocs valides au nœud Bitcoin.
*   Mode de minage continu : Récupère automatiquement les nouveaux modèles et continue le minage après l'épuisement d'une plage de nonces ou la découverte d'un bloc.
*   Plage de nonces et paramètres de connexion RPC configurables.
*   Envoie périodiquement des blocs de "vérification" au nœud pour vérifier la connectivité et la construction des blocs (même s'il ne s'agit pas d'une solution PoW valide).

## Prérequis

*   Python 3.6 ou supérieur.
*   Un nœud Bitcoin en cours d'exécution (par exemple, Bitcoin Core) accessible via RPC.
    *   Assurez-vous que votre nœud Bitcoin est entièrement synchronisé si vous vous connectez à Testnet ou Mainnet. Pour RegTest, ce n'est pas un problème.
*   La bibliothèque Python `requests`.

## Configuration et Installation

1.  **Clonez le dépôt ou téléchargez les fichiers.**
    ```bash
    # Si vous avez git d'installé
    # git clone <url_du_depot>
    # cd <repertoire_du_depot>
    ```

2.  **Installez les dépendances :**
    Naviguez vers le répertoire contenant le fichier `requirements.txt` et exécutez :
    ```bash
    pip install -r requirements.txt
    ```

3.  **Configurez le RPC du nœud Bitcoin :**
    Vous devez configurer votre nœud Bitcoin pour autoriser les connexions RPC. Cela implique généralement de configurer `rpcuser` et `rpcpassword` dans votre fichier `bitcoin.conf`. Par exemple :
    ```
    rpcuser=votre_utilisateur_rpc
    rpcpassword=votre_mot_de_passe_rpc_robuste
    server=1
    txindex=1 # Recommandé pour certains appels RPC, bien que pas strictement nécessaire pour les fonctions principales de ce script.
    ```
    N'oubliez pas de redémarrer votre nœud Bitcoin après avoir modifié `bitcoin.conf`.

4.  **Configurez le script du mineur :**
    Ouvrez le fichier `bitcoin_miner.py` et modifiez les variables de configuration RPC suivantes en haut du script :

    ```python
    # === Configuración ===
    RPC_USER = "usuario"  # Remplacez par le nom d'utilisateur RPC de votre nœud Bitcoin
    RPC_PASSWORD = "constraseña"  # Remplacez par le mot de passe RPC de votre nœud Bitcoin
    RPC_PORT = 8332  # Par défaut pour Mainnet. Testnet: 18332, Regtest: 18443
    RPC_URL = f"http://{RPC_USER}:{RPC_PASSWORD}@127.0.0.1:{RPC_PORT}/"
    ```
    -   `RPC_USER`: Le nom d'utilisateur RPC que vous avez défini dans `bitcoin.conf`.
    -   `RPC_PASSWORD`: Le mot de passe RPC que vous avez défini dans `bitcoin.conf`.
    -   `RPC_PORT`: Le port RPC de votre nœud Bitcoin.
        *   Mainnet: `8332`
        *   Testnet: `18332`
        *   Regtest: `18443`
    -   L'`RPC_URL` est construite automatiquement à partir de ces valeurs. Assurez-vous que `127.0.0.1` est correct si votre nœud s'exécute sur un hôte différent.

    Vous pouvez également ajuster la variable `ENVIO_CADA_NONCES` :
    ```python
    # Configuración de minado
    ENVIO_CADA_NONCES = 1000000  # Enviar bloque de comprobación cada 1 millón de nonces
    ```
    Ceci contrôle la fréquence à laquelle un bloc de "vérification" est soumis au nœud pendant le minage.

## Comment Exécuter

1.  Assurez-vous que votre nœud Bitcoin est en cours d'exécution et que vous avez configuré `bitcoin_miner.py` avec les informations d'identification RPC et le port corrects.

2.  Ouvrez votre terminal ou invite de commandes, naviguez vers le répertoire où se trouve `bitcoin_miner.py`.

3.  Exécutez le script en utilisant Python :
    ```bash
    python bitcoin_miner.py
    ```

4.  **Configuration Initiale :**
    Le script démarrera en "Mode Continu" et vous invitera à configurer la plage de nonces initiale :
    ```
    💡 MODO CONTINUO ACTIVADO
    🔄 El minero reiniciará automáticamente al completar cada rango
    📝 Configuración por defecto:
       • Nonce inicio: 0
       • Nonce fin: 4294967295
       • Rango total: 4,294,967,296 nonces

    ❓ ¿Usar configuración por defecto? (Enter=sí, 'n'=configurar manualmente):
    ```
    *   Appuyez sur `Entrée` pour utiliser la plage de nonces complète par défaut (0 à 2^32 - 1).
    *   Tapez `n` et appuyez sur `Entrée` pour spécifier un nonce de début et de fin personnalisé.

5.  Le mineur tentera alors de se connecter à votre nœud Bitcoin, de récupérer un modèle de bloc et de démarrer le processus de minage. Vous verrez des messages de sortie indiquant sa progression, son taux de hachage et tous les blocs trouvés или soumis.

6.  **Arrêter le Mineur :**
    Appuyez sur `Ctrl+C` pour interrompre le mineur. En mode continu, il vous demandera une confirmation pour arrêter :
    ```
    [⚠️] INTERRUPCIÓN DETECTADA EN CICLO #X
    🔄 El minero está en MODO CONTINUO

    ❓ ¿Realmente quieres DETENER el minado continuo? (escribe 'DETENER' para confirmar):
    ```
    Tapez `DETENER` et appuyez sur `Entrée` pour arrêter le script. Sinon, il reprendra le minage.

## Notes Importantes

*   **Usage Éducatif Uniquement :** Ce script est destiné à l'apprentissage du processus de minage de Bitcoin. Il n'est pas optimisé pour les performances et il est très peu probable qu'il trouve des blocs sur le mainnet Bitcoin ou un testnet public en raison de l'immense puissance de hachage mondiale.
*   **Nœud Bitcoin Requis :** Vous DEVEZ disposer d'un nœud Bitcoin en cours d'exécution (comme Bitcoin Core) accessible via RPC. Ce script ne fonctionne pas de manière autonome.
*   **Regtest Recommandé :** Pour les tests et l'expérimentation, il est fortement recommandé d'exécuter ce mineur sur un nœud Bitcoin en mode `regtest` (test de régression). En mode `regtest`, vous pouvez générer des blocs instantanément et créer un environnement de test privé.
*   **Sécurité RPC :** Soyez prudent avec l'accès RPC à votre nœud Bitcoin, surtout s'il détient des fonds réels. Assurez-vous que `rpcpassword` est robuste et envisagez des règles de pare-feu pour limiter l'accès au port RPC.
*   **Gestion des Erreurs :** Le script inclut une gestion basique des erreurs pour les appels RPC et les problèmes de connexion, mais il se peut qu'il ne couvre pas tous les scénarios possibles.

## Comment ça Marche (Bref Aperçu)

1.  **Obtenir le Modèle de Bloc (RPC `getblocktemplate`) :** Le mineur demande un modèle de bloc au nœud Bitcoin. Ce modèle contient des informations sur le bloc précédent, les transactions à inclure, la difficulté actuelle (`bits`) et d'autres données nécessaires.
2.  **Construire la Transaction Coinbase :** Une transaction spéciale "coinbase" est créée. Cette transaction inclut la récompense de minage et les éventuels frais de transaction. C'est la première transaction d'un nouveau bloc.
3.  **Calculer la Racine de Merkle :** Les hachages de toutes les transactions (coinbase + autres du modèle) sont organisés dans un arbre de Merkle, et la racine de cet arbre est calculée. La racine de Merkle résume efficacement toutes les transactions du bloc.
4.  **Construire l'En-tête du Bloc :** L'en-tête du bloc est assemblé en utilisant :
    *   Version
    *   Hachage du bloc précédent
    *   Racine de Merkle
    *   Horodatage (Timestamp)
    *   Bits (cible de difficulté)
    *   Nonce (c'est la valeur que le mineur itère)
5.  **Preuve de Travail (Hachage) :** Le mineur effectue à plusieurs reprises un double hachage SHA256 sur l'en-tête du bloc, en modifiant la valeur du `nonce` à chaque fois.
6.  **Vérifier par rapport à la Cible :** Le hachage résultant est converti en un entier et comparé à la "cible" de difficulté (dérivée de `bits`). Si le hachage est inférieur ou égal à la cible, un bloc valide est trouvé.
7.  **Soumettre le Bloc (RPC `submitblock`) :** Si un nonce valide est trouvé, le bloc complet (en-tête + transactions) est envoyé au nœud Bitcoin. Si le nœud l'accepte, le bloc est diffusé sur le réseau.

Ce script automatise ces étapes dans une boucle continue.
