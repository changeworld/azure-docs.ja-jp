---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 09/03/2020
ms.author: msmbaldwin
ms.openlocfilehash: 59359d37fe347c8568c7944f75accdbc04cddb93
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967126"
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

    ```bash
    export KEY_VAULT_NAME=<your-unique-keyvault-name>
    ```