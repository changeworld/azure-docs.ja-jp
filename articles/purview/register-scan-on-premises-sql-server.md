---
title: オンプレミスの SQL Server の登録とスキャン
description: このチュートリアルでは、Azure Purview のセルフホステッド IR を使用してオンプレミスの SQL Server をスキャンする方法について説明します。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 1921349be07d129e9889da3af6c72a7b25a58ff5
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130074439"
---
# <a name="register-and-scan-an-on-premises-sql-server"></a>オンプレミスの SQL Server の登録とスキャン

この記事では、Purview に SQL Server データ ソースを登録し、そのスキャンを設定する方法について説明します。

## <a name="supported-capabilities"></a>サポートされる機能

オンプレミスの SQL Server データ ソースは、次の機能をサポートしています。

- **完全スキャンと増分スキャン**。オンプレミス ネットワーク、または Azure VM にインストールされている SQL Server で、メタデータと分類をキャプチャします。

- ADF コピー/データフロー アクティビティのための、データ アセット間の **データ系列**

オンプレミスの SQL Server データ ソースは以下をサポートします。

- SQL Server 2000 から SQL Server 2019 までのすべてのバージョンの SQL

- 認証方法: SQL 認証

## <a name="prerequisites"></a>前提条件

- データ ソースを登録する前に、Azure Purview アカウントを作成します。 Purview アカウントの作成の詳細については、[クイック スタート: Azure Purview アカウントの作成](create-catalog-portal.md)に関する記事を参照してください。

- [セルフホステッド統合ランタイム](manage-integration-runtimes.md)を設定して、データ ソースをスキャンします。

## <a name="setting-up-authentication-for-a-scan"></a>スキャンの認証の設定

オンプレミス SQL Server の認証を設定する方法は 1 つだけです。

- SQL 認証

### <a name="sql-authentication"></a>SQL 認証

SQL アカウントには、**master** データベースへのアクセス権が必要です。 これは、`sys.databases` が master データベース内にあるためです。 Purview スキャナーは、サーバー上のすべての SQL データベースを検出するために、`sys.databases` を列挙する必要があります。

#### <a name="creating-a-new-login-and-user"></a>新しいログインとユーザーの作成

新しいログインとユーザーを作成して SQL Server をスキャンできるようにするには、次の手順に従います。

> [!Note]
   > 下のすべての手順は、[こちら](https://github.com/Azure/Purview-Samples/blob/master/TSQL-Code-Permissions/grant-access-to-on-prem-sql-databases.sql)で提供されているコードを使用して実行できます。

1. SQL Server Management Studio (SSMS) に移動してサーバーに接続し、セキュリティに移動し、[ログイン] を選択したままにして (または右クリックして) 新しいログインを作成します。 必ず SQL 認証を選択します。

   :::image type="content" source="media/register-scan-on-premises-sql-server/create-new-login-user.png" alt-text="新しいログインとユーザーを作成します。":::

2. 左側のナビゲーションで [サーバー ロール] を選択し、public ロールが割り当てられていることを確認します。

3. 左側のナビゲーションで [ユーザー マッピング] を選択し、マップ内のすべてのデータベースを選択し、データベース ロール: **db_datareader** を選択します。

   :::image type="content" source="media/register-scan-on-premises-sql-server/user-mapping.png" alt-text="ユーザー マッピング。":::

4. [OK] を選択して保存します。

5. 選択したままにして (または右クリックして) **[プロパティ]** を選択し、作成したユーザーにもう一度移動します。 新しいパスワードを入力し、それを確認します。 [古いパスワードを指定する] を選択し、古いパスワードを入力します。 **新しいログインを作成したらすぐに、パスワードを変更する必要があります。**

   :::image type="content" source="media/register-scan-on-premises-sql-server/change-password.png" alt-text="パスワードを変更します。":::

#### <a name="storing-your-sql-login-password-in-a-key-vault-and-creating-a-credential-in-purview"></a>キー コンテナーへの SQL ログイン パスワードの格納と、Purview での資格情報の作成

1. Azure portal で、キー コンテナーに移動します。 **[設定] > [シークレット]** の順に選択します。
1. **[生成/インポート]** を選択し、**名前** と **値** を SQL Server ログインの *パスワード* として入力します。
1. **[作成]** を選択して完了します。
1. キー コンテナーが Purview にまだ接続されていない場合は、[新しいキー コンテナーの接続を作成](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)する必要があります。
1. 最後に、**ユーザー名** と **パスワード** を使用して [新しい資格情報を作成](manage-credentials.md#create-a-new-credential)し、スキャンを設定します。

## <a name="register-a-sql-server-data-source"></a>SQL Server データ ソースの登録

1. Purview アカウントに移動します。

1. 左側のナビゲーションの Sources and scanning\(ソースとスキャン\) で、**統合ランタイム** を選択します。 セルフホステッド統合ランタイムが設定されていることを確認してください。 設定されていない場合は、[ここ](manage-integration-runtimes.md)に記載されている手順に従って、オンプレミスまたはオンプレミス ネットワークにアクセスできる Azure VM でスキャンするためのセルフホステッド統合ランタイムを作成します。

1. 左側のナビゲーションで **[Data Map]** を選択します。

1. **[登録]** を選択します

1. **[SQL Server]** を選択し、 **[続行]** を選択します。

   :::image type="content" source="media/register-scan-on-premises-sql-server/set-up-sql-data-source.png" alt-text="SQL データ ソースを設定します。":::

1. フレンドリ名を指定します。これは、サーバーとサーバー エンドポイントを識別するために使用できる短い名前です。 
 
1. **[完了]** を選択してデータ ソースを登録します。

## <a name="creating-and-running-a-scan"></a>スキャンを作成し、実行する

新しいスキャンを作成して実行するには、次の操作を行います。

1. [Purview Studio](https://web.purview.azure.com/resource/) の左側のペインで **[Data Map]** タブを選択します。

1. 登録した SQL Server のソースを選択します。

1. **[新しいスキャン]** 選択する

1. 対象のデータ ソースに接続するための資格情報を選択します。

   :::image type="content" source="media/register-scan-on-premises-sql-server/on-premises-sql-set-up-scan.png" alt-text="スキャンを設定する":::

1. リストから適切な項目を選択することによって、特定のテーブルに対するスキャンの範囲を指定することができます。

   :::image type="content" source="media/register-scan-on-premises-sql-server/on-premises-sql-scope-your-scan.png" alt-text="スキャンの範囲を指定する":::

1. 次に、スキャン ルール セットを選択します。 システムの既定のものを選択するか、既存のカスタム ルール セットを使用するか、新しいルール セットをインラインで作成することができます。

   :::image type="content" source="media/register-scan-on-premises-sql-server/on-premises-sql-scan-rule-set.png" alt-text="スキャン ルール セット":::

1. スキャン トリガーを選択します。 スケジュールを設定することも、1 回限りのスキャンを実行することもできます。

   :::image type="content" source="media/register-scan-on-premises-sql-server/trigger-scan.png" alt-text="trigger":::

1. スキャンを確認し、 **[保存および実行]** を選択します。

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>次のステップ

- [Azure Purview データ カタログを参照する](how-to-browse-catalog.md)
- [Azure Purview データ カタログを検索する](how-to-search-catalog.md)
