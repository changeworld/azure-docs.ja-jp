---
title: Azure Files をスキャンする方法
description: このハウツーガイドでは、Azure Files をスキャンする方法の詳細について説明します。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 06/22/2021
ms.openlocfilehash: dbb29fea5253bbd00e66f0ed3d123ddc38533b8e
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/31/2021
ms.locfileid: "123256449"
---
# <a name="register-and-scan-azure-files"></a>Azure Files の登録とスキャン

## <a name="supported-capabilities"></a>サポートされる機能

Azure Files では、システム既定とカスタムの分類ルールに基づいて、メタデータと分類をキャプチャするために、フル スキャンと増分スキャンがサポートされます。

csv、tsv、psv、ssv などのファイルの種類では、次のロジックが適用されている場合にスキーマが抽出されます。

1. 最初の行の値が空ではない
2. 最初の行の値が一意である
3. 最初の行の値が日付でも数値でもない

## <a name="prerequisites"></a>前提条件

- データ ソースを登録する前に、Azure Purview アカウントを作成します。 Purview アカウントの作成の詳細については、[Azure Purview アカウントの作成](create-catalog-portal.md)に関するクイックスタートを参照してください。
- スキャンを設定してスケジュールするには、データ ソース管理者である必要があります。詳細については、[カタログのアクセス許可](catalog-permissions.md)に関する記事を参照してください。

## <a name="setting-up-authentication-for-a-scan"></a>スキャンでの認証の設定

現在、Azure ファイル共有の認証を設定する方法は 1 つだけです。

- アカウント キー

### <a name="account-key"></a>アカウント キー

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

## <a name="register-an-azure-files-storage-account"></a>Azure Files ストレージ アカウントを登録する

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

## <a name="creating-and-running-a-scan"></a>スキャンを作成し、実行する

新しいスキャンを作成して実行するには、次の手順のようにします。

1. Purview Studio の左側にあるペインで **[Data Map]** タブを選択します。

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

- [Azure Purview データ カタログを参照する](how-to-browse-catalog.md)
- [Azure Purview データ カタログを検索する](how-to-search-catalog.md)
