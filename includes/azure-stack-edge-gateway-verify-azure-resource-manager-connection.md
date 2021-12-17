---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 06/30/2021
ms.author: alkohli
ms.openlocfilehash: e0770eb27bfebc085d99af9fdc9749676b699b70
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/30/2021
ms.locfileid: "113106598"
---
次の手順に従い、クライアントからデバイスにアクセスできることを確認します。

クライアントがローカル Azure Resource Manager に接続できることを確認します。 

1. ローカル デバイス API を呼び出して認証します。

    ### <a name="az"></a>[Az](#tab/az)

    ```powershell
    login-AzAccount -EnvironmentName <Environment Name> -TenantId c0257de7-538f-415c-993a-1b87a031879d  
    ```

    ### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

    ```powershell
    login-AzureRMAccount -EnvironmentName <Environment Name> -TenantId c0257de7-538f-415c-993a-1b87a031879d  
    ```

1. ユーザー名 (`EdgeArmUser`) とパスワードを指定して Azure Resource Manager 経由で接続します。 パスワードを覚えていない場合は、[Azure Resource Manager のパスワードを再設定](../articles/databox-online/azure-stack-edge-gpu-set-azure-resource-manager-password.md)し、そのパスワードを使用してサインインします。