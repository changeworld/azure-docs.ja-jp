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
ms.date: 03/09/2018
ms.author: anwestg
ms.openlocfilehash: 34823b856b48af66ab81dda3359d2e50289f8d2f
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/19/2018
---
# <a name="how-to-redistribute-azure-app-service-on-azure-stack-across-fault-domains"></a>障害ドメインに分散して Azure App Service on Azure Stack を再配布する方法

*適用対象: Azure Stack 統合システム*

1802 更新プログラムにより、Azure Stack で、障害ドメイン間のワークロードの分散がサポートされるようになりました。この機能は高可用性のために重要です。

> [!IMPORTANT]
> 障害ドメインのサポートを利用するには、Azure Stack 統合システムを 1802 に更新しておく必要があります。  このドキュメントは、1802 更新プログラムの前に完了していた App Service リソース プロバイダーの展開にのみ適用されます。  Azure Stack に 1802 更新プログラムが適用された後に Azure Stack に App Service を展開した場合、リソース プロバイダーは既に障害ドメイン間で分散されています。
>
>

## <a name="rebalance-an-app-service-resource-provider-across-fault-domains"></a>障害ドメイン間で App Service リソース プロバイダーを再調整する

App Service リソース プロバイダー向けに展開されたスケール セットを再配布するには、スケール セットごとに以下の手順を実行する必要があります。  既定でスケール セットの名前は次のとおりです。

* ManagementServersScaleSet
* FrontEndsScaleSet
* PublishersScaleSet
* SharedWorkerTierScaleSet
* SmallWorkerTierScaleSet
* MediumWorkerTierScaleSet
* LargeWorkerTierScaleSet

> [!NOTE]
> 一部の worker 階層スケール セットに展開されているインスタンスがない場合、それらのスケール セットを再調整する必要はありません。  スケール セットは、今後スケールアウトするときに正しく調整されます。
>
>

1. Azure Stack 管理者ポータルで Virtual Machine Scale Sets を参照します。  App Service 展開の一部として展開されている既存のスケール セットは、インスタンス数情報と共に表示されます。

    ![Azure App Service スケール セットは、仮想マシン スケール セット UX に表示されます][1]

2. 次に、各セットをスケールアウトします。  たとえば、スケール セット内に 3 つの既存のインスタンスがある場合は、3 つの新しいインスタンスが障害ドメイン間でプロビジョニングされるように、6 にスケールアウトする必要があります。
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 [PowerShell で Azure Stack 管理環境を設定します](azure-stack-powershell-configure-admin.md)。b. この例を使用して、スケール セットをスケールアウトします。
        ```powershell
                Connect-AzureRmAccount -EnvironmentName AzureStackAdmin 

                # Get current scale set
                $vmss = Get-AzureRmVmss -ResourceGroupName "AppService.local" -VMScaleSetName "SmallWorkerTierScaleSet"

                # Set and update the capacity of your scale set
                $vmss.sku.capacity = 6
                Update-AzureRmVmss -ResourceGroupName "AppService.local" -Name "SmallWorkerTierScaleSet" -VirtualMachineScaleSet $vmss
        '''
> [!NOTE]
> ロールの種類とインスタンスの数によっては、この手順が完了するまでに何時間もかかる場合があります。
>
>

3. App Service の管理ロール ブレードで新しいロール インスタンスの状態を監視します。  リスト内のロールの種類をクリックして、個々のロール インスタンスの状態を確認します。

    ![Azure Stack ロール上の Azure App Service][2]

4. 1 つの新しいインスタンスが**準備完了**状態であることを確認したら、仮想マシン スケール セット ブレードに戻り、古いインスタンスを**削除**します。

5. 仮想マシンスケールセット**ごとに**これらの手順を繰り返します。

## <a name="next-steps"></a>次の手順

その他の [Platform as a Service (PaaS) サービス](azure-stack-tools-paas-services.md)を試してみることもできます。

* [SQL Server リソースプロバイダー](azure-stack-sql-resource-provider-deploy.md)
* [MySQL リソースプロバイダー](azure-stack-mysql-resource-provider-deploy.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-fault-domain-update/app-service-scale-sets.png
[2]: ./media/azure-stack-app-service-fault-domain-update/app-service-roles.png
