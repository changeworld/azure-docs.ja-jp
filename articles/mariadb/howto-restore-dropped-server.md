---
title: 削除された Azure Database for MariaDB サーバーを復元する
description: この記事では、Azure portal を使用して Azure Database for MariaDB の削除されたサーバーを復元する方法について説明します。
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 4/12/2021
ms.openlocfilehash: 7cf2ce61239c7f2320f7b789acbb8f340e6de471
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315318"
---
# <a name="restore-a-deleted-azure-database-for-mariadb-server"></a>削除された Azure Database for MariaDB サーバーを復元する

サーバーが削除されると、データベース サーバーのバックアップをサービスで最大 5 日間保持できます。 データベースのバックアップは、元々サーバーをホストしている Azure サブスクリプションからのみアクセスおよび復元できます。 次の推奨される手順に従って、サーバーの削除から 5 日以内に、削除された MariaDB サーバー リソースを復旧できます。 推奨される手順は、サーバーのバックアップがまだ使用可能であり、システムから削除されていない場合にのみ機能します。 

## <a name="pre-requisites"></a>前提条件
削除された Azure Database for MariaDB サーバーを復元するには、次のことを行う必要があります。
- 元のサーバーをホストしている Azure サブスクリプションの名前
- サーバーが作成された場所

## <a name="steps-to-restore"></a>復元の手順

1. Azure portal のモニター ブレイドから[アクティビティ ログ](https://ms.portal.azure.com/#blade/Microsoft_Azure_ActivityLog/ActivityLogBlade)に移動します 

2. アクティビティ ログで、次のように **[Add filter (フィルターの追加)]** をクリックし、次のフィルターを設定します。 

    - **サブスクリプション** = 削除されたサーバーをホストしているサブスクリプション
    - **リソースの種類** = Azure Database for MariaDB サーバー (Microsoft.DBForMariaDB/servers) 
    - **操作** = MariaDB サーバーの削除 (Microsoft.DBForMariaDB/servers/delete) 
 
     [![MariaDB サーバーの削除操作のためにフィルター処理されたアクティビティ ログ](./media/howto-restore-dropped-server/activity-log.png)](./media/howto-restore-dropped-server/activity-log.png#lightbox)
   
 3. [MariaDB サーバーの削除] イベントをダブルクリックし、[JSON] タブをクリックして、JSON 出力の "resourceId" と "submissionTimestamp" の各属性を記録します。 resourceId の形式は次のとおりです: /subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/TargetResourceGroup/providers/Microsoft.DBForMariaDB/servers/deletedserver。
 
 4. 次に、[サーバー REST API の作成ページ](/rest/api/mariadb/servers/create)に移動し、緑色で強調表示されている [使ってみる] タブを クリックして、Azure アカウントでログインします
 
 5. ステップ 3 でキャプチャした resourceId 属性から得られる resourceGroupName、serverName (削除されたサーバーの名前)、subscriptionId を指定します。画像に示されるように、API のバージョンはあらかじめ設定されています。
 
     [![REST API を使用したサーバーの作成](./media/howto-restore-dropped-server/create-server-from-rest-api.png)](./media/howto-restore-dropped-server/create-server-from-rest-api.png#lightbox)
  
 6. 要求本文のセクションを下にスクロールし、次を貼り付けます。
     
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

7. 上記要求本文内で次の値を置き換えます。
   * "Dropped server Location" を、削除されたサーバーが最初に作成された Azure リージョンに
   * "submissionTimestamp" と "resourceId" を、ステップ 3 で取得した値に。 
   * restorePointInTime については、コマンドがエラーにならないように、submissionTimestamp の値から **15 分** だけ引いた値を指定します。

8. 応答コード 201 または 202 が表示された場合は、復元要求が正常に送信されています。 

9. サーバーの作成には、元のサーバーでプロビジョニングされたデータベースのサイズとコンピューティング リソースによって時間がかかることがあります。 復元の状態は、次をフィルター処理することによって、アクティビティ ログから監視できます。 
   - **サブスクリプション** = 自分のサブスクリプション
   - **リソースの種類** = Azure Database for MariaDB サーバー (Microsoft.DBForMariaDB/servers) 
   - **操作** = MariaDB サーバー作成の更新

## <a name="next-steps"></a>次のステップ
- 5 日以内にサーバーを復元しようとしても、前に説明した手順に正確に従ってエラーが発生する場合は、サポート インシデントを開いてください。 5 日が経過した後で、削除されたサーバーを復元しようとすると、バックアップ ファイルが見つからないため、エラーが発生します。 このシナリオではサポート チケットを開かないでください。 バックアップがシステムから削除された場合、サポート チームはサポートを提供できません。 
- サーバーが誤って削除されないように、[リソース ロック](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/preventing-the-disaster-of-accidental-deletion-for-your-mysql/ba-p/825222)を使用することを強くお勧めします。
