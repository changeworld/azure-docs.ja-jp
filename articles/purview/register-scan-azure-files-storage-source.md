---
title: Azure Filesに接続して管理する
description: このガイドでは、Azure 管理範囲の Azure Files に接続し、管理範囲の機能を使用して、Azure Files ソースをスキャンおよび管理する方法について説明します。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 95abf27060748255f24b089cfc4fb9229f33bfa5
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131848061"
---
# <a name="connect-to-and-manage-azure-files-in-azure-purview"></a>Azure 管理範囲での Azure Files の接続と管理

この記事では、Azure Files を登録する方法と、Azure 管理範囲で Azure Files を認証および操作する方法について説明します。 Azure Purview の詳細については、[概要の記事](overview.md)を参照してください。

## <a name="supported-capabilities"></a>サポートされる機能

|**メタデータの抽出**|  **フル スキャン**  |**増分スキャン**|**スコープ スキャン**|**分類**|**アクセス ポリシー**|**系列**|
|---|---|---|---|---|---|---|
| [あり](#register) | [あり](#scan) | [あり](#scan) | [あり](#scan) | [あり](#scan) | いいえ | 制限あり** |

\** データセットが [Data Factory Copy アクティビティ](how-to-link-azure-data-factory.md)でソース/シンクとして使用される場合、系列はサポートされています 

Azure Files では、システム既定とカスタムの分類ルールに基づいて、メタデータと分類をキャプチャするために、フル スキャンと増分スキャンがサポートされます。

csv、tsv、psv、ssv などのファイルの種類では、次のロジックが適用されている場合にスキーマが抽出されます。

1. 最初の行の値が空ではない
2. 最初の行の値が一意である
3. 最初の行の値が日付でも数値でもありません

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* アクティブな [Purview リソース](create-catalog-portal.md)。

* Purview Studio でソースを登録して管理するには、データ ソース管理者およびデータ閲覧者である必要があります。 詳細については、[Azure Purview のアクセス許可](catalog-permissions.md)に関するページを参照してください。

## <a name="register"></a>登録

このセクションでは、 [管理範囲 Studio](https://web.purview.azure.com/)を使用して Azure 管理範囲に Azure Files を登録する方法について説明します。

### <a name="authentication-for-registration"></a>登録の認証

現在、Azure ファイル共有の認証を設定する方法は 1 つだけです。

- アカウント キー

#### <a name="account-key-to-register"></a>登録するアカウントキー

選択した認証方法が **アカウント キー** の場合は、アクセス キーを取得して、キー コンテナーに格納する必要があります。

1. ストレージ アカウントに移動します
1. **[設定] > [アクセス キー]** を選択します
1. "*キー*" をコピーし、次の手順のためにどこかに保存します
1. お使いのキー コンテナーに移動する
1. **[設定] > [シークレット]** の順に選択します。
1. **[+ 生成/インポート]** を選択し、 **[名前]** と *[値]* にストレージ アカウントの **キー** を入力します
1. **[作成]** を選択して完了します。
1. 自分のキー コンテナーが Purview にまだ接続されていない場合は、[新しいキー コンテナーの接続を作成](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)する必要があります
1. 最後に、キーを使用して[新しい資格情報を作成](manage-credentials.md#create-a-new-credential)し、スキャンを設定します

### <a name="steps-to-register"></a>登録する手順

新しい Azure Files アカウントをデータ カタログに登録するには、次の手順のようにします。

1. Purview Data Studio に移動します。
1. 左側のナビゲーションで **[Data Map]** を選択します。
1. **[登録]** を選択します
1. **[ソースの登録]** で、 **[Azure Files]** を選択します
1. **[続行]** を選択します

:::image type="content" source="media/register-scan-azure-files/register-sources.png" alt-text="新しいデータ ソースの登録" border="true":::

**[ソースの登録 (Azure Files)]** 画面で、次の手順のようにします。

1. データ ソースがカタログに表示される際の **[名前]** を入力します。
2. Azure サブスクリプションを選択して、Azure Storage アカウントの検索結果を絞り込みます。
3. Azure Storage アカウントを選択します。
4. コレクションを選択するか、新しいものを作成します (省略可能)。
5. **[登録]** を選択してデータ ソースを登録します。

:::image type="content" source="media/register-scan-azure-files/azure-file-register-source.png" alt-text="ソースの登録のオプション" border="true":::

## <a name="scan"></a>スキャン

次の手順に従って、アセットを自動的に識別し、データを分類するために Azure Files をスキャンします。 スキャン全般の詳細については、[スキャンとインジェストの概要](concept-scans-and-ingestion.md)に関するページを参照してください。

### <a name="create-and-run-scan"></a>スキャンの作成と実行

新しいスキャンを作成して実行するには、次の手順のようにします。

1. [Purview Studio](https://web.purview.azure.com/resource/) の左側のペインで **[Data Map]** タブを選択します。

1. 登録した Azure Files ソースを選択します。

1. **[新しいスキャン]** を選択します。

1. データ ソースに接続するためのアカウント キー資格情報を選択します。

   :::image type="content" source="media/register-scan-azure-files/set-up-scan-azure-file.png" alt-text="スキャンを設定する":::

1. リストから適切な項目を選択することによって、特定のデータベースに対するスキャンの範囲を指定することができます。

   :::image type="content" source="media/register-scan-azure-files/azure-file-scope-your-scan.png" alt-text="スキャンの範囲を指定する":::

1. 次に、スキャン ルール セットを選択します。 システムの既定のものを選択するか、既存のカスタム ルール セットを使用するか、新しいルール セットをインラインで作成することができます。

   :::image type="content" source="media/register-scan-azure-files/azure-file-scan-rule-set.png" alt-text="スキャン ルール セット":::

1. スキャン トリガーを選択します。 スケジュールを設定して実行することも、1 回限りのスキャンを実行することもできます。

   :::image type="content" source="media/register-scan-azure-files/trigger-scan.png" alt-text="trigger":::

1. スキャンを確認し、 **[保存および実行]** を選択します。

[!INCLUDE [create and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>次のステップ

ソースの登録が完了したので、以下のガイドに従って Azure Purview とご利用のデータの詳細について学習します。

- [Azure Purview のデータ分析情報](concept-insights.md)
- [Azure Purview のデータ系列](catalog-lineage-user-guide.md)
- [Data Catalog の検索](how-to-search-catalog.md)
