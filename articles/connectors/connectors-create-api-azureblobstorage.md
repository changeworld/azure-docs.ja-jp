---
title: Azure Blob Storage に接続する
description: Azure Logic Apps を使用して Azure ストレージ アカウントに BLOB を作成して管理します。
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: conceptual
ms.date: 06/23/2021
tags: connectors
ms.openlocfilehash: e3d8730976d623b5304a50341bcf257f35047e25
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2021
ms.locfileid: "113359553"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-by-using-azure-logic-apps"></a>Azure Logic Apps を使用して Azure Blob Storage に BLOB を作成して管理する

自分の Azure ストレージ アカウントに BLOB として格納されているファイルは、Azure Logic Apps 内から [Azure Blob Storage コネクタ](/connectors/azureblobconnector/)を使用してアクセス、管理することができます。 ロジック アプリ ワークフロー内の BLOB 操作に使用されるトリガーとアクションが、このコネクタから得られます。 これらの操作を使用して、自分のストレージ アカウント内のファイルを管理するためのタスクとワークフローを自動化できます。 [利用できるコネクタのアクション](/connectors/azureblobconnector/#actions)としては、BLOB のチェック、削除、読み取り、アップロードなどがあります。 BLOB が追加または変更されると、[利用可能なトリガー](/connectors/azureblobconnector/#triggers)が作動します。 

Blob Storage には、Standard と従量課金プランの両方のタイプのロジック アプリ リソースから接続することができます。 コネクタは、シングルテナント、マルチテナント、または統合サービス環境 (ISE) のロジック アプリで使用できます。 シングルテナント環境のロジック アプリでは、Blob Storage の組み込みの操作のほか、マネージド コネクタの操作を利用できます。

> [!NOTE]
> [統合サービス環境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) のロジック アプリの場合、このコネクタの ISE のラベルが付いたバージョンでは、代わりに [ISE メッセージ制限](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)が使用されます。

トリガー、アクション、制限など、このコネクタの詳しい技術情報については、[コネクタのリファレンス ページ](/connectors/azureblobconnector/)を参照してください。

Blob Storage コネクタの代わりに、[HTTP トリガーまたは HTTP アクションでマネージド ID を使用して BLOB の操作を実行する](#access-blob-storage-with-managed-identities)こともできます。

> [!IMPORTANT]
> 同じリージョンにある場合、ロジック アプリはファイア ウォールの背後にあるストレージ アカウントに直接アクセスできません。 回避策として、お使いのロジック アプリとストレージアカウントを別のリージョンに格納してください。 Azure Logic Apps からファイアウォールの背後にあるストレージ アカウントへのアクセスを有効にする方法の詳細については、このトピックの後半の「[ファイアウォールの背後にあるストレージ アカウントにアクセスする](#access-storage-accounts-behind-firewalls)」を参照してください。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。
- [Azure ストレージ アカウントとストレージ コンテナー](../storage/blobs/storage-quickstart-blobs-portal.md)
- 自分の BLOB ストレージ アカウントにアクセスするロジック アプリ ワークフロー。 Blob Storage トリガーを使用してロジック アプリを起動する場合は、[空のロジック アプリ](../logic-apps/quickstart-create-first-logic-app-workflow.md)が必要です。

## <a name="limits"></a>制限

- 既定では、Blob Storage アクションで "*50 MB 以下*" のファイルの読み取りまたは書き込みが可能です。 Blob Storage のアクションは、50 MB から 1,024 MB までのファイルを処理するために、[メッセージ チャンク](../logic-apps/logic-apps-handle-large-messages.md)をサポートしています。 [**BLOB コンテンツの取得** アクションは](/connectors/azureblobconnector/#get-blob-content)、暗黙的にチャンクを使用します。
- Blob Storage トリガーではチャンクはサポートされていません。 ファイルのコンテンツを要求すると、トリガーによって 50 MB 以下のファイルのみが選択されます。 50 MB より大きいファイルを取得するには、次のパターンに従います。
  - [**BLOB が追加または変更されたとき (プロパティのみ)** ](/connectors/azureblobconnector/#when-a-blob-is-added-or-modified-(properties-only)) といった、ファイルのプロパティを返す Blob Storage トリガーを使用します。
  - Blob Storage トリガーの [**BLOB コンテンツの取得** アクション](/connectors/azureblobconnector/#get-blob-content)に従います。これは、暗黙的にチャンクを使用してファイル全体を読み取ります。

## <a name="add-blob-storage-trigger"></a>Blob Storage トリガーを追加する

Logic Apps では、すべてのロジック アプリは、必ず[トリガー](../logic-apps/logic-apps-overview.md#logic-app-concepts)から起動されます。トリガーは、特定のイベントが起こるか特定の条件が満たされたときに発生します。 

このコネクタに用意されているトリガーは 1 つで、[ **[When a blob is Added or Modified in Azure Storage]\(Azure Storage 内の BLOB が追加または変更されたとき\)** または **[BLOB が追加または変更されたとき (プロパティのみ)]** ](/connectors/azureblobconnector/#when-a-blob-is-added-or-modified-(properties-only)) という名前が付けられています。 ストレージ コンテナー内で BLOB のプロパティが追加されるか更新されたときに、このトリガーが作動します。 そのたびに、Logic Apps エンジンによってロジック アプリ インスタンスが作成されてワークフローが開始されます。

### <a name="single-tenant"></a>[シングルテナント](#tab/single-tenant)

Standard プランを使用するシングルテナントのロジック アプリに Blob Storage アクションを追加するには:

1. [Azure portal](https://portal.azure.com) にサインインします。
1. デザイナーでワークフローを開きます。
1. 検索ボックスに、フィルターとして「`Azure blob`」と入力します。 トリガーの一覧から、 **[When a blob is Added or Modified in Azure Storage]\(Azure Storage 内の BLOB が追加または変更されたとき\)** という名前のトリガーを選択します。
    :::image type="content" source="./media/connectors-create-api-azureblobstorage/standard-trigger-add.png" alt-text="Standard ロジック アプリのデザイナー画面のスクリーンショット。[When a blob is Added or Modified in Azure Storage]\(Azure Storage 内の BLOB が追加または変更されたとき\) という名前のトリガーを選択したところ。":::
1. 接続の詳細の入力を求められたら、[BLOB ストレージ接続を今すぐ作成](#connect-to-storage-account)します。
1. トリガーに必要な情報を指定します。
    1. **[パラメーター]** タブの **[BLOB パス]** に、監視する BLOB のパスを追加します。
        BLOB のパスを見つけるには、Azure portal で自分のストレージ アカウントを開きます。 ナビゲーション メニューの **[データ ストレージ]** で **[コンテナー]** を選択します。 BLOB コンテナーを選択します。 コンテナーのナビゲーション メニューの **[設定]** で、 **[プロパティ]** を選択します。 **[URL]** の値 (BLOB のパス) をコピーします。 このパスは、`https://{your-storage-account}.blob.core.windows.net/{your-blob}` のような形式になっています。
        :::image type="content" source="./media/connectors-create-api-azureblobstorage/standard-trigger-configure.png" alt-text="Standard ロジック アプリのデザイナー画面のスクリーンショット。Blob Storage トリガーのパラメーターの構成が表示されている。":::
    1. 必要に応じて他のトリガー設定を構成します。
    1. **[Done]** を選択します。
1. ワークフローに 1 つまたは複数のアクションを追加します。
1. デザイナーのツール バーで **[保存]** を選択して、変更を保存します。

### <a name="multi-tenant"></a>[マルチテナント](#tab/multi-tenant)

従量課金プランを使用するマルチテナント ロジック アプリで Blob Storage アクションを追加するには:

1. [Azure portal](https://portal.azure.com) にサインインします。
1. Logic Apps デザイナーでワークフローを開きます。
1. 検索ボックスに、フィルターとして「Azure blob」と入力します。 トリガーの一覧から、 **[BLOB が追加または変更されたとき (プロパティのみ)]** トリガーを選択します。
    :::image type="content" source="./media/connectors-create-api-azureblobstorage/consumption-trigger-add.png" alt-text="従量課金プランのロジック アプリのデザイナー画面のスクリーンショット。Blob Storage トリガーを選択するところ。":::
1. 接続の詳細の入力を求められたら、[BLOB ストレージ接続を今すぐ作成](#connect-to-storage-account)します。
1. トリガーに必要な情報を指定します。
    1. **[コンテナー]** で、フォルダー アイコンを選択して Blob Storage コンテナーを選択します。 または、パスを手動で入力します。
    1. 必要に応じて他のトリガー設定を構成します。
        :::image type="content" source="./media/connectors-create-api-azureblobstorage/consumption-trigger-configure.png" alt-text="従量課金プランのロジック アプリのデザイナー画面のスクリーンショット。Blob Storage トリガーのパラメーターの構成が表示されている。":::
1. ワークフローに 1 つまたは複数のアクションを追加します。
1. デザイナーのツール バーで **[保存]** を選択して、変更を保存します。

---

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>BLOB ストレージ アクションを追加する

Logic Apps では、[アクション](../logic-apps/logic-apps-overview.md#logic-app-concepts)とは、トリガーまたは別のアクションに続くワークフロー内のステップです。

### <a name="single-tenant"></a>[シングルテナント](#tab/single-tenant)

シングルテナント環境のロジック アプリの場合:

1. [Azure portal](https://portal.azure.com) にサインインします。
1. Logic Apps デザイナーでワークフローを開きます。
1. トリガーを追加します。 この例は、[ **[繰り返し]** トリガー](../connectors/connectors-native-recurrence.md)で始まります。
1. ワークフローに新しいステップを追加します。 検索ボックスに、フィルターとして「Azure blob」と入力します。 次に、使用する Blob Storage アクションを選択します。 この例では、 **[Reads Blob Content from Azure Storage]\(Azure Storage から BLOB の内容を読み取る\)** を使用します。 
   :::image type="content" source="./media/connectors-create-api-azureblobstorage/standard-action-add.png" alt-text="Standard ロジック アプリのデザイナー画面のスクリーンショット。利用可能な Blob Storage アクションが一覧表示されている。":::
1. 接続の詳細の入力を求められたら、[自分の BLOB ストレージ アカウントへの接続を作成](#connect-to-storage-account)します。
1. アクションに必要な情報を指定します。
    1. **[コンテナー名]** に、使用する BLOB コンテナーのパスを入力します。
    1. **[BLOB 名]** に、使用する BLOB のパスを入力します。
        :::image type="content" source="./media/connectors-create-api-azureblobstorage/standard-action-configure.png" alt-text="Standard ロジック アプリのデザイナー画面のスクリーンショット。Blob Storage トリガーを選択するところ。":::
    1. 必要に応じて他のアクション設定を構成します。
1. デザイナーのツール バーで、 **[保存]** を選択します。 
1. ロジック アプリをテストして、選択したコンテナーに BLOB が存在することを確認します。 

> [!TIP]
> この例では、BLOB の内容の読み取りのみが実行されます。 内容を表示するには、別のコネクタを使用して、BLOB のファイルを作成する別のアクションを追加します。 たとえば、BLOB の内容に基づいてファイルを作成する OneDrive アクションを追加します。

### <a name="multi-tenant"></a>[マルチテナント](#tab/multi-tenant)

マルチテナント環境のロジック アプリの場合:

1. [Azure portal](https://portal.azure.com) にサインインします。
1. Logic Apps デザイナーでワークフローを開きます。
1. トリガーを追加します。 この例は、[ **[繰り返し]** トリガー](../connectors/connectors-native-recurrence.md)で始まります。
1. ワークフローに新しいステップを追加します。 検索ボックスに、フィルターとして「Azure blob」と入力します。 次に、使用する Blob Storage アクションを選択します。 この例では、 **[BLOB コンテンツの取得]** を使用します。
    :::image type="content" source="./media/connectors-create-api-azureblobstorage/consumption-action-add.png" alt-text="従量課金プランのロジック アプリのデザイナー画面のスクリーンショット。利用可能な Blob Storage アクションが一覧表示されている。":::
1. 接続の詳細の入力を求められたら、[自分の BLOB ストレージ アカウントへの接続を作成](#connect-to-storage-account)します。
1. アクションに必要な情報を指定します。
    1. **[BLOB]** で、フォルダー アイコンを選択して Blob Storage コンテナーを選択します。 または、パスを手動で入力します。
        :::image type="content" source="./media/connectors-create-api-azureblobstorage/consumption-action-configure.png" alt-text="従量課金プランのロジック アプリのデザイナー画面のスクリーンショット。Blob Storage アクションの構成が表示されている。":::
    1. 必要に応じて他のアクション設定を構成します。

---

## <a name="connect-to-storage-account"></a>ストレージ アカウントに接続する

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[Blob Storage トリガー](#add-blob-storage-trigger)または [Blob Storage アクション](#add-blob-storage-action)を構成するには、あらかじめストレージ アカウントに接続する必要があります。 接続には、次のプロパティが必要になります。

| プロパティ | 必須 | 値 | 説明 |
|----------|----------|-------|-------------|
| **Connection Name** | はい | <*connection-name*> | 作成する接続の名前 |
| **Azure Blob Storage の接続文字列** | はい | <*storage-account*> | 自分のストレージ アカウントを一覧から選択するか、文字列を指定します。 |

> [!TIP]
> 接続文字列を見つけるには、ストレージ アカウントのページに移動します。 ナビゲーション メニューの **[セキュリティとネットワーク]** で **[アクセス キー]** を選択します。 **[キーの表示/非表示]** を選択します。 使用可能な 2 つの接続文字列の値のいずれかをコピーします。

### <a name="single-tenant"></a>[シングルテナント](#tab/single-tenant)

シングルテナント環境のロジック アプリの場合:

1. **[接続名]** に、接続の名前を入力します。
1. **[Azure Blob Storage Connection String]\(Azure Blob Storage 接続文字列\)** に、使用するストレージ アカウントの接続文字列を入力します。
1. **[作成]** を選択して接続を確立します。
    :::image type="content" source="./media/connectors-create-api-azureblobstorage/standard-connection-create.png" alt-text="Standard ロジック アプリのデザイナー画面のスクリーンショット。Blob Storage への新しい接続を追加するステップのプロンプトが表示されている。":::

既に接続はあるものの、別の接続を選択したい場合は、このステップのエディターで **[接続の変更]** を選択してください。

ストレージ アカウントへの接続に問題がある場合は、[ファイアウォールの背後にあるストレージ アカウントにアクセスする方法](#access-storage-accounts-behind-firewalls)に関するセクションを参照してください。

### <a name="multi-tenant"></a>[マルチテナント](#tab/multi-tenant)

マルチテナント環境のロジック アプリの場合:

1. **[接続名]** に、接続の名前を入力します。
1. **[ストレージ アカウント]** で、BLOB コンテナーがあるストレージ アカウントを選択します。 または、 **[接続情報を手動で入力する]** を選択して手動でパスを入力します。
1. **[作成]** を選択して接続を確立します。
    :::image type="content" source="./media/connectors-create-api-azureblobstorage/consumption-connection-create.png" alt-text="従量課金プランのロジック アプリのデザイナー画面のスクリーンショット。Blob Storage への新しい接続を追加するステップのプロンプトが表示されている。":::

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

ロジック アプリは、同じリージョンにある場合、ファイア ウォールの背後にあるストレージ アカウントに直接アクセスできません。 回避策として、ストレージ アカウントとは異なるリージョンにロジック アプリを配置してください。 そのうえで、[リージョン内のマネージド コネクタのアウトバウンド IP アドレス](../logic-apps/logic-apps-limits-and-config.md#outbound)にアクセスを許可します。

> [!NOTE]
> このソリューションは、Azure Table Storage コネクタと Azure Queue Storage コネクタには該当しません。 お使いの Table Storage または Queue Storage にアクセスするには、代わりに[組み込みの HTTP トリガーとアクションを使用](../logic-apps/logic-apps-http-endpoint.md)してください。

ストレージ アカウントのファイアウォールにアウトバウンド IP アドレスを追加するには:

1. ロジック アプリのリージョンの[アウトバウンド IP アドレス](../logic-apps/logic-apps-limits-and-config.md#outbound)をメモします。
1. [Azure portal](https://portal.azure.com) にサインインします。
1. ストレージ アカウントのページを開きます。 ナビゲーション メニューの **[セキュリティとネットワーク]** で **[ネットワーク]** を選択します。 
1. **[許可するアクセス元]** で、**[選択されたネットワーク]** オプションを選択します。 関連する設定がページに表示されます。
1. **[ファイアウォール]** に、アクセスする必要のある IP アドレスまたは範囲を追加します。 
    :::image type="content" source="./media/connectors-create-api-azureblobstorage/storage-ip-configure.png" alt-text="Azure portal における BLOB ストレージ アカウントのネットワーク ページのスクリーンショット。許可リストに IP アドレスおよびアドレス範囲を追加するためのファイアウォール設定が表示されている。":::

### <a name="access-storage-accounts-through-trusted-virtual-network"></a>信頼された仮想ネットワーク経由でストレージ アカウントにアクセスする

自分が管理する Azure 仮想ネットワークにストレージ アカウントを配置した後、その仮想ネットワークを信頼された仮想ネットワークの一覧に追加することができます。 [信頼された仮想ネットワーク](../virtual-network/virtual-networks-overview.md)を介して対象のロジック アプリがストレージ アカウントにアクセスできるようにするには、仮想ネットワーク内のリソースに接続できる[統合サービス環境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) にそのロジック アプリをデプロイする必要があります。 その後、その ISE 内のサブネットを、信頼された項目の一覧に追加できます。 Blob Storage コネクタなどの Azure Storage コネクタでは、ストレージ コンテナーに直接アクセスできます。 この設定は、ISE からサービス エンドポイントを使用するのと同じエクスペリエンスです。

### <a name="access-storage-accounts-through-azure-api-management"></a>Azure API Management 経由でストレージ アカウントにアクセスする

[API Management](../api-management/api-management-key-concepts.md) に専用階層を使用する場合、API Management を使用し、ファイアウォールを介して後者の IP アドレスを許可することで、Storage API に面することができます。 基本的には、API Management によって使用される Azure 仮想ネットワークをストレージ アカウントのファイアウォール設定に追加します。 その後、API Management アクションまたは HTTP アクションを使用し、Azure Storage API を呼び出すことができます。 ただし、このオプションを選択した場合、認証プロセスを自分で処理する必要があります。 詳細については、[単純なエンタープライズ統合アーキテクチャ](/azure/architecture/reference-architectures/enterprise-integration/basic-enterprise-integration)を参照してください。

## <a name="access-blob-storage-with-managed-identities"></a>マネージド ID で Blob Storage にアクセスする

この Logic Apps コネクタを使用せずに Blob Storage にアクセスしたい場合は、[認証にマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) を使用することもできます。 ファイアウォールを介してストレージ アカウントにアクセスすることを Microsoft の信頼済みサービス (マネージド ID など) に許可する例外を作成できます。

ロジック アプリでマネージド ID を使用して Blob Storage にアクセスするには:

1. [ストレージ アカウントへのアクセスを構成する](#configure-storage-account-access)
1. [ロジック アプリのロールの割り当てを作成する](#create-role-assignment-for-logic-app)
1. [ロジック アプリにおけるマネージド ID のサポートを有効にする](#enable-support-for-managed-identity-in-logic-app)

> [!NOTE]
> このソリューションの制限事項:
> - ワークフローで使用できるのは、HTTP トリガーまたは HTTP アクション "*のみ*" です。
> - ストレージ アカウント接続を認証するには、マネージド ID を設定する必要があります。
> - マネージド ID で認証を行った場合、Blob Storage の組み込み操作は使用できません。
> - シングルテナント環境のロジック アプリで利用できるのは、システム割り当てマネージド ID のみです。ユーザー割り当てマネージド ID はサポートされません。

### <a name="configure-storage-account-access"></a>ストレージ アカウントへのアクセスを構成する

例外とマネージド ID のサポートを設定するには、まずストレージ アカウントへの適切なアクセスを構成します。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ストレージ アカウントのページを開きます。 ナビゲーション メニューの **[セキュリティとネットワーク]** で **[ネットワーク]** を選択します。 
1. **[許可するアクセス元]** で、**[選択されたネットワーク]** オプションを選択します。 関連する設定がページに表示されます。
1. 自分のコンピューターからストレージ アカウントにアクセスする必要がある場合、 **[ファイアウォール]** で **[クライアント IP アドレスを追加する]** を有効にする必要があります。
1. **[例外]** の **[信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します]** を有効にします。 
    :::image type="content" source="./media/connectors-create-api-azureblobstorage/storage-networking-configure.png" alt-text="Azure portal における BLOB ストレージ アカウントのネットワーク ページのスクリーンショット。選択したネットワーク、クライアント IP アドレス、信頼された Microsoft サービスを許可するための設定が表示されている。":::
1. **[保存]** を選択します。

> [!TIP]
> ワークフローからストレージ アカウントへの接続を試みたときに "**403 Forbidden (許可されていません)** " というエラーが表示された場合、複数の原因が考えられます。 その先の手順に進む前に次の解決策を試してみてください。 まず、 **[信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します]** 設定を無効にして変更内容を保存します。 次に、設定を再度有効にして、変更を保存し直してください。 

### <a name="create-role-assignment-for-logic-app"></a>ロジック アプリのロールの割り当てを作成する

次に、ロジック アプリの[マネージド ID のサポートを有効](../logic-apps/create-managed-service-identity.md)にします。

1. Azure portal でロジック アプリを開きます。
1. ナビゲーション メニューの **[設定]** で、 **[ID]** を選択します。
1. **[システム割り当て]** で、 **[状態]** を **[オン]** に設定します。 この設定は既に有効になっている場合もあります。
1. **[アクセス許可]** で、 **[Azure でのロールの割り当て]** を選択します。
    :::image type="content" source="./media/connectors-create-api-azureblobstorage/role-assignment-add-1.png" alt-text="Azure portal におけるロジック アプリ メニューのスクリーンショット。Azure ロールの割り当てアクセス許可を追加するためのボタンが ID 設定ペインに表示されている。":::
1. **[Azure のロールの割り当て]** ペインで、 **[ロールの割り当ての追加]** を選択します。
    :::image type="content" source="./media/connectors-create-api-azureblobstorage/role-assignment-add-2.png" alt-text="ロジック アプリのロールの割り当てペインのスクリーンショット。選択したサブスクリプションと新しいロールの割り当てを追加するためのボタンが表示されている。":::
1. 次のように新しいロールの割り当てを構成します。
    1. **[スコープ]** で **[ストレージ]** を選択します。
    1. **[サブスクリプション]** で、対象のストレージ アカウントがあるサブスクリプションを選択します。
    1. **[リソース]** で、ロジック アプリからアクセスしたいストレージ アカウントを選択します。
    1. **[ロール]** で、実際のシナリオに合った適切なアクセス許可を選択します。 この例では、 **[ストレージ BLOB データ共同作成者]** を使用しており、BLOB コンテナーと日付に対する読み取り、書き込み、削除アクセスが許可されます。 ドロップダウン メニューでロールの横にある情報アイコンにマウス ポインターを合わせると、アクセス許可の詳細が表示されます。
    1. **[保存]** を選択して、ロールの割り当ての作成を終了します。
        :::image type="content" source="./media/connectors-create-api-azureblobstorage/role-assignment-configure.png" alt-text="ロールの割り当ての構成ペインのスクリーンショット。スコープ、サブスクリプション、リソース、ロールの設定が表示されている。":::

### <a name="enable-support-for-managed-identity-in-logic-app"></a>ロジック アプリでマネージド ID のサポートを有効にする

次に、[HTTP トリガーまたは HTTP アクション](connectors-native-http.md)をワークフローに追加します。 必ず、[マネージド ID を使用するように認証の種類を設定](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity)してください。 

シングルテナント環境でもマルチテナント環境でもロジック アプリの手順は同じです。

1. Logic Apps デザイナーでワークフローを開きます。
1. シナリオに応じて **HTTP** トリガーまたはアクションを含んだ新しいステップをワークフローに追加します。
1. **HTTP** トリガーまたはアクションに必要なパラメーターをすべて構成します。
    1. 要求の **メソッド** を選択します。 この例では、HTTP PUT メソッドを使用します。
    1. BLOB の **URI** を入力します。 このパスは、`https://{your-storage-account}.blob.core.windows.net/{your-blob}` のような形式になっています。
    1. **[ヘッダー]** で、BLOB タイプ ヘッダー `x-ms-blob-type` を `BlockBlob` という値で追加します。
    1. また、 **[ヘッダー]** で、API バージョン ヘッダー `x-ms-version` を適切な値で追加します。 詳細については、「[マネージド ID を使用してアクセスを認証する](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity)」と「[Azure Storage サービスのバージョン管理](/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests)」を参照してください。
    :::image type="content" source="./media/connectors-create-api-azureblobstorage/managed-identity-connect.png" alt-text="必要な HTTP PUT アクションのパラメーターを示す Logic Apps デザイナーのスクリーンショット。":::
1. **[新しいパラメーターの追加]** を選択し、 **[認証]** を選択して[マネージド ID を構成](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity)します。
    1. **[認証]** の **[認証の種類]** で、 **[マネージド ID]** を選択します。
    1. **[マネージド ID]** で **[システム割り当てマネージド ID]** を選択します。
    :::image type="content" source="./media/connectors-create-api-azureblobstorage/managed-identity-authenticate.png" alt-text="Logic Apps デザイナーのスクリーンショット。HTTP アクションのマネージド ID に使用する認証パラメーターの設定が表示されている。":::
1. Logic Apps デザイナーのツール バーで **[保存]** を選択します。

これで [Blob service の REST API](/rest/api/storageservices/blob-service-rest-api) を呼び出して、必要なストレージ操作を実行することができます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Logic Apps コネクタの詳細情報](../connectors/apis-list.md)
