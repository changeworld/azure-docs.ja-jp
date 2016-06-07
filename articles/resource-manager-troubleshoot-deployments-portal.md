<properties
   pageTitle="ポータルでのデプロイ操作の表示 | Microsoft Azure"
   description="Azure ポータルを使用して、リソース マネージャーのデプロイからのエラーを検出する方法について説明します。"
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="05/19/2016"
   ms.author="tomfitz"/>

# Azure ポータルでのデプロイ操作の表示

> [AZURE.SELECTOR]
- [ポータル](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [Azure CLI](resource-manager-troubleshoot-deployments-cli.md)
- [REST API](resource-manager-troubleshoot-deployments-rest.md)

Azure にリソースをデプロイするときにエラーが発生した場合、実行したデプロイ操作に関して、より詳しい情報が必要になることがあります。Azure ポータルでは、簡単にエラーを見つけ、可能性のある修正を確認することができるように、インターフェイスを提供します。

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

## 監査ログを使用してトラブルシューティングを行う

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

デプロイのエラーを表示するには、次の手順を使用します。

1. **[参照]**、**[監査ログ]** の順に選択して、ポータルで監査ログを表示します。

    ![監査ログの選択](./media/resource-manager-troubleshoot-deployments-portal/select-audit-logs.png)

2. **[監査ログ]** ブレードで、サブスクリプション内のすべてのリソース グループに対する最近の操作の概要が表示されます。これには、時間のグラフィック表示、操作の状態、および操作の一覧が含まれます。

    ![アクションの表示](./media/resource-manager-troubleshoot-deployments-portal/audit-summary.png)

3. 一覧で任意の操作を選択できます。調査するエラーを含む操作を選択します。

    ![操作の選択](./media/resource-manager-troubleshoot-deployments-portal/select-operation.png)
  
4. 操作のすべてのイベントが表示されます。サマリーの **[関連付け ID]** を確認します。この ID は、関連するイベントを追跡するために使用されます。これは、問題のトラブルシューティングを行うためにテクニカル サポートと共に作業を行うときに、役立つ場合があります。イベントのいずれかを選択して、イベントに関する詳細を表示することができます。

    ![イベントの選択](./media/resource-manager-troubleshoot-deployments-portal/select-event.png)

5. イベントに関する詳細が表示されます。特に、エラーに関する情報の **[プロパティ]** に注意します。

    ![監査ログの詳細の表示](./media/resource-manager-troubleshoot-deployments-portal/audit-details.png)

特定の条件に注目するために、監査ログの表示をフィルター処理することができます。監査ログの表示をカスタマイズするには

1. **[監査ログ]** ブレードの上部にある **[フィルター]** を選択します。

    ![ログのフィルター](./media/resource-manager-troubleshoot-deployments-portal/filter-logs.png)

2. **[フィルター]** ブレードで、条件を選択して、監査ログの表示を表示する操作のみに制限します。たとえば、操作をフィルター処理して、特定のリソース グループのエラーのみを表示することができます。

    ![フィルター オプションの設定](./media/resource-manager-troubleshoot-deployments-portal/set-filter.png)

3. 時間間隔を設定して、操作をさらにフィルター処理できます。次の図では、特定の 20 分間にビューをフィルター処理します。

    ![時間の設定](./media/resource-manager-troubleshoot-deployments-portal/select-time.png)

監査ログのビューを更新すると、指定した条件に一致する操作のみが表示されます。これらの設定は、次に監査ログを表示するまで保持されるため、操作のビューの範囲を広げるために、これらの値を変更する必要がある場合があります。

デプロイが失敗した理由を確認できていれば幸いです。次のセクションで示すように、状態に関する情報のデプロイ操作を検索することもできます。

## デプロイ操作を使用してトラブルシューティングを行う

デプロイ操作を表示するには、次の手順に従います。

1. デプロイに含まれるリソース グループには、最後のデプロイの状態を確認します。この状態を選択して、詳細を取得することができます。

    ![デプロイの状態](./media/resource-manager-troubleshoot-deployments-portal/deployment-status.png)

2. デプロイの最近の履歴が表示されます。失敗したデプロイを選択します。

    ![デプロイの状態](./media/resource-manager-troubleshoot-deployments-portal/select-deployment.png)

3. デプロイに関する情報を表示し、失敗した操作を選択して、エラーに関する詳細を表示します。

    ![失敗したデプロイの表示](./media/resource-manager-troubleshoot-deployments-portal/view-failed-deployment.png)

4. **[操作の詳細]** ブレードに、失敗した操作に関する情報が表示されます。特に、ステータス メッセージに注意してください。

    ![ステータス メッセージの表示](./media/resource-manager-troubleshoot-deployments-portal/operations-status.png)



## 次のステップ

- 特定のデプロイ エラーの解決については、「[Azure Resource Manager を使用してリソースを Azure にデプロイするときに発生する一般的なエラーを解決する](resource-manager-common-deployment-errors.md)」を参照してください。
- 他の種類のアクションを監視するために監査ログを使用する方法については、「[Resource Manager の監査操作](resource-group-audit.md)」を参照してください。
- デプロイを実行する前に検証するには、「[Azure Resource Manager のテンプレートを使用したリソースのデプロイ](resource-group-template-deploy.md)」を参照してください。

<!---HONumber=AcomDC_0525_2016-->