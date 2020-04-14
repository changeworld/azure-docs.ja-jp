---
title: Azure Log Analytics ワークスペースの削除と復旧 | Microsoft Docs
description: 個人用の月額プランで Log Analytics ワークスペースを作成した場合や、ワークスペース モデルを再構築する場合に、Log Analytics ワークスペースを削除する方法について説明します。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/14/2020
ms.openlocfilehash: 1dceb3db4572ecdaf504745dba1099a5eccead43
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2020
ms.locfileid: "80395785"
---
# <a name="delete-and-recover-azure-log-analytics-workspace"></a>Azure Log Analytics ワークスペースの削除と復旧

この記事では、Azure Log Analytics ワークスペースの論理的な削除の概念および削除したワークスペースを回復させる方法について説明します。 

## <a name="considerations-when-deleting-a-workspace"></a>ワークスペースを削除するときの考慮事項

Log Analytics ワークスペースを削除すると、論理的な削除操作が実行されます。削除操作が不注意によるものであれ、意図的なものであれ、14 日以内であればそのデータや接続されているエージェントを含め、ワークスペースを回復させることができます。 論理的な削除期間が終了すると、ワークスペース リソースとそのデータは回復できなくなります。そのデータは完全な削除用のキューに格納され、30 日以内に完全に消去されます。 ワークスペース名は "リリース済み" であり、新しいワークスペースの作成に使用できます。

> [!NOTE]
> 論理的な削除の動作をオーバーライドして、ご利用のワークスペースを完全に削除する場合は、「[ワークスペースの完全削除](#permanent-workspace-delete)」の手順に従います。

重要なデータや構成が含まれているとサービスの運用に悪影響が生じるおそれがあるため、ワークスペースを削除する場合は注意が必要です。 エージェントやソリューションなど、Log Analytics にデータを格納する各種 Azure サービスとソースを再確認してください。そうしたサービスとソースの例を次に示します。

* 管理ソリューション
* Azure Automation
* Windows と Linux の仮想マシンで実行されているエージェント
* 環境内の Windows および Linux のコンピューターで実行されているエージェント
* System Center Operations Manager

論理的な削除操作では、ワークスペース リソースが削除され、関連するユーザーのアクセス許可がすべて破棄されます。 ユーザーは、他のワークスペースに関連付けられていれば、それらのワークスペースを使用して Log Analytics を使い続けることができます。

## <a name="soft-delete-behavior"></a>論理的な削除の動作

ワークスペースの削除操作では、ワークスペースの Resource Manager リソースが削除されますが、ワークスペースは見かけ上、削除されたように見えるだけで、その構成とデータは 14 日間維持されます。 ワークスペースに対してレポートするよう構成されたエージェントと System Center Operations Manager の管理グループは、論理的な削除の期間、孤立した状態で維持されます。 さらに、削除されたワークスペース (そのデータと接続されているリソースを含む) を回復させる、実質的には削除を元に戻すメカニズムも用意されています。

> [!NOTE] 
> インストールされているソリューションおよびリンクされたサービス (Azure Automation アカウントなど) は、削除時点でワークスペースから完全に削除され、回復させることはできません。 ワークスペースを以前に構成した状態に戻すには、回復操作の後で再構成する必要があります。

ワークスペースは、[PowerShell](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/remove-azurermoperationalinsightsworkspace?view=azurermps-6.13.0)、[REST API](https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete)、[Azure portal](https://portal.azure.com) のいずれかの方法で削除することができます。

### <a name="azure-portal"></a>Azure portal

1. サインインするには、[Azure portal](https://portal.azure.com) に移動します。 
2. Azure Portal で **[すべてのサービス]** を選択します。 リソースの一覧で、「**Log Analytics**」と入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **[Log Analytics ワークスペース]** を選択します。
3. Log Analytics ワークスペースの一覧でワークスペースを選択して、中央のウィンドウの上部にある **[削除]** をクリックします。
   ![ワークスペースのプロパティ ウィンドウの削除オプション](media/delete-workspace/log-analytics-delete-workspace.png)
4. ワークスペースの削除を確定するよう求める確認メッセージ ウィンドウが表示されたら、 **[はい]** をクリックします。
   ![ワークスペースの削除の確定](media/delete-workspace/log-analytics-delete-workspace-confirm.png)

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Remove-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
```

### <a name="troubleshooting"></a>トラブルシューティング

Log Analytics ワークスペースを削除するには、「Log Analytics 共同作成者」アクセス許可が必要です。<br>
ワークスペースの作成時に「*このワークスペース名は既に使用されています*」というエラーメッセージが表示された場合は、次の原因が考えられます。
* ワークスペース名を使用できず、組織内の誰か、または他の顧客によって使用されている。
* ワークスペースが過去 14 日以内に削除されており、その名前は論理的な削除期間にわたって予約されている。 論理的な削除をオーバーライドし、すぐにワークスペースを削除して同じ名前の新しいワークスペースを作成するには、次の手順に従って、最初にワークスペースを回復してから、完全な削除を実行します。<br>
   1. ワークスペースを[回復](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace#recover-workspace)します。
   2. ワークスペースを[完全に削除](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace#permanent-workspace-delete)します。
   3. 同じワークスペース名を使用して新しいワークスペースを作成します。


## <a name="permanent-workspace-delete"></a>ワークスペースの完全削除
開発やテストなどの一部のシナリオでは、同じ設定とワークスペース名を使用してデプロイを繰り返す必要があるため、論理的な削除方法は適さない場合があります。 このような場合は、ワークスペースを完全に削除し、論理的な削除期間を "オーバーライド" できます。 ワークスペースの完全削除を行うと、そのワークスペース名は解放され、同じ名前を使用して新しいワークスペースを作成できるようになります。


> [!IMPORTANT]
> ワークスペースの完全削除操作を行うと元に戻すことができず、ワークスペースとそのデータを復元できないため、注意して使用してください。

現在、ワークスペースの完全削除は REST API を使用して実行できます。

> [!NOTE]
> すべての API 要求には、要求ヘッダーにベアラー認証トークンを含める必要があります。
>
> 以下を使用してトークンを取得できます。
> - [アプリの登録](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens)
> - ブラウザーで、開発者コンソール (F12) を使用して Azure portal に移動します。 **[要求ヘッダー]** 下にある認証文字列について **batch?** インスタンスのいずれかを見つけます。 これは、*authorization:Bearer <token>* というパターンです。 次の例に示すように、これをコピーして API 呼び出しに追加します。
> - Azure REST ドキュメント サイトに移動します。 任意の API で **[使ってみる]** を押し、ベアラー トークンをコピーして API 呼び出しに追加します。
ワークスペースを完全に削除するには、「[ワークスペース - Delete]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete)」REST API 呼び出しを force タグと共に使用します。
>
> ```rst
> DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>?api-version=2015-11-01-preview&force=true
> Authorization: Bearer eyJ0eXAiOiJKV1Qi….
> ```
「eyJ0eXAiOiJKV1Qi...」 は 完全な承認トークンを表します。

## <a name="recover-workspace"></a>ワークスペースを回復させる

論理的な削除操作の前にワークスペースが関連付けられていたサブスクリプションとリソース グループに対する共同作成者のアクセス許可を持っているユーザーは、論理的な削除の期間中であれば、データと構成、接続されているエージェントを含め、ワークスペースを回復させることができます。 論理的な削除の期間が過ぎると、ワークスペースは回復不能となり、完全な削除の対象に割り当てられます。 論理的な削除期間中は、削除されたワークスペースの名前が維持されるため、新しいワークスペースを作成しようとするときにその名前を使用することはできません。  

以下のワークスペースの作成方法を使用して、ワークスペースを再作成することにより、復旧できます。使用するのは、[PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/New-AzOperationalInsightsWorkspace) または [REST API]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/createorupdate) です。ただし、以下のプロパティに、削除したワークスペースの詳細情報を設定する必要があります。

* サブスクリプション ID
* リソース グループ名
* ワークスペース名
* リージョン

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Select-AzSubscription "subscription-name-the-workspace-was-in"
PS C:\>New-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name-the-workspace-was-in" -Name "deleted-workspace-name" -Location "region-name-the-workspace-was-in"
```

ワークスペースとそのすべてのデータは、復旧操作の後に戻されます。 ソリューションおよびリンクされたサービスは、ワークスペースが削除されたときに完全に削除されます。ワークスペースを以前に構成した状態に戻すには、これらを再構成する必要があります。 ワークスペースの復旧後、関連するソリューションが再インストールされ、そのスキーマがワークスペースに追加されるまで、一部のデータがクエリに使用できないことがあります。

> [!NOTE]
> * [Azure portal](https://portal.azure.com) ではワークスペースの回復はサポートされていません。 
> * 論理的な削除期間中にワークスペースを再作成すると、このワークスペース名が既に使用中であることが表示されます。 
> 
