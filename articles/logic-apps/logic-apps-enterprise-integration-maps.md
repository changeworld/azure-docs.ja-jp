---
title: XSLT マップを使用した XML 変換
description: Enterprise Integration Pack を備えた Azure Logic Apps で、XML を変換するための XSLT マップを追加します
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 08/04/2021
ms.openlocfilehash: 86e7c07ba3ade77ec3913178a8dc24bb135c0a54
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733772"
---
# <a name="transform-xml-with-maps-in-azure-logic-apps-with-enterprise-integration-pack"></a>Enterprise Integration Pack を備えた Azure Logic Apps でマップを使用して XML を変換する

Azure Logic Apps でのエンタープライズ統合シナリオ用に XML データの形式を変換する必要がある場合は、ロジック アプリでマップを使用できます (より具体的に言うと、Extensible Stylesheet Language Transformation (XSLT) マップ)。 マップとは、データを XML ドキュメントから別の形式に変換する方法について記述した XML ドキュメントのことです。

たとえば、YYYMMDD という日付形式を使用している顧客から、B2B の注文や請求書を定期的に受け取っているとします。 ただし、あなたの組織では MMDDYYY という日付形式を使用しているとします。 そのような場合には、日付形式を YYYMMDD から MMDDYYY に変換するマップを定義して使用できます。その後、顧客活動データベースに注文や請求書の詳細を保存できます。

> [!NOTE]
> Azure Logic Apps サービスにより、XML 変換を処理するために有限のメモリが割り当てられます。 **ロジック アプリ (従量課金)** リソースの種類に基づいてロジック アプリを作成し、マップまたはペイロードの変換でのメモリ消費量が多い場合、そのような変換は失敗し、メモ のエラーが発生する可能性があります。 このシナリオを回避するには、次のオプションを検討します。
>
> * マップまたはペイロードを編集して、メモリ消費量を減らします。
>
> * 代わりに、**ロジック アプリ (Standard)** リソースの種類を使用して、ロジック アプリを作成します。
>
>   これらのワークフローはシングルテナントの Azure Logic Apps で実行され、コンピューティング リソースとメモリ リソースに専用の柔軟なオプションが提供されます。 詳細については、次のドキュメントを確認してください。
>
>   * [Azure Logic Apps とは - リソースの種類とホスト環境](logic-apps-overview.md#resource-type-and-host-environment-differences)
>   * [シングルテナントの Azure Logic Apps (Standard) で統合ワークフローを作成する](create-single-tenant-workflows-azure-portal.md)
>   * [Azure Logic Apps でのシングルテナント、マルチテナント、統合サービス環境の比較](single-tenant-overview-compare.md)
>   * [Azure Logic Apps の使用量の測定、課金、価格モデル](logic-apps-pricing.md)

統合アカウントとアーティファクト (マップなど) に関連する制限については、「[Azure Logic Apps の制限と構成情報](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)」を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 サブスクリプションをお持ちでない場合には、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* エンタープライズ統合および企業間取引 (B2B) ソリューションのためのマップとその他のアーティファクトを格納する[統合アカウント](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)。

* マップで外部アセンブリが参照されている場合は、64 ビット アセンブリが必要です。 変換サービスでは 64 ビット プロセスが実行されるため、32 ビット アセンブリはサポートされていません。 32 ビット アセンブリ用のソース コードがある場合は、コードを 64 ビット アセンブリとして再コンパイルします。 ソース コードがなくても、サードパーティのプロバイダーからバイナリを取得した場合は、そのプロバイダーから 64 ビット バージョンを取得します。 たとえば、一部のベンダーからは、32 ビットと 64 ビット両方のバージョンのアセンブリがパッケージで提供されています。 オプションがある場合は、代わりに 64 ビット バージョンを使用してください。

* マップで外部アセンブリを参照する場合は、"*アセンブリとマップの両方*" を統合アカウントにアップロードする必要があります。 [*最初にアセンブリをアップロード*](#add-assembly)し、その後、そのアセンブリを参照するマップをアップロードするようにしてください。

  アセンブリが 2 MB 以下の場合は、Azure portal からアセンブリを統合アカウントに "*直接*" 追加できます。 ただし、アセンブリやマップが 2 MB よりも大きく、かつ[アセンブリやマップのサイズ制限](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits)を超えていない場合には、次のオプションを利用できます。

  * アセンブリの場合は、アセンブリをアップロードできる Azure BLOB コンテナーと、そのコンテナーの場所が必要になります。 これにより、後でアセンブリを統合アカウントに追加する際に、その場所を指定できるようになります。 このタスクを実行するには、次の項目が必要です。

    | Item | 説明 |
    |------|-------------|
    | [Azure Storage アカウント](../storage/common/storage-account-overview.md) | このアカウントには、アセンブリの Azure BLOB コンテナーを作成します。 [ストレージ アカウントの作成方法](../storage/common/storage-account-create.md)を確認してください。 |
    | BLOB コンテナー | このコンテナーに、アセンブリをアップロードできます。 このコンテナーの場所は、アセンブリを統合アカウントに追加するときにも必要になります。 [BLOB コンテナーの作成方法についてはこちら](../storage/blobs/storage-quickstart-blobs-portal.md)を参照してください。 |
    | [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) | このツールを使用すると、ストレージ アカウントと BLOB コンテナーをより簡単に管理できます。 Storage Explorer を使用するには、[Azure Storage Explorer をダウンロードしてインストール](https://www.storageexplorer.com/)します。 次に、「[Storage Explorer の概要](../vs-azure-tools-storage-manage-with-storage-explorer.md)」の手順に従って Storage Explorer をストレージ アカウントに接続します。 詳しくは、「[クイック スタート: Azure Storage Explorer を使用してオブジェクト ストレージ内に BLOB を作成する](../storage/blobs/storage-quickstart-blobs-storage-explorer.md)」を参照してください。 <p>または、Azure portal で、自分のストレージ アカウントを選択します。 ストレージ アカウント メニューから **[Storage Explorer]** を選択します。 |
    |||

  * マップの場合は、現在のところ、[Azure Logic Apps REST API のマップ](/rest/api/logic/maps/createorupdate)を使用することで、より大きなマップを追加できます。

マップの作成時と追加時にロジック アプリは必要ありません。 ただし、マップを使用するには、ロジック アプリからそのマップを格納する統合アカウントにリンクする必要があります。 [統合アカウントにロジック アプリをリンクする方法についてはこちら](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account)を参照してください。 ロジック アプリを用意していない場合は、[ロジック アプリを作成する方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)を確認してください。

<a name="add-assembly"></a>

## <a name="add-referenced-assemblies"></a>参照先アセンブリを追加する

1. Azure アカウントの資格情報で [Azure portal](https://portal.azure.com) にサインインします。

1. Azure のメインの検索ボックスに「`integration accounts`」と入力し、 **[統合アカウント]** を選択します。

1. 次のように、アセンブリの追加先となる統合アカウントを選択します。

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

     ![パブリック アクセス](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * BLOB コンテナーにパブリック アクセス権がない場合は、 **[キャンセル]** を選択し、このページで後述する「[パブリック アクセス権を持たないコンテナーにアップロードする](#no-public-access-assemblies)」の手順に従います

     ![パブリック アクセス権なし](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

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

## <a name="create-maps"></a>マップを作成する

マップとして使用できる Extensible Stylesheet Language Transformation (XSLT) ドキュメントを作成するには、Visual Studio 2015 または 2019 で [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md) を使用することにより統合プロジェクトを作成できます。 そのプロジェクトで、統合マップ ファイルを作成できます。これにより、2 つの XML スキーマ ファイルの間でアイテムを視覚的にマップできます。 このプロジェクトをビルドすると、XSLT ドキュメントが生成されます。 統合アカウント内のマップの数に関する制限については、[Azure Logic Apps の制限と構成](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)に関するページをご覧ください。

## <a name="add-maps"></a>マップを追加する

マップから参照するすべてのアセンブリをアップロードしたら、次にマップをアップロードできます。

1. まだサインインしていない場合は、Azure アカウントの資格情報を使用して [Azure portal](https://portal.azure.com) にサインインします。

1. 統合アカウントがまだ開かれていない場合は、Azure のメイン検索ボックスに「`integration accounts`」と入力して、 **[統合アカウント]** を選択します。

1. マップを追加する統合アカウントを選択します。

1. 統合アカウントのメニューで、 **[概要]** を選択します。 **[設定]** で **[Maps]** を選択します。

1. **[Maps]** ペインのツール バーで、 **[追加]** を選択します。

1. [最大 2 MB](#smaller-map) または [2 MB を超える](#larger-map)マップの追加を続けます。

<a name="smaller-map"></a>

### <a name="add-maps-up-to-2-mb"></a>最大 2 MB のマップを追加する

1. **[マップの追加]** で、マップの一意の名前を入力します。

1. **[マップの種類]** で、種類を選択します。例:**Liquid**、**XSLT**、**XSLT 2.0**、**XSLT 3.0**。

1. **[マップ]** ボックスの横にあるフォルダー アイコンを選択します。 次のように、アップロードするマップを検索して選択します。

   **[名前]** プロパティを空のままにした場合は、マップ ファイルを選択した後で、マップのファイル名がそのプロパティに自動的に表示されます。

1. 準備ができたら、 **[OK]** を選択します。

   マップ ファイルのアップロードが完了すると、 **[マップ]** の一覧にマップが表示されます。

   統合アカウントの **[概要]** ページの **[成果物]** には、アップロードしたマップも表示されます。

<a name="larger-map"></a>

### <a name="add-maps-more-than-2-mb"></a>2 MB 以上のマップを追加する

現在のところ、大規模なマップを追加するには [Azure Logic Apps REST API のマップ](/rest/api/logic/maps/createorupdate)を使用します。

<!--

To add larger maps, you can upload your map to 
an Azure blob container in your Azure storage account. 
Your steps for adding maps differ based whether your 
blob container has public read access. So first, check 
whether or not your blob container has public read 
access by following these steps: 
[Set public access level for blob container](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### Check container access level

1. Open Azure Storage Explorer. In the Explorer window, 
   expand your Azure subscription if not already expanded.

1. Expand **Storage Accounts** > {*your-storage-account*} > 
   **Blob Containers**. Select your blob container.

1. From your blob container's shortcut menu, 
   select **Set Public Access Level**.

   * If your blob container has at least public access, choose **Cancel**, 
   and follow these steps later on this page: 
   [Upload to containers with public access](#public-access)

     ![Public access](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * If your blob container doesn't have public access, choose **Cancel**, 
   and follow these steps later on this page: 
   [Upload to containers without public access](#public-access)

     ![No public access](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access-maps"></a>

### Add maps to containers with public access

1. Upload the map to your storage account. 
   In the right-side window, choose **Upload**. 

1. After you finish uploading, select your 
   uploaded map. On the toolbar, choose **Copy URL** 
   so that you copy the map's URL.

1. Return to the Azure portal where the 
   **Add Map** pane is open. Choose **Large file**. 

   The **Content URI** box now appears, 
   rather than the **Map** box.

1. In the **Content URI** box, paste your map's URL. 
   Finish adding your map.

After your map finishes uploading, 
the map appears in the **Maps** list.

<a name="no-public-access-maps"></a>

### Add maps to containers with no public access

1. Upload the map to your storage account. 
   In the right-side window, choose **Upload**.

1. After you finish uploading, generate a 
   shared access signature (SAS) for your schema. 
   From your map's shortcut menu, 
   select **Get Shared Access Signature**.

1. In the **Shared Access Signature** pane, select 
   **Generate container-level shared access signature URI** > **Create**. 
   After the SAS URL gets generated, next to the **URL** box, choose **Copy**.

1. Return to the Azure portal where the 
   **Add Maps** pane is open. Choose **Large file**.

   The **Content URI** box now appears, 
   rather than the **Map** box.

1. In the **Content URI** box, paste the SAS URI 
   you previously generated. Finish adding your map.

After your map finishes uploading, 
the map appears in the **Maps** list.

-->

## <a name="edit-maps"></a>マップを編集する

既存のマップを更新するには、目的の変更を含む新しいマップ ファイルをアップロードする必要があります。 ただし、編集対象の既存のマップをまずダウンロードすることができます。

1. [Azure portal](https://portal.azure.com) で、統合アカウントをまだ開いていない場合は開きます。

1. 統合アカウント メニューで、 **[設定]** の **[マップ]** を選択します。

1. **[マップ]** ペインが開いたら、マップを選択します。 最初にマップをダウンロードして編集するには、 **[マップ]** ペインのツール バーで **[ダウンロード]** を選択して、マップを保存します。

1. 更新したマップをアップロードする準備ができたら、 **[マップ]** ペインで更新するマップを選択します。 **[マップ]** ペインのツール バーで、 **[更新]** を選択します。

1. アップロードする更新済みマップを探して選択します。

   マップ ファイルのアップロードが完了すると、 **[マップ]** の一覧に更新されたマップが表示されます。

## <a name="delete-maps"></a>マップを削除する

1. [Azure portal](https://portal.azure.com) で、統合アカウントをまだ開いていない場合は検索して開きます。

1. 統合アカウント メニューで、 **[設定]** の **[マップ]** を選択します。

1. **[マップ]** ペインが開いたら、マップを選択して、 **[削除]** を選択します。

1. マップの削除を確定するには、 **[はい]** を選択します。

## <a name="next-steps"></a>次のステップ

* [Enterprise Integration Pack についての詳細情報](../logic-apps/logic-apps-enterprise-integration-overview.md)  
* [スキーマについての詳細情報](../logic-apps/logic-apps-enterprise-integration-schemas.md)
* [変換についての詳細情報](../logic-apps/logic-apps-enterprise-integration-transform.md)
