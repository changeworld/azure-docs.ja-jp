---
title: 'App Service on Azure Stack: 障害ドメインの更新プログラム | Microsoft Docs'
description: 障害ドメインに分散して Azure App Service on Azure Stack を再配布する方法
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2018
ms.author: anwestg
ms.openlocfilehash: 53766099f283f802482fe8e84144502d386b1d69
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39440153"
---
# <a name="how-to-redistribute-azure-app-service-on-azure-stack-across-fault-domains"></a>障害ドメインに分散して Azure App Service on Azure Stack を再配布する方法

*適用対象: Azure Stack 統合システム*

1802 更新プログラムにより、Azure Stack で、障害ドメイン間のワークロードの分散がサポートされるようになりました。この機能は高可用性のために重要です。

>[!IMPORTANT]
>障害ドメインのサポートを利用するには、Azure Stack 統合システムを 1802 に更新する必要があります。 このドキュメントは、1802 更新プログラムの前に終了していた App Service リソース プロバイダーのデプロイにのみ適用されます。 Azure Stack に 1802 更新プログラムが適用された後に Azure Stack に App Service をデプロイした場合、リソース プロバイダーは既に障害ドメイン間で分散されています。

## <a name="rebalance-an-app-service-resource-provider-across-fault-domains"></a>障害ドメイン間で App Service リソース プロバイダーを再調整する

App Service リソース プロバイダー向けにデプロイされたスケール セットを再配布するには、この記事の手順をスケール セットごとに実行する必要があります。 既定でスケール セットの名前は次のとおりです。

* ManagementServersScaleSet
* FrontEndsScaleSet
* PublishersScaleSet
* SharedWorkerTierScaleSet
* SmallWorkerTierScaleSet
* MediumWorkerTierScaleSet
* LargeWorkerTierScaleSet

>[!NOTE]
> 一部の worker 階層スケール セットにデプロイされているインスタンスがない場合、それらのスケール セットを再調整する必要はありません。 スケール セットは、今後スケールアウトするときに正しく調整されます。

スケール セットをスケールアウトするには、次の手順を実行します。

1. Azure Stack 管理者ポータルにサインインします。
1. **[そのほかのサービス]** を選択します。
1. [Compute] で、**[Virtual Machine Scale Sets]** を選択します。 App Service 展開の一部として展開されている既存のスケール セットは、インスタンス数情報と共に表示されます。 次の画面キャプチャは、スケール セットの例を示しています。

      ![Azure App Service スケール セットは、仮想マシン スケール セット UX に表示されます][1]

1. 各セットをスケールアウトします。 たとえば、スケール セット内に 3 つの既存のインスタンスがある場合は、3 つの新しいインスタンスが障害ドメインにデプロイされるように、6 にスケールアウトする必要があります。 次の PowerShell の例は、スケール セットをスケールアウトする方法を示します。

   ```powershell
   Add-AzureRmAccount -EnvironmentName AzureStackAdmin 

   # Get current scale set
   $vmss = Get-AzureRmVmss -ResourceGroupName "AppService.local" -VMScaleSetName "SmallWorkerTierScaleSet"

   # Set and update the capacity of your scale set
   $vmss.sku.capacity = 6
   Update-AzureRmVmss -ResourceGroupName AppService.local" -Name "SmallWorkerTierScaleSet" -VirtualMachineScaleSet $vmss
   ```

   >[!NOTE]
   >ロールの種類とインスタンスの数によっては、この手順が終了するまでに何時間もかかる場合があります。

1. **App Service の管理ロール**で、新しいロール インスタンスの状態を監視します。 ロール インスタンスの状態を確認するために、リスト内のロールの種類を選択します

    ![Azure Stack ロール上の Azure App Service][2]

1. 新しいロール インスタンスの状態が **[準備完了]** の場合は、**[仮想マシン スケール セット]** に戻り、古いロール インスタンスを**削除**します。

1. 仮想マシンスケールセット**ごとに**これらの手順を繰り返します。

## <a name="next-steps"></a>次の手順

その他の [Platform as a Service (PaaS) サービス](azure-stack-tools-paas-services.md)を試してみることもできます。

* [SQL Server リソースプロバイダー](azure-stack-sql-resource-provider-deploy.md)
* [MySQL リソースプロバイダー](azure-stack-mysql-resource-provider-deploy.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-fault-domain-update/app-service-scale-sets.png
[2]: ./media/azure-stack-app-service-fault-domain-update/app-service-roles.png
