---
title: Azure リソースを新しいサブスクリプションまたはリソース グループに移動する | Microsoft Docs
description: Azure Resource Manager を使用して、リソースを新しいリソース グループまたはサブスクリプションに移動します。
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ab7d42bd-8434-4026-a892-df4a97b60a9b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/02/2018
ms.author: tomfitz
ms.openlocfilehash: 69614fe84941ea2003d39de165c692b812d10785
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2018
ms.locfileid: "39503582"
---
# <a name="move-resources-to-new-resource-group-or-subscription"></a>新しいリソース グループまたはサブスクリプションへのリソースの移動

この記事では、リソースを新しいサブスクリプションまたは同じサブスクリプション内の新しいリソース グループに移動する方法について説明します。 リソースの移動には、ポータル、PowerShell、Azure CLI、または REST API を使用できます。 この記事の移動操作は、Azure サポートの支援を受けなくても利用できます。

リソースを移動する場合は、その操作の間、ソース グループとターゲット グループの両方がロックされます。 これらのリソース グループに対する書き込み操作および削除操作は、移動が完了するまでブロックされます。 このロックはリソース グループでリソースを追加、更新、削除できなくなることを意味しますが、リソースが停止されるわけではありません。 たとえば、SQL Server とそのデータベースを新しいリソース グループに移動する場合、そのデータベースを使用するアプリケーションにダウンタイムは発生しません。 これまでどおり、データベースの読み取りと書き込みを行うことができます。

リソースの場所を変更することはできません。 リソースを移動しても、新しいリソース グループに移動されるだけです。 新しいリソース グループは別の場所に存在する場合もありますが、リソース自体の場所は変更されません。

> [!NOTE]
> この記事では、既存の Azure アカウント プラン内のリソースを移動する方法について説明します。 実際に Azure アカウント オファーを変更 (従量課金制から前払いにアップグレードするなど) しながら既存のリソースの処理を継続する場合は、 [別の Azure サブスクリプション オファーへの切り替え](../billing/billing-how-to-switch-azure-offer.md)に関するページをご覧ください。
>
>

## <a name="checklist-before-moving-resources"></a>リソースの移動前のチェック リスト

リソースを移動する前に実行すべき重要な手順がいくつかあります。 これらの条件を確認することにより、エラーの発生を回避できます。

1. 移動元と移動先のサブスクリプションが同じ [Azure Active Directory テナント](../active-directory/develop/quickstart-create-new-tenant.md)内に存在している必要があります。 両方のサブスクリプションに同じテナント ID があることを確認するには、Azure PowerShell または Azure CLI を使用します。

  Azure PowerShell では、次を使用します。

  ```powershell
  (Get-AzureRmSubscription -SubscriptionName <your-source-subscription>).TenantId
  (Get-AzureRmSubscription -SubscriptionName <your-destination-subscription>).TenantId
  ```

  Azure CLI では、次を使用します。

  ```azurecli-interactive
  az account show --subscription <your-source-subscription> --query tenantId
  az account show --subscription <your-destination-subscription> --query tenantId
  ```

  移動元と移動先のサブスクリプションのテナント ID が同じでない場合、次の方法でテナント ID を調整する必要があります。

  * [Azure サブスクリプションの所有権を別のアカウントに譲渡する](../billing/billing-subscription-transfer.md)
  * [Azure サブスクリプションを Azure Active Directory に関連付けるまたは追加する方法](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

2. サービスでリソースの移動機能を有効にする必要があります。 この記事で、リソースの移動を有効にするサービスと、リソースの移動を有効にしないサービスを示します。
3. 移動するリソースのリソース プロバイダーについて、移動先のサブスクリプションに登録する必要があります。 登録しないと、 **リソースの種類についてサブスクリプションへの登録が行われていない**ことを示すエラーが発生します。 この問題は、リソースを新しいサブスクリプションに移動するが、そのサブスクリプションがそのリソースの種類で使用されたことがない場合に発生する可能性があります。

  PowerShell で登録状態を取得するには、次のコマンドを使用します。

  ```powershell
  Set-AzureRmContext -Subscription <destination-subscription-name-or-id>
  Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
  ```

  リソース プロバイダーを登録するには、次のコマンドを使用します。

  ```powershell
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
  ```

  Azure CLI で登録状態を取得するには、次のコマンドを使用します。

  ```azurecli-interactive
  az account set -s <destination-subscription-name-or-id>
  az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
  ```

  リソース プロバイダーを登録するには、次のコマンドを使用します。

  ```azurecli-interactive
  az provider register --namespace Microsoft.Batch
  ```

4. リソースを動かすアカウントには少なくとも次のアクセス許可を与える必要があります。

   * ソース リソース グループの **Microsoft.Resources/subscriptions/resourceGroups/moveResources/action**。
   * ターゲット リソース グループの **Microsoft.Resources/subscriptions/resourceGroups/write**。

5. リソースを移動する前に、リソースの移動先となるサブスクリプションのサブスクリプション クォータをチェックします。 リソースを移動することでサブスクリプションが制限を上回る場合、クォータの引き上げを要求できるかどうかを確認する必要があります。 制限の一覧と引き上げを要求する方法については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-subscription-service-limits.md)」を参照してください。

5. 可能であれば、大規模な移動は個別の移動操作に分けます。 Resource Manager で単一の操作で 800 を超えるリソースを移動しようとすると、すぐに失敗します。 ただし、800 未満のリソースの移動でも、タイムアウトで失敗になることがあります。

## <a name="when-to-call-support"></a>サポートに問い合わせる場合

この記事で説明するセルフサービス操作を使用すれば、ほとんどのリソースを移動できます。 次の場合にセルフサービス操作を使用します。

* Resource Manager のリソースを移動する。
* [クラシック デプロイメントの制限事項](#classic-deployment-limitations)に従って、クラシック リソースを移動する。

次の操作を実行する必要がある場合は、[サポート](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)にお問い合わせください。

* リソースを新しい Azure アカウント (および Azure Active Directory テナント) に移動するにあたり、前出のセクションの手順に関して支援が必要。
* クラシック リソースを移動するときに制限事項に関連する問題が発生した。

## <a name="services-that-can-be-moved"></a>移動可能なサービス

新しいリソース グループへの移動と新しいサブスクリプションへの移動の両方が可能なサービスは、次のとおりです。

* API Management
* App Service アプリ (Web Apps) - 「 [App Service の制限事項](#app-service-limitations)
* App Service 証明書
* Application Insights
* Analysis Services
* Automation
* Azure Active Directory B2C
* Azure Cosmos DB
* Azure Maps
* Azure Relay
* Azure Stack - 登録
* Azure Migrate
* Batch
* BizTalk Services
* ボット サービス
* CDN
* Cloud Services - 「 [クラシック デプロイメントの制限事項](#classic-deployment-limitations)
* Cognitive Services
* Container Registry
* Content Moderator
* Data Catalog
* Data Factory
* Data Lake Analytics
* Data Lake Store
* DNS
* Event Grid
* Event Hubs
* HDInsight クラスター - 「[HDInsight の制限事項](#hdinsight-limitations)」を参照
* IoT Hub
* Key Vault
* Load Balancer - 「[Load Balancer の制限事項](#lb-limitations)」を参照
* Log Analytics
* Logic Apps
* Machine Learning - Machine Learning Studio Web サービスは、同じサブスクリプション内のリソース グループには移動できますが、別のサブスクリプションには移動できません。 他の Machine Learning リソースは、異なるサブスクリプションに移動できます。
* Media Services
* Mobile Engagement
* Notification Hubs
* Operational Insights
* Operations Management
* ポータルのダッシュボード
* Power BI - Power BI Embedded と Power BI ワークスペース コレクションの両方
* パブリック IP - 「[パブリック IP の制限事項](#pip-limitations)」を参照
* Redis Cache
* Scheduler
* Search
* Service Bus
* Service Fabric
* SignalR Service
* Storage
* Storage (クラシック) - 「 [クラシック デプロイメントの制限事項](#classic-deployment-limitations)
* Stream Analytics - 実行中状態の Stream Analytics ジョブは移動できません。
* SQL Database サーバー - データベースとサーバーは同じリソース グループ内に存在する必要があります。 SQL Server を移動すると、そのデータベースもすべて移動されます。 この動作は、Azure SQL Database と Azure SQL Data Warehouse データベースに適用されます。
* Time Series Insights
* Traffic Manager
* Virtual Machines - マネージド ディスクを使用する VM を移動することはできません。 「[Virtual Machines の制限事項](#virtual-machines-limitations)」を参照してください。
* Virtual Machines (クラシック) - 「 [クラシック デプロイメントの制限事項](#classic-deployment-limitations)
* Virtual Machine Scale Sets - 「[Virtual Machines の制限事項](#virtual-machines-limitations)」を参照してください。
* Virtual Networks - 「[Virtual Networks の制限事項](#virtual-networks-limitations)」を参照してください。
* Visual Studio Team Services - Microsoft 以外の拡張機能を購入している VSTS アカウントは、[それらの購入をキャンセル](https://go.microsoft.com/fwlink/?linkid=871160)してからでないと、アカウントを異なるサブスクリプションに移動できません。
* VPN Gateway

## <a name="services-that-cannot-be-moved"></a>移動不可能なサービス

現在、リソースの移動が不可能なサービスは、次のとおりです。

* AD Domain Services
* AD Hybrid Health Service
* Application Gateway
* Azure Database for MySQL
* Azure Database for PostgreSQL
* Azure Database Migration
* Azure Databricks
* Batch AI
* 証明書 - App Service 証明書は移動できますが、アップロードした証明書には[制限](#app-service-limitations)があります。
* Container Service
* Dynamics LCS
* ExpressRoute
* Kubernetes Service
* Lab Services - 同じサブスクリプション内の新しいリソース グループへの移動が有効になっています。ただし、サブスクリプション間の移動は有効になっていません。
* Load Balancer - 「[Load Balancer の制限事項](#lb-limitations)」を参照
* Managed Applications
* Managed Disks - 「[Virtual Machines の制限事項](#virtual-machines-limitations)」を参照してください。
* Microsoft Genomics
* パブリック IP - 「[パブリック IP の制限事項](#pip-limitations)」を参照
* Recovery Services コンテナー - Recovery Services コンテナーに関連付けられているコンピューティング リソース、ネットワーク リソース、ストレージ リソースも移動できません。「[Recovery Services の制限事項](#recovery-services-limitations)」をご覧ください。
* SAP HANA on Azure
* セキュリティ
* Site Recovery
* StorSimple デバイス マネージャー
* Virtual Networks (クラシック) - 「 [クラシック デプロイメントの制限事項](#classic-deployment-limitations)

## <a name="virtual-machines-limitations"></a>Virtual Machines の制限事項

マネージド ディスクは移動をサポートしていません。 この制限は、いくつかの関連リソースも移動できないことを意味します。 以下を移動することはできません。

* マネージド ディスク
* マネージド ディスクを使用する仮想マシン
* マネージド ディスクから作成されたイメージ
* マネージド ディスクから作成されたスナップショット
* マネージド ディスクを使用する仮想マシンの可用性セット

マネージド ディスクは移動できませんが、コピーを作成した後、その既存のマネージド ディスクから新しい仮想マシンを作成できます。 詳細については、次を参照してください。

* [PowerShell](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md) または [Azure CLI](../virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md) で、マネージド ディスクを同じサブスクリプションまたは別のサブスクリプションにコピーする
* [PowerShell](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md) or [Azure CLI](../virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md)で既存のマネージド OS ディスクを使用して仮想マシンを作成する

プランが添付された Marketplace リソースから作成された仮想マシンは、リソース グループまたはサブスクリプションの間で移動できません。 現在のサブスクリプションで仮想マシンをプロビジョニング解除し、新しいサブスクリプションにデプロイし直す必要があります。

証明書が Key Vault に格納されている Virtual Machines は、同じサブスクリプション内の新しいリソース グループへの移動は可能ですが、サブスクリプション間の移動は可能ではありません。

## <a name="virtual-networks-limitations"></a>Virtual Networks の制限事項

仮想ネットワークを移動するときは、その依存リソースも移動する必要があります。 たとえば、仮想ネットワークと一緒にゲートウェイを移動する必要があります。

ピアリングされた仮想ネットワークを移動するには、最初に仮想ネットワークのピアリングを無効にする必要があります。 無効にすると、仮想ネットワークを移動できます。 移動後に、仮想ネットワークのピアリングを再度有効にします。

仮想ネットワークにリソース ナビゲーション リンクのあるサブネットが含まれる場合、仮想ネットワークを別のサブスクリプションに移動することはできません。 たとえば、Redis Cache リソースがサブネットにデプロイされている場合、そのサブネットにはリソース ナビゲーション リンクがあります。

仮想ネットワークにカスタム DNS サーバーが含まれる場合、仮想ネットワークを別のサブスクリプションに移動することはできません。 仮想ネットワークを移動するには、それを既定の (Azure が提供する) DNS サーバーに設定します。 移動後に、カスタム DNS サーバーを再構成します。

## <a name="app-service-limitations"></a>App Service の制限事項

App Service のリソースを移動することに関しての制限事項は、リソースをサブスクリプション内で移動するか、新しいサブスクリプションに移動するかによって異なります。

以上のセクションで説明した制限は、App Service 証明書ではなく、アップロードされた証明書に適用されます。 App Service 証明書は、新しいリソース グループまたはサブスクリプションに制限なく移動できます。 複数の Web アプリで同じ App Service 証明書が使用される場合、最初にすべての Web アプリを移動し、次に証明書を移動します。

### <a name="moving-within-the-same-subscription"></a>同じサブスクリプション内で移動する場合

Web アプリを "_同じサブスクリプション内_" で移動する場合には、アップロードした SSL 証明書は移動できません。 ただし、アップロードした SSL 証明書を移動せずに Web アプリを新しいリソース グループに移動することはできます。その場合でも、アプリの SSL 機能は引き続き機能します。

SSL 証明書を Web アプリと共に移動したい場合は、次の手順に従います。

1.  アップロードした証明書を Web アプリから削除します。
2.  Web アプリを移動します。
3.  移動した Web アプリに証明書をアップロードします。

### <a name="moving-across-subscriptions"></a>サブスクリプション間で移動する場合

Web App を_サブスクリプション間_で移動する場合には、次の制限事項が適用されます。

- 移動先のリソース グループに既存の App Service リソースが含まれていてはいけません。 App Service リソースには次のものがあります。
    - Web Apps
    - App Service プラン
    - アップロードまたはインポートした SSL 証明書
    - App Service Environment
- リソース グループ内のすべての App Service リソースを一緒に移動する必要があります。
- App Service リソースは、最初に作成されたときのリソース グループからのみ移動できます。 App Service リソースが元のリソース グループから移動されている場合は、まず元のリソース グループに戻してから、サブスクリプション間の移動を行うことができます。

## <a name="classic-deployment-limitations"></a>クラシック デプロイメントの制限事項

クラシック モデルを使用してデプロイされるリソースを移動するためのオプションは、リソースをサブスクリプション内で移動するか、新しいサブスクリプションに移動するかによって異なります。

### <a name="same-subscription"></a>同じサブスクリプション

リソースを同じサブスクリプション内のリソース グループ間で移動する場合は、次の制限が適用されます。

* 仮想ネットワーク (クラシック) を移動することはできません。
* Virtual Machines (クラシック) はクラウド サービスで移動する必要があります。
* クラウド サービスは、移動にその仮想マシンがすべて含まれている場合にのみ移動できます。
* 一度に移動できるクラウド サービスは 1 つだけです。
* 一度に移動できるストレージ アカウント (クラシック) は 1 つだけです。
* ストレージ アカウント (クラシック) は、仮想マシンまたはクラウド サービスと同じ操作では移動できません。

クラシック リソースを同じサブスクリプション内の新しリソース グループに移動する場合は、[ポータル](#use-portal)、[Azure PowerShell](#use-powershell)、[Azure CLI](#use-azure-cli)、または [REST API](#use-rest-api) の標準の移動操作を使用します。 Resource Manager のリソースの移動に使用した方法と同じ操作を使用します。

### <a name="new-subscription"></a>新しいサブスクリプション

リソースを新しいサブスクリプションに移動する場合は、次の制限が適用されます。

* サブスクリプション内のすべてのクラシック リソースは、同じ操作で移動する必要があります。
* 対象のサブスクリプションには、他のクラシック リソースは含めないでください。
* クラシック リソースの場合、移動は、別の REST API を通じてのみ要求できます。 標準の Resource Manager の移動コマンドは、クラシック リソースを新しいサブスクリプションに移動する場合は機能しません。

クラシック リソースを新しいサブスクリプションに移動する場合は、クラシック リソース固有の REST 操作を使用してください。 REST を使用するには、次の手順を実行します。

1. 移動元のサブスクリプションがサブスクリプション間の移動に参加できることを確認します。 次の操作を行います。

  ```HTTP
  POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
  ```

     要求本文は次のようになります。

  ```json
  {
    "role": "source"
  }
  ```

     検証操作の応答は次のような形式になります。

  ```json
  {
    "status": "{status}",
    "reasons": [
      "reason1",
      "reason2"
    ]
  }
  ```

2. 移動先のサブスクリプションがサブスクリプション間の移動に参加できることを確認します。 次の操作を行います。

  ```HTTP
  POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
  ```

     要求本文は次のようになります。

  ```json
  {
    "role": "target"
  }
  ```

     応答は移動元のサブスクリプションの検証と同じ形式になります。
3. 両方のサブスクリプションが検証に合格し、すべてのクラシック リソースをあるサブスクリプションから別のサブスクリプションに移動する場合は、次の操作を行います。

  ```HTTP
  POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01
  ```

    要求本文は次のようになります。

  ```json
  {
    "target": "/subscriptions/{target-subscription-id}"
  }
  ```

この操作には数分かかる場合があります。

## <a name="recovery-services-limitations"></a>Recovery Services の制限事項

Azure Site Recovery では、ディザスター リカバリーの設定に使用されるストレージ リソース、ネットワーク リソース、またはコンピューティング リソースは移動できません。

たとえば、ストレージ アカウント (Storage1) へのオンプレミス コンピューターのレプリケーションが設定済みで、Azure へのフェールオーバー後、保護されたコンピューターを、Azure 仮想ネットワーク (Network1) に接続された仮想マシン (VM1) として使用する必要があるとします。 こうした Azure リソース、つまり Storage1、VM1、および Network1 はどれも、同じサブスクリプション内のリソース グループ間、またはサブスクリプション間で移動することはできません。

**Azure Backup** に登録された VM をリソース グループ間で移動するには:
 1. バックアップを一時的に停止し、バックアップ データを保持します
 2. VM をターゲット リソース グループに移動します
 3. 同じコンテナーまたは新しいコンテナーで VM を再び保護します。ユーザーは、移動操作の前に作成された使用可能な復元ポイントから復元できます。
バックアップした VM をサブスクリプション間で移動する場合、手順 1 と手順 2 は同じです。 手順 3 では、ターゲット サブスクリプションに存在する、または作成した新しいコンテナーで、VM を保護する必要があります。 Recovery Services コンテナーでは、異なるサブスクリプション間のバックアップはサポートされていません。

## <a name="hdinsight-limitations"></a>HDInsight の制限事項

HDInsight クラスターは、新しいサブスクリプションまたはリソース グループに移動できます。 ただし、HDInsight クラスターにリンクされているネットワーク リソース (仮想ネットワーク、NIC、ロード バランサーなど) をサブスクリプション間で移動することはできません。 また、クラスターの仮想マシンに接続されている NIC を新しいリソース グループに移動することはできません。

HDInsight クラスターを新しいサブスクリプションに移動するときは、まず、他のリソース (ストレージ アカウントなど) を移動します。 その後、HDInsight クラスターを単独で移動します。

## <a name="search-limitations"></a>Search の制限事項

異なるリージョンにデプロイされている複数の Search リソースを一度に移動することはできません。
そのような場合は、それらを個別に移動する必要があります。

## <a name="lb-limitations"></a> Load Balancer の制限事項

Basic SKU の Load Balancer は移動できます。
Standard SKU の Load Balancer は移動できません。

## <a name="pip-limitations"></a> パブリック IP の制限事項

Basic SKU のパブリック IP は移動できます。
Standard SKU のパブリック IP は移動できません。

## <a name="use-portal"></a>ポータルの使用

リソースを移動するには、そのリソースが含まれるリソース グループを選択し、**[移動]** を選択します。

![リソースの移動](./media/resource-group-move-resources/select-move.png)

リソースを新しいリソース グループに移動するか新しいサブスクリプションに移動するかを選択します。

移動するリソースを、移動先のリソース グループを選択します。 そのリソースのスクリプトを更新する必要があること確認し、 **[OK]** を選択します。 前の手順でサブスクリプションの編集アイコンを選択した場合は、移動先のサブスクリプションも選択する必要があります。

![移動先の選択](./media/resource-group-move-resources/select-destination.png)

**[通知]** に、移動操作が実行されていることが表示されます。

![移動の状態の表示](./media/resource-group-move-resources/show-status.png)

完了すると、結果が表示されます。

![移動の結果の表示](./media/resource-group-move-resources/show-result.png)

## <a name="use-powershell"></a>PowerShell の使用

既存のリソースを別のリソース グループまたはサブスクリプションに移動するには、 [Move-AzureRmResource](/powershell/module/azurerm.resources/move-azurermresource) コマンドを使用します。 次の例では、複数のリソースを新しいリソース グループに移動する方法を示します。

```powershell
$webapp = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

新しいサブスクリプションに移動する場合は、`DestinationSubscriptionId` パラメーターの値を含めます。

## <a name="use-azure-cli"></a>Azure CLI の使用

既存のリソースを別のリソース グループまたはサブスクリプションに移動するには、[az resource move](/cli/azure/resource?view=azure-cli-latest#az-resource-move) コマンドを使用します。 移動するリソースのリソース ID を指定します。 次の例では、複数のリソースを新しいリソース グループに移動する方法を示します。 `--ids` パラメーターには、移動するリソース ID のスペース区切りリストを指定します。

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

新しいサブスクリプションに移動するには、`--destination-subscription-id` パラメーターを指定します。

## <a name="use-rest-api"></a>REST API を使用する

既存のリソースを別のリソース グループまたはサブスクリプションに移動するには、次のコマンドを実行します。

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

要求の本文で、ターゲット リソース グループと、移動するリソースを指定します。 REST による移動操作の詳細については、「 [リソースの移動](/rest/api/resources/Resources/MoveResources)」を参照してください。

## <a name="next-steps"></a>次の手順

* サブスクリプションを管理するための PowerShell コマンドレットについては、「 [Resource Manager での Azure PowerShell の使用](powershell-azure-resource-manager.md)」を参照してください。
* サブスクリプションを管理するための Azure CLI コマンドについては、「 [リソース マネージャーでの Azure CLI の使用](xplat-cli-azure-resource-manager.md)」を参照してください。
* サブスクリプションを管理するためのポータル機能については、 [Azure Portal を使用したリソースの管理](resource-group-portal.md)に関するページをご覧ください。
* リソースを論理的に整理する方法については、「 [タグを使用したリソースの整理](resource-group-using-tags.md)」を参照してください。
