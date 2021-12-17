---
title: オンプレミスの SQL サーバー インスタンスへの接続と管理
description: このガイドでは、Azure Purview でオンプレミスの SQL サーバー インスタンスに接続し、Purview の機能を使用してオンプレミスの SQL サーバー ソースをスキャンおよび管理する方法について説明します。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 5d2a08a1ed51028d63e7bc98825c972b869b50ea
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131848896"
---
# <a name="connect-to-and-manage-an-on-premises-sql-server-instance-in-azure-purview"></a>Azure Purview でのオンプレミスの SQL サーバー インスタンスへの接続と管理

この記事では、オンプレミスの SQL サーバー インスタンスを登録する方法と、Azure Purview でオンプレミスの SQL サーバー インスタンスを認証および対話的に操作する方法について概要を説明します。 Azure Purview の詳細については、[概要の記事](overview.md)を参照してください。

## <a name="supported-capabilities"></a>サポートされる機能

|**メタデータの抽出**|  **フル スキャン**  |**増分スキャン**|**スコープ スキャン**|**分類**|**アクセス ポリシー**|**系列**|
|---|---|---|---|---|---|---|
| [あり](#register) | [あり](#scan) | [あり](#scan) | [あり](#scan) | [あり](#scan) | いいえ| いいえ** |

\** データセットが [Data Factory Copy アクティビティ](how-to-link-azure-data-factory.md)でソース/シンクとして使用される場合、系列はサポートされています 

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* アクティブな [Purview リソース](create-catalog-portal.md)。

* Purview Studio でソースを登録して管理するには、データ ソース管理者およびデータ閲覧者である必要があります。 詳細については、[Azure Purview のアクセス許可](catalog-permissions.md)に関するページを参照してください。

* 最新の[セルフホステッド統合ランタイム](https://www.microsoft.com/download/details.aspx?id=39717)を設定します。 詳細については、[セルフホステッド統合ランタイムの作成および構成ガイド](../data-factory/create-self-hosted-integration-runtime.md)に関する記事を参照してください。

## <a name="register"></a>登録

このセクションでは、Azure Purview で [Purview Studio](https://web.purview.azure.com/) を使用して、オンプレミスの SQL サーバー インスタンスを登録する方法について説明します。

### <a name="authentication-for-registration"></a>登録の認証

オンプレミス SQL Server の認証を設定する方法は 1 つだけです。

- SQL 認証

#### <a name="sql-authentication-to-register"></a>登録する SQL 認証

SQL アカウントには、**master** データベースへのアクセス権が必要です。 これは、`sys.databases` が master データベース内にあるためです。 Purview スキャナーは、サーバー上のすべての SQL データベースを検出するために、`sys.databases` を列挙する必要があります。

##### <a name="creating-a-new-login-and-user"></a>新しいログインとユーザーの作成

新しいログインとユーザーを作成して SQL Server をスキャンできるようにするには、次の手順に従います。

> [!Note]
> 下のすべての手順は、[こちら](https://github.com/Azure/Purview-Samples/blob/master/TSQL-Code-Permissions/grant-access-to-on-prem-sql-databases.sql)で提供されているコードを使用して実行できます。

1. SQL Server Management Studio (SSMS) に移動してサーバーに接続し、セキュリティに移動し、[ログイン] を選択したままにして (または右クリックして) 新しいログインを作成します。 必ず SQL 認証を選択します。

   :::image type="content" source="media/register-scan-on-premises-sql-server/create-new-login-user.png" alt-text="新しいログインとユーザーを作成します。":::

1. 左側のナビゲーションで [サーバー ロール] を選択し、public ロールが割り当てられていることを確認します。

1. 左側のナビゲーションで [ユーザー マッピング] を選択し、マップ内のすべてのデータベースを選択し、データベース ロール: **db_datareader** を選択します。

   :::image type="content" source="media/register-scan-on-premises-sql-server/user-mapping.png" alt-text="ユーザー マッピング。":::

1. [OK] を選択して保存します。

1. 選択したままにして (または右クリックして) **[プロパティ]** を選択し、作成したユーザーにもう一度移動します。 新しいパスワードを入力し、それを確認します。 [古いパスワードを指定する] を選択し、古いパスワードを入力します。 **新しいログインを作成したらすぐに、パスワードを変更する必要があります。**

   :::image type="content" source="media/register-scan-on-premises-sql-server/change-password.png" alt-text="パスワードを変更します。":::

##### <a name="storing-your-sql-login-password-in-a-key-vault-and-creating-a-credential-in-purview"></a>キー コンテナーへの SQL ログイン パスワードの格納と、Purview での資格情報の作成

1. Azure portal で、キー コンテナーに移動します。 **[設定] > [シークレット]** の順に選択します。
1. **[生成/インポート]** を選択し、**名前** と **値** を SQL Server ログインの *パスワード* として入力します。
1. **[作成]** を選択して完了します。
1. キー コンテナーが Purview にまだ接続されていない場合は、[新しいキー コンテナーの接続を作成](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)する必要があります。
1. 最後に、**ユーザー名** と **パスワード** を使用して [新しい資格情報を作成](manage-credentials.md#create-a-new-credential)し、スキャンを設定します。

### <a name="steps-to-register"></a>登録の手順

1. Purview アカウントに移動します。

1. 左側のナビゲーションの Sources and scanning\(ソースとスキャン\) で、**統合ランタイム** を選択します。 セルフホステッド統合ランタイムが設定されていることを確認してください。 設定されていない場合は、[ここ](manage-integration-runtimes.md)に記載されている手順に従って、オンプレミスまたはオンプレミス ネットワークにアクセスできる Azure VM でスキャンするためのセルフホステッド統合ランタイムを作成します。

1. 左側のナビゲーションで **[Data Map]** を選択します。

1. **[登録]** を選択します

1. **[SQL Server]** を選択し、 **[続行]** を選択します。

   :::image type="content" source="media/register-scan-on-premises-sql-server/set-up-sql-data-source.png" alt-text="SQL データ ソースを設定します。":::

1. フレンドリ名を指定します。これは、サーバーとサーバー エンドポイントを識別するために使用できる短い名前です。

1. **[完了]** を選択してデータ ソースを登録します。

## <a name="scan"></a>スキャン

以下の手順に従って、オンプレミスの SQL サーバー インスタンスをスキャンし、資産を自動的に識別してデータを分類します。 スキャン全般の詳細については、[スキャンとインジェストの概要](concept-scans-and-ingestion.md)に関するページを参照してください。

### <a name="create-and-run-scan"></a>スキャンの作成と実行

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

ソースの登録が完了したので、以下のガイドに従って Azure Purview とご利用のデータの詳細について学習します。

- [Azure Purview のデータ分析情報](concept-insights.md)
- [Azure Purview のデータ系列](catalog-lineage-user-guide.md)
- [Data Catalog の検索](how-to-search-catalog.md)
