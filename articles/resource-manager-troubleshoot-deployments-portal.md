---
title: ポータルでのデプロイ操作の表示 | Microsoft Docs
description: Azure ポータルを使用して、リソース マネージャーのデプロイからのエラーを検出する方法について説明します。
services: azure-resource-manager,virtual-machines
documentationcenter: ''
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn

ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: infrastructure
ms.date: 06/15/2016
ms.author: tomfitz

---
# Azure ポータルでのデプロイ操作の表示
> [!div class="op_single_selector"]
> * [ポータル](resource-manager-troubleshoot-deployments-portal.md)
> * [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
> * [Azure CLI](resource-manager-troubleshoot-deployments-cli.md)
> * [REST API](resource-manager-troubleshoot-deployments-rest.md)
> 
> 

デプロイの操作は、Azure ポータルから確認することができます。最も一般的な確認の対象としては、デプロイ中にエラーが発生したときに実行されていた操作が挙げられます。この記事では、失敗した操作の表示について重点的に取り上げます。Azure ポータルには、すぐにエラーを見つけ出し、有効と考えられる解決策を特定できるインターフェイスが用意されています。

[!INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

## デプロイ操作を使用してトラブルシューティングを行う
デプロイ操作を表示するには、次の手順に従います。

1. デプロイに含まれるリソース グループには、最後のデプロイの状態を確認します。この状態を選択して、詳細を取得することができます。
   
    ![デプロイの状態](./media/resource-manager-troubleshoot-deployments-portal/deployment-status.png)
2. デプロイの最近の履歴が表示されます。失敗したデプロイを選択します。
   
    ![デプロイの状態](./media/resource-manager-troubleshoot-deployments-portal/select-deployment.png)
3. **[失敗しました。詳細については、ここをクリックしてください]** を選択して、なぜデプロイに失敗したかの説明を表示します。以下の画像を見ると、DNS レコードの重複が原因であることがわかります。
   
    ![失敗したデプロイの表示](./media/resource-manager-troubleshoot-deployments-portal/view-error.png)
   
    このエラー メッセージは、トラブルシューティングの糸口をつかむうえでは十分でしょう。しかし、どのようなタスクが実行されたのかについて、もっと詳しい情報が必要である場合は、以降の手順に従って操作を確認することができます。
4. すべてのデプロイ操作は、**[デプロイ]** ブレードで表示することができます。いずれかの操作を選択すると、さらに詳しい情報が表示されます。
   
    ![view operations](./media/resource-manager-troubleshoot-deployments-portal/view-operations.png)
   
    このケースでは、ストレージ アカウント、仮想ネットワーク、可用性セットは正しく作成されていることが確認できます。パブリック IP アドレスはエラーとなり、他のリソースは実行されていません。
5. デプロイのイベントは、**[イベント]** を選択して表示できます。
   
    ![view events](./media/resource-manager-troubleshoot-deployments-portal/view-events.png)
6. デプロイに関するイベントをすべて表示し、いずれかを選択すると、さらに詳しい情報が表示されます。
   
    ![see events](./media/resource-manager-troubleshoot-deployments-portal/see-all-events.png)

## 監査ログを使用してトラブルシューティングを行う
[!INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

デプロイのエラーを表示するには、次の手順を使用します。

1. **[監査ログ]** を選択してリソース グループの監査ログを表示します。
   
    ![監査ログの選択](./media/resource-manager-troubleshoot-deployments-portal/select-audit-logs.png)
2. **[監査ログ]** ブレードで、サブスクリプション内のすべてのリソース グループに対する最近の操作の概要が表示されます。これには、時間のグラフィック表示、操作の状態、および操作の一覧が含まれます。
   
    ![アクションの表示](./media/resource-manager-troubleshoot-deployments-portal/audit-summary.png)
3. 特定の条件に注目するために、監査ログの表示をフィルター処理することができます。**[監査ログ]** ブレードの上部にある **[フィルター]** を選択します。
   
    ![ログのフィルター](./media/resource-manager-troubleshoot-deployments-portal/filter-logs.png)
4. **[フィルター]** ブレードで条件を選択し、目的の操作だけが表示されるよう監査ログの表示を制限します。たとえば、操作をフィルター処理して、特定のリソース グループのエラーのみを表示することができます。
   
    ![フィルター オプションの設定](./media/resource-manager-troubleshoot-deployments-portal/set-filter.png)
5. 時間間隔を設定して、操作をさらにフィルター処理できます。次の図では、特定の 20 分間にビューをフィルター処理します。
   
    ![時間の設定](./media/resource-manager-troubleshoot-deployments-portal/select-time.png)
6. 一覧で任意の操作を選択できます。調査するエラーを含む操作を選択します。
   
    ![操作の選択](./media/resource-manager-troubleshoot-deployments-portal/select-operation.png)
7. 操作のすべてのイベントが表示されます。サマリーの **[関連付け ID]** を確認します。この ID は、関連するイベントを追跡するために使用されます。これは、問題のトラブルシューティングを行うためにテクニカル サポートと共に作業を行うときに、役立つ場合があります。イベントのいずれかを選択して、イベントに関する詳細を表示することができます。
   
    ![イベントの選択](./media/resource-manager-troubleshoot-deployments-portal/select-event.png)
8. イベントに関する詳細が表示されます。特に、エラーに関する情報の **[プロパティ]** に注意します。
   
    ![監査ログの詳細の表示](./media/resource-manager-troubleshoot-deployments-portal/audit-details.png)

監査ログに適用したフィルターは、次回その監査ログを表示するまで保持されるため、操作のビューの範囲を広げるために、これらの値を変更する必要がある場合があります。

## 次のステップ
* 特定のデプロイ エラーの解決については、[Azure Resource Manager を使用してリソースを Azure にデプロイするときに発生する一般的なエラーの解決](resource-manager-common-deployment-errors.md)に関するページを参照してください。
* 他の種類のアクションを監視するために監査ログを使用する方法については、「[Resource Manager の監査操作](resource-group-audit.md)」を参照してください。
* デプロイを実行する前に検証するには、[Azure Resource Manager テンプレートを使用したリソース グループのデプロイ](resource-group-template-deploy.md)に関するページを参照してください。

<!---HONumber=AcomDC_0622_2016-->