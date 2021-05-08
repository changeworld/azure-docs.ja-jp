---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: ffd053b98a54d3e23eec62427f5c3d82df58954d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107799848"
---
<!-- List and set subscriptions -->

1. [az account list](/cli/azure/account#az_account_list) コマンドを使用して、サブスクリプションの一覧を取得します。

    ```
    az account list --output table
    ```

2. サブスクリプション ID または切り替え先の名前を指定して、`az account set` を実行します。

    ```
    az account set --subscription "My Demos"
    ```
