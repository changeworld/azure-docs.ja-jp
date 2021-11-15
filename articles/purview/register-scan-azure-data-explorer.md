---
title: Azure Data Explorer に接続して管理する
description: このガイドでは、Azure Purview で Azure Data Explorer に接続する方法と、Purview の機能を使用して Azure Data Explorer ソースをスキャンおよび管理する方法について説明します。
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 954fbb8edef1e35993acb9ec7590018719c4627d
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131848080"
---
# <a name="connect-to-and-manage-azure-data-explorer-in-azure-purview"></a>Azure Purview で Azure Data Explorer に接続して管理する


この記事では、Azure Purview で、Azure Data Explorer を登録する方法、および Azure Data Explorer を認証して操作する方法について説明します。 Azure Purview の詳細については、[概要の記事](overview.md)を参照してください。

## <a name="supported-capabilities"></a>サポートされる機能

|**メタデータの抽出**|  **フル スキャン**  |**増分スキャン**|**スコープ スキャン**|**分類**|**アクセス ポリシー**|**系列**|
|---|---|---|---|---|---|---|
| [あり](#register) | [あり](#scan) | [あり](#scan) | [あり](#scan)| [あり](#scan)| いいえ | いいえ** |

\** データセットが [Data Factory Copy アクティビティ](how-to-link-azure-data-factory.md)でソース/シンクとして使用される場合、系列はサポートされています 

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* アクティブな [Purview リソース](create-catalog-portal.md)。

* Purview Studio でソースを登録して管理するには、データ ソース管理者およびデータ閲覧者である必要があります。 詳細については、[Azure Purview のアクセス許可](catalog-permissions.md)に関するページを参照してください。

## <a name="register"></a>登録

このセクションでは、[Purview Studio](https://web.purview.azure.com/) を使用して Azure Purview に Azure Data Explorer を登録する方法について説明します。

### <a name="authentication-for-registration"></a>登録の認証

Azure Data Explorer の認証を設定する方法は 1 つだけです。

- サービス プリンシパル

#### <a name="service-principal-to-register"></a>登録するサービス プリンシパル

スキャンにサービス プリンシパルの認証を使用するには、既存のものを使用するか、新しく作成することができます。

> [!Note]
> 新しいサービス プリンシパルを作成する必要がある場合は、次の手順に従います。
> 1. [Azure Portal](https://portal.azure.com) に移動します。
> 1. 左側のメニューで、 **[Azure Active Directory]** を選択します。
> 1. **[アプリの登録]** を選択します。
> 1. **[+ 新しいアプリケーションの登録]** を選択します。
> 1. **アプリケーション** の名前 (サービス プリンシパル名) を入力します。
> 1. **[この組織のディレクトリ内のアカウントのみ]** を選択します。
> 1. [リダイレクト URI] には **[Web]** を選択し、必要な URL を入力します。これは、実際の URL でなくてもよく、機能しなくてもかまいません。
> 1. 次に、 **[登録]** を選択します。

サービス プリンシパルのアプリケーション ID とシークレットを取得する必要があります。

1. [Azure portal](https://portal.azure.com) でサービス プリンシパルに移動します
1. **[概要]** から **[アプリケーション (クライアント) ID]** 、 **[証明書とシークレット]** から **[クライアント シークレット]** の値をコピーします。
1. お使いのキー コンテナーに移動する
1. **[設定]、[シークレット]** の順に選択します
1. **[生成/インポート]** を選択し、サービス プリンシパルの **クライアント シークレット** として任意の **名前** と **値** を入力します
1. **[作成]** を選択して完了します
1. キー コンテナーが Purview にまだ接続されていない場合は、[新しいキー コンテナーの接続を作成](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)する必要があります。
1. 最後に、サービス プリンシパルを使用して[新しい資格情報を作成](manage-credentials.md#create-a-new-credential)し、スキャンを設定します

#### <a name="granting-the-service-principal-access-to-your-azure-data-explorer-instance"></a>サービス プリンシパルに Azure Data Explorer インスタンスへのアクセス権を付与する

1. Azure Portal に移動します。 次に、Azure Data Explorer インスタンスに移動します。

1. **[アクセス許可]** タブで、サービス プリンシパルを **[AllDatabasesViewer]** ロールに追加します。

### <a name="steps-to-register"></a>登録する手順

新しい Azure Data Explorer (Kusto) アカウントをデータ カタログに登録するには、次の手順を実行します。

1. Purview アカウントに移動します
1. 左側のナビゲーションで **[Data Map]** を選択します。
1. **[登録]** を選択します
1. **[ソースの登録]** で、 **[Azure Data Explorer]** を選択します
1. **[続行]** を選択します

:::image type="content" source="media/register-scan-azure-data-explorer/register-new-data-source.png" alt-text="新しいデータ ソースの登録" border="true":::

**[ソースの登録 (Azure Data Explorer (Kusto))]** 画面で、次の手順を実行します。

1. データ ソースがカタログに表示される際の **[名前]** を入力します。
2. Azure サブスクリプションを選択して、Azure Data Explorer の検索結果を絞り込みます。
3. 適切なクラスターを選択します。
4. コレクションを選択するか新規作成します (オプション)。
5. **[登録]** を選択してデータ ソースを登録します。

:::image type="content" source="media/register-scan-azure-data-explorer/register-sources.png" alt-text="ソースの登録のオプション" border="true":::

## <a name="scan"></a>スキャン

次の手順に従って、Azure Data Explorer をスキャンし、自動的に資産を識別してデータを分類します。 スキャン全般の詳細については、[スキャンとインジェストの概要](concept-scans-and-ingestion.md)に関するページを参照してください

### <a name="create-and-run-scan"></a>スキャンの作成と実行

新しいスキャンを作成して実行するには、次の操作を行います。

1. [Purview Studio](https://web.purview.azure.com/resource/) の左側のペインで **[Data Map]** タブを選択します。

1. 登録した Azure Data Explorer ソースを選択します。

1. **[新しいスキャン]** を選択します。

1. 対象のデータ ソースに接続するための資格情報を選択します。 

   :::image type="content" source="media/register-scan-azure-data-explorer/set-up-scan-data-explorer.png" alt-text="スキャンを設定する":::

1. リストから適切な項目を選択することによって、特定のデータベースに対するスキャンの範囲を指定することができます。

   :::image type="content" source="media/register-scan-azure-data-explorer/scope-your-scan-data-explorer.png" alt-text="スキャンの範囲を指定する":::

1. 次に、スキャン ルール セットを選択します。 システムの既定のものを選択するか、既存のカスタム ルール セットを使用するか、新しいルール セットをインラインで作成することができます。

   :::image type="content" source="media/register-scan-azure-data-explorer/scan-rule-set-data-explorer.png" alt-text="スキャン ルール セット":::

1. スキャン トリガーを選択します。 スケジュールを設定することも、1 回限りのスキャンを実行することもできます。

   :::image type="content" source="media/register-scan-azure-data-explorer/trigger-scan.png" alt-text="trigger":::

1. スキャンを確認し、 **[保存および実行]** を選択します。

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>次のステップ

ソースの登録が完了したので、以下のガイドに従って Azure Purview とご利用のデータの詳細について学習します。

- [Azure Purview のデータ分析情報](concept-insights.md)
- [Azure Purview のデータ系列](catalog-lineage-user-guide.md)
- [Data Catalog の検索](how-to-search-catalog.md)
