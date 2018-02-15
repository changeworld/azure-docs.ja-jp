---
title: "Azure 管理ソリューション | Microsoft Docs"
description: "管理ソリューションには、Azure のパッケージ化された管理シナリオが含まれており、お客様はそれを Log Analytics ワークスペースに追加できます。  この記事では、お客様およびパートナーによるカスタム ソリューションの作成方法の詳細を説明します。"
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 1f054a4e-6243-4a66-a62a-0031adb750d8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2b9ad6da3963fefc5441581d113f6f690bd72be0
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2018
---
# <a name="working-with-management-solutions-in-azure-preview"></a>Azure での管理ソリューションの使用 (プレビュー)
> [!NOTE]
> この記事は、現在プレビュー段階である Azure の管理ソリューションに関する暫定版ドキュメントです。    
> 
> 

管理ソリューションには、パッケージ化された管理シナリオが含まれており、お客様はそれを Azure 環境に追加できます。  パートナーとお客様は、[Microsoft 提供のソリューション](../log-analytics/log-analytics-add-solutions.md)とは別に、自身の環境で使用する管理ソリューションを作成したり、作成したソリューションをコミュニティを通じて公開したりすることができます。

## <a name="finding-and-installing-management-solutions"></a>管理ソリューションの検索とインストール
管理ソリューションを検索してインストールする方法は、以下の各セクションで示すように複数あります。

### <a name="azure-marketplace"></a>Azure Marketplace
Microsoft および信頼されたパートナーが提供する管理ソリューションは、Azure Portal の Azure Marketplace でインストールできます。

1. Azure ポータルにログインします。
2. 左側のウィンドウで、**[その他のサービス]** を選択します。
3. **[ソリューション]** までスクロールするか、**[フィルター]** ダイアログに「*ソリューション*」と入力します。
4. **[+ 追加]** ボタンをクリックします。
5. 興味のあるソリューションを検索します。検索は、ブラウジングするか、**[フィルター]** ボタンをクリックするか、または **[Search Everthing]** (全ソリューションを検索) ボックスに入力して行います。
6. Marketplace のアイテムをクリックして詳細情報を確認します。
7. **[作成]** をクリックして、**[ソリューションの追加]** ウィンドウを開きます。
8. ソリューションのパラメーターの値に加えて、[Log Analytics ワークスペースや Automation アカウント](#log-analytics-workspace-and-automation-account)などの必要な情報を入力するように求められます。
9. **[作成]** をクリックして、ソリューションをインストールします。

### <a name="oms-portal"></a>OMS ポータル
Microsoft 提供の管理ソリューションは、OMS ポータルのソリューション ギャラリーからインストールすることもできます。

1. OMS ポータルにログインします。
2. **[ソリューション ギャラリー]** タイルをクリックします。
3. OMS ソリューション ギャラリー ページでは、利用可能な各ソリューションの詳細を確認できます。 追加するソリューションの名前をクリックします。
4. 選択したソリューションのページに、ソリューションに関する詳細情報が表示されます。 **[追加]**をクリックします。
5. 追加したソリューションの新しいタイルが、ポータルの [概要] ページに表示されます。Log Analytics によってデータが処理された後、ソリューションを使えるようになります。

### <a name="azure-quickstart-templates"></a>Azure のクイック スタート テンプレート
コミュニティのメンバーは、Azure クイック スタート テンプレートに管理ソリューションを提出できます。  こうしたテンプレートは、ダウンロードしてインストールすることも、[独自のソリューションを作成する](#creating-a-solution)方法の参考にすることもできます。

1. 「[Log Analytics ワークスペースと Automation アカウント](#log-analytics-workspace-and-automation-account)」で説明されているプロセスに従って、ワークスペースとアカウントをリンクします。
2. [Azure クイックスタート テンプレート](https://azure.microsoft.com/documentation/templates/)にアクセスします。  
3. 興味のあるソリューションを検索します。
4. 検索結果のソリューションを選択して、詳細を確認します。
5. **[Deploy to Azure]** (Azure にデプロイ) ボタンをクリックします。
6. ソリューションのパラメーターの値に加えて、リソース グループや場所などの必須情報を入力するように求められます。
7. **[購入]** をクリックして、ソリューションをインストールします。

### <a name="deploy-azure-resource-manager-template"></a>Azure Resource Manager テンプレートのデプロイ
コミュニティで入手したソリューションや[独自に作成した](#creating-a-solution)ソリューションは、Resource Manager テンプレートとして実装されるため、標準的な[テンプレートのデプロイ方法](../azure-resource-manager/resource-group-template-deploy-portal.md)のいずれかを使用できます。  ソリューションをインストールする前に、[Log Analytics ワークスペースと Automation アカウント](#log-analytics-workspace-and-automation-account)を作成してリンクする必要があることに注意してください。

## <a name="log-analytics-workspace-and-automation-account"></a>Log Analytics ワークスペースと Automation アカウント
ほとんどの管理ソリューションでは、ビューを格納するために [Log Analytics ワークスペース](../log-analytics/log-analytics-manage-access.md)が、Runbook と関連リソースを格納するために [Automation アカウント](../automation/automation-security-overview.md#automation-account-overview)が必要です。 ワークスペースとアカウントは、次の要件を満たす必要があります。

* ソリューションで使うことができる Log Analytics ワークスペースと Automation アカウントは、それぞれ 1 つのみです。  
* ソリューションで使う Log Analytics ワークスペースと Automation アカウントは、互いにリンクさせる必要があります。 1 つの Automation アカウントにリンクできる Log Analytics ワークスペースは 1 つのみであり、同様に 1 つの Log Analytics ワークスペースにリンクできる Automation アカウントは 1 つのみです。
* Log Analytics ワークスペースと Automation アカウントをリンクさせるには、これらのリソース グループとリージョンが同じである必要があります。  例外として、米国東部リージョンのワークスペースと米国東部 2 の Automation アカウントはリンク可能です。

### <a name="creating-a-link-between-a-log-analytics-workspace-and-automation-account"></a>Log Analytics ワークスペースと Automation アカウント間のリンクの作成
Log Analytics ワークスペースと Automation アカウントを指定する方法は、ソリューションのインストール方法によって異なります。

* OMS ポータルから Microsoft のソリューションをインストールする場合、ソリューションは現在のワークスペースにインストールされるため、Automation アカウントは求められません。
* Azure Marketplace からソリューションをインストールする場合は、ワークスペースと Automation アカウントが求められますが、これらの間のリンクは自動で作成されます。  
* Azure Marketplace 外部のソリューションについては、ソリューションのインストール前に Log Analytics ワークスペースと Automation アカウントをリンクさせる必要があります。  これを行うには、Azure Marketplace で任意のソリューションを選んでから、Log Analytics ワークスペースと Automation アカウントを選びます。  Log Analytics ワークスペースと Automation アカウントを選ぶとすぐにリンクが作成されるため、実際にソリューションをインストールする必要はありません。  リンクが作成されると、すべてのソリューションで選んだ Log Analytics ワークスペースと Automation アカウントを使えるようになります。 

### <a name="verifying-the-link-between-a-log-analytics-workspace-and-automation-account"></a>Log Analytics ワークスペースと Automation アカウント間のリンクの検証
Log Analytics ワークスペースと Automation アカウント間のリンクは、次の方法で確認できます。

1. Azure Portal で Automation アカウントを選択します。
2. メニューの **[関連リソース]** セクションの **[ワークスペース]** の設定が有効になっている場合、このアカウントは Log Analytics ワークスペースに関連付けられています。  **[ワークスペース]** をクリックして、ワークスペースの詳細を表示できます。

## <a name="listing-management-solutions"></a>管理ソリューションの一覧表示
Azure サブスクリプションにリンクされているワークスペース内の管理ソリューションを確認するには、次の手順に従います。

1. Azure ポータルにログインします。
2. 左側のウィンドウで、**[その他のサービス]** を選択します。
3. **[ソリューション]** までスクロールするか、**[フィルター]** ダイアログに「*ソリューション*」と入力します。
4. すべてのワークスペースにインストールされているソリューションが表示されます。

表示されるのは、OMS ポータルを使用して現在のワークスペースにインストールされた Microsoft ソリューションのみであることに注意してください。

## <a name="removing-a-management-solution"></a>管理ソリューションの削除
管理ソリューションが削除されると、そのソリューション内のすべてのリソースも削除されます。  

1. [ソリューションの一覧表示](#listing-solutions)に関するセクションの手順に従って、削除するソリューションを Azure Portal で検索します。
2. 削除するソリューションを選択します。
3. **[削除]** ボタンをクリックします。

## <a name="creating-a-management-solution"></a>管理ソリューションの作成
管理ソリューションの作成に関する完全なガイダンスについては、「[Creating solutions in Operations Management Suite (OMS)](operations-management-suite-solutions-creating.md)」(Operations Management Suite (OMS) でのソリューションの作成) をご覧ください。 

## <a name="next-steps"></a>次の手順
* Resource Manager テンプレートの様々なサンプルは、[Azure クイックスタート テンプレート](https://azure.microsoft.com/documentation/templates) で検索できます。
* 独自の[管理ソリューション](operations-management-suite-solutions-creating.md)を作成する。

