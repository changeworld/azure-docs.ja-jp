---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/09/2021
ms.author: alkohli
ms.openlocfilehash: 1298673c475a0308f1db27641aae93837e6b5df3
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305560"
---
次の手順に従い、クライアントからデバイスにアクセスできることを確認します。

クライアントがローカル Azure Resource Manager に接続できることを確認します。 

1. ローカル デバイス API を呼び出して認証します。

    ```powershell
    login-AzureRMAccount -EnvironmentName <Environment Name> -TenantId c0257de7-538f-415c-993a-1b87a031879d  
    ```

1. ユーザー名 (`EdgeArmUser`) とパスワードを指定して Azure Resource Manager 経由で接続します。 パスワードを覚えていない場合は、[Azure Resource Manager のパスワードを再設定](../articles/databox-online/azure-stack-edge-gpu-set-azure-resource-manager-password.md)し、そのパスワードを使用してサインインします。