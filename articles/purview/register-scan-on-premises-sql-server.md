---
title: オンプレミスの SQL Server の登録とスキャン
description: このチュートリアルでは、セルフホステッド IR を使用してオンプレミスの SQL Server をスキャンする方法について説明します。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/18/2020
ms.openlocfilehash: 9003366ec0d64057ca7426d5b6b99986bc21fc9d
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/09/2020
ms.locfileid: "96920285"
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

### <a name="known-limitations"></a>既知の制限事項

Azure Purview は、SQL Server での[ビュー](https://docs.microsoft.com/sql/relational-databases/views/views?view=sql-server-ver15)のスキャンはサポートしません。 

## <a name="prerequisites"></a>前提条件

- データ ソースを登録する前に、Azure Purview アカウントを作成します。 Purview アカウントの作成の詳細については、[クイック スタート: Azure Purview アカウントの作成](create-catalog-portal.md)に関する記事を参照してください。

- [セルフホステッド統合ランタイム](manage-integration-runtimes.md)を設定して、データ ソースをスキャンします。

## <a name="setting-up-authentication-for-a-scan"></a>スキャンの認証の設定

オンプレミス SQL Server の認証を設定する方法は 1 つだけです。

- SQL 認証

### <a name="sql-authentication"></a>SQL 認証

SQL ID には、プライマリ データベースへのアクセス権が必要です。 この場所は `sys.databases` が格納される場所です。 Purview スキャナーは、サーバー内のすべての SQL DB インスタンスを検出するために、`sys.databases` を列挙する必要があります。

#### <a name="using-an-existing-server-administrator"></a>既存のサーバー管理者の使用

既存のサーバー管理者 (sa) ユーザーを使用してオンプレミスの SQL Server をスキャンする予定がある場合は、次のことを確認してください。

1. `sa` が Windows 認証方法ではありません。

2. 使用する予定のサーバー レベルのユーザーが、public および sysadmin のサーバー ロールを持っている必要があります。 これを確認するには、SQL Server Management Studio (SSMS) に移動し、サーバーに接続し、セキュリティに移動します。次に、使用する予定のログインを選択し、 **[プロパティ]** を右クリックして **[サーバー ロール]** を選択します。

   :::image type="content" source="media/register-scan-on-premises-sql-server/server-level-login.png" alt-text="サーバー レベルのログイン。":::

3. データベースは、各データベースに対して少なくとも db_datareader レベルのアクセス権を持つユーザーにマップされます。

   :::image type="content" source="media/register-scan-on-premises-sql-server/user-mapping-sa.png" alt-text="sa のユーザー マッピング。":::

#### <a name="creating-a-new-login-and-user"></a>新しいログインとユーザーの作成

新しいログインとユーザーを作成して SQL Server をスキャンできるようにするには、次の手順に従います。

1. SQL Server Management Studio (SSMS) に移動してサーバ＾に接続し、セキュリティに移動し、[ログイン] を右クリックして新しいログインを作成します。 必ず SQL 認証を選択します。

   :::image type="content" source="media/register-scan-on-premises-sql-server/create-new-login-user.png" alt-text="新しいログインとユーザーを作成します。":::

2. 左側のナビゲーションで [サーバー ロール] を選択し、public と sysadmin の両方を選択します。

3. 左側のナビゲーションで [ユーザー マッピング] を選択し、マップ内のすべてのデータベースを選択します。

   :::image type="content" source="media/register-scan-on-premises-sql-server/user-mapping.png" alt-text="ユーザー マッピング。":::

4. [OK] をクリックして保存します。

5. 右クリックして **[プロパティ]** を選択し、作成したユーザーにもう一度移動します。 新しいパスワードを入力し、それを確認します。 [古いパスワードを指定する] を選択し、古いパスワードを入力します。 **新しいログインを作成したらすぐに、パスワードを変更する必要があります。**

   :::image type="content" source="media/register-scan-on-premises-sql-server/change-password.png" alt-text="パスワードを変更します。":::

#### <a name="storing-your-sql-login-password-in-a-key-vault-and-creating-a-credential-in-purview"></a>キー コンテナーへの SQL ログイン パスワードの格納と、Purview での資格情報の作成

1. Azure portal で、ご自身のキー コンテナーに移動します。
1. **[設定] > [シークレット]** の順に選択します。
1. **[生成/インポート]** を選択し、**名前** と **値** を SQL Server ログインの *パスワード* として入力します。
1. **[作成]** を選択して完了します。
1. キー コンテナーが Purview にまだ接続されていない場合は、[新しいキー コンテナーの接続を作成](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)する必要があります。
1. 最後に、**ユーザー名** と **パスワード** を使用して [新しい資格情報を作成](manage-credentials.md#create-a-new-credential)し、スキャンを設定します。

## <a name="register-a-sql-server-data-source"></a>SQL Server データ ソースの登録

1. Purview アカウントに移動します。

1. 左側のナビゲーションの Sources and scanning\(ソースとスキャン\) で、**統合ランタイム** を選択します。 セルフホステッド統合ランタイムが設定されていることを確認してください。 設定されていない場合は、[ここ](manage-integration-runtimes.md)に記載されている手順に従って、オンプレミスまたはオンプレミス ネットワークにアクセスできる Azure VM でスキャンするためのセルフホステッド統合ランタイムを作成します。

1. 左側のナビゲーションで **[ソース]** を選択します。

1. **[登録]** を選択します

1. **[SQL Server]** を選択し、 **[続行]** を選択します。

   :::image type="content" source="media/register-scan-on-premises-sql-server/set-up-sql-data-source.png" alt-text="SQL データ ソースを設定します。":::

5. フレンドリ名とサーバー エンドポイントを指定し、 **[完了]** を選択してデータ ソースを登録します。 たとえば、SQL Server の FQDN が **foobar.database.windows.net** の場合は、サーバー エンドポイントとして *foobar* を入力します。

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>次のステップ

- [Azure Purview データ カタログを参照する](how-to-browse-catalog.md)
- [Azure Purview データ カタログを検索する](how-to-search-catalog.md)
