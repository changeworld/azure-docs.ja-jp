---
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.topic: include
ms.date: 11/08/2019
ms.openlocfilehash: ea0ae1b1527aa1f527c8ac8fbcd3b4e4f6b6fe2f
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789200"
---
* Azure SQL Database を使用している場合は、「[Azure SQL Database に接続する](#connect-azure-sql-db)」の手順に従ってください。

* SQL Server を使用している場合は、「[SQL Server への接続](#connect-sql-server)」の手順に従ってください。

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database"></a>Azure SQL Database に接続する

SQL のトリガーまたはアクションから接続情報を求められた場合は、以下の手順に従います。手順はトリガーとアクションの両方に適用されます。

1. **[接続名]** では、接続の名前を作成します。

1. **[SQL サーバー名]** では、お使いの Azure SQL サーバーを選択します。 **[SQL Database 名]** 一覧が表示されたら、お使いのデータベースを選択します。 お使いの Azure SQL サーバーのユーザー名とパスワードを入力します。

   この情報は、Azure portal の、SQL データベースのプロパティまたは次の接続文字列にもあります。

   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![Azure SQL Database への接続を作成する](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)

1. 完了したら **[作成]** を選択します。

1. 接続を作成したら、次に [SQL トリガーを追加](#add-sql-trigger)するか、[SQL アクションを追加](#add-sql-action)します。

<a name="connect-sql-server"></a>

### <a name="connect-to-sql-server"></a>SQL Server への接続

SQL のトリガーまたはアクションから接続情報を求められた場合は、以下の手順に従います。手順はトリガーとアクションの両方に適用されます。 [オンプレミス データ ゲートウェイ](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-install)をローカル コンピューターにインストールし、[Azure データ ゲートウェイ リソースを作成する](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection)ことが必要なシナリオでは、必ず最初にこれらの要件を完了してください。 設定されていない場合、接続を作成するとき、ゲートウェイの一覧にゲートウェイ リソースが表示されません。

また、[統合サービス環境 (ISE)](https://docs.microsoft.com/azure/logic-apps/connect-virtual-network-vnet-isolated-environment-overview) で SQL Server コネクタとの Windows 認証を使用するには、コネクタの ISE 以外のバージョンとオンプレミス データ ゲートウェイを使用します。 ISE とラベル付けされたバージョンでは、Windows 認証はサポートされていません。

1. **[接続名]** では、接続の名前を作成します。

1. トリガーまたはアクションで、 **[オンプレミスのデータ ゲートウェイ経由で接続]** を選択し、SQL Server のオプションが表示されるようにします。

1. **[SQL サーバー名]** と **[SQL データベース名]** に、お使いの SQL サーバーのアドレスとお使いのデータベースの名前を指定します。 **[ユーザー名]** と **[パスワード]** に、お使いのサーバーのユーザー名とパスワードを指定します。

   接続文字列でもこの情報を確認できます。

   * `Server=<your-server-address>`
   * `Database=<your-database-name>`
   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![SQL Server への接続を作成する](./media/connectors-create-api-sqlazure/sql-server-create-connection.png)

1. SQL サーバーで Windows 認証または基本認証を使用する場合、**認証の種類**を選択します。

1. **[ゲートウェイ]** の下では、前に作成したオンプレミス データ ゲートウェイに関連付けられている Azure サブスクリプションを選択し、お使いのオンプレミス データ ゲートウェイの名前を選択します。

   ゲートウェイが一覧に表示されない場合は、正しく[ゲートウェイを設定](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection)していることを確認します。

   ![SQL Server 接続の作成完了](./media/connectors-create-api-sqlazure/sql-server-create-connection-complete.png)

1. 完了したら **[作成]** を選択します。

1. 接続を作成した後で、[SQL トリガーの追加](#add-sql-trigger)または[ SQL アクションの追加](#add-sql-action)を続けて実行します。
