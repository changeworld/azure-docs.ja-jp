---
title: Azure Monitor の管理ソリューション | Microsoft Docs
description: Azure Monitor の管理ソリューションには、特定の問題点に関するメトリックを提供するロジックや視覚化、データ取得規則が集約されています。  この記事では、管理ソリューションのインストールと使用について説明します。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: f029dd6d-58ae-42c5-ad27-e6cc92352b3b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/07/2018
ms.author: bwren
ms.openlocfilehash: 574391fd64d0525d3d77b80bb488bea8109729af
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53187336"
---
# <a name="management-solutions-in-azure-monitor"></a>Azure Monitor の管理ソリューション
管理ソリューションは、Azure のサービスを活用して、特定のアプリケーションまたはサービスの操作に対して詳細な分析情報を提供します。 この記事では、Azure の管理ソリューションの簡単な概要と、管理ソリューションの使用とインストールの詳細について説明します。

通常、管理ソリューションではログ データが収集されます。また、収集されたデータを分析するクエリとビューが用意されています。 また、Azure Automation などの他のサービスを活用して、アプリケーションまたはサービスに関連した操作を実行することも可能です。

使用する任意のアプリケーションおよびサービスの Azure Monitor に管理ソリューションを追加できます。 管理ソリューションは通常、無料で使用できますが、データの収集に対して利用料金が発生する場合があります。 パートナーとお客様は、Microsoft 提供のソリューションとは別に、自身の環境で使用する[管理ソリューションを作成](solutions-creating.md)したり、作成したソリューションをコミュニティを通じて公開したりすることができます。

## <a name="use-management-solutions"></a>管理ソリューションを使用する
Log Analytics ワークスペースの **[概要]** ページを開くと、ワークスペースにインストールされた各ソリューションのタイルが表示されます。 

1. Azure ポータルにログインします。
1. **[すべてのサービス]** を開き、**[モニター]** を探します。
1. **[Insights]** メニューの **[詳細]** を選択します。
1. 画面上部のドロップダウン ボックスを使用して、タイルに使用するワークスペースまたは時間の範囲を変更します。
1. ソリューションのタイルをクリックすると、収集されたデータに対するより詳細な分析を含むビューが開かれます。

![概要](media/solutions/overview.png)

管理ソリューションには、複数の種類の Azure リソースが含まれる可能性があり、ソリューションに含まれる任意のリソースは、他のリソースと同様に表示できます。 たとえば、ソリューションに含まれるすべてのログ クエリは、[クエリ エクスプローラー](../log-query/get-started-portal.md#load-queries)の **[ソリューション クエリ]** に一覧表示されます。Log Analytics でアドホック分析を実行するときにこれらのクエリを使用できます。

## <a name="list-installed-management-solutions"></a>インストールされた管理ソリューションの一覧表示 
サブスクリプションにインストールされている管理ソリューションを一覧表示するには、次の手順を使用します。

1. Azure ポータルにログインします。
1. **[すべてのサービス]** を開き、**[ソリューション]** を探します。
4. すべてのワークスペースにインストールされているソリューションが一覧表示されます。 ソリューション名の後に、そのソリューションがインストールされている Log Analytics ワークスペースの名前が続けて表示されます。
1. 画面上部のドロップダイン ボックスを使用して、サブスクリプションまたはリソース グループごとにフィルター処理します。


![すべてのソリューションの一覧表示](media/solutions/list-solutions-all.png)

ソリューション名をクリックして、ソリューションの概要ページを開きます。 このページには、ソリューションに含まれるすべてのビューが表示されます。またソリューション自体とソリューションのワークスペースに対してさまざまなオプションが用意されています。 ソリューションを一覧表示する上記のいずれかの手順を使用して、ソリューションの概要ページを表示し、ソリューションの名前をクリックします。

![ソリューションのプロパティ](media/solutions/solution-properties.png)



## <a name="install-a-management-solution"></a>管理ソリューションのインストール
Microsoft およびパートナーの管理ソリューションは、[Azure Marketplace](https://azuremarketplace.microsoft.com) から入手できます。 次の手順を使用して、使用可能なソリューションを検索し、インストールできます。 ソリューションをインストールするときに、ソリューションがインストールされる [Log Analytics ワークスペース](../platform/manage-access.md)と、データが収集される場所を選択する必要があります。

1. [お使いのサブスクリプションのソリューションの一覧](#list-installed-monitoring-solutions)で、**[追加]** をクリックします。 
1. **[管理ソリューション]** の右側の **[More (その他)]** をクリックします。 
1. 目的の管理ソリューションを見つけ、その説明を読みます。
1. **[作成]** をクリックして、インストール プロセスを開始します。
1. インストール プロセスが開始されると、ソリューションごとに異なる必要な構成を提供するように求められます。

![ソリューションをインストールする](media/solutions/install-solution.png)

### <a name="install-a-solution-from-the-community"></a>コミュニティからソリューションをインストールする
コミュニティのメンバーは、Azure クイック スタート テンプレートに管理ソリューションを提出できます。 これらのソリューションを直接インストールするか、または後でインストールするためにテンプレートにダウンロードできます。

1. 「[Log Analytics ワークスペースと Automation アカウント](#log-analytics-workspace-and-automation-account)」で説明されているプロセスに従って、ワークスペースとアカウントをリンクします。
2. [Azure クイックスタート テンプレート](https://azure.microsoft.com/documentation/templates/)にアクセスします。 
3. 興味のあるソリューションを検索します。
4. 検索結果のソリューションを選択して、詳細を確認します。
5. **[Deploy to Azure]** (Azure にデプロイ) ボタンをクリックします。
6. ソリューションのパラメーターの値に加えて、リソース グループや場所などの必須情報を入力するように求められます。
7. **[購入]** をクリックして、ソリューションをインストールします。


## <a name="log-analytics-workspace-and-automation-account"></a>Log Analytics ワークスペースと Automation アカウント
ソリューションによって収集されたデータを格納し、ログ検索とビューをホストするために、すべての管理ソリューションで [Log Analytics ワークスペース](../platform/manage-access.md)を必要とします。 また、一部のソリューションでは、Runbook と関連するリソースを格納するために、[Automation アカウント](../../automation/automation-security-overview.md#automation-account-overview)も必要になります。 ワークスペースとアカウントは、次の要件を満たす必要があります。

* ソリューションの各インストールで使うことができる Log Analytics ワークスペースと Automation アカウントは、それぞれ 1 つのみです。 複数のワークスペースにソリューションを個別にインストールできます。
* ソリューションに Automation アカウントが必要な場合は、Log Analytics ワークスペースと Automation アカウントを互いにリンクさせる必要があります。 1 つの Automation アカウントにリンクできる Log Analytics ワークスペースは 1 つのみであり、同様に 1 つの Log Analytics ワークスペースにリンクできる Automation アカウントは 1 つのみです。
* Log Analytics ワークスペースと Automation アカウントをリンクさせるには、これらのリソース グループとリージョンが同じである必要があります。 例外として、米国東部リージョンのワークスペースと米国東部 2 の Automation アカウントはリンク可能です。

### <a name="create-a-link-between-a-log-analytics-workspace-and-automation-account"></a>Log Analytics ワークスペースと Automation アカウント間のリンクを作成する
Log Analytics ワークスペースと Automation アカウントを指定する方法は、ソリューションのインストール方法によって異なります。

* Azure Marketplace からソリューションをインストールする場合は、ワークスペースと Automation アカウントが求められます。 まだリンクされていない場合は、これらの間にリンクが作成されます。
* Azure Marketplace 外部のソリューションについては、ソリューションのインストール前に Log Analytics ワークスペースと Automation アカウントをリンクさせる必要があります。 これを行うには、Azure Marketplace で任意のソリューションを選んでから、Log Analytics ワークスペースと Automation アカウントを選びます。 Log Analytics ワークスペースと Automation アカウントを選ぶとすぐにリンクが作成されるため、実際にソリューションをインストールする必要はありません。 リンクが作成されると、すべてのソリューションで選んだ Log Analytics ワークスペースと Automation アカウントを使えるようになります。

### <a name="verify-the-link-between-a-log-analytics-workspace-and-automation-account"></a>Log Analytics ワークスペースと Automation アカウント間のリンクを検証する
Log Analytics ワークスペースと Automation アカウント間のリンクは、次の方法で確認できます。

1. Azure Portal で Automation アカウントを選択します。
1. メニューの **[関連リソース]** セクションまでスクロールします。
1. **[ワークスペース]** 設定が有効になっている場合、このアカウントは Log Analytics ワークスペースにリンクされます。 **[ワークスペース]** をクリックして、ワークスペースの詳細を表示できます。

## <a name="remove-a-management-solution"></a>管理ソリューションの削除
インストール済みのソリューションを削除するには、[インストールされているソリューションの一覧](#list-installed-monitoring-solutions)で検索します。 ソリューション名をクリックして [概要] ページを開き、**[削除]** をクリックします。




## <a name="next-steps"></a>次の手順
* [Microsoft の管理ソリューションの一覧](solutions-inventory.md)を取得します。
* [クエリを作成](../../azure-monitor/log-query/log-query-overview.md)して、管理ソリューションで収集したデータを分析する方法を確認します。

