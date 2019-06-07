---
title: Azure NetApp Files に登録する | Microsoft Docs
description: Azure NetApp Files に使用して使用する方法について説明します。
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
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: b-juche
ms.openlocfilehash: fbe0b82008d7b15332c4e2cd62c49c611f20fe89
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/16/2019
ms.locfileid: "65794708"
---
# <a name="register-for-azure-netapp-files"></a>Azure NetApp Files に登録する

> [!IMPORTANT] 
> Azure NetApp Files リソース プロバイダーを登録する前に、Azure NetApp Files チームから、サービスへのアクセスが付与されていることを確認できるメールが必ず送信されます。 

この記事では、Azure NetApp Files に登録してサービスの使用を開始する方法について説明します。

## <a name="waitlist"></a>サービスにアクセスするための順番待ちリクエストを送信する

1. [Azure NetApp Files の順番待ち送信ページ](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u)から、Azure NetApp Files サービスにアクセスするための順番待ちリクエストを送信します。 

    順番待ちにサインアップしても、すぐにサービスにアクセスできるとは限りません。 

2. Azure NetApp Files チームからの正式な確認メールを待ってから他のタスクを続けてください。 

## <a name="resource-provider"></a>NetApp リソース プロバイダーを登録する

サービスを使用するには、Azure NetApp Files 用に Azure リソース プロバイダーを登録する必要があります。

> [!NOTE] 
> サービスのアクセスが許可されていなくても、NetApp リソース プロバイダーは正常に登録できます。 ただし、アクセスの承認がないと、NetApp アカウントまたはその他の Azure NetApp Files リソースを作成する Azure portal または API 要求は、次のエラーで拒否されます。  
>
> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"NotFound","message":"{\r\n \"error\": {\r\n \"code\": \"InvalidResourceType\",\r\n \"message\": \"The resource type could not be found in the namespace 'Microsoft.NetApp' for api version '2017-08-15'.\"\r\n }\r\n}"}]}`


1. Azure portal で、右上隅にある Azure Cloud Shell アイコンをクリックします。

      ![Azure Cloud Shell アイコン](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. お客様の Azure アカウントにサブスクリプションが複数ある場合は、Azure NetApp Files のホワイトリストに登録されているものを選択します。
    
        az account set --subscription <subscriptionId>

3. Azure Cloud Shell コンソールで次のコマンドを入力して、お客様のサブスクリプションがホワイトリストに登録されていることを確認します。
    
        az feature list | grep NetApp

   コマンドの出力は次のようになります。
   
       "id": "/subscriptions/<SubID>/providers/Microsoft.Features/providers/Microsoft.NetApp/features/publicPreviewADC",  
       "name": "Microsoft.NetApp/publicPreviewADC" 
       
   `<SubID>` がお客様のサブスクリプション ID です。

    機能名 `Microsoft.NetApp/publicPreviewADC` が表示されない場合は、サービスにアクセスできません。 この手順で止めてください。 続行する前に、「[サービスにアクセスするための順番待ちリクエストを送信する](#waitlist)」の指示に従ってください。 

4. Azure Cloud Shell コンソールで次のコマンドを入力して、Azure リソース プロバイダーを登録します。 
    
        az provider register --namespace Microsoft.NetApp --wait

   `--wait` パラメーターで、登録が完了するまで待機するようコンソールに指示します。 登録プロセスは、完了するまでに時間がかかることがあります。

5. Azure Cloud Shell コンソールで次のコマンドを入力して、Azure リソース プロバイダーが登録されたことを確認します。 
    
        az provider show --namespace Microsoft.NetApp

   コマンドの出力は次のようになります。
   
        {
        "id": "/subscriptions/<SubID>/providers/Microsoft.NetApp",
        "namespace": "Microsoft.NetApp", 
        "registrationState": "Registered", 
        "resourceTypes": […. 

   `<SubID>` がお客様のサブスクリプション ID です。  `state` パラメーターの値は `Registered` を示しています。

6. Azure portal で、 **[サブスクリプション]** ブレードをクリックします。
7. [サブスクリプション] ブレードで、お客様のサブスクリプション ID をクリックします。 
8. サブスクリプションの設定で、 **[リソース プロバイダー]** をクリックして、Microsoft.NetApp プロバイダーの状態が Registered であることを確認します。 

      ![登録済みの Microsoft.NetApp](../media/azure-netapp-files/azure-netapp-files-registered-resource-providers.png)


## <a name="next-steps"></a>次の手順

[NetApp アカウントを作成する](azure-netapp-files-create-netapp-account.md)