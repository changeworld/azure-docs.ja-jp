---
title: Azure Storage の BLOB をスキャンする方法
description: Azure Purview データ カタログで Azure Blob Storage をスキャンする方法について説明します。
author: shsandeep123
ms.author: sandeepshah
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 05/08/2021
ms.openlocfilehash: 7b295fd67052d91c229977571056b3ea95d56773
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2021
ms.locfileid: "122177853"
---
# <a name="register-and-scan-azure-blob-storage"></a>Azure Blob Storage の登録とスキャン

この記事では、Purview に Azure Blob Storage アカウントを登録し、スキャンを設定する方法について説明します。

## <a name="supported-capabilities"></a>サポートされる機能

Azure Blob Storage では、フル スキャンと増分スキャンがサポートされ、メタデータとスキーマがキャプチャされます。 また、システムおよびカスタムの分類規則に基づいてデータが自動的に分類されます。

csv、tsv、psv、ssv などのファイルの種類では、次のロジックが適用されている場合にスキーマが抽出されます。

1. 最初の行の値が空ではない
2. 最初の行の値が一意である
3. 最初の行の値が日付でも数値でもない

## <a name="prerequisites"></a>前提条件

- データ ソースを登録する前に、Azure Purview アカウントを作成します。 Purview アカウントの作成の詳細については、[Azure Purview アカウントの作成](create-catalog-portal.md)に関するクイックスタートを参照してください。
- Azure Purview データ ソース管理者である必要があります

## <a name="setting-up-authentication-for-a-scan"></a>スキャンでの認証の設定

Azure Blob Storage の認証を設定するには、次の 3 つの方法があります。

- マネージド ID
- アカウント キー
- サービス プリンシパル

### <a name="managed-identity-recommended"></a>マネージド ID (推奨)

**マネージド ID** を選択する場合、接続を設定するには、まず、データ ソースをスキャンするためのアクセス許可を Purview アカウントに付与する必要があります。

1. ストレージ アカウントに移動します。
1. 左側のナビゲーション メニューから **[アクセス制御 (IAM)]** を選択します。 
1. **[+ 追加]** を選択します。
1. **[ロール]** に **[ストレージ BLOB データ リーダー]** を設定し、 **[選択]** 入力ボックスに Azure Purview アカウント名を入力します。 次に、 **[保存]** を選択して、このロールの割り当てを Purview アカウントに付与します。

> [!Note]
> 詳細については、「[Azure Active Directory を使用して BLOB とキューへのアクセスを承認する](../storage/blobs/authorize-access-azure-active-directory.md)」の手順を参照してください

### <a name="account-key"></a>アカウント キー

選択した認証方法が **アカウント キー** の場合は、アクセス キーを取得して、キー コンテナーに格納する必要があります。

1. ストレージ アカウントに移動します
1. **[設定] > [アクセス キー]** を選択します
1. "*キー*" をコピーし、次の手順のためにどこかに保存します
1. お使いのキー コンテナーに移動する
1. **[設定] > [シークレット]** の順に選択します。
1. **[+ 生成/インポート]** を選択し、 **[名前]** と *[値]* にストレージ アカウントの **キー** を入力します
1. **[作成]** を選択して完了します。
1. キー コンテナーが Purview にまだ接続されていない場合は、[新しいキー コンテナーの接続を作成](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)する必要があります。
1. 最後に、キーを使用して[新しい資格情報を作成](manage-credentials.md#create-a-new-credential)し、スキャンを設定します

### <a name="service-principal"></a>サービス プリンシパル

サービス プリンシパルを使用するには、既存のものを使用するか、新しく作成することができます。 

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
1. **[設定] > [シークレット]** の順に選択します。
1. **[生成/インポート]** を選択し、サービス プリンシパルの **クライアント シークレット** として任意の **名前** と **値** を入力します
1. **[作成]** を選択して完了します。
1. キー コンテナーが Purview にまだ接続されていない場合は、[新しいキー コンテナーの接続を作成](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)する必要があります。
1. 最後に、サービス プリンシパルを使用して[新しい資格情報を作成](manage-credentials.md#create-a-new-credential)し、スキャンを設定します

#### <a name="granting-the-service-principal-access-to-your-blob-storage"></a>Blob Storage へのアクセス権をサービス プリンシパルに付与する

1. ストレージ アカウントに移動します。
1. 左側のナビゲーション メニューから **[アクセス制御 (IAM)]** を選択します。 
1. **[+ 追加]** を選択します。
1. **[ロール]** に **[ストレージ BLOB データ リーダー]** を設定し、 **[選択]** 入力ボックスにサービス プリンシパル名またはオブジェクト ID を入力します。 次に、 **[保存]** を選択して、このロールの割り当てをサービス プリンシパルに付与します。

## <a name="firewall-settings"></a>ファイアウォールの設定

> [!NOTE]
> ストレージ アカウントに対してファイアウォールが有効になっている場合は、スキャンを設定するときに、 **[マネージド ID]** の認証方法を使用する必要があります。

1. [Azure portal](https://portal.azure.com) でストレージ アカウントに移動します
1. **[設定] > [ネットワーク]** に移動します
1. **[許可するアクセス元]** の **[選択されたネットワーク]** を選択します
1. **[ファイアウォール]** セクションで、 **[信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します]** を選択し、 **[保存]** を選択します

:::image type="content" source="./media/register-scan-azure-blob-storage-source/firewall-setting.png" alt-text="ファイアウォールの設定を示すスクリーンショット":::

## <a name="register-an-azure-blob-storage-account"></a>Azure Blob Storage アカウントを登録する

新しい BLOB アカウントをデータ カタログに登録するには、次の手順を実行します。

1. ポータルで Purview アカウントから Purview Studio に移動します。
1. Purview Studio のホームページで **[ソースの登録]** をクリックします。
1. **[登録]** を選択します
1. **[ソースの登録]** で、 **[Azure Blob Storage]** を選択します
1. **[続行]** を選択します

**[ソースの登録 (Azure Blob Storage)]** 画面で、次の手順を実行します。

1. データ ソースがカタログに表示される際の **[名前]** を入力します。 
1. サブスクリプションを選択して、ストレージ アカウントをフィルター処理します。
1. ストレージ アカウントを選択します。
1. コレクションを選択するか、新しいものを作成します (省略可能)。
1. **[登録]** を選択してデータ ソースを登録します。

:::image type="content" source="media/register-scan-azure-blob-storage-source/register-sources.png" alt-text="ソースの登録のオプション" border="true":::

## <a name="creating-and-running-a-scan"></a>スキャンを作成し、実行する

新しいスキャンを作成して実行するには、次の操作を行います。

1. Purview Studio の左側のペインで **[Data Map]** タブを選択します。

1. 登録した Azure Blob のデータ ソースをクリックします。

1. **[新しいスキャン]** をクリックします。

1. 対象のデータ ソースに接続するための資格情報を選択します。 

   :::image type="content" source="media/register-scan-azure-blob-storage-source/set-up-scan-blob.png" alt-text="スキャンを設定する":::

1. 特定のフォルダーまたはサブフォルダーをリストから選んで、スキャンのスコープに指定できます。

   :::image type="content" source="media/register-scan-azure-blob-storage-source/blob-scope-your-scan.png" alt-text="スキャンの範囲を指定する":::

1. 次に、スキャン ルール セットを選択します。 システムの既定のものを選択するか、既存のカスタム ルール セットを使用するか、新しいルール セットをインラインで作成することができます。

   :::image type="content" source="media/register-scan-azure-blob-storage-source/blob-scan-rule-set.png" alt-text="スキャン ルール セット":::

1. スキャン トリガーを選択します。 スケジュールを設定することも、1 回限りのスキャンを実行することもできます。

   :::image type="content" source="media/register-scan-azure-blob-storage-source/trigger-scan.png" alt-text="trigger":::

1. スキャンを確認し、 **[保存および実行]** を選択します。

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>次のステップ

- [Azure Purview データ カタログを参照する](how-to-browse-catalog.md)
- [Azure Purview データ カタログを検索する](how-to-search-catalog.md)