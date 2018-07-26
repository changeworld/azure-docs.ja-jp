---
title: Azure Stack に使用量と課金のためのテナントを追加する | Microsoft Docs
description: クラウド サービス プロバイダー (CSP) によって管理されている Azure Stack にエンド ユーザーを追加する手順を説明します。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: brenduns
ms.reviewer: alfredo
ms.openlocfilehash: d5f775761def1c06063cd02b4141c5de2a752cce
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2018
ms.locfileid: "39089912"
---
# <a name="add-tenant-for-usage-and-billing-to-azure-stack"></a>Azure Stack に使用量と課金のためのテナントを追加する

*適用対象: Azure Stack 統合システム*

この記事では、クラウド サービス プロバイダー (CSP) によって管理されている Azure Stack にエンド ユーザーを追加する手順を説明します。 新しいテナントによってリソースが使用されると、Azure Stack から CSP サブスクリプションに使用量がレポートされます。

多くの場合、CSP は Azure Stack デプロイ上で複数のエンド カスタマー (テナント) にサービスを提供します。 Azure Stack の登録にテナントを追加すると、各テナントの使用量が報告され、対応する CSP サブスクリプションに課金されます。 この記事の手順を完了しなかった場合、テナントの使用量は、Azure Stack の初期登録で使用したサブスクリプションに課金されます。 使用量の追跡とテナントの管理のために Azure Stack にエンド カスタマーを追加する前に、Azure Stack を CSP として構成する必要があります。 手順とリソースについては、「[Manage usage and billing for Azure Stack as a Cloud Service Provider (クラウド サービス プロバイダーとしての Azure Stack の使用量と課金の管理)](azure-stack-add-manage-billing-as-a-csp.md)」を参照してください。

次の図は、新しい顧客が Azure Stack を使用できるように、また、その顧客の使用量の追跡を設定するために、CSP が従う必要がある手順を示しています。 エンド カスタマーを追加すると、Azure Stack 内のリソースも管理できるようになります。 リソースの管理には 2 つのオプションがあります。

1. エンド カスタマーを管理し、エンド カスタマー用のローカルの Azure Stack サブスクリプションに資格情報を提供できます。  
2. または、エンド カスタマーが自身のサブスクリプションをローカルで管理し、所有者アクセス許可を持つゲストとして CSP を追加できます。  

**エンド カスタマーを追加する手順**

![使用量の追跡とエンド カスタマー アカウントの管理のためにクラウド サービス プロバイダーを設定する](media\azure-stack-csp-enable-billing-usage-tracking\process-csp-enable-billing.png)

## <a name="create-a-new-customer-in-partner-center"></a>パートナー センターに新しい顧客を作成する

パートナー センターで、顧客の新しい Azure サブスクリプションを作成します。 手順については、「[新しい顧客の追加](https://msdn.microsoft.com/partner-center/add-a-new-customer)」を参照してください。


##  <a name="create-an-azure-subscription-for-the-end-customer"></a>エンド カスタマーの Azure サブスクリプションを作成する

パートナー センターで顧客のレコードを作成したら、その顧客にカタログ内の製品のサブスクリプションを販売できます。 手順については、「[顧客サブスクリプションの作成、停止、キャンセル](https://msdn.microsoft.com/partner-center/create-a-new-subscription)」を参照してください。

## <a name="create-a-guest-user-in-the-end-customer-directory"></a>エンド カスタマー ディレクトリにゲスト ユーザーを作成する

エンド カスタマーが自分のアカウントを管理する場合は、エンド カスタマーのディレクトリにゲスト ユーザーを作成し、エンド カスタマーにその情報を送信します。 その後エンド カスタマーはゲストを追加し、ゲストのアクセス許可を Azure Stack の CSP アカウントの**所有者**に昇格させます。
 
## <a name="update-the-registration-with-the-end-customer-subscription"></a>エンド カスタマーのサブスクリプションで登録を更新する

エンド カスタマーの新しいサブスクリプションで登録を更新します。 Azure は、パートナー センターの顧客 ID を使用して顧客の使用量をレポートします。 この手順により、各顧客の使用量が、その顧客それぞれの CSP サブスクリプションごとにレポートされるようになります。 これで、ユーザーの使用量の追跡と課金がはるかに簡単になります。

> [!Note]  
> この手順を実行するためには、[Azure Stack に登録](azure-stack-register.md)済みである必要があります。

1. 管理者特権のプロンプトで Windows PowerShell を開き、次のコマンドを実行します。  
    `Add-AzureRmAccount`
2. Azure の資格情報を入力します。
3. PowerShell セッションで、次のコマンドを実行します。

```powershell
    New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01 -Properties <PSObject>
```
### <a name="new-azurermresource-powershell-parameters"></a>PowerShell の New-AzureRmResource パラメーター
| パラメーター | 説明 |
| --- | --- | 
|registrationSubscriptionID | Azure Stack の初期登録に使用された Azure サブスクリプション。 |
| customerSubscriptionID | 登録される顧客の Azure サブスクリプション (Azure Stack ではありません)。 CSP のサービス内で作成される必要があるので、実際にはパートナー センターを介して作成されます。 顧客が複数の Azure Active Directory テナントを持っている場合は、Azure Stack へのログインに使用されるテナントでこのサブスクリプションを作成する必要があります。
| resourceGroup | 登録が格納されている Azure 内のリソース グループ。 
| registrationName | Azure Stack の登録名。 Azure にオブジェクトとして格納されています。 | 
| Properties | リソースのプロパティを指定します。 このパラメーターを使用して、リソースの種類に固有のプロパティの値を指定します。


> [!Note]  
> テナントには、使用する各 Azure Stack を登録する必要があります。 Azure Stack のデプロイが 2 つあり、テナントがその両方を使用する必要がある場合は、各デプロイの初期登録をテナントのサブスクリプションで更新する必要があります。

## <a name="onboard-tenant-to-azure-stack"></a>Azure Stack でテナントの利用を開始する

複数の Azure AD テナントのユーザーが Azure Stack のサービスを使用できるように Azure Stack を構成します。 手順については、「[Azure Stack でのマルチテナントの有効化](azure-stack-enable-multitenancy.md)」を参照してください。


## <a name="create-a-local-resource-in-the-end-customer-tenant-in-azure-stack"></a>Azure Stack 内のエンド カスタマーのテナントにローカル リソースを作成する

Azure Stack に新しい顧客を追加するか、エンド カスタマーのテナントで所有者特権を持つゲスト アカウントを有効化したら、そのテナントにリソースを作成できることを確認します。 たとえば、[Azure Stack ポータルで Windows 仮想マシンを作成する](user\azure-stack-quick-windows-portal.md)ことができます。

## <a name="next-steps"></a>次の手順

 - 登録プロセスでエラーが発生した場合に、そのエラー メッセージを確認する方法については、[テナント登録時のエラー メッセージ](azure-stack-csp-ref-infrastructure.md#usage-and-billing-error-codes)に関するページを参照してください。
 - リソース使用量の情報を Azure Stack から取得する方法の詳細については、[Azure Stack での使用状況と課金](/azure-stack-billing-and-chargeback.md)に関するページを参照してください。
 - エンド カスタマーが CSP をエンド カスタマーの Azure Stack やテナントの管理者として追加する方法については、「[Enable a Cloud Service Provider to manage your Azure Stack subscription (クラウド サービス プロバイダーが Azure Stack サブスクリプションを管理できるようにする)](user\azure-stack-csp-enable-billing-usage-tracking.md)」を参照してください。
