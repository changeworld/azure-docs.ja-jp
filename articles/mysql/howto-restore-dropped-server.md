---
title: 削除した Azure Database for MySQL サーバーを復元する
description: この記事では、Azure Portal を使用して Azure Database for MySQL で削除されたサーバーを復元する方法について説明します。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 10/09/2020
ms.openlocfilehash: 5fc1ab1b3dfbc324668873749c143846c2015cd4
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107306281"
---
# <a name="restore-a-deleted-azure-database-for-mysql-server"></a>削除した Azure Database for MySQL サーバーを復元する

サーバーが削除された場合、データベース サーバーのバックアップはサービス内で最大 5 日間保持できます。 データベースのバックアップは、元々サーバーをホストしている Azure サブスクリプションからのみアクセスおよび復元できます。 次の推奨手順に従うと、サーバーが削除されてから 5 日以内に削除された MySQL サーバーを復旧させることができます。 推奨される手順は、サーバーのバックアップがまだ使用可能であり、システムから削除されていない場合にのみ機能します。 

## <a name="pre-requisites"></a>前提条件
削除された Azure Database for MySQL サーバーを復元するには、次の手順を行う必要があります。
- 元のサーバーをホストしている Azure サブスクリプションの名前
- サーバーが作成された場所

## <a name="steps-to-restore"></a>復元の手順

1. Azure portal のモニター ブレイドから[アクティビティ ログ](https://ms.portal.azure.com/#blade/Microsoft_Azure_ActivityLog/ActivityLogBlade)に移動します 

2. アクティビティ ログで、次のように **[Add filter (フィルターの追加)]** をクリックし、次のフィルターを設定します。 

    - **サブスクリプション** = 削除されたサーバーをホストしているサブスクリプション
    - **リソース タイプ** = Azure Database for MySQL servers (Microsoft.DBforMySQL/servers) 
    - **操作** = Delete MySQL Server (Microsoft.DBforMySQL/servers/delete) 
 
     [![MySQL サーバーの削除操作のためにフィルター処理されたアクティビティログ](./media/howto-restore-dropped-server/activity-log.png)](./media/howto-restore-dropped-server/activity-log.png#lightbox)
   
 3. [MySQL サーバーの削除] イベントをダブルクリックし、[JSON] タブをクリックして、JSON 出力の "resourceId" 属性と "submissionTimestamp" 属性を記録します。 ResourceId の形式は次のとおりです:/subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/TargetResourceGroup/providers/Microsoft.DBforMySQL/servers/deletedserver.
 
 4. 次に、[サーバー REST API の作成ページ](/rest/api/mysql/servers/create)に移動し、緑色で強調表示されている [使ってみる] タブを クリックして、Azure アカウントでログインします
 
 5. ステップ 3 でキャプチャした resourceId 属性から得られる resourceGroupName、serverName (削除されたサーバーの名前)、subscriptionId を指定します。画像に示されるように、API のバージョンはあらかじめ設定されています。
 
     [![REST API を使用したサーバーの作成](./media/howto-restore-dropped-server/create-server-from-rest-api.png)](./media/howto-restore-dropped-server/create-server-from-rest-api.png#lightbox)
  
 6. 要求本文セクションの下にスクロールして、以下を貼り付けます。
 
    ```json
    {
        "location": "Dropped Server Location",  
        "properties": 
            {
                "restorePointInTime": "submissionTimestamp - 15 minutes",
                "createMode": "PointInTimeRestore",
                "sourceServerId": "resourceId"
            }
    }
    ```
7. 上記の要求本文にある次の値を置き換えます。
   * "Dropped server Location" を、削除されたサーバーが最初に作成された Azure リージョンに
   * "submissionTimestamp" と "resourceId" をステップ 3 で取得した値に 
   * restorePointInTime については、コマンドがエラーにならないように、submissionTimestamp の値から **15 分** だけ引いた値を指定します。
   
8. 応答コード 201 または 202 が表示された場合は、復元要求が正常に送信されています。 

9. サーバーの作成には、元のサーバーでプロビジョニングされたデータベースのサイズとコンピューティング リソースによって時間がかかることがあります。 復元の状態は、次をフィルター処理することによって、アクティビティ ログから監視できます。 
   - **サブスクリプション** = 自分のサブスクリプション
   - **リソース タイプ** = Azure Database for MySQL servers (Microsoft.DBforMySQL/servers) 
   - **操作** = MySQL サーバー作成の更新

## <a name="next-steps"></a>次のステップ
- 5 日以内にサーバーを復元しようとしても、前に説明した手順に正確に従ってエラーが発生する場合は、サポート インシデントを開いてください。 削除されたサーバーを 5 日後に復元しようとすると、バックアップファイルが見つからないため、エラーが発生します。 このシナリオではサポート チケットを開かないでください。 バックアップがシステムから削除された場合、サポート チームはサポートを提供できません。 
- サーバーが誤って削除されないように、[リソース ロック](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/preventing-the-disaster-of-accidental-deletion-for-your-mysql/ba-p/825222)を使用することを強くお勧めします。
