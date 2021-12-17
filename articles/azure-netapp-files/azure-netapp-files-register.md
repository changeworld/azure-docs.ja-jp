---
title: Azure NetApp Files で使用するための NetApp リソース プロバイダーを登録する | Microsoft Docs
description: Azure NetApp Files 用の NetApp リソース プロバイダーを登録する方法を説明します。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/04/2021
ms.author: b-juche
ms.openlocfilehash: b8b70a06a6bda9757f9019a6f753af226235a985
ms.sourcegitcommit: f3f2ec7793ebeee19bd9ffc3004725fb33eb4b3f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/04/2021
ms.locfileid: "129407743"
---
# <a name="register-for-netapp-resource-provider"></a>NetApp リソース プロバイダーを登録する

Azure NetApp Files サービスを使用するには、NetApp リソース プロバイダーを登録する必要があります。

1. Azure portal で、右上隅にある Azure Cloud Shell アイコンをクリックします。

      ![Azure Cloud Shell アイコン](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. お客様の Azure アカウントにサブスクリプションが複数ある場合は、Azure NetApp Files に対して承認されているものを選択します。
    
    ```azurecli
    az account set --subscription <subscriptionId>
    ```

3. Azure Cloud Shell コンソールで次のコマンドを入力して、お客様のサブスクリプションが承認されていることを確認します。
    
    ```azurecli
    az feature list | grep NetApp
    ```

   コマンドの出力は次のようになります。
   
    ```output
    "id": "/subscriptions/<SubID>/providers/Microsoft.Features/providers/Microsoft.NetApp/features/ANFGA",  
    "name": "Microsoft.NetApp/ANFGA" 
    ```
       
   `<SubID>` がお客様のサブスクリプション ID です。



4. Azure Cloud Shell コンソールで次のコマンドを入力して、Azure リソース プロバイダーを登録します。 
    
    ```azurecli
    az provider register --namespace Microsoft.NetApp --wait
    ```

   `--wait` パラメーターで、登録が完了するまで待機するようコンソールに指示します。 登録プロセスは、完了するまでに時間がかかることがあります。

5. Azure Cloud Shell コンソールで次のコマンドを入力して、Azure リソース プロバイダーが登録されたことを確認します。 
    
    ```azurecli
    az provider show --namespace Microsoft.NetApp
    ```

   コマンドの出力は次のようになります。
   
    ```output
    {
     "id": "/subscriptions/<SubID>/providers/Microsoft.NetApp",
     "namespace": "Microsoft.NetApp", 
     "registrationState": "Registered", 
     "resourceTypes": […. 
    ```

   `<SubID>` がお客様のサブスクリプション ID です。  `state` パラメーターの値は `Registered` を示しています。

6. Azure portal で、 **[サブスクリプション]** ブレードをクリックします。
7. [サブスクリプション] ブレードで、お客様のサブスクリプション ID をクリックします。 
8. サブスクリプションの設定で、 **[リソース プロバイダー]** をクリックして、Microsoft.NetApp プロバイダーの状態が Registered であることを確認します。 

      ![登録済みの Microsoft.NetApp](../media/azure-netapp-files/azure-netapp-files-registered-resource-providers.png)


## <a name="next-steps"></a>次のステップ

* [NetApp アカウントを作成する](azure-netapp-files-create-netapp-account.md)
* [Azure サポート要求を作成する](../azure-portal/supportability/how-to-create-azure-support-request.md)
