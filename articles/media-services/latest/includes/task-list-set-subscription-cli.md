---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI, devx-track-azurecli
ms.openlocfilehash: 94df457cc93a059de3f9bb294bd1c568b095781c
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/18/2020
ms.locfileid: "94885305"
---
<!-- List and set subscriptions -->

1. [az account list](/cli/azure/account#az-account-list) コマンドを使用して、サブスクリプションの一覧を取得します。

    ```
    az account list --output table
    ```

2. サブスクリプション ID または切り替え先の名前を指定して、`az account set` を実行します。

    ```
    az account set --subscription "My Demos"
    ```
