---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 09/03/2020
ms.author: msmbaldwin
ms.openlocfilehash: 5e912b76c2ef68aa01dae57d1b42abc386a8e67b
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2020
ms.locfileid: "89482135"
---
1. リソース グループを作成するには、`az group create` コマンドを使用します。

    ```azurecli
    az group create --name KeyVault-PythonQS-rg --location eastus
    ```

    必要に応じて、"eastus" をお近くの場所に変更することができます。

1. `az keyvault create` を使用して、キー コンテナーを作成します。

    ```azurecli
    az keyvault create --name <your-unique-keyvault-name> --resource-group KeyVault-PythonQS-rg
    ```

    `<your-unique-keyvault-name>` を Azure 全体で一意の名前に置き換えます。 通常、個人名または会社名は、その他の数字や識別子と併せて使用します。 

1. コードに Key Vault の名前を指定する環境変数を作成します。

    # <a name="cmd"></a>[cmd](#tab/cmd)

    ```cmd
    set KEY_VAULT_NAME=<your-unique-keyvault-name>
    ```

    # <a name="bash"></a>[bash](#tab/bash)

    ```bash
    export KEY_VAULT_NAME=<your-unique-keyvault-name>
    ```

    ---
