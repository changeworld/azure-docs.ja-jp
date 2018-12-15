---
title: Azure Stack での VM の更新と管理 | Microsoft Docs
description: Azure Automation の Update Management、Change Tracking、および Inventory ソリューションを使用して、Azure Stack にデプロイされている Windows VM を管理する方法について説明します。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: rtiberiu
ms.openlocfilehash: be793fa5d346d05e6b7bd9f93f1108b7a3542fa6
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52959174"
---
# <a name="azure-stack-vm-update-and-management"></a>Azure Stack の VM の更新と管理
以下の Azure Automation ソリューション機能を使用すると、Azure Stack を使用してデプロイされている Windows VM を管理することができます。

- **[Update Management](https://docs.microsoft.com/azure/automation/automation-update-management)**. Update Management ソリューションでは、すべてのエージェント コンピューターで利用可能な更新プログラムの状態をすばやく評価し、Windows VM に必要な更新プログラムをインストールするプロセスを管理できます。

- **[Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking)**. 監視対象サーバーにインストールされているソフトウェア、Windows サービス、Windows レジストリ、ファイルの変更が、クラウドの Log Analytics サービスに送信され、処理されます。 受信したデータにロジックが適用され、クラウド サービスによってそのデータが記録されます。 [変更の追跡] ダッシュボードの情報を使用して、サーバー インフラストラクチャで行われた変更を簡単に確認できます。

- **[Inventory](https://docs.microsoft.com/azure/automation/automation-vm-inventory)**. Azure Stack の Windows 仮想マシンを追跡する Inventory では、インベントリ コレクションを設定して構成するためのブラウザー ベースのユーザー インターフェイスが提供されます。 

> [!IMPORTANT]
> これらのソリューションは、Azure VM の管理に使用されるものと同じです。 Azure と Azure Stack の Windows VM はどちらも、同じインターフェイスから同じツールを使用して同じ方法で管理されます。 また、Azure Stack で Update Management、Change Tracking、Inventory ソリューションを使用すると、Azure Stack VM の課金も Azure VM と同じです。

## <a name="prerequisites"></a>前提条件
これらの機能を使用して Azure Stack の Windows VM を更新および管理するには、事前にいくつかの前提条件を満たす必要があります。 これらには、Azure portal および Azure Stack 管理ポータルで実行する必要のある手順が含まれます。

### <a name="in-the-azure-portal"></a>Azure ポータルで次の操作を行います。
Azure Stack の Windows VM に対して Inventory、Change Tracking、Update Management の Azure Automation 機能を使用するには、まず、Azure でこれらのソリューションを有効にする必要があります。

> [!TIP]
> Azure VM に対してこれらの機能を既に有効にしてある場合は、Log Analytics ワークスペースの既存の資格情報を使用できます。 使用する Log Analytics のワークスペース ID とプライマリ キーが既にある場合は、[次のセクション](./vm-update-management.md#in-the-azure-stack-administration-portal)に進んでください。 それ以外の場合は、引き続きこのセクションで新しい Log Analytics ワークスペースと Automation アカウントを作成します。

これらのソリューションを有効にする最初のステップは、Azure サブスクリプションで [Log Analytics ワークスペースを作成する](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace)ことです。 Log Analytics ワークスペースは、独自のデータ リポジトリ、データ ソース、ソリューションを備えた固有の Log Analytics 環境です。 ワークスペースを作成した後、ワークスペース ID とキーを書き留めておいてください。 この情報は、ワークスペース ブレードに移動し、**[詳細設定]** をクリックして、**[ワークスペース ID]** と **[プライマリ キー]** で確認できます。 

次に、[Automation アカウントを作成する](https://docs.microsoft.com/azure/automation/automation-create-standalone-account)必要があります。 Automation アカウントは、Azure Automation リソースのコンテナーです。 お使いの環境を分離したり、Automation のワークフローとリソースをさらに整理したりする手段を提供します。 Automation アカウントを作成した後は、Inventory、Change Tracking、Update Management の各機能を有効にする必要があります。 そのためには、以下の手順のようにします。

1. Azure portal で、使用する Automation アカウントに移動します。

2. 有効にするソリューションを選択します (**[インベントリ]**、**[変更の追跡]**、または **[更新の管理]**)。

3. **[ワークスペースの選択...]** ドロップダウン リストで、使用する Log Analytics ワークスペースを選択します。

4. 残りのすべての情報が正しいことを確認し、**[有効化]** をクリックしてソリューションを有効にします。

5. 手順 2 ～ 4 を繰り返して、3 つのソリューションをすべて有効にします。 

   [![](media/vm-update-management/1-sm.PNG "Automation アカウントの機能を有効にする")](media/vm-update-management/1-lg.PNG#lightbox)

### <a name="in-the-azure-stack-administration-portal"></a>Azure Stack 管理者ポータルで
Azure portal で Azure Automation のソリューションを有効にしたら、次に、クラウド管理者として Azure Stack 管理ポータルにサインインし、**[Azure Update and Configuration Management]\(Azure 更新および構成管理\)** 拡張機能 Azure Stack マーケットプレース項目をダウンロードします。 

   ![[Azure Update and Configuration Management]\(Azure 更新および構成管理\) 拡張機能マーケットプレース項目](media/vm-update-management/2.PNG) 

## <a name="enable-update-management-for-azure-stack-virtual-machines"></a>Update Management を Azure Stack 仮想マシンに対して有効にする
以下の手順のようにして、Azure Stack Windows VM の更新管理を有効にします。

1. Azure Stack ユーザー ポータルにログインします。

2. Azure Stack ユーザー ポータルで、ソリューションを有効にする Windows 仮想マシンの [拡張機能] ブレードに移動して、**[+ 追加]** をクリックし、**[Azure Update and Configuration Management]\(Azure 更新および構成管理\)** 拡張機能を選択して、**[作成]** をクリックします。

   [![](media/vm-update-management/3-sm.PNG "Windows VM 拡張機能ブレード")](media/vm-update-management/3-lg.PNG#lightbox)

3. 前に作成したワークスペース ID とプライマリ キーを入力して、エージェントと Log Analytics ワークスペースをリンクし、**[OK]** をクリックして拡張機能をデプロイします。

   [![](media/vm-update-management/4-sm.PNG "ワークスペース ID とキーの指定")](media/vm-update-management/4-lg.PNG#lightbox) 

4. [Automation Update Management のドキュメント](https://docs.microsoft.com/azure/automation/automation-update-management)の説明に従って、管理する各 VM に対して Update Management ソリューションを有効にする必要があります。 ワークスペースに報告するすべての VM でソリューションを有効にするには、**[更新の管理]** を選択し、**[マシンの管理]** をクリックして、**[使用可能なマシンと今後のマシンすべてで有効にします]** オプションを選択します。

   [![](media/vm-update-management/5-sm.PNG "ワークスペース ID とキーの指定")](media/vm-update-management/5-lg.PNG#lightbox) 

   > [!TIP]
   > この手順を繰り返して、ワークスペースに報告する Azure Stack の Windows VM で各ソリューションを有効にします。 
  
Azure Update and Configuration Management (Azure 更新および構成管理) 拡張機能が有効になると、管理対象の各 Windows VM で 1 日に 2 回スキャンが実行されます。 15 分ごとに Windows API が呼び出され、最後の更新時間を照会することで、状態が変化したかどうかが確認されます。 状態が変更された場合、コンプライアンス スキャンが開始されます。

スキャンされた VM は、Azure Automation アカウントの Update Management ソリューションに表示されます。 

   [![](media/vm-update-management/6-sm.PNG "ワークスペース ID とキーの指定")](media/vm-update-management/6-lg.PNG#lightbox) 

> [!IMPORTANT]
> 管理対象のコンピューターの更新されたデータがダッシュボードに表示されるまでに、30 分～ 6 時間かかる場合があります。

Azure Stack の Windows VM は、Azure VM と共に、スケジュールされた更新プログラムのデプロイに含めることができるようになります。

## <a name="enable-update-management-using-a-resource-manager-template"></a>Resource Manager テンプレートを使用して Update Management を有効にする
Azure Stack の Windows VM の数が多い場合は、[この Azure Resource Manager テンプレート](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win)を使用して、さらに簡単に Windows VM にソリューションをデプロイできます。 このテンプレートは、Microsoft Monitoring Agent 拡張機能を既存の Windows VM にデプロイし、既存の Azure LogAnalytics ワークスペースにそれを追加します。
 
## <a name="next-steps"></a>次の手順
[SQL Server のパフォーマンスを最適化する](azure-stack-sql-server-vm-considerations.md)
