---
title: Azure Synapse ワークスペースをスキャンする方法
description: Azure Purview データ カタログで Synapse ワークスペースをスキャンする方法について説明します。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 06/11/2021
ms.openlocfilehash: 20ad72a1c0e464c8d34442dd2d7056d3f7b4eea7
ms.sourcegitcommit: 23040f695dd0785409ab964613fabca1645cef90
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/14/2021
ms.locfileid: "112060950"
---
# <a name="register-and-scan-azure-synapse-workspaces"></a>Azure Synapse ワークスペースの登録とスキャン

この記事では、Purview に Azure Synapse ワークスペースを登録し、そのスキャンを設定する方法の概要について説明します。

## <a name="supported-capabilities"></a>サポートされる機能

Azure Synapse Workspace スキャンは、内部の専用のサーバーレス SQL データベースのメタデータとスキーマのキャプチャをサポートしています。 また、システムおよびカスタムの分類規則に基づいてデータが自動的に分類されます。

## <a name="prerequisites"></a>前提条件

- データ ソースを登録する前に、Azure Purview アカウントを作成します。 Purview アカウントの作成の詳細については、[Azure Purview アカウントの作成](create-catalog-portal.md)に関するクイックスタートを参照してください。
- Azure Purview データ ソース管理者である必要があります
- 以下のセクションの説明に従った認証の設定

## <a name="steps-to-register-and-scan-a-synapse-workspace"></a>Synapse ワークスペースを登録してスキャンする手順

> [!NOTE]
> ワークスペースを正常にスキャンするには、これらの手順を指定された順番で実行し、該当する各ステップで指定されている正確なアクセス許可を適用する **必要** があります。

### <a name="step-1-register-your-source-a-user-with-at-least-reader-role-on-the-synapse-workspace-who-is-also-a-data-source-admin-in-purview-can-carry-out-this-step"></a>**ステップ 1:** ソースを登録する (Purview のデータ ソース管理者である Synapse ワークスペースで少なくとも閲覧者ロールを持つユーザーは、この手順を実行できます)

新しい Azure Synapse のソースをデータ カタログに登録するには、次の手順を行います。

1. Purview アカウントに移動します。
1. 左側のナビゲーションで **[ソース]** を選択します。
1. **[登録]** を選択します
1. **[ソースの登録]** で、 **[Azure Synapse Analytics (複数)]** を選択します
1. **[続行]** を選択します。

   :::image type="content" source="media/register-scan-synapse-workspace/register-synapse-source.png" alt-text="Azure Synapse ソースを設定する":::

**[ソースの登録 (Azure Synapse Analytics)]** 画面で、次の操作を行います。

1. データ ソースがカタログに表示される際の **[名前]** を入力します。
1. 必要に応じて **[サブスクリプション]** を選択し、フィルター処理します。
1. ドロップダウンから **Synapse のワークスペース名を選択** します。 ワークスペースの選択に基づいて、SQL エンドポイントが自動的に入力されます。 
1. **コレクション** を選択するか、新しいものを作成します (省略可能)
1. データ ソースの登録を **[完了]** します。

    :::image type="content" source="media/register-scan-synapse-workspace/register-synapse-source-details.png" alt-text="Azure Synapse ソースの詳細を入力する":::


### <a name="step-2-applying-permissions-to-enumerate-the-contents-of-the-workspace"></a>**ステップ 2:** ワークスペースの内容を列挙するアクセス許可を適用する

#### <a name="setting-up-authentication-for-enumerating-dedicated-sql-database-resources-under-a-synapse-workspace"></a>Synapse ワークスペースで専用の SQL データベース リソースを列挙するための認証を設定する

1. Azure portal で Azure Synapse ワークスペース リソースに移動します。  
1. 左側のナビゲーション メニューで  **[アクセス制御 (IAM)]**   を選択します。 
1. リソースにロールを追加するには、所有者またはユーザー アクセス管理者である必要があります。 *[+ 追加]* ボタンを選択します。 
1. **[閲覧者]** ロールを設定し、[入力の選択] ボックスに Azure Purview のアカウント名 (MSI を表すもの) を入力します。 *[保存]* をクリックして、ロールの割り当てを完了します。

> [!NOTE]
> Azure Purview アカウントで複数の Azure Synapse ワークスペースを登録してスキャンする予定の場合、**リソース グループ** や **サブスクリプション** などの上位のレベルからロールを割り当てることもできます。 

#### <a name="setting-up-authentication-for-enumerating-serverless-sql-database-resources-under-a-synapse-workspace"></a>Synapse ワークスペースでサーバーレス SQL データベース リソースを列挙するための認証を設定する

> [!NOTE]
> これらのコマンドを実行するには、ワークスペースの **Synapse 管理者** である必要があります。 Synapse のアクセス許可については[こちら](../synapse-analytics/security/how-to-set-up-access-control.md)を参照してください。

1. Synapse ワークスペースに移動します
1. **[データ]** セクションに移動し、サーバーレス SQL データベースのいずれかに移動します
1. 省略記号アイコンをクリックして、新しい SQL スクリプトを開始します
1. SQL スクリプトで次のコマンドを実行し、**db_datareader** という Azure Purview アカウント MSI (アカウント名で表されます) をサーバーレス SQL データベースに追加します。
    ```sql
    CREATE LOGIN [PurviewAccountName] FROM EXTERNAL PROVIDER;
    CREATE USER [PurviewAccountName] FOR LOGIN [PurviewAccountName];
    ALTER ROLE db_datareader ADD MEMBER [PurviewAccountName]; 
    ```
> [!NOTE]
> Synapse ワークスペース内のすべてのサーバーレス SQL データベースに対して、前の手順を繰り返します。 


1. Azure portal で、Synapse ワークスペースが所属する **[リソース グループ]** または **[サブスクリプション]** に移動します。
1. 左側のナビゲーション メニューで  **[アクセス制御 (IAM)]**   を選択します。 
1. リソース グループまたはサブスクリプションにロールを追加するには、**所有者** または **ユーザー アクセス管理者** である必要があります。 *[+ 追加]* ボタンを選択します。 
1. **ストレージ BLOB データ閲覧者** ロールを設定し、[選択] 入力ボックスに Azure Purview のアカウント名 (MSI を表すもの) を入力します。 *[保存]* をクリックして、ロールの割り当てを完了します。

### <a name="step-3-applying-permissions-to-scan-the-contents-of-the-workspace"></a>**ステップ 3:** ワークスペースの内容をスキャンするアクセス許可を適用する

Azure Synapse ソースの認証を設定する方法は 2 つあります。

- マネージド ID
- サービス プリンシパル

> [!NOTE]
> 登録およびスキャンする Synapse ワークスペース内の各専用 SQL データベースに認証を設定する必要があります。 下記のサーバーレス SQL データベースのアクセス許可は、ワークスペース内のすべてのデータベースに適用されます。つまり、一度だけ実行する必要があります。

#### <a name="using-managed-identity-for-dedicated-sql-databases"></a>専用 SQL データベースのマネージド ID の使用

1. **Synapse ワークスペース** に移動します
1. **[データ]** セクションに移動し、専用 SQL データベースのいずれかに移動します
1. 省略記号アイコンをクリックして、新しい SQL スクリプトを開始します
1. SQL スクリプトで次のコマンドを実行し、**db_datareader** という Azure Purview アカウント MSI (アカウント名で表されます) を専用 SQL データベースに追加します。

    ```sql
    CREATE USER [PurviewAccountName] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_datareader', [PurviewAccountName]
    GO
    ```
> [!NOTE]
> Synapse ワークスペース内のすべての専用 SQL データベースに対して、前の手順を繰り返します。 

#### <a name="using-managed-identity-for-serverless-sql-databases"></a>サーバーレス SQL データベースへのマネージド ID の使用

1. **Synapse ワークスペース** に移動します
1. **[データ]** セクションに移動し、サーバーレス SQL データベースのいずれかに移動します
1. 省略記号アイコンをクリックして、新しい SQL スクリプトを開始します
1. SQL スクリプトで次のコマンドを実行し、**db_datareader** という Azure Purview アカウント MSI (アカウント名で表されます) をサーバーレス SQL データベースに追加します。
    ```sql
    CREATE LOGIN [PurviewAccountName] FROM EXTERNAL PROVIDER;
    CREATE USER [PurviewAccountName] FOR LOGIN [PurviewAccountName];
    ALTER ROLE db_datareader ADD MEMBER [PurviewAccountName]; 
    ```
> [!NOTE]
> Synapse ワークスペース内のすべてのサーバーレス SQL データベースに対して、前の手順を繰り返します。 

#### <a name="using-service-principal-for-dedicated-sql-databases"></a>専用 SQL データベースへのサービス プリンシパルの使用

> [!NOTE]
> まず、[こちら](manage-credentials.md)の指示に従って、サービス プリンシパルの種類の新しい **資格情報** を設定する必要があります。

1. **Synapse ワークスペース** に移動します
1. **[データ]** セクションに移動し、専用 SQL データベースのいずれかに移動します
1. 省略記号アイコンをクリックして、新しい SQL スクリプトを開始します
1. SQL スクリプトで次のコマンドを実行し、**db_datareader** という **サービス プリンシパル ID** を専用 SQL データベースに追加します。

    ```sql
    CREATE USER [ServicePrincipalID] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_datareader', [ServicePrincipalID]
    GO
    ```
> [!NOTE]
> Synapse ワークスペース内のすべての専用 SQL データベースに対して、前の手順を繰り返します。 

#### <a name="using-service-principal-for-serverless-sql-databases"></a>サーバーレス SQL データベースへのサービス プリンシパルの使用

1. **Synapse ワークスペース** に移動します
1. **[データ]** セクションに移動し、サーバーレス SQL データベースのいずれかに移動します
1. 省略記号アイコンをクリックして、新しい SQL スクリプトを開始します
1. SQL スクリプトで次のコマンドを実行し、**db_datareader** という Azure Purview アカウント MSI (アカウント名で表されます) をサーバーレス SQL データベースに追加します。
    ```sql
    CREATE LOGIN [PurviewAccountName] FROM EXTERNAL PROVIDER;
    CREATE USER [PurviewAccountName] FOR LOGIN [PurviewAccountName];
    ALTER ROLE db_datareader ADD MEMBER [PurviewAccountName]; 
    ```
> [!NOTE]
> Synapse ワークスペース内のすべてのサーバーレス SQL データベースに対して、前の手順を繰り返します。 

### <a name="step-4-setting-up-synapse-workspace-firewall-access"></a>**ステップ 4:** Synapse ワークスペースのファイアウォール アクセスを設定する

1. Azure portal で Synapse ワークスペースに移動します。 

3. 左側のナビゲーションから [ファイアウォール] を選択します。

4. **[Azure サービスおよびリソースに、このワークスペースへのアクセスを許可する]** で **[オン]** をクリックします。

5. [保存] をクリックします。

### <a name="step-5-setting-up-a-scan-on-the-workspace"></a>**ステップ 5:** ワークスペースでスキャンを設定する

新しいスキャンを作成して実行するには、次の操作を行います。

1. **[ソース]** セクションに移動します。

1. 登録したデータ ソースを選択します。

1. [詳細の表示] をクリックし、 **[+ 新しいスキャン]** を選択するか、ソース タイルのスキャン クイック操作アイコンを使用します。

1. "*名前*" を入力し、このソース内でスキャンするすべての種類のリソースを選択します。 **SQL Database** は、現在 Synapse ワークスペース内で現在サポートされている唯一の種類です。
   
    :::image type="content" source="media/register-scan-synapse-workspace/synapse-scan-setup.png" alt-text="Azure Synapse ソースのスキャン":::

1. データ ソース内のリソースに接続する **資格情報** を選択します。 
  
1. 各種類では、すべてのリソースをスキャンするか、またはそのサブセットを名前でスキャンするかを選択できます。
1.  **[続行]** をクリックして続行します。 

1.  種類が Azure Synapse SQL の **スキャン ルールセット** を選択します。 また、スキャン ルール セットをインラインで作成することもできます。

1. スキャン トリガーを選択します。 **[weekly/monthly]\(毎週/毎月\)** または **[1 回]** 、実行するようにスケジュールできます。

1. スキャンをレビューし、[保存] を選択して設定を完了します。   

#### <a name="viewing-your-scans-and-scan-runs"></a>スキャンとスキャンの実行を確認する

1. [ソース] セクションの下にあるタイルで **[詳細の表示]** をクリックして、ソースの詳細を表示します。 

      :::image type="content" source="media/register-scan-synapse-workspace/synapse-source-details.png" alt-text="Azure Synapse ソースの詳細"::: 

1. **[スキャンの詳細]** に移動して、スキャン実行の詳細を表示します。
    1. "*ステータス バー*" は、子リソースの実行中の状態に関する概要です。 これはワークスペース レベルのスキャンで表示されます。
    1. 緑色は成功を意味し、赤色は失敗を意味します。 グレーは、スキャンがまだ進行中であることを意味します。
    1. 各スキャンをクリックすると、より詳細な情報を表示できます。

      :::image type="content" source="media/register-scan-synapse-workspace/synapse-scan-details.png" alt-text="Azure Synapse スキャンの詳細" lightbox="media/register-scan-synapse-workspace/synapse-scan-details.png"::: 

1. [ソースの詳細] ページの下部に、最近失敗したスキャン実行の概要が表示されます。 また、クリックして、これらの実行に関するより詳細な情報を表示することもできます。

#### <a name="manage-your-scans---edit-delete-or-cancel"></a>スキャンを管理する - 編集、削除、またはキャンセル
スキャンを管理または削除するには、次の操作を行います。

- 管理センターに移動します。 [Sources and scanning]\(ソースとスキャン\) セクションの [データ ソース] を選択し、目的のデータ ソースを選択します。

- 管理するスキャンを選択します。 スキャンを編集するには、 [編集] を選択します。

- スキャンを削除するには、 [削除] を選択します。
- 実行中のスキャンもキャンセルできます。

## <a name="next-steps"></a>次のステップ

- [Azure Purview データ カタログを参照する](how-to-browse-catalog.md)
- [Azure Purview データ カタログを検索する](how-to-search-catalog.md)   
