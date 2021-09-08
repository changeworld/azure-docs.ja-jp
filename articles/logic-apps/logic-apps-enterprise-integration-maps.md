---
title: XML 変換のための XSLT マップを追加する
description: Azure Logic Apps で Enterprise Integration Pack を使用して、XML を変換するための XSLT マップを作成、追加します。
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 08/26/2021
ms.openlocfilehash: c597a7d44b620ee33acec028812aa2d1651283ff
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123100456"
---
# <a name="add-xslt-maps-for-xml-transformation-in-azure-logic-apps"></a>Azure Logic Apps で XML 変換のための XSLT マップを追加する

企業間でやりとりできるよう、Azure Logic Apps で XML データを変換するには、ロジック アプリ リソースでマップ、具体的にいうと Extensible Stylesheet Language Transformation (XSLT) マップを使用します。 マップとは、データを XML ドキュメントから別の形式に変換する方法について記述した XML ドキュメントのことです。

たとえば、YearMonthDay の年月日形式 (YYYYMMDD) を使用している顧客から、B2B の注文書または請求書を定期的に受け取るとします。 自分の組織では、MonthDayYear の年月日形式 (MMDDYYYY) を使用しています。 YYYYMMDD 形式を MMDDYYYY 形式に変換するマップを用意してこれを適用してから、注文書や請求書の詳細を顧客アクティビティ データベースに保存することができます。

マップを使用すれば、あるドキュメントから別のドキュメントに名前と住所をコピーするといった、単純な変換を設定できます。 または、設定不要なマップ操作を使用して、より複雑な変換を作成することができます。

> [!NOTE]
> Azure Logic Apps では、XML 変換の処理に割り当てるメモリに上限があります。 **ロジック アプリ (従量課金)** リソースの種類に基づいてロジック アプリを作成し、マップまたはペイロードの変換でのメモリ消費量が多い場合、そのような変換は失敗し、メモ のエラーが発生する可能性があります。 このシナリオを回避するには、次のオプションを検討します。
>
> * マップまたはペイロードを編集して、メモリ消費量を減らします。
>
> * 代わりに、**ロジック アプリ (Standard)** リソースの種類を使用して、ロジック アプリを作成します。
>
>   これらのワークフローはシングルテナントの Azure Logic Apps で実行され、コンピューティング リソースとメモリ リソースに専用の柔軟なオプションが提供されます。 
>   ただし、Standard のロジック アプリ リソースでは現在、マップから外部のアセンブリを参照する操作をサポートしていません。 また、現在 Extensible Stylesheet Language Transformation (XSLT) 1.0 しかサポートしていません。

はじめてロジック アプリを使うという方は、次のドキュメントをご覧ください。

* [Azure Logic Apps とは - リソースの種類とホスト環境](logic-apps-overview.md#resource-type-and-host-environment-differences)

* [シングルテナントの Azure Logic Apps (Standard) で統合ワークフローを作成する](create-single-tenant-workflows-azure-portal.md)

* [シングルテナントのロジック アプリのワークフローの作成に関する記事](create-single-tenant-workflows-azure-portal.md)

* [Azure Logic Apps の使用量の測定、課金、価格モデル](logic-apps-pricing.md)

## <a name="limits"></a>制限

* **Standard** のロジック アプリ リソースでは、マップ ファイルのサイズに上限はありません。

* **Consumption** ロジック アプリ リソースでは、統合アカウントとマップなどのアーティファクトに対する上限があります。 詳しくは、[Azure Logic Apps の制約と構成の情報](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)に関する記事をご覧ください。

## <a name="prerequisites"></a>前提条件

* Azure アカウントとサブスクリプション。 サブスクリプションをまだお持ちでない場合には、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。

* **Logic App (Standard)** リソースを使用している場合、統合アカウントは必要ありません。 その代わり、Azure portal か Visual Studio Code で、ロジック アプリ リソースにマップを直接追加できます。 現在、XSLT 1.0 だけをサポートしています。 これらのマップは、*同一のロジック アプリ リソース* 内の複数のワークフローで共用できます。

* **Logic App (Consumption)** リソースを使用している場合は、企業間の連携および B2B ソリューションで使用するマップなどのアーティファクトを保存するための[統合アカウント リソース](logic-apps-enterprise-integration-create-integration-account.md)が必要です。 このリソースは次の要件を満たす必要があります。

  * 当該のロジック アプリ リソースと同じ Azure サブスクリプションに関連付けてある。

  * **Transform XML** アクションを使用する予定のロジック アプリ リソースと同じ場所または Azure リージョンに存在する。

  * マップを使用するロジック アプリ リソースに[リンク](logic-apps-enterprise-integration-create-integration-account.md#link-account)してある。

    Consumption のロジック アプリのワークフローで使用するマップを作成、追加するのに、ロジック アプリ リソースはまだ必要ありません。 ただし、それらのマップをワークフローで使用するときに、ロジック アプリ リソースで、それらのマップを保存するための、リンクされた統合アカウントが必要になります。

* **Logic App (Consumption)** ではマップから外部のアセンブリを参照する機能をサポートしていますが、**Logic App (Standard)** では現在この機能をサポートしていません。 アセンブリを参照すると、XSLT マップからカスタム .NET のコードへの呼び出しを直接実行できます。

  * 64 ビットのアセンブリが必要です。 変換サービスでは 64 ビット プロセスが実行されるため、32 ビット アセンブリはサポートされていません。 32 ビット アセンブリ用のソース コードがある場合は、コードを 64 ビット アセンブリとして再コンパイルします。 ソース コードがなくても、サードパーティのプロバイダーからバイナリを取得した場合は、そのプロバイダーから 64 ビット バージョンを取得します。 たとえば、一部のベンダーからは、32 ビットと 64 ビット両方のバージョンのアセンブリがパッケージで提供されています。 オプションがある場合は、代わりに 64 ビット バージョンを使用してください。

  * 統合アカウントに、特定の順序で、*アセンブリとマップの両方* をアップロードする必要があります。 [*最初にアセンブリをアップロード*](#add-assembly)し、その後、そのアセンブリを参照するマップをアップロードするようにしてください。

  * アセンブリが [2 MB 以下](#smaller-map) であれば、Azure portal で、アセンブリとマップを統合アカウントに *直接* 追加できます。 アセンブリまたはマップが 2 MB よりも大きく、[アセンブリまたはマップのサイズ上限](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits)以下である場合は、アセンブリをアップロードできる Azure Blob コンテナーと、そのコンテナーの場所を使用できます。 これにより、後でアセンブリを統合アカウントに追加する際に、その場所を指定できるようになります。 このタスクを実行するには、次の項目が必要です。

    | Item | 説明 |
    |------|-------------|
    | [Azure Storage アカウント](../storage/common/storage-account-overview.md) | このアカウントには、アセンブリの Azure BLOB コンテナーを作成します。 [ストレージ アカウントの作成方法](../storage/common/storage-account-create.md)を確認してください。 |
    | BLOB コンテナー | このコンテナーに、アセンブリをアップロードできます。 統合アカウントにアセンブリを追加するときは、このコンテナーのコンテンツ URI の場所も必要です。 [BLOB コンテナーの作成方法についてはこちら](../storage/blobs/storage-quickstart-blobs-portal.md)を参照してください。 |
    | [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) | このツールを使用すると、ストレージ アカウントと BLOB コンテナーをより簡単に管理できます。 Storage Explorer を使用するには、[Azure Storage Explorer をダウンロードしてインストール](https://www.storageexplorer.com/)します。 次に、「[Storage Explorer の概要](../vs-azure-tools-storage-manage-with-storage-explorer.md)」の手順に従って Storage Explorer をストレージ アカウントに接続します。 詳しくは、「[クイック スタート: Azure Storage Explorer を使用してオブジェクト ストレージ内に BLOB を作成する](../storage/blobs/storage-quickstart-blobs-storage-explorer.md)」を参照してください。 <p>または、Azure portal で、自分のストレージ アカウントを選択します。 ストレージ アカウント メニューから **[Storage Explorer]** を選択します。 |
    |||

  * Consumption のロジック アプリ リソースに大きなマップを追加するときは、[Azure Logic Apps REST API - Maps](/rest/api/logic/maps/createorupdate) も使用できます。 ですが、Standard のロジック アプリ リソースでは現在、Azure Logic Apps REST API は利用できません。

<a name="add-assembly"></a>

## <a name="add-referenced-assemblies-consumption-resource-only"></a>参照するアセンブリを追加する (Consumption のみ)

1. Azure アカウントの資格情報で [Azure portal](https://portal.azure.com) にサインインします。

1. Azure のメインの検索ボックスに「`integration accounts`」と入力し、 **[統合アカウント]** を選択します。

1. アセンブリを追加する統合アカウントを選択します。

1. 統合アカウントのメニューで、 **[概要]** を選択します。 **[設定]** で **[アセンブリ]** を選択します。

1. **[アセンブリ]** ペインのツール バーで、 **[追加]** を選択します。

アセンブリ ファイルのサイズに基づいて、[最大 2 MB](#smaller-assembly)、または [2 MB を超える最大 8 MB](#larger-assembly) のアセンブリをアップロードする手順を実行します。 統合アカウントでのアセンブリの数に関する制限については、[Azure Logic Apps の制限と構成](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)に関するページで確認してください。

> [!NOTE]
> アセンブリを変更する場合、マップに変更があるかどうかに関わらず、マップも更新する必要があります。

<a name="smaller-assembly"></a>

### <a name="add-assemblies-up-to-2-mb"></a>最大 2 MB のアセンブリを追加する

1. **[アセンブリの追加]** で、アセンブリの名前を入力します。 **[小さいファイル]** を選択したままにします。 **[アセンブリ]** ボックスの横にあるフォルダー アイコンを選択します。 アップロードするアセンブリを見つけて選択します。

   アセンブリを選択した後、 **[アセンブリ名]** プロパティにはアセンブリのファイル名が自動的に表示されます。

1. 準備ができたら、 **[OK]** を選択します。

   アセンブリ ファイルのアップロードが完了すると、 **[アセンブリ]** の一覧にアセンブリが表示されます。 統合アカウントの **[概要]** ペインの **[成果物]** には、アップロードしたアセンブリも表示されます。

<a name="larger-assembly"></a>

### <a name="add-assemblies-more-than-2-mb"></a>2 MB 以上のアセンブリを追加する

より大きなアセンブリを追加するには、アセンブリを Azure ストレージ アカウントの Azure BLOB コンテナーにアップロードします。 アセンブリを追加する手順は、お使いの BLOB コンテナーにパブリック読み取りアクセス権が与えられているかどうかによって変わります。 そのため、まず最初に BLOB コンテナーがパブリック読み取りアクセス権を持っているかどうかを確認します。このとき、「[BLOB コンテナーのパブリック アクセス レベルを設定する](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)」の手順に従います。

#### <a name="check-container-access-level"></a>コンテナーのアクセス レベルを確認する

1. Azure ストレージ エクスプローラーを開きます。 Explorer ウィンドウで、Azure サブスクリプションがまだ展開されていない場合は展開します。

1. **[ストレージ アカウント]** > {*自分のストレージ アカウント*} > **[BLOB コンテナー]** の順に展開します。 BLOB コンテナーを選択します。

1. BLOB コンテナーのショートカット メニューから **[Set Public Access Level]\(パブリック アクセス レベルの設定\)** を選択します。

   * BLOB コンテナーに少なくともパブリック アクセス権がある場合は、 **[キャンセル]** を選択し、このページで後述する「[パブリック アクセス権を持つコンテナーにアップロードする](#public-access-assemblies)」の手順に従います

     ![パブリック アクセス](media/logic-apps-enterprise-integration-maps/azure-blob-container-public-access.png)

   * BLOB コンテナーにパブリック アクセス権がない場合は、 **[キャンセル]** を選択し、このページで後述する「[パブリック アクセス権を持たないコンテナーにアップロードする](#no-public-access-assemblies)」の手順に従います

     ![パブリック アクセス権なし](media/logic-apps-enterprise-integration-maps/azure-blob-container-no-public-access.png)

<a name="public-access-assemblies"></a>

#### <a name="upload-to-containers-with-public-access"></a>パブリック アクセス権を持つコンテナーにアップロードする

1. ストレージ アカウントにアセンブリをアップロードします。 右側のウィンドウで、 **[アップロード]** を選択します。

1. アップロードを完了したら、アップロードしたアセンブリを選択します。 ツール バーで **[URL のコピー]** を選択し、アセンブリの URL をコピーします。

1. **[アセンブリの追加]** ウィンドウが開いている Azure portal に戻ります。 アセンブリの名前を入力します。 **[大きいファイル (2 MB 超)]** を選択します。

   **[アセンブリ]** ボックスではなく、 **[コンテンツ URI]** ボックスが表示されます。

1. **[コンテンツ URI]** ボックスに、アセンブリの URL を貼り付けます。 アセンブリの追加を完了します。

   アセンブリのアップロードが完了すると、 **[アセンブリ]** の一覧にスキーマが表示されます。 統合アカウントの **[概要]** ペインの **[成果物]** には、アップロードしたアセンブリも表示されます。

<a name="no-public-access-assemblies"></a>

#### <a name="upload-to-containers-without-public-access"></a>パブリック アクセス権を持たないコンテナーにアップロードする

1. ストレージ アカウントにアセンブリをアップロードします。 右側のウィンドウで、 **[アップロード]** を選択します。

1. アップロードが完了したら、アセンブリの共有アクセス署名 (SAS) を生成します。 アセンブリのショートカット メニューから **[Get Shared Access Signature]\(Shared Access Signature の取得\)** を選択します。

1. **[Shared Access Signature]** ウィンドウで **[Generate container-level shared access signature URI]\(コンテナーレベルの共有アクセス署名 URI の生成\)**  >  **[作成]** の順に選択します。 SAS URL が生成されたら、 **[URL]** ボックスの横にある **[コピー]** を選択します。

1. **[アセンブリの追加]** ウィンドウが開いている Azure portal に戻ります。 アセンブリの名前を入力します。 **[大きいファイル (2 MB 超)]** を選択します。

   **[アセンブリ]** ボックスではなく、 **[コンテンツ URI]** ボックスが表示されます。

1. **[コンテンツ URI]** ボックスに、前に生成した SAS URI を貼り付けます。 アセンブリの追加を完了します。

アセンブリのアップロードが完了すると、 **[スキーマ]** の一覧にアセンブリが表示されます。 統合アカウントの **[概要]** ページの **[成果物]** には、アップロードしたアセンブリも表示されます。

<a name="create-maps"></a>

## <a name="create-maps"></a>マップを作成する

マップとして使用できる Extensible Stylesheet Language Transformation (XSLT) を作成するために、Visual Studio 2015 または 2019 で [Enterprise Integration SDK](https://aka.ms/vsmapsandschemas) を使用して統合プロジェクトを作成できます。 そのプロジェクトで、統合マップ ファイルを作成できます。これにより、2 つの XML スキーマ ファイルの間でアイテムを視覚的にマップできます。 このプロジェクトをビルドすると、XSLT ドキュメントが生成されます。 統合アカウント内のマップの数に関する制限については、[Azure Logic Apps の制限と構成](logic-apps-limits-and-config.md#artifact-number-limits)に関するページをご覧ください。

マップには、次に挙げる属性と、アセンブリのコードへの呼び出しを含む `CDATA` セクションが必要です。

* `name` はカスタム アセンブリの名前です。

* `namespace` はカスタム コードを含むアセンブリの名前空間です。

次の例は、`XslUtilitiesLib` というアセンブリを参照してそのアセンブリから `circumference` メソッドを呼び出すマップです。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform" xmlns:msxsl="urn:schemas-microsoft-com:xslt" xmlns:user="urn:my-scripts">
<msxsl:script language="C#" implements-prefix="user">
    <msxsl:assembly name="XsltHelperLib"/>
    <msxsl:using namespace="XsltHelpers"/>
    <![CDATA[public double circumference(int radius){ XsltHelper helper = new XsltHelper(); return helper.circumference(radius); }]]>
</msxsl:script>
<xsl:template match="data">
<circles>
    <xsl:for-each select="circle">
        <circle>
            <xsl:copy-of select="node()"/>
                <circumference>
                    <xsl:value-of select="user:circumference(radius)"/>
                </circumference>
        </circle>
    </xsl:for-each>
</circles>
</xsl:template>
</xsl:stylesheet>
```

## <a name="tools-and-capabilities-for-maps"></a>マップのためのツールと機能

* Visual Studio と [Enterprise Integration SDK](https://aka.ms/vsmapsandschemas) でマップを作成するときは、図で表現されたマップ上で作業します。そこには作成したすべての関係とリンクが表示されます。

* スキーマ間で直接データをコピーできます。 Visual Studio 用の [Enterprise Integration SDK](https://aka.ms/vsmapsandschemas) には、ソースの XML スキーマの要素とターゲットの XML スキーマの要素を線でつなぐだけでこのタスクを実行できるマッパーが含まれています。

* 複数のマップに対する操作または文字列関数、日時関数などの関数を使用できます。  

* マップ テスト機能を使用して、XML メッセージのサンプルを追加できます。 作成したマップをワン クリックでテストし、生成される出力を確認できます。

## <a name="add-maps"></a>マップを追加する

### <a name="consumption-resource"></a>[Consumption](#tab/consumption-1)

マップから参照するすべてのアセンブリをアップロードしたら、次にマップをアップロードできます。

1. 統合アカウントをまだ作っていない場合は、Azure portal でメインの Azure 検索欄に `integration accounts` と入力し、 **[Integration accounts]\(統合アカウント\)** を選択します。

1. マップを追加する統合アカウントを選択します。

1. 統合アカウントのメニューで、 **[概要]** を選択します。 **[設定]** で **[Maps]** を選択します。

1. **[Maps]** ペインのツール バーで、 **[追加]** を選択します。

1. [最大 2 MB](#smaller-map) または [2 MB を超える](#larger-map)マップの追加を続けます。

<a name="smaller-map"></a>

#### <a name="add-maps-up-to-2-mb"></a>最大 2 MB のマップを追加する

1. **[マップの追加]** で、マップの一意の名前を入力します。

1. **[マップの種類]** で、種類を選択します。例:**Liquid**、**XSLT**、**XSLT 2.0**、**XSLT 3.0**。

1. **[マップ]** ボックスの横にあるフォルダー アイコンを選択します。 アップロードするマップを選択します。

   **[名前]** プロパティを空のままにした場合は、マップ ファイルを選択した後で、マップのファイル名がそのプロパティに自動的に表示されます。

1. 準備ができたら、 **[OK]** を選択します。

   マップ ファイルのアップロードが完了すると、 **[マップ]** の一覧にマップが表示されます。 統合アカウントの **[概要]** ページの **[成果物]** には、アップロードしたマップも表示されます。

<a name="larger-map"></a>

#### <a name="add-maps-more-than-2-mb"></a>2 MB 以上のマップを追加する

現在のところ、大規模なマップを追加するには [Azure Logic Apps REST API のマップ](/rest/api/logic/maps/createorupdate)を使用します。

### <a name="standard-resource"></a>[Standard](#tab/standard-1)

#### <a name="azure-portal"></a>Azure portal

1. ロジック アプリ リソースのメニューで、 **[Settings]\(設定\)** 、 **[Maps]\(マップ\)** を順に選択します。

1. **[Maps]** ペインのツール バーで、 **[追加]** を選択します。

1. **[Add map]\(マップの追加\)** で一意のマップ名を入力します。その名前には `.xslt` 拡張子を付けます。

1. **[マップ]** ボックスの横にあるフォルダー アイコンを選択します。 アップロードするマップを選択します。

1. 準備ができたら、 **[OK]** を選択します。

   マップ ファイルのアップロードが完了すると、 **[マップ]** の一覧にマップが表示されます。 統合アカウントの **[概要]** ページの **[成果物]** には、アップロードしたマップも表示されます。

#### <a name="visual-studio-code"></a>Visual Studio Code

1. ロジック アプリのプロジェクトの構造で、 **[Artifacts]\(アーティファクト\)** フォルダー、 **[Maps]\(マップ\)** フォルダーを順に開きます。

1. **[Maps]\(マップ\)** フォルダーでマップを追加します。

---

## <a name="edit-maps"></a>マップを編集する

既存のマップを更新するには、目的の変更を含む新しいマップ ファイルをアップロードする必要があります。 ただし、編集対象の既存のマップをまずダウンロードすることができます。

### <a name="consumption-resource"></a>[Consumption](#tab/consumption-2)

1. [Azure portal](https://portal.azure.com) で、統合アカウントをまだ開いていない場合は開きます。

1. 統合アカウント メニューで、 **[設定]** の **[マップ]** を選択します。

1. **[マップ]** ペインが開いたら、マップを選択します。 最初にマップをダウンロードして編集するには、 **[マップ]** ペインのツール バーで **[ダウンロード]** を選択して、マップを保存します。

1. 更新したマップをアップロードする準備ができたら、 **[マップ]** ペインで更新するマップを選択します。 **[マップ]** ペインのツール バーで、 **[更新]** を選択します。

1. アップロードする更新済みマップを探して選択します。

   マップ ファイルのアップロードが完了すると、 **[マップ]** の一覧に更新されたマップが表示されます。

### <a name="standard-resource"></a>[Standard](#tab/standard-2)

1. まだ開いていなければ、[Azure portal](https://portal.azure.com) でロジック アプリ リソースを開きます。

1. ロジック アプリ リソースのメニューで、 **[Settings]\(設定\)** 、 **[Maps]\(マップ\)** を順に選択します。

1. **[マップ]** ペインが開いたら、マップを選択します。 最初にマップをダウンロードして編集するには、 **[マップ]** ペインのツール バーで **[ダウンロード]** を選択して、マップを保存します。

1. **[Maps]** ペインのツール バーで、 **[追加]** を選択します。

1. **[Add map]\(マップの追加\)** で一意のマップ名を入力します。その名前には `.xslt` 拡張子を付けます。

1. **[マップ]** ボックスの横にあるフォルダー アイコンを選択します。 アップロードするマップを選択します。

1. 準備ができたら、 **[OK]** を選択します。

   マップ ファイルのアップロードが完了すると、 **[マップ]** の一覧に更新されたマップが表示されます。

---

## <a name="delete-maps"></a>マップを削除する

### <a name="consumption-resource"></a>[Consumption](#tab/consumption-3)

1. [Azure portal](https://portal.azure.com) で、統合アカウントをまだ開いていない場合は開きます。

1. 統合アカウント メニューで、 **[設定]** の **[マップ]** を選択します。

1. **[Maps]\(マップ\)** ペインを開き、マップを選択し、 **[Delete]\(削除\)** をクリックします。

1. マップの削除を確定するには、 **[はい]** を選択します。

### <a name="standard-resource"></a>[Standard](#tab/standard-3)

1. まだ開いていなければ、[Azure portal](https://portal.azure.com) でロジック アプリ リソースを開きます。

1. ロジック アプリ リソースのメニューで、 **[Settings]\(設定\)** 、 **[Maps]\(マップ\)** を順に選択します。

1. **[Maps]\(マップ\)** ペインを開き、マップを選択し、 **[Delete]\(削除\)** をクリックします。

1. マップの削除を確定するには、 **[はい]** を選択します。

---

## <a name="next-steps"></a>次のステップ

* [Azure Logic Apps のワークフロー用に XML を変換する](logic-apps-enterprise-integration-transform.md)
* [Azure Logic Apps のワークフロー用に XML を承認する](logic-apps-enterprise-integration-xml-validation.md)
