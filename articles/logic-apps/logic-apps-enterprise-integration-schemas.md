---
title: XML を検証するためのスキーマを追加する
description: Enterprise Integration Pack を使用する Azure Logic Apps で XML ドキュメントを検証するためのスキーマを追加します。
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 08/25/2021
ms.openlocfilehash: dc55e70e9ceaa9546890b2ed7dd5df0d705b1a92
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123099992"
---
# <a name="add-schemas-to-validate-xml-in-azure-logic-apps"></a>Azure Logic Apps で XML を検証するためのスキーマを追加する

ドキュメントが有効な XML を使用していること、Azure Logic Apps のエンタープライズ統合シナリオ用に事前に定義された形式の想定データが含まれていることを確認するために、ロジック アプリにスキーマを使用できます。 スキーマを使用して、ロジック アプリが企業間取引 (B2B) シナリオで交換するメッセージを検証することもできます。

ロジック アプリを初めて使用する場合は、次のドキュメントを参照してください。

* [Azure Logic Apps とは - リソースの種類とホスト環境](logic-apps-overview.md#resource-type-and-host-environment-differences)

* [シングルテナントの Azure Logic Apps (Standard) で統合ワークフローを作成する](create-single-tenant-workflows-azure-portal.md)

* [シングルテナントのロジック アプリのワークフローを作成する](create-single-tenant-workflows-azure-portal.md)

* [Azure Logic Apps の使用量の測定、課金、各価格モデル](logic-apps-pricing.md)

## <a name="limits"></a>制限

* **Standard** ロジック アプリ リソースには、スキーマ ファイルのサイズに関する制限はありません。

* **従量課金** ロジック アプリ リソースの場合は、統合アカウントの数とスキーマなどの成果物の数に関して制限があります。 詳細については、[Azure Logic Apps の制限と構成の情報](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)に関する記事を参照してください。

  通常、ワークフローで統合アカウントを使用していて、XML を検証する場合は、スキーマを追加するか、そのアカウントにアップロードします。 統合アカウントに含まれていないスキーマを参照またはインポートしている場合は、要素 `xsd:redefine` を使用すると次のエラーが表示されることがあります。

  `An error occurred while processing the XML schemas: ''SchemaLocation' must successfully resolve if <redefine> contains any child other than <annotation>.'.`

  このエラーを解決するには、要素 `xsd:import` または `xsd:include` を `xsd:redefine` の代わりにを使用するか、URI を使用する必要があります。

## <a name="prerequisites"></a>前提条件

* Azure アカウントとサブスクリプション。 サブスクリプションをまだお持ちでない場合には、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。

* **ロジック アプリ (Standard)** のリソースの種類を使用している場合、統合アカウントは必要ありません。 代わりに、Azure portal または Visual Studio Code でロジック アプリ リソースにスキーマを直接追加できます。 その後、"*同じロジック アプリ リソース*" 内の複数のワークフローでこれらのスキーマを使用できます。

* **ロジック アプリ (従量課金)** のリソースの種類を使用している場合は、エンタープライズ統合および企業間 (B2B) ソリューションで使用するスキーマや他の成果物を格納できる[統合アカウント リソース](logic-apps-enterprise-integration-create-integration-account.md)が必要です。 このリソースでは、次の要件が満たされている必要があります。

  * ロジック アプリ リソースと同じ Azure サブスクリプションに関連付けられている。

  * XML 検証アクションを使用する予定のロジック アプリ リソースと同じ場所または Azure リージョンに存在する。

  * スキーマを使用するときに、ロジック アプリ リソースに[リンク](logic-apps-enterprise-integration-create-integration-account.md#link-account)される。

    従量課金ロジック アプリのワークフローで使用するスキーマを作成して追加するには、ロジック アプリ リソースはまだ必要ありません。 ただし、ワークフローでそれらのスキーマを使用する準備ができたときは、それらのスキーマを格納するリンクされた統合アカウントがロジック アプリ リソースに必要です。

* スキーマが [2 MB 以下](#smaller-schema)の場合は、Azure portal から "*直接*" スキーマを統合アカウントに追加できます。 一方、スキーマが 2 MB を超えていても、[スキーマのサイズ制限](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits)を超えていない場合は、スキーマを Azure ストレージ アカウントにアップロードできます。 そのスキーマを統合アカウントに追加するには、統合アカウントからストレージ アカウントにリンクする方法があります。 このタスクを実行するには、以下が必要です。

    | Item | 説明 |
    |------|-------------|
    | [Azure Storage アカウント](../storage/common/storage-account-overview.md) | このアカウントに、スキーマ用の Azure BLOB コンテナーを作成します。 [ストレージ アカウントの作成方法](../storage/common/storage-account-create.md)を確認してください。 |
    | BLOB コンテナー | このコンテナーに、スキーマをアップロードできます。 また、後で統合アカウントにスキーマを追加するときに、このコンテナーのコンテンツ URI が必要です。 [BLOB コンテナーの作成方法についてはこちら](../storage/blobs/storage-quickstart-blobs-portal.md)を参照してください。 |
    | [Azure 記憶域エクスプローラー](../vs-azure-tools-storage-manage-with-storage-explorer.md) | このツールを使用すると、ストレージ アカウントと BLOB コンテナーをより簡単に管理できます。 Storage Explorer を使用するには、次の手順を選択します。 <p>- Azure portal で、自分のストレージ アカウントを選択します。 ストレージ アカウント メニューから **[Storage Explorer]** を選択します。 <p>- デスクトップ バージョンの場合、[Azure Storage Explorer をダウンロードしてインストール](https://www.storageexplorer.com/)します。 次に、「[Storage Explorer の概要](../vs-azure-tools-storage-manage-with-storage-explorer.md)」の手順に従って Storage Explorer をストレージ アカウントに接続します。 詳しくは、「[クイック スタート: Azure Storage Explorer を使用してオブジェクト ストレージ内に BLOB を作成する](../storage/blobs/storage-quickstart-blobs-storage-explorer.md)」を参照してください。  |
    |||

  従量課金ロジック アプリ リソース用にさらに大きなマップを追加するには、[Azure Logic Apps REST API - Schemas](/rest/api/logic/schemas/create-or-update) を使用することもできます。 ただし、Standard ロジック アプリ リソースの場合は、現在は Azure Logic Apps REST API を使用できません。

<a name="add-schemas"></a>

## <a name="add-schemas"></a>スキーマを追加する

### <a name="consumption-resource"></a>[従量課金リソース](#tab/consumption-1)

1. Azure アカウントの資格情報で [Azure portal](https://portal.azure.com) にサインインします。

1. Azure のメインの検索ボックスに「`integration accounts`」と入力し、 **[統合アカウント]** を選択します。

1. スキーマを追加する統合アカウントを選択します。

1. 統合アカウント メニューで、 **[設定]** の **[スキーマ]** を選択します。

1. **[スキーマ]** ペインのツール バーで、 **[追加]** を選択します。

スキーマ (.xsd) ファイルのサイズに基づいて、[最大 2 MB](#smaller-schema)、または [2 MB を超える最大 8 MB](#larger-schema) のスキーマをアップロードする手順を実行します。

<a name="smaller-schema"></a>

### <a name="add-schemas-up-to-2-mb"></a>最大 2 MB のスキーマを追加する

1. **[スキーマの追加]** で、スキーマの名前を入力します。 **[小さいファイル]** を選択したままにします。 **[スキーマ]** ボックスの横にあるフォルダー アイコンを選択します。 アップロードするスキーマを検索して選択します。

1. 準備ができたら、 **[OK]** を選択します。

   スキーマのアップロードが完了したら、 **[スキーマ]** 一覧にスキーマが表示されます。

<a name="larger-schema"></a>

### <a name="add-schemas-more-than-2-mb"></a>2 MB を超えるスキーマを追加する

より大きなスキーマを追加するには、スキーマを Azure ストレージ アカウントの Azure BLOB コンテナーにアップロードします。 スキーマを追加する手順は、ご使用の BLOB コンテナーがパブリック読み取りアクセス権を持っているかどうかによって変わります。 そのため、まず最初に BLOB コンテナーがパブリック読み取りアクセス権を持っているかどうかを確認します。このとき、「[BLOB コンテナーのパブリック アクセス レベルを設定する](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)」の手順に従います。

#### <a name="check-container-access-level"></a>コンテナーのアクセス レベルを確認する

1. Azure ストレージ エクスプローラーを開きます。 Explorer ウィンドウで、Azure サブスクリプションがまだ展開されていない場合は展開します。

1. **[ストレージ アカウント]** > {*自分のストレージ アカウント*} > **[BLOB コンテナー]** の順に展開します。 BLOB コンテナーを選択します。

1. BLOB コンテナーのショートカット メニューから **[Set Public Access Level]\(パブリック アクセス レベルの設定\)** を選択します。

   * BLOB コンテナーに少なくともパブリック アクセス権がある場合は、 **[キャンセル]** を選択し、このページで後述する「[パブリック アクセス権を持つコンテナーにアップロードする](#public-access)」の手順に従います

     ![パブリック アクセス](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * BLOB コンテナーにパブリック アクセス権がない場合は、 **[キャンセル]** を選択し、このページで後述する「[パブリック アクセス権を持たないコンテナーにアップロードする](#public-access)」の手順に従います

     ![パブリック アクセス権なし](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access"></a>

#### <a name="upload-to-containers-with-public-access"></a>パブリック アクセス権を持つコンテナーにアップロードする

1. スキーマをストレージ アカウントにアップロードします。 右側のウィンドウで、 **[アップロード]** を選択します。

1. アップロードを完了したら、アップロードしたスキーマを選択します。 ツール バーで **[URL のコピー]** を選択し、スキーマの URL をコピーします。

1. **[スキーマの追加]** ウィンドウが開いている Azure portal に戻ります。 アセンブリの名前を入力します。 **[大きいファイル (2 MB 超)]** を選択します。

   **[スキーマ]** ボックスではなく、 **[コンテンツ URI]** ボックスが表示されます。

1. **[コンテンツ URI]** ボックスに、スキーマの URL を貼り付けます。 スキーマの追加を完了します。

スキーマのアップロードが完了したら、 **[スキーマ]** 一覧にスキーマが表示されます。 統合アカウントの **[概要]** ページの **[成果物]** には、アップロードしたスキーマが表示されます。

<a name="no-public-access"></a>

#### <a name="upload-to-containers-without-public-access"></a>パブリック アクセス権を持たないコンテナーにアップロードする

1. スキーマをストレージ アカウントにアップロードします。 右側のウィンドウで、 **[アップロード]** を選択します。

1. アップロードが完了したら、スキーマの共有アクセス署名 (SAS) を生成します。 スキーマのショートカット メニューから **[Get Shared Access Signature]\(Shared Access Signature の取得\)** を選択します。

1. **[Shared Access Signature]** ウィンドウで **[Generate container-level shared access signature URI]\(コンテナーレベルの共有アクセス署名 URI の生成\)**  >  **[作成]** の順に選択します。 SAS URL が生成されたら、 **[URL]** ボックスの横にある **[コピー]** を選択します。

1. **[スキーマの追加]** ウィンドウが開いている Azure portal に戻ります。 **[大きいファイル]** を選択します。

   **[スキーマ]** ボックスではなく、 **[コンテンツ URI]** ボックスが表示されます。

1. **[コンテンツ URI]** ボックスに、前に生成した SAS URI を貼り付けます。 スキーマの追加を完了します。

スキーマのアップロードが完了したら、 **[スキーマ]** 一覧にスキーマが表示されます。 統合アカウントの **[概要]** ページの **[成果物]** には、アップロードしたスキーマが表示されます。

### <a name="standard-resource"></a>[Standard リソース](#tab/standard-1)

#### <a name="azure-portal"></a>Azure portal

1. ロジック アプリ リソースのメニューの **[設定]** で、 **[スキーマ]** を選択します。

1. **[スキーマ]** ペインのツール バーで、 **[追加]** を選択します。

1. **[スキーマの追加]** で、スキーマの一意の名前を入力します。

1. **[スキーマ]** ボックスの横にあるフォルダー アイコンを選択します。 アップロードするスキーマを選択します。

1. 準備ができたら、 **[OK]** を選択します。

   スキーマ ファイルのアップロードが完了すると、 **[スキーマ]** の一覧にスキーマが表示されます。 統合アカウントの **[概要]** ページの **[成果物]** には、アップロードしたスキーマも表示されます。

#### <a name="visual-studio-code"></a>Visual Studio Code

1. ロジック アプリ プロジェクトの構造で、 **[Artifacts]** フォルダーを開き、次に **[Schemas]** フォルダーを開きます。

1. **[Schemas]** フォルダーにスキーマを追加します。

---

## <a name="edit-schemas"></a>スキーマを編集する

既存のスキーマを更新するには、目的の変更を含む新しいスキーマ ファイルをアップロードする必要があります。 ただし、編集対象の既存のスキーマをまずダウンロードすることができます。

### <a name="consumption-resource"></a>[従量課金リソース](#tab/consumption-2)

1. [Azure portal](https://portal.azure.com) で、統合アカウントをまだ開いていない場合は開きます。

1. 統合アカウント メニューで、 **[設定]** の **[スキーマ]** を選択します。

1. **[スキーマ]** ペインが開いたら、スキーマを選択します。 最初にスキーマをダウンロードして編集するには、 **[スキーマ]** ペインのツール バーで **[ダウンロード]** を選択して、スキーマを保存します。

1. 更新したスキーマをアップロードする準備ができたら、 **[スキーマ]** ペインで更新するスキーマを選択します。 **[スキーマ]** ペインのツール バーで、 **[更新]** を選択します。

1. アップロードする更新済みスキーマを探して選択します。

   スキーマ ファイルのアップロードが完了したら、 **[スキーマ]** 一覧に更新されたスキーマが表示されます。

### <a name="standard-resource"></a>[Standard リソース](#tab/standard-2)

1. [Azure portal](https://portal.azure.com) で、ロジック アプリ リソースをまだ開いていない場合は開きます。

1. ロジック アプリ リソースのメニューの **[設定]** で、 **[スキーマ]** を選択します。

1. **[スキーマ]** ペインが開いたら、スキーマを選択します。 最初にスキーマをダウンロードして編集するには、 **[スキーマ]** ペインのツール バーで **[ダウンロード]** を選択して、スキーマを保存します。

1. **[スキーマ]** ペインのツール バーで、 **[追加]** を選択します。

1. **[スキーマの追加]** で、スキーマの一意の名前を入力します。

1. **[スキーマ]** ボックスの横にあるフォルダー アイコンを選択します。 アップロードするスキーマを選択します。

1. 準備ができたら、 **[OK]** を選択します。

   スキーマ ファイルのアップロードが完了したら、 **[スキーマ]** 一覧に更新されたスキーマが表示されます。

---

## <a name="delete-schemas"></a>スキーマを削除する

### <a name="consumption-resource"></a>[従量課金リソース](#tab/consumption-3)

1. [Azure portal](https://portal.azure.com) で、統合アカウントをまだ開いていない場合は開きます。

1. 統合アカウント メニューで、 **[設定]** の **[スキーマ]** を選択します。

1. **[スキーマ]** ペインが開いたら、スキーマを選択し、 **[削除]** を選択します。

1. スキーマの削除を確定するには、 **[はい]** を選択します。

### <a name="standard-resource"></a>[Standard リソース](#tab/standard-3)

1. [Azure portal](https://portal.azure.com) で、ロジック アプリ リソースをまだ開いていない場合は開きます。

1. ロジック アプリ リソースのメニューの **[設定]** で、 **[スキーマ]** を選択します。

1. **[スキーマ]** ペインが開いたら、スキーマを選択し、 **[削除]** を選択します。

1. スキーマの削除を確定するには、 **[はい]** を選択します。

---

## <a name="next-steps"></a>次のステップ

* [Azure Logic Apps でワークフロー用の XML を検証する](logic-apps-enterprise-integration-xml-validation.md)
* [Azure Logic Apps でワークフロー用に XML を変換する](logic-apps-enterprise-integration-transform.md)