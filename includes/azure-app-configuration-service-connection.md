---
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: include
ms.date: 05/03/2021
ms.openlocfilehash: 7b35a3c4b26640b872b161bd8130db728645f1d0
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2021
ms.locfileid: "108748299"
---
[サービス接続](/azure/devops/pipelines/library/service-endpoints)により、Azure DevOps プロジェクトから Azure サブスクリプション内のリソースにアクセスできます。

1. Azure DevOps で、ターゲット パイプラインを含むプロジェクトに移動します。 左下隅にある **[プロジェクトの設定]** を選択します。
1. **[パイプライン]** で、**[サービス接続]** を選択します。 右上隅の **[新しいサービス接続]** を選択します。
1. **[新しいサービス接続]** で、 **[Azure Resource Manager]** を選択します。

    :::image type="content" source="./media/azure-app-configuration-service-connection/new-service-connection.png" alt-text="[新しいサービス接続] ドロップダウン リストからの [Azure Resource Manager] の選択を示すスクリーンショット。":::
1. **[認証方法]** ダイアログで、 **[サービス プリンシパル (自動)]** を選択して新しいサービス プリンシパルを作成するか、 **[サービス プリンシパル (手動)]** を選択して [既存のサービス プリンシパルを使用](/azure/devops/pipelines/library/connect-to-azure?view=azure-devops#use-spn&preserve-view=true)します。
1. ご自分のサブスクリプション、リソース、サービス接続の名前を入力します。

新しいサービス プリンシパルを作成した場合、サービス接続に割り当てられているサービス プリンシパルの名前を探します。 次の手順では、このサービス プリンシパルに新しいロールの割り当てを追加します。

1. **[プロジェクトの設定]**  >  **[サービス接続]** の順に移動します。
1. 新しいサービス接続を選択します。
1. **[サービス プリンシパルの管理]** を選択します。
1. **[表示名]** の値をメモします。

    :::image type="content" source="./media/azure-app-configuration-service-connection/service-principal-display-name.png" alt-text="サービス プリンシパルの表示名を示すスクリーンショット。":::
