---
title: Azure Blob Storage に接続する
description: Azure Logic Apps を使用して Azure ストレージ アカウントに BLOB を作成して管理します。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 10/11/2021
tags: connectors
ms.openlocfilehash: 7ceafa54a202433e84535b19e73e99d38726dd0c
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130064067"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-by-using-azure-logic-apps"></a>Azure Logic Apps を使用して Azure Blob Storage に BLOB を作成して管理する

Azure Logic Apps のワークフローから、Azure ストレージ アカウントに BLOB として格納されているファイルに [Azure Blob Storage コネクタ](/connectors/azureblobconnector/)を使用してアクセス、管理することができます。 このコネクタは、ワークフローが BLOB 操作に使用できるトリガーとアクションを提供します。 これにより、ストレージ アカウント内のファイルを管理するタスクを自動化することができます。 たとえば、[コネクタのアクション](/connectors/azureblobconnector/#actions)には、BLOB のチェック、削除、読み取り、アップロードなどが含まれています。 BLOB が追加または変更されると、[利用可能なトリガー](/connectors/azureblobconnector/#triggers)が作動します。

Blob Storage には、**Logic App (従量課金プラン)** および **Logic App (Standard)** の両方のリソースの種類から接続することができます。 コネクタは、マルチテナントの Azure Logic Apps、シングルテナントの Azure Logic Apps、および統合サービス環境 (ISE) のロジック アプリのワークフローで使用できます。 **Logic App (Standard)** を使用している場合、Blob Storage では組み込みの操作、*および* マネージド コネクタの操作を利用できます。

> [!IMPORTANT]
> ロジック アプリ ワークフローは、同じリージョンにある場合、ファイアウォールの内側にあるストレージ アカウントに直接アクセスすることはできません。 回避策として、お使いのロジック アプリとストレージ アカウントを別のリージョンに格納してください。 Azure Logic Apps からファイアウォールの内側にあるストレージ アカウントへのアクセスを有効にする方法の詳細については、このトピックの後半の「[ファイアウォールの背後にあるストレージ アカウントにアクセスする](#access-storage-accounts-behind-firewalls)」を確認してください。

## <a name="prerequisites"></a>前提条件

- Azure アカウントとサブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。

- [Azure ストレージ アカウントとストレージ コンテナー](../storage/blobs/storage-quickstart-blobs-portal.md)

- 自分の BLOB ストレージ アカウントにアクセスするロジック アプリ ワークフロー。 Blob Storage トリガーを使用してワークフローを起動する場合は、[空のロジック アプリ ワークフロー](../logic-apps/quickstart-create-first-logic-app-workflow.md)が必要です。

## <a name="limits"></a>制限

- [統合サービス環境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) で実行されているロジック アプリ ワークフローの場合、このコネクタの ISE のラベルが付いたバージョンでは、代わりに [ISE メッセージ制限](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)が使用されます。

- 既定では、Blob Storage アクションで "*50 MB 以下*" のファイルの読み取りまたは書き込みが可能です。 Blob Storage のアクションは、50 MB から 1,024 MB までのファイルを処理するために、[メッセージ チャンク](../logic-apps/logic-apps-handle-large-messages.md)をサポートしています。 [**BLOB コンテンツの取得** アクションは](/connectors/azureblobconnector/#get-blob-content)、暗黙的にチャンクを使用します。

- Blob Storage トリガーではチャンクはサポートされていません。 ファイルのコンテンツを要求すると、トリガーによって 50 MB 以下のファイルのみが選択されます。 50 MB より大きいファイルを取得するには、次のパターンに従います。

  - [**BLOB が追加または変更されたとき (プロパティのみ)**](/connectors/azureblobconnector/#when-a-blob-is-added-or-modified-(properties-only)) といった、ファイルのプロパティを返す Blob Storage トリガーを使用します。

  - Blob Storage トリガーの [**BLOB コンテンツの取得** アクション](/connectors/azureblobconnector/#get-blob-content)に従います。これは、暗黙的にチャンクを使用してファイル全体を読み取ります。

## <a name="connector-reference"></a>コネクタのレファレンス

トリガー、アクション、制限など、このコネクタの詳しい技術情報については、[コネクタのリファレンス ページ](/connectors/azureblobconnector/)を確認してください。 Blob Storage コネクタを使用しない場合は、[代わりに HTTP トリガーまたはアクションを、マネージド ID と使用して BLOB 操作を行うことができます](#access-blob-storage-with-managed-identities)。

## <a name="add-blob-storage-trigger"></a>Blob Storage トリガーを追加する

Azure Logic Apps では、すべてのワークフローは、必ず[トリガー](../logic-apps/logic-apps-overview.md#logic-app-concepts)で開始します。トリガーは、特定のイベントが起こるか特定の条件が満たされたときに発生します。

このコネクタに用意されているトリガーは 1 つで、[ **[When a blob is Added or Modified in Azure Storage]\(Azure Storage 内の BLOB が追加または変更されたとき\)** または **[BLOB が追加または変更されたとき (プロパティのみ)]**](/connectors/azureblobconnector/#when-a-blob-is-added-or-modified-(properties-only)) という名前が付けられています。 ストレージ コンテナー内で BLOB のプロパティが追加されるか更新されたときに、このトリガーが作動します。 そのたびに、Azure Logic Apps エンジンによってロジック アプリ インスタンスが作成されてワークフローが開始されます。

### <a name="consumption"></a>[従量課金プラン](#tab/consumption)

マルチテナントの Azure Logic Apps でロジック アプリ ワークフローに Azure Blob Storage トリガーを追加するには、以下の手順に従います。

1. [Azure portal](https://portal.azure.com) で、ロジック アプリ ワークフローをデザイナーで開きます。

1. デザイナーの検索ボックスに、フィルターとして「`Azure blob`」と入力します。 トリガーの一覧から、 **[BLOB が追加または変更されたとき (プロパティのみ)]** という名前のトリガーを選択します。

   :::image type="content" source="./media/connectors-create-api-azureblobstorage/consumption-trigger-add.png" alt-text="従量課金プランのロジック アプリと &quot;BLOB が追加または変更されたとき (プロパティのみ)&quot; というトリガーが選択されている Azure portal とワークフロー デザイナーのスクリーンショット。":::

1. 接続の詳細の入力を求められたら、[Azure Blob Storage 接続を今すぐ作成](#connect-blob-storage-account)します。

1. トリガーに必要な情報を指定します。

   1. **[コンテナー]** のプロパティ値については、フォルダー アイコンを選択して BLOB ストレージ コンテナーを参照します。 または、パスを手動で入力します。

   1. 必要に応じて他のトリガー設定を構成します。

      :::image type="content" source="./media/connectors-create-api-azureblobstorage/consumption-trigger-configure.png" alt-text="パラメーターの構成を含む Azure BLOB トリガーを示すスクリーンショット。":::

1. ワークフローに 1 つまたは複数のアクションを追加します。

1. デザイナーのツール バーで **[保存]** を選択して、変更を保存します。

### <a name="standard"></a>[Standard](#tab/standard)

シングルテナントの Azure Logic Apps でロジック アプリ ワークフローに Azure Blob トリガーを追加するには、以下の手順に従います。

1. [Azure portal](https://portal.azure.com) で、ロジック アプリ ワークフローをデザイナーで開きます。

1. デザイナーで、 **[操作を選択してください]** を選択します。 開いた **[トリガーの追加]** ペインの **[Choose an operation]\(操作の選択\)** 検索ボックスの下で、**Azure Blob** "*組み込み*" トリガーを探す場合は **[組み込み]** を、**Azure Blob Storage** "*マネージド コネクタ*" トリガーを探す場合は **[Azure]** を選択します。

1. 検索ボックスに「 `Azure blob`」と入力します。 トリガーの一覧から、 **[When a blob is Added or Modified in Azure Storage]\(Azure Storage 内の BLOB が追加または変更されたとき\)** という名前のトリガーを選択します。

   :::image type="content" source="./media/connectors-create-api-azureblobstorage/standard-trigger-add.png" alt-text="Azure portal、ワークフロー デザイナー、Standard ロジック アプリ ワークフロー、および Azure Blob トリガーが選択されているスクリーンショット。":::

1. 接続の詳細の入力を求められたら、[Azure Blob Storage 接続を今すぐ作成](#connect-blob-storage-account)します。

1. トリガーに必要な情報を指定します。 **[パラメーター]** タブで、監視する BLOB の **[BLOB パス]** を追加します。

   1. BLOB のパスを見つけるには、Azure portal で自分のストレージ アカウントを開きます。

   1. ナビゲーション メニューの **[データ ストレージ]** で **[コンテナー]** を選択します。

   1. BLOB コンテナーを選択します。 コンテナーのナビゲーション メニューの **[設定]** で、 **[プロパティ]** を選択します。

   1. **[URL]** の値 (BLOB のパス) をコピーします。 このパスは、`https://<storage-container-name>/<folder-name>/{name}` のような形式になっています。 リテラル文字列 `{name}` は残したまま、実際のコンテナー名とフォルダー名で置き換えます。

      :::image type="content" source="./media/connectors-create-api-azureblobstorage/standard-trigger-configure.png" alt-text="Blob Storage トリガーとパラメーターの構成が含まれている、Standard ロジック アプリ ワークフローのワークフロー デザイナーを示すスクリーンショット。":::

1. 引き続き、1 つまたは複数のアクションを追加して、ワークフローを作成します。

1. デザイナーのツール バーで **[保存]** を選択して、変更を保存します。

---

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>BLOB ストレージ アクションを追加する

Azure Logic Apps では、[アクション](../logic-apps/logic-apps-overview.md#logic-app-concepts)とは、トリガーまたは別のアクションに続くワークフロー内のステップです。

### <a name="consumption"></a>[従量課金プラン](#tab/consumption)

マルチテナントの Azure Logic Apps でロジック アプリ ワークフローに Azure Blob Storage アクションを追加するには、以下の手順に従います。

1. [Azure portal](https://portal.azure.com) で、ワークフローをデザイナーで開きます。

1. ワークフローが空の場合は、任意のトリガーを追加します。

   この例は、[ **[繰り返し]** トリガー](connectors-native-recurrence.md)で始まります。

1. Blob Storage アクションを追加するトリガーまたはアクションの下にある **[新しいステップ]** または **[アクションの追加]** (ステップの途中の場合) を選択します。

1. デザイナーの検索ボックスに、「`Azure blob`」と入力します。 使用する Blob Storage アクションを選択します。

   この例では、**BLOB コンテンツの取得** という名前のアクションを使用します。

   :::image type="content" source="./media/connectors-create-api-azureblobstorage/consumption-action-add.png" alt-text="従量課金プランのロジック アプリのデザイナー画面のスクリーンショット。利用可能な Blob Storage アクションが一覧表示されている。":::

1. 接続の詳細の入力を求められたら、[自分の BLOB ストレージ アカウントへの接続を作成](#connect-blob-storage-account)します。

1. アクションに必要な情報を指定します。

    1. **[BLOB]** のプロパティの値については、フォルダー アイコンを選択して BLOB ストレージ コンテナーを参照します。 または、パスを手動で入力します。

       :::image type="content" source="./media/connectors-create-api-azureblobstorage/consumption-action-configure.png" alt-text="従量課金プランのロジック アプリのデザイナー画面のスクリーンショット。Blob Storage アクションの構成が表示されている。":::

    1. 必要に応じて他のアクション設定を構成します。

### <a name="standard"></a>[Standard](#tab/standard)

シングルテナントの Azure Logic Apps でロジック アプリ ワークフローに Azure Blob アクションを追加するには、以下の手順に従います。

1. [Azure portal](https://portal.azure.com) で、ワークフローをデザイナーで開きます。

1. ワークフローが空の場合は、任意のトリガーを追加します。

   この例は、[ **[繰り返し]** トリガー](connectors-native-recurrence.md)で始まります。

1. Blob Storage アクションを追加するトリガーまたはアクションで、 **[Insert a new step]\(新しいステップを挿入\)** ( **+** ) > **[アクションの追加]** の順に選択します。

1. デザイナーで、 **[操作の追加]** が選択されていることを確認してください。 開いた **[アクションの追加]** ペインの **[Choose an operation]\(操作の選択\)** 検索ボックスの下で、**Azure Blob** "*組み込み*" アクションを探す場合は **[組み込み]** を、**Azure Blob Storage** "*マネージド コネクタ*" アクションを探す場合は **[Azure]** を選択します。

1. 検索ボックスに「 `Azure blob`」と入力します。 使用する Azure BLOB アクションを選択します。

   この例では、BLOB コンテンツを読み込むだけの、 **[Reads Blob Content from Azure Storage]\(Azure Storage から BLOB の内容を読み取る\)** という名前のアクションを使用します。 後でコンテンツを表示するには、別のコネクタを使用して、BLOB コンテンツを含むファイルを作成する別のアクションを追加します。 たとえば、BLOB の内容に基づいてファイルを作成する OneDrive アクションを追加することができます。

   :::image type="content" source="./media/connectors-create-api-azureblobstorage/standard-action-add.png" alt-text="Azure portal と、Standard ロジック アプリ ワークフローと使用可能な Azure Blob Storage アクションを含むワークフロー デザイナーを示すスクリーンショット。":::

1. 接続の詳細の入力を求められたら、[自分の BLOB ストレージ アカウントへの接続を作成](#connect-blob-storage-account)します。

1. アクションに必要な情報を指定します。

    1. **[コンテナー名]** に、使用する BLOB ストレージ コンテナーのパスを入力します。

    1. **[BLOB 名]** のプロパティ値に、使用する BLOB のパスを入力します。

       :::image type="content" source="./media/connectors-create-api-azureblobstorage/standard-action-configure.png" alt-text="Standard ロジック アプリのデザイナー画面のスクリーンショット。Blob Storage トリガーを選択するところ。":::

    1. 必要に応じて他のアクション設定を構成します。

1. デザイナーのツール バーで、 **[保存]** を選択します。

1. ロジック アプリをテストして、選択したコンテナーに BLOB が存在することを確認します。

---

<a name="connect-blob-storage-account"></a>

## <a name="connect-to-blob-storage-account"></a>Blob Storage アカウントに接続する

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

### <a name="consumption"></a>[従量課金プラン](#tab/consumption)

[Azure Blob Storage トリガー](#add-blob-storage-trigger)または [Azure Blob Storage アクション](#add-blob-storage-action)を構成するには、あらかじめストレージ アカウントに接続する必要があります。 接続するには、次のプロパティが必要になります。

| プロパティ | 必須 | 値 | 説明 |
|----------|----------|-------|-------------|
| **接続名** | はい | <*connection-name*> | 接続に使用する名前。 |
| **ストレージ アカウント** | はい | <*storage-account*> | 自分のストレージ アカウントを一覧から選択するか、文字列を指定します。 <p>**注意**: 接続文字列を見つけるには、ストレージ アカウントのページに移動します。 ナビゲーション メニューの **[セキュリティとネットワーク]** で **[アクセス キー]**  >  **[キーの表示/非表示]** の順に選択します。 使用可能な接続文字列の値のいずれかをコピーします。 |
|||||

マルチテナントの Azure Logic Apps のロジック アプリ ワークフローからの Azure Blob Storage 接続を追加するには、以下の手順に従います。

1. **[接続名]** には、接続の名前を指定します。

1. **[ストレージ アカウント]** には、BLOB コンテナーが存在しているストレージ アカウントを選択します。 または、 **[接続情報を手動で入力する]** を選択して手動でパスを入力します。

1. **[作成]** を選択して接続を確立します。

   :::image type="content" source="./media/connectors-create-api-azureblobstorage/consumption-connection-create.png" alt-text="従量課金プランのロジック アプリ ワークフローと、Azure Blob Storage ステップの新しい接続を追加するプロンプトが表示されているワークフロー デザイナーのスクリーンショット。":::

> [!NOTE]
> 接続を作成した後、代わりに使用したい既存の Azure Blob ストレージ接続が別である場合は、トリガーまたはアクションの詳細エディターで **[接続の変更]** を選択します。

ストレージ アカウントへの接続に問題がある場合は、[ファイアウォールの背後にあるストレージ アカウントにアクセスする方法](#access-storage-accounts-behind-firewalls)に関するセクションを確認してください。

### <a name="standard"></a>[Standard](#tab/standard)

[Azure Blob トリガー](#add-blob-storage-trigger)または [Azure Blob アクション](#add-blob-storage-action)を構成するには、あらかじめストレージ アカウントに接続する必要があります。 接続するには、次のプロパティが必要になります。

| プロパティ | 必須 | 値 | 説明 |
|----------|----------|-------|-------------|
| **接続名** | はい | <*connection-name*> | 接続に使用する名前。 |
| **Azure Blob Storage の接続文字列** | はい | <*storage-account*> | 自分のストレージ アカウントを一覧から選択するか、文字列を指定します。 <p>**注意**: 接続文字列を見つけるには、ストレージ アカウントのページに移動します。 ナビゲーション メニューの **[セキュリティとネットワーク]** で **[アクセス キー]**  >  **[キーの表示/非表示]** の順に選択します。 使用可能な接続文字列の値のいずれかをコピーします。 |
|||||

シングルテナントの Azure Logic Apps のロジック アプリ ワークフローからの Azure Blob Storage 接続を追加するには、以下の手順に従います。

1. **[接続名]** に、接続の名前を入力します。

1. **[Azure Blob Storage Connection String]\(Azure Blob Storage 接続文字列\)** に、使用するストレージ アカウントの接続文字列を入力します。

1. **[作成]** を選択して接続を確立します。

   :::image type="content" source="./media/connectors-create-api-azureblobstorage/standard-connection-create.png" alt-text="Standard ロジック アプリ ワークフローと、Azure Blob Storage ステップの新しい接続を追加するプロンプトが表示されているワークフロー デザイナーのスクリーンショット。":::

> [!NOTE]
> 接続を作成した後、代わりに使用したい既存の Azure Blob ストレージ接続が別である場合は、トリガーまたはアクションの詳細エディターで **[接続の変更]** を選択します。

ストレージ アカウントへの接続に問題がある場合は、[ファイアウォールの背後にあるストレージ アカウントにアクセスする方法](#access-storage-accounts-behind-firewalls)に関するセクションを確認してください。

---

## <a name="access-storage-accounts-behind-firewalls"></a>ファイアウォールの背後にあるストレージ アカウントにアクセスする

[ファイアウォールとファイアウォール規則](../storage/common/storage-network-security.md)を使用してアクセスを制限することで、Azure ストレージ アカウントにネットワーク セキュリティを追加できます。 ただし、この設定では、ストレージ アカウントへのアクセスを必要とする Azure およびその他の Microsoft サービスにおいて問題が生じます。 データセンター内のローカル通信では内部 IP アドレスが抽象化されるため、IP 制限を使用してファイアウォール規則を設定することはできません。

Blob Storage コネクタを使用してファイアウォールの背後にあるストレージ アカウントにアクセスするには:

- [他のリージョンにあるストレージ アカウントにアクセスする](#access-storage-accounts-in-other-regions)
- [信頼された仮想ネットワーク経由でストレージ アカウントにアクセスする](#access-storage-accounts-through-trusted-virtual-network)

その他、ファイアウォールの背後にあるストレージ アカウントにアクセスするための方法:

- [マネージド ID を使用して、信頼されたサービスとしてストレージ アカウントにアクセスする](#access-blob-storage-with-managed-identities)
- [Azure API Management 経由でストレージ アカウントにアクセスする](#access-storage-accounts-through-azure-api-management)

### <a name="access-storage-accounts-in-other-regions"></a>他のリージョンにあるストレージ アカウントにアクセスする

ロジック アプリ ワークフローは、同じリージョンにある場合、ファイア ウォールの背後にあるストレージ アカウントに直接アクセスできません。 回避策として、ストレージ アカウントとは異なるリージョンにロジック アプリ リソースを配置してください。 そのうえで、[リージョン内のマネージド コネクタのアウトバウンド IP アドレス](/connectors/common/outbound-ip-addresses#azure-logic-apps)にアクセスを許可します。

> [!NOTE]
> このソリューションは、Azure Table Storage コネクタと Azure Queue Storage コネクタには該当しません。 代わりに、[組み込み HTTP トリガーとアクションを使用して](../logic-apps/logic-apps-http-endpoint.md)、Table Storage または Queue Storage にアクセスします。

ストレージ アカウントのファイアウォールにアウトバウンド IP アドレスを追加するには、以下の手順を実行してください。

1. ロジック アプリ リソースのリージョンの[マネージド コネクタ送信 IP アドレス](/connectors/common/outbound-ip-addresses#azure-logic-apps)を確認します。

1. [Azure portal](https://portal.azure.com) で、ストレージ アカウント リソースを検索して開きます。

1. ストレージ アカウントのナビゲーション メニューの **[セキュリティとネットワーク]** で、 **[ネットワーク]** を選択します。

   1. **[許可するアクセス元]** の **[選択されたネットワーク]** を選択すると、関連する設定が表示されます。

   1. **[ファイアウォール]** に、アクセスする必要のある IP アドレスまたは範囲を追加します。 自分のコンピューターからストレージ アカウントにアクセスする必要がある場合は、 **[クライアント IP アドレスを追加する]** を選択します。

      :::image type="content" source="./media/connectors-create-api-azureblobstorage/storage-ip-configure.png" alt-text="Azure portal における BLOB ストレージ アカウントのネットワーク ページのスクリーンショット。許可リストに IP アドレスおよびアドレス範囲を追加するためのファイアウォール設定が表示されている。":::

   1. 終了したら、 **[保存]** を選択します。

### <a name="access-storage-accounts-through-trusted-virtual-network"></a>信頼された仮想ネットワーク経由でストレージ アカウントにアクセスする

自分が管理する Azure 仮想ネットワークにストレージ アカウントを配置した後、その仮想ネットワークを信頼された仮想ネットワークの一覧に追加することができます。 [信頼された仮想ネットワーク](../virtual-network/virtual-networks-overview.md)を介して対象のロジック アプリがストレージ アカウントにアクセスできるようにするには、仮想ネットワーク内のリソースに接続できる[統合サービス環境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) にそのロジック アプリをデプロイする必要があります。 その後、その ISE 内のサブネットを、信頼された項目の一覧に追加できます。 Blob Storage コネクタなどの Azure Storage コネクタでは、ストレージ コンテナーに直接アクセスできます。 この設定は、ISE からサービス エンドポイントを使用するのと同じエクスペリエンスです。

### <a name="access-storage-accounts-through-azure-api-management"></a>Azure API Management 経由でストレージ アカウントにアクセスする

[API Management](../api-management/api-management-key-concepts.md) に専用階層を使用する場合、API Management を使用し、ファイアウォールを介して後者の IP アドレスを許可することで、Storage API に面することができます。 基本的には、API Management によって使用される Azure 仮想ネットワークをストレージ アカウントのファイアウォール設定に追加します。 その後、API Management アクションまたは HTTP アクションを使用し、Azure Storage API を呼び出すことができます。 ただし、このオプションを選択した場合、認証プロセスを自分で処理する必要があります。 詳細については、[単純なエンタープライズ統合アーキテクチャ](/azure/architecture/reference-architectures/enterprise-integration/basic-enterprise-integration)を参照してください。

## <a name="access-blob-storage-with-managed-identities"></a>マネージド ID で Blob Storage にアクセスする

このコネクタを使用せずに Blob Storage にアクセスしたい場合は、[認証にマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) を使用することもできます。 ファイアウォールを介してストレージ アカウントにアクセスすることを Microsoft の信頼済みサービス (マネージド ID など) に許可する例外を作成できます。

ロジック アプリでマネージド ID を使用して Blob Storage にアクセスするには、以下の手順を実行します。

1. [ストレージ アカウントへのアクセスを構成します](#configure-storage-account-access)。

1. [ロジック アプリのロールの割り当てを作成します](#create-role-assignment-logic-app)。

1. [ロジック アプリにおけるマネージド ID のサポートを有効にします](#enable-managed-identity-support)。

> [!NOTE]
> このソリューションの制限事項:
>
> - ワークフローで使用できるのは、HTTP トリガーまたは HTTP アクション "*のみ*" です。
> - ストレージ アカウント接続を認証するには、マネージド ID を設定する必要があります。
> - マネージド ID で認証を行った場合、Blob Storage の組み込み操作は使用できません。
> - シングルテナント環境のロジック アプリで利用できるのは、システム割り当てマネージド ID のみです。ユーザー割り当てマネージド ID はサポートされません。

### <a name="configure-storage-account-access"></a>ストレージ アカウントへのアクセスを構成する

例外とマネージド ID のサポートを設定するには、まずストレージ アカウントへの適切なアクセスを構成します。

1. [Azure portal](https://portal.azure.com) で、ストレージ アカウント リソースを検索して開きます。

1. ストレージ アカウントのナビゲーション メニューの **[セキュリティとネットワーク]** で、 **[ネットワーク]** を選択します。

   1. **[許可するアクセス元]** の **[選択されたネットワーク]** を選択すると、関連する設定が表示されます。

   1. 自分のコンピューターからストレージ アカウントにアクセスする必要がある場合、 **[ファイアウォール]** で **[クライアント IP アドレスを追加する]** を選択します。

   1. **[例外]** の **[信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します]** を選択します。

      :::image type="content" source="./media/connectors-create-api-azureblobstorage/storage-networking-configure.png" alt-text="許可設定が表示されている、Azure portal と Blob Storage アカウントのネットワーク ペインを示すスクリーンショット。":::

   1. 終了したら、 **[保存]** を選択します。

> [!NOTE]
> ワークフローからストレージ アカウントへの接続を試みたときに "**403 Forbidden (許可されていません)** " というエラーが表示された場合、複数の原因が考えられます。 その先の手順に進む前に次の解決策を試してみてください。 まず、 **[信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します]** 設定を無効にして変更内容を保存します。 次に、設定を再度有効にして、変更を保存し直してください。

<a name="create-role-assignment-logic-app"></a>

### <a name="create-role-assignment-for-logic-app"></a>ロジック アプリのロールの割り当てを作成する

次に、ロジック アプリ リソースの[マネージド ID のサポートを有効](../logic-apps/create-managed-service-identity.md)にします。

以下の手順は、マルチテナント環境の従量課金プランのロジック アプリと、シングルテナント環境の Standard ロジック アプリのどちらでも同じです。

1. [Azure portal](https://portal.azure.com) で、ロジック アプリ リソースを開きます。

1. ロジック アプリ リソースのナビゲーション メニューで、 **[設定]** の配下にある **[ID]** を選択します。

1. **[システム割り当て]** ペインで、**[状態]** を **[オン]** に設定します (まだ有効になっていない場合)。**[保存]** を選択し、変更を確定します。 **[アクセス許可]** で、 **[Azure でのロールの割り当て]** を選択します。

   :::image type="content" source="./media/connectors-create-api-azureblobstorage/role-assignment-add-1.png" alt-text="Azure portal と、&quot;ID&quot; 設定ペインと &quot;Azure でのロール割り当ての許可&quot; ボタンが表示されているロジック アプリのリソース メニューを示すスクリーンショット。":::

1. **[Azure のロールの割り当て]** ペインで、 **[ロールの割り当ての追加]** を選択します。

   :::image type="content" source="./media/connectors-create-api-azureblobstorage/role-assignment-add-2.png" alt-text="ロジック アプリのロールの割り当てペインのスクリーンショット。選択したサブスクリプションと新しいロールの割り当てを追加するためのボタンが表示されています。":::

1. **[ロールの割り当ての追加]** ペインで、新しいロールの割り当てを次の値で設定します。

   | プロパティ | 値 | 説明 |
   |----------|-------|-------------|
   | **スコープ** | <*resource-scope*> | ロールの割り当てを適用するリソースセット。 この例では、**[ストレージ]** を選択します。 |
   | **サブスクリプション** | <*Azure-subscription*> | お使いのストレージ アカウントの Azure サブスクリプション。 |
   | **リソース** | <*storage-account-name*> | ロジック アプリ ワークフローからアクセスするストレージ アカウントの名前。 |
   | **ロール** | <*role-to-assign*> | シナリオのワークフローでリソースを操作するために必要なロール。 この例では、BLOB コンテナーと日付に対する読み取り、書き込み、削除アクセスが許可される **[ストレージ BLOB データ共同作成者]** が必要です。 ドロップダウン メニューのロールの横にある情報アイコンにマウス ポインターを合わせると、アクセス許可の詳細が表示されます。 |
   ||||

   :::image type="content" source="./media/connectors-create-api-azureblobstorage/role-assignment-configure.png" alt-text="ロールの割り当ての構成ペインのスクリーンショット。スコープ、サブスクリプション、リソース、ロールの設定が表示されている。":::

1. 操作を完了したら、**[保存]** を選択して、ロールの割り当ての作成を終了します。

<a name="enable-managed-identity-support"></a>

### <a name="enable-managed-identity-support-on-logic-app"></a>ロジック アプリのマネージド ID のサポートを有効にする

次に、[HTTP トリガーまたは HTTP アクション](connectors-native-http.md)をワークフローに追加します。 必ず、[マネージド ID を使用するように認証の種類を設定](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity)してください。

以下の手順は、マルチテナント環境の従量課金プランのロジック アプリと、シングルテナント環境の Standard ロジック アプリのどちらでも同じです。

1. デザイナーでロジック アプリ ワークフローを開きます。

1. 実際のシナリオに基づいて、**HTTP** トリガーまたはアクションをワークフローに追加します。 必要なパラメーター値を設定します。

   1. 要求の **メソッド** を選択します。 この例では、HTTP **PUT** メソッドを使用します。

   1. BLOB の **URI** を入力します。 このパスは、`https://<storage-container-name>/<folder-name>/{name}` のような形式になっています。 リテラル文字列 `{name}` は残したまま、実際のコンテナー名とフォルダー名で置き換えます。

   1. **[ヘッダー]** に、以下の項目を追加します。

      - 値 `BlockBlob` の BLOB タイプ ヘッダー `x-ms-blob-type`。

      - 適切な値の API バージョン ヘッダー `x-ms-version`。

      詳細については、「[マネージド ID を使用してアクセスを認証する](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity)」と「[Azure Storage サービスのバージョン管理](/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests)」を参照してください。

    :::image type="content" source="./media/connectors-create-api-azureblobstorage/managed-identity-connect.png" alt-text="ワークフロー デザイナーと、必要な &quot;PUT&quot; パラメータを持つ HTTP トリガーまたはアクションを示すスクリーンショット。":::

1. **[新しいパラメーターの追加]** リストから **[認証]** を選択して [マネージド ID を構成](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity)します。

    1. **[認証]** の下にある **[認証の種類]** で、 **[マネージド ID]** を選択します。

    1. **[マネージド ID]** プロパティには、 **[システム割り当てマネージド ID]** を選択します。

    :::image type="content" source="./media/connectors-create-api-azureblobstorage/managed-identity-authenticate.png" alt-text="マネージド ID 認証のパラメーターの設定が行われたワークフロー デザイナーと HTTP アクションのスクリーンショット。":::

1. 操作が完了したら、デザイナーのツールバーで、 **[保存]** を選択します。

これで [Blob service の REST API](/rest/api/storageservices/blob-service-rest-api) を呼び出して、必要なストレージ操作を実行することができます。

## <a name="next-steps"></a>次のステップ

[Azure Logic Apps のコネクタの概要](apis-list.md)