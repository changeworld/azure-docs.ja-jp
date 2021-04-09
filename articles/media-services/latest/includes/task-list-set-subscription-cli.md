---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: aa76f7b85302651f6874747610c3355f0572a7ee
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
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
