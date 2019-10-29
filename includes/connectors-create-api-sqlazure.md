---
title: インクルード ファイル
description: インクルード ファイル
services: logic-apps
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.author: estfan
ms.custom: include file
ms.date: 05/15/2018
ms.openlocfilehash: aa1001661d8fe03855e1a28b882f674bee3606b2
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2019
ms.locfileid: "72312006"
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

   ![Azure SQL Database 接続を作成する](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)

1. 完了したら **[作成]** を選択します。

1. 接続を作成したら、次に [SQL トリガーを追加](#add-sql-trigger)するか、[SQL アクションを追加](#add-sql-action)します。

<a name="connect-sql-server"></a>

### <a name="connect-to-sql-server"></a>SQL Server への接続

SQL のトリガーまたはアクションから接続情報を求められた場合は、以下の手順に従います。手順はトリガーとアクションの両方に適用されます。 ただし、開始する前に、既に[オンプレミス データ ゲートウェイが設定されている](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection)ことを確認します。 設定されていない場合、接続を作成するとき、ゲートウェイの一覧にゲートウェイが表示されません。

1. **[接続名]** では、接続の名前を作成します。

1. トリガーまたはアクションで、 **[オンプレミスのデータ ゲートウェイ経由で接続]** を選択し、SQL Server のオプションが表示されるようにします。

1. **[SQL サーバー名]** と **[SQL データベース名]** に、お使いの SQL サーバーのアドレスとお使いのデータベースの名前を指定します。 **[ユーザー名]** と **[パスワード]** に、お使いのサーバーのユーザー名とパスワードを指定します。

   接続文字列でもこの情報を確認できます。

   * `Server=<your-server-address>`
   * `Database=<your-database-name>`
   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![SQL Server 接続を作成する](./media/connectors-create-api-sqlazure/sql-server-create-connection.png)

1. SQL サーバーで Windows 認証または基本認証を使用する場合、**認証の種類**を選択します。

1. **[ゲートウェイ]** の下では、前に作成したオンプレミス データ ゲートウェイに関連付けられている Azure サブスクリプションを選択し、お使いのオンプレミス データ ゲートウェイの名前を選択します。

   ゲートウェイが一覧に表示されない場合は、正しく[ゲートウェイを設定](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection)していることを確認します。

   ![SQL Server 接続の作成完了](./media/connectors-create-api-sqlazure/sql-server-create-connection-complete.png)

1. 完了したら **[作成]** を選択します。

1. 接続を作成した後で、[SQL トリガーの追加](#add-sql-trigger)または[ SQL アクションの追加](#add-sql-action)を続けて実行します。
