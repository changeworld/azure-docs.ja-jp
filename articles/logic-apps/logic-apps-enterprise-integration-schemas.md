---
title: ワークフローの XML を検証するためのスキーマを追加する
description: Azure Logic Apps と Enterprise Integration Pack で、ワークフローの XML ドキュメントを検証するためのスキーマを追加します。
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/14/2021
ms.openlocfilehash: 2c64deb35d89d6e1381fd3b296c7c73d82567ade
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129363839"
---
# <a name="add-schemas-to-validate-xml-in-workflows-with-azure-logic-apps"></a>Azure Logic Apps でワークフローの XML を検証するためのスキーマを追加する

ドキュメントで有効な XML が使用され、必要なデータが定義済みの形式で含まれていることを確認するために、ロジック アプリ ワークフローでは、**XML 検証** アクションで XML スキーマを使用できます。 XML スキーマでは、[XML スキーマ定義 (XSD)](https://www.w3.org/TR/xmlschema11-1/) を使用して XML で表されたビジネス ドキュメントを記述します。

ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](logic-apps-overview.md)」を参照してください。 B2B エンタープライズ統合の詳細については、「[Azure Logic Apps と Enterprise Integration Pack を使用した B2B エンタープライズ統合ワークフロー](logic-apps-enterprise-integration-overview.md)」をご確認ください。

## <a name="prerequisites"></a>前提条件

* Azure アカウントとサブスクリプション。 サブスクリプションをまだお持ちでない場合には、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。

* スキーマを作成するには、次のツールを使用できます。

  * Visual Studio 2019 と [Microsoft Azure Logic Apps Enterprise Integration Tools 拡張機能](https://aka.ms/vsenterpriseintegrationtools)。

  * Visual Studio 2015 と [Microsoft Azure Logic Apps Enterprise Integration Tools for Visual Studio 2015 2.0](https://aka.ms/vsmapsandschemas) 拡張機能。

   > [!IMPORTANT]
   > この拡張機能を BizTalk Server 拡張機能と同時にインストールしないでください。 両方の拡張機能をインストールすると、予期しない動作が発生する可能性があります。 これらの拡張機能のいずれかのみがインストールされていることを確認してください。

   > [!NOTE]
   > 高解像度モニターでは、Visual Studio の[マップ デザイナーで表示の問題](/visualstudio/designers/disable-dpi-awareness)が発生することがあります。 この表示の問題を解決するには、[Visual Studio を DPI 非対応モードで再起動する](/visualstudio/designers/disable-dpi-awareness#restart-visual-studio-as-a-dpi-unaware-process)か、[DPIUNAWARE レジストリ値](/visualstudio/designers/disable-dpi-awareness#add-a-registry-entry)を追加します。

* エンタープライズ統合および B2B ワークフローで使用する成果物 (取引先、契約、証明書など) を定義して保存する[統合アカウント リソース](logic-apps-enterprise-integration-create-integration-account.md)。 このリソースでは、次の要件が満たされている必要があります。

  * ロジック アプリ リソースと同じ Azure サブスクリプションに関連付けられている。

  * **XML 検証** アクションを使用する予定のロジック アプリ リソースと同じ場所または Azure リージョンに存在する。

  * [**ロジック アプリ (従量課金)** のリソースの種類](logic-apps-overview.md#resource-type-and-host-environment-differences)を使用する場合、ワークフローで成果物を使用するには、[統合アカウントをロジック アプリ リソースにリンクする](logic-apps-enterprise-integration-create-integration-account.md#link-account)必要があります。

    **ロジック アプリ (従量課金)** のワークフローで使用するスキーマを作成して追加するには、ロジック アプリ リソースはまだ必要ありません。 ただし、ワークフローでそれらのスキーマを使用する準備ができたときは、それらのスキーマを格納するリンクされた統合アカウントがロジック アプリ リソースに必要です。

  * [**ロジック アプリ (Standard)** のリソースの種類](logic-apps-overview.md#resource-type-and-host-environment-differences)を使用する場合、統合アカウントにスキーマを保存しないため、既存のロジック アプリ リソースが必要です。 代わりに、Azure portal または Visual Studio Code を使用して、ロジック アプリ リソースにスキーマを直接追加することができます。 その後、"*同じロジック アプリ リソース*" 内の複数のワークフローでこれらのスキーマを使用できます。

    取引先、契約、証明書などの他の成果物を保存すると共に、[AS2](logic-apps-enterprise-integration-as2.md)、[X12](logic-apps-enterprise-integration-x12.md)、および [EDIFACT](logic-apps-enterprise-integration-edifact.md) 操作を使用するには、引き続き統合アカウントが必要です。 ただし、ロジック アプリ リソースを統合アカウントにリンクする必要はないため、リンク機能は存在しません。 統合アカウントは、ロジック アプリ リソースと同じ Azure サブスクリプションを使用することや、ロジック アプリ リソースと同じ場所に存在することなど、他の要件も満たす必要があります。

    > [!NOTE]
    > 現時点では、[RosettaNet](logic-apps-enterprise-integration-rosettanet.md) の操作をサポートしているのは、**ロジック アプリ (従量課金)** のリソースの種類のみです。 **ロジック アプリ (Standard)** のリソースの種類には、[RosettaNet](logic-apps-enterprise-integration-rosettanet.md) の操作が含まれていません。

* スキーマが [2 MB 以下](#smaller-schema)の場合は、Azure portal から "*直接*" スキーマを統合アカウントに追加できます。 一方、スキーマが 2 MB を超えていても、[スキーマのサイズ制限](logic-apps-limits-and-config.md#artifact-capacity-limits)を超えていない場合は、スキーマを Azure ストレージ アカウントにアップロードできます。 そのスキーマを統合アカウントに追加するには、統合アカウントからストレージ アカウントにリンクする方法があります。 このタスクを実行するには、以下が必要です。

    | Item | 説明 |
    |------|-------------|
    | [Azure Storage アカウント](../storage/common/storage-account-overview.md) | このアカウントに、スキーマ用の Azure BLOB コンテナーを作成します。 [ストレージ アカウントの作成方法](../storage/common/storage-account-create.md)を確認してください。 |
    | BLOB コンテナー | このコンテナーに、スキーマをアップロードできます。 また、後で統合アカウントにスキーマを追加するときに、このコンテナーのコンテンツ URI が必要です。 [BLOB コンテナーの作成方法についてはこちら](../storage/blobs/storage-quickstart-blobs-portal.md)を参照してください。 |
    | [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) | このツールを使用すると、ストレージ アカウントと BLOB コンテナーをより簡単に管理できます。 Storage Explorer を使用するには、次の手順を選択します。 <p>- Azure portal で、自分のストレージ アカウントを選択します。 ストレージ アカウント メニューから **[Storage Explorer]** を選択します。 <p>- デスクトップ バージョンの場合、[Azure Storage Explorer をダウンロードしてインストール](https://www.storageexplorer.com/)します。 次に、「[Storage Explorer の概要](../vs-azure-tools-storage-manage-with-storage-explorer.md)」の手順に従って Storage Explorer をストレージ アカウントに接続します。 詳しくは、「[クイック スタート: Azure Storage Explorer を使用してオブジェクト ストレージ内に BLOB を作成する](../storage/blobs/quickstart-storage-explorer.md)」を参照してください。  |
    |||

  **ロジック アプリ (従量課金)** のリソースの種類にさらに大規模なスキーマを追加するには、[Azure Logic Apps REST API - Schemas](/rest/api/logic/schemas/create-or-update) を使用することもできます。 ただし、**ロジック アプリ (Standard)** のリソースの種類の場合、Azure Logic Apps REST API は現在使用できません。

## <a name="limits"></a>制限

* **ロジック アプリ (Standard)** では、スキーマ ファイルのサイズに制限はありません。

* **ロジック アプリ (従量課金)** では、統合アカウントの数とスキーマなどの成果物の数に制限があります。 詳細については、[Azure Logic Apps の制限と構成の情報](logic-apps-limits-and-config.md#integration-account-limits)に関する記事を参照してください。

  通常、ワークフローで統合アカウントを使用していて、XML を検証する場合は、スキーマを追加するか、そのアカウントにアップロードします。 統合アカウントに含まれていないスキーマを参照またはインポートしている場合は、要素 `xsd:redefine` を使用すると次のエラーが表示されることがあります。

  `An error occurred while processing the XML schemas: ''SchemaLocation' must successfully resolve if <redefine> contains any child other than <annotation>.'.`

  このエラーを解決するには、要素 `xsd:import` または `xsd:include` を `xsd:redefine` の代わりにを使用するか、URI を使用する必要があります。

<a name="add-schema"></a>

## <a name="add-schemas"></a>スキーマを追加する

### <a name="consumption"></a>[従量課金プラン](#tab/consumption)

1. Azure アカウントの資格情報で [Azure portal](https://portal.azure.com) にサインインします。

1. Azure のメインの検索ボックスに「`integration accounts`」と入力し、 **[統合アカウント]** を選択します。

1. スキーマを追加する統合アカウントを選択します。

1. 統合アカウント メニューで、 **[設定]** の **[スキーマ]** を選択します。

1. **[スキーマ]** ペインのツール バーで、 **[追加]** を選択します。

スキーマ (.xsd) ファイルのサイズに基づいて、[最大 2 MB](#smaller-schema)、または [2 MB を超える最大 8 MB](#larger-schema) のスキーマをアップロードする手順を実行します。

<a name="smaller-schema"></a>

### <a name="add-schemas-up-to-2-mb"></a>最大 2 MB のスキーマを追加する

1. **[スキーマの追加]** ペインで、スキーマの名前を入力します。 **[小さいファイル]** を選択したままにします。 **[スキーマ]** ボックスの横にあるフォルダー アイコンを選択します。 アップロードするスキーマを検索して選択します。

1. 終了したら、 **[OK]** を選択します。

   スキーマのアップロードが完了したら、 **[スキーマ]** 一覧にスキーマが表示されます。

<a name="larger-schema"></a>

### <a name="add-schemas-more-than-2-mb"></a>2 MB を超えるスキーマを追加する

より大きなスキーマを追加するには、スキーマを Azure ストレージ アカウントの Azure BLOB コンテナーにアップロードします。 スキーマを追加する手順は、ご使用の BLOB コンテナーがパブリック読み取りアクセス権を持っているかどうかによって変わります。 そのため、まず最初に BLOB コンテナーがパブリック読み取りアクセス権を持っているかどうかを確認します。このとき、「[BLOB コンテナーのパブリック アクセス レベルを設定する](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)」の手順に従います。

#### <a name="check-container-access-level"></a>コンテナーのアクセス レベルを確認する

1.Azure Storage Explorer を開きます。 Explorer ウィンドウで、Azure サブスクリプションがまだ展開されていない場合は展開します。

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

### <a name="standard"></a>[Standard](#tab/standard)

#### <a name="azure-portal"></a>Azure portal

1. ロジック アプリ リソースのメニューの **[設定]** で、 **[スキーマ]** を選択します。

1. **[スキーマ]** ペインのツール バーで、 **[追加]** を選択します。

1. **[スキーマの追加]** ペインで、スキーマの一意の名前を入力します。

1. **[スキーマ]** ボックスの横にあるフォルダー アイコンを選択します。 アップロードするスキーマを選択します。

1. 終了したら、 **[OK]** を選択します。

   スキーマ ファイルのアップロードが完了すると、 **[スキーマ]** の一覧にスキーマが表示されます。 統合アカウントの **[概要]** ページの **[成果物]** には、アップロードしたスキーマも表示されます。

#### <a name="visual-studio-code"></a>Visual Studio Code

1. ロジック アプリ プロジェクトの構造で、 **[Artifacts]** フォルダーを開き、次に **[Schemas]** フォルダーを開きます。

1. **[Schemas]** フォルダーにスキーマを追加します。

---

<a name="edit-schema"></a>

## <a name="edit-a-schema"></a>スキーマを編集する

既存のスキーマを更新するには、目的の変更を含む新しいスキーマ ファイルをアップロードする必要があります。 ただし、編集対象の既存のスキーマをまずダウンロードすることができます。

### <a name="consumption"></a>[従量課金プラン](#tab/consumption)

1. [Azure portal](https://portal.azure.com) で、統合アカウントをまだ開いていない場合は開きます。

1. 統合アカウント メニューで、 **[設定]** の **[スキーマ]** を選択します。

1. **[スキーマ]** ペインが開いたら、スキーマを選択します。 最初にスキーマをダウンロードして編集するには、 **[スキーマ]** ペインのツール バーで **[ダウンロード]** を選択して、スキーマを保存します。

1. 更新したスキーマをアップロードする準備ができたら、 **[スキーマ]** ペインで更新するスキーマを選択します。 **[スキーマ]** ペインのツール バーで、 **[更新]** を選択します。

1. アップロードする更新済みスキーマを探して選択します。

1. 終了したら、 **[OK]** を選択します。

   スキーマ ファイルのアップロードが完了したら、 **[スキーマ]** 一覧に更新されたスキーマが表示されます。

### <a name="standard"></a>[Standard](#tab/standard)

1. [Azure portal](https://portal.azure.com) で、ロジック アプリ リソースをまだ開いていない場合は開きます。

1. ロジック アプリ リソースのメニューの **[設定]** で、 **[スキーマ]** を選択します。

1. **[スキーマ]** ペインが開いたら、スキーマを選択します。 最初にスキーマをダウンロードして編集するには、 **[スキーマ]** ペインのツール バーで **[ダウンロード]** を選択して、スキーマを保存します。

1. **[スキーマ]** ペインのツール バーで、 **[追加]** を選択します。

1. **[スキーマの追加]** ペインで、スキーマの一意の名前を入力します。

1. **[スキーマ]** ボックスの横にあるフォルダー アイコンを選択します。 アップロードするスキーマを選択します。

1. 終了したら、 **[OK]** を選択します。

   スキーマ ファイルのアップロードが完了したら、 **[スキーマ]** 一覧に更新されたスキーマが表示されます。

---

<a name="delete-schema"></a>

## <a name="delete-a-schema"></a>スキーマを削除する

### <a name="consumption"></a>[従量課金プラン](#tab/consumption)

1. [Azure portal](https://portal.azure.com) で、統合アカウントをまだ開いていない場合は開きます。

1. 統合アカウント メニューで、 **[設定]** の **[スキーマ]** を選択します。

1. **[スキーマ]** ペインが開いたら、スキーマを選択し、 **[削除]** を選択します。

1. スキーマの削除を確定するには、 **[はい]** を選択します。

### <a name="standard"></a>[Standard](#tab/standard)

1. [Azure portal](https://portal.azure.com) で、ロジック アプリ リソースをまだ開いていない場合は開きます。

1. ロジック アプリ リソースのメニューの **[設定]** で、 **[スキーマ]** を選択します。

1. **[スキーマ]** ペインが開いたら、スキーマを選択し、 **[削除]** を選択します。

1. スキーマの削除を確定するには、 **[はい]** を選択します。

---

## <a name="next-steps"></a>次のステップ

* [Azure Logic Apps でワークフロー用の XML を検証する](logic-apps-enterprise-integration-xml-validation.md)
* [Azure Logic Apps でワークフロー用に XML を変換する](logic-apps-enterprise-integration-transform.md)
