---
title: 削除された Azure Database for MySQL フレキシブル サーバーを復元する
description: この記事では、Azure portal を利用し、Azure Database for MySQL フレキシブル サーバーで削除されたサーバーを復元する方法について説明します。
author: adig
ms.author: adig
ms.service: mysql
ms.topic: how-to
ms.date: 11/10/2021
ms.openlocfilehash: a41aad558c067247868e1f83118c5feb86e83b37
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132350107"
---
# <a name="restore-a-deleted-azure-database-for-mysql-flexible-server"></a>削除された Azure Database for MySQL フレキシブル サーバーを復元する

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

フレキシブル サーバーが削除された場合、そのサーバーのバックアップはサービス内で最大 5 日間保持できます。 サーバーのバックアップは、元々サーバーをホストしている Azure サブスクリプションからのみアクセスし、復元できます。 次の推奨手順に従うと、サーバーが削除されてから 5 日以内であれば、削除された MySQL フレキシブル サーバーを復旧できます。 推奨される手順は、サーバーのバックアップがまだ使用可能であり、システムから削除されていない場合にのみ機能します。

## <a name="pre-requisites"></a>前提条件
削除された Azure Database for MySQL フレキシブル サーバーを復元するには、次の手順を行う必要があります。
- 元のサーバーをホストしている Azure サブスクリプションの名前
- サーバーが作成された場所

## <a name="steps-to-restore"></a>復元の手順

1. Azure portal のモニター ブレイドから[アクティビティ ログ](https://ms.portal.azure.com/#blade/Microsoft_Azure_ActivityLog/ActivityLogBlade)に移動します 

2. アクティビティ ログで、次のように **[Add filter (フィルターの追加)]** をクリックし、次のフィルターを設定します。 

    - **サブスクリプション** = 削除されたサーバーをホストしているサブスクリプション
    - **リソースの種類** = Azure Database for MySQL フレキシブル サーバー (Microsoft.DBforMySQL/flexibleServers) 
    - **操作** = Delete MySQL Server (Microsoft.DBforMySQL/flexibleServers/delete) 
 
     [![MySQL サーバーの削除操作のためにフィルター処理されたアクティビティログ](./media/how-to-restore-server-portal/monitor-log-delete-server.png)](./media/how-to-restore-server-portal/monitor-log-delete-server.png#lightbox)
   
 3. [MySQL サーバーの削除] イベントをダブルクリックし、[JSON] タブをクリックして、JSON 出力の "resourceId" 属性と "submissionTimestamp" 属性を記録します。 resourceId の形式は /subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/TargetResourceGroup/providers/Microsoft.DBforMySQL/flexibleServers/deletedserver です。
 
 4. 次に、[サーバー REST API の作成ページ](/rest/api/mysql/flexibleserver/servers/create)に移動し、緑色で強調表示されている [使ってみる] タブを クリックして、Azure アカウントでログインします
 
 5. ステップ 3 でキャプチャした "resourceId" 属性から得られる resourceGroupName、serverName (削除されたサーバーの名前)、subscriptionId を指定します。画像に示されるように、API のバージョンはあらかじめ設定されています。
 
     [![REST API を使用したサーバーの作成](./media/how-to-restore-server-portal/server-create-rest-api.png)](./media/how-to-restore-server-portal/server-create-rest-api.png#lightbox)
  
 6. 要求本文セクションの下にスクロールして、以下を貼り付けます。
 
    ```json
    {
        "location": "Dropped Server Location",  
        "properties": 
            {
                "restorePointInTime": "submissionTimestamp - 15 minutes",
                "createMode": "PointInTimeRestore",
                "sourceServerResourceId": "resourceId"
            }
    }
    ```
7. 上記の要求本文にある次の値を置き換えます。
   * "Dropped server Location" を、削除されたサーバーが最初に作成された Azure リージョンに
   * "submissionTimestamp" と "resourceId" をステップ 3 で取得した値に 
   * restorePointInTime については、コマンドがエラーにならないように、submissionTimestamp の値から **15 分** だけ引いた値を指定します。
   
8. 応答コード 201 または 202 が表示された場合は、復元要求が正常に送信されています。 

9. サーバーの作成には、元のサーバーでプロビジョニングされたデータベースのサイズとコンピューティング リソースによって時間がかかることがあります。 復元の状態は、次をフィルター処理することによってアクティビティ ログから監視できます。 
   - **サブスクリプション** = 自分のサブスクリプション
   - **リソースの種類** = Azure Database for MySQL フレキシブル サーバー (Microsoft.DBforMySQL/flexibleServers) 
   - **操作** = MySQL サーバー作成の更新

## <a name="next-steps"></a>次のステップ
- 5 日以内にサーバーを復元しようとしても、前に説明した手順に正確に従ってエラーが発生する場合は、サポート インシデントを開いてください。 削除されたサーバーを 5 日後に復元しようとすると、バックアップファイルが見つからないため、エラーが発生します。 このシナリオではサポート チケットを開かないでください。 バックアップがシステムから削除された場合、サポート チームはサポートを提供できません。 
- サーバーが誤って削除されないように、[リソース ロック](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/preventing-the-disaster-of-accidental-deletion-for-your-mysql/ba-p/825222)を使用することを強くお勧めします。
