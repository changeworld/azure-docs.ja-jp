---
title: インスタンスを別の Azure リージョンに移動する
titleSuffix: Azure Digital Twins
description: Azure Digital Twins インスタンスを Azure リージョン間で移動する方法について説明します。
author: baanders
ms.author: baanders
ms.date: 9/8/2021
ms.topic: how-to
ms.custom: subject-moving-resources
ms.service: digital-twins
ms.openlocfilehash: 9fc28555be055fe74f183c965190b2c784c4533b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128676791"
---
# <a name="move-an-azure-digital-twins-instance-to-a-different-azure-region"></a>Azure Digital Twins インスタンスを別の Azure リージョンに移動する

Azure Digital Twins インスタンスをリージョン間で移動する必要がある場合、現在のプロセスでは、新しいリージョン内でリソースを再作成します。 リソースが新しいリージョン内で再作成されたら、元のリソースは削除されます。 このプロセスの終わりには、更新された場所を除いて最初のものと同じである新しい Azure Digital Twins インスタンスを操作することになります。

この記事では、新しいインスタンスを元のものと一致させるために必要なすべてを完全に移動して、コピーする方法についてのガイダンスを提供します。

このプロセスには、次の手順が含まれます。

1. 準備:元のモデル、ツイン、グラフをダウンロードします。
1. 移動:新しい Azure Digital Twins インスタンスを新しいリージョンに作成します。
1. 移動:新しい Azure Digital Twins インスタンスにデータを再入力します。
    - 元のモデル、ツイン、グラフをアップロードします。
    - エンドポイントとルートを再作成します。
    - 接続されたリソースを再リンクします。
1. ソース リソースのクリーンアップ: 元のインスタンスを削除します。

## <a name="prerequisites"></a>前提条件

Azure Digital Twins インスタンスを再作成しようとしている場合は、事前に元のインスタンスのコンポーネントを確認し、再作成する必要があるすべての要素を明確に把握しておいてください。

考慮すべきいくつかの質問を次に示します。

* どのような *モデル* がインスタンスにアップロードされているか? いくつあるか?
* インスタンス内の *ツイン* は何か? いくつあるか?
* インスタンス内の "*グラフ*" の一般的な形状はどのようなものか? リレーションシップはいくつあるか?
* どのような *エンドポイント* がインスタンスにあるか?
* どのような *ルート* がインスタンスにあるか? フィルターはあるか?
* インスタンスはどこで *他の Azure サービスに接続* するか? 一般的な統合ポイントをいくつか以下に示します。

    - Azure Event Grid、Azure Event Hubs、または Azure Service Bus
    - Azure Functions
    - Azure Logic Apps
    - Azure Time Series Insights
    - Azure Maps
    - Azure IoT Hub Device Provisioning Service
* インスタンスに接続する *個人または会社のアプリ* には、他にどのようなものがあるか?

この情報は、[Azure portal](https://portal.azure.com)、[Azure Digital Twins の API と SDK](concepts-apis-sdks.md)、[Azure Digital Twins CLI コマンド](/cli/azure/dt?view=azure-cli-latest&preserve-view=true)、または [Azure Digital Twins Explorer](concepts-azure-digital-twins-explorer.md) を使用して収集できます。

## <a name="prepare"></a>準備

このセクションでは、元のインスタンスから元のモデル、ツイン、グラフをダウンロードすることによってインスタンスを再作成するための準備を行います。 この記事では、このタスクに [Azure Digital Twins Explorer](concepts-azure-digital-twins-explorer.md) を使用します。

>[!NOTE]
>モデルやグラフを含むファイルがインスタンスに既に存在している場合があります。 その場合、すべてを再度ダウンロードする必要はありません。欠落している要素や、これらのファイルを最初にアップロードした後に変更された可能性があるもののみをダウンロードしてください。 たとえば、新しいデータで更新されたツインがある場合があります。

### <a name="download-models-twins-and-graph-with-azure-digital-twins-explorer"></a>Azure Digital Twins Explorer を使用してモデル、ツイン、グラフをダウンロードする

最初に、[Azure portal](https://portal.azure.com) で Azure Digital Twins インスタンスの **Azure Digital Twins Explorer** を開きます。 それには、ポータルの検索バーで名前を検索することによって、ポータルの新しい Azure Digital Twins インスタンスに移動します。 次に、 **[Explorer に移動する (プレビュー)]** ボタンを選択します。 

:::image type="content" source="media/includes/azure-digital-twins-explorer-portal-access.png" alt-text="Azure Digital Twins インスタンスの [概要] ページが表示されている Azure portal のスクリーンショット。[Explorer に移動する (プレビュー)] ボタンが強調されています。" lightbox="media/includes/azure-digital-twins-explorer-portal-access.png":::

このボタンを選択すると、このインスタンスに接続された Azure Digital Twins Explorer ウィンドウが開きます。

:::image type="content" source="media/quickstart-azure-digital-twins-explorer/explorer-blank.png" alt-text="インターネット ブラウザーでの Azure portal のスクリーンショット。ポータルには、データが含まれていない Azure Digital Twins Explorer が表示されています。" lightbox="media/quickstart-azure-digital-twins-explorer/explorer-blank.png":::

<<<<<<< HEAD
* [Azure Digital Twins CLI コマンド](how-to-use-cli.md)
* [Azure Digital Twins API および SDK](how-to-use-apis-sdks.md)

### <a name="set-up-the-azure-digital-twins-explorer-application"></a>Azure Digital Twins Explorer アプリケーションを設定する

Azure Digital Twins Explorer の設定を進めるには、まず、サンプル アプリケーション コードをダウンロードして設定し、コンピューター上で実行します。

サンプルを入手するには、[Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) に関するページを参照してください。 **[Download ZIP]** ボタンを選択して、このサンプル コードの .zip ファイルを、ご利用のコンピューターに **Azure_Digital_Twins__ADT__explorer.zip** としてダウンロードします。 ファイルを解凍します。

次に、Azure Digital Twins Explorer のアクセス許可を設定し、構成します。 Azure Digital Twins クイックスタートの「[Azure Digital Twins と Azure Digital Twins Explorer を設定する](quickstart-adt-explorer.md#set-up-azure-digital-twins-and-azure-digital-twins-explorer)」セクションの指示に従ってください。 このセクションでは、次の手順について説明します。

1. Azure Digital Twins インスタンスを設定します。 インスタンスは既にあるため、この部分はスキップしてかまいません。
1. インスタンスへのアクセスを提供するようにローカルの Azure 資格情報を設定します。
1. Azure Digital Twins Explorer を実行し、お使いのインスタンスに接続するように構成します。 移動する元の Azure Digital Twins インスタンスの "*ホスト名*" を使用します。

これで、コンピューター上のブラウザーで Azure Digital Twins Explorer サンプル アプリが実行されます。 このサンプルが、元の Azure Digital Twins インスタンスに接続していることを確認します。

:::image type="content" source="media/how-to-move-regions/explorer-blank.png" alt-text="localhost:3000 で実行中のアプリがブラウザー ウィンドウに表示されている。このアプリは Azure Digital Twins Explorer と呼ばれ、クエリ エクスプローラー、モデル ビュー、グラフ ビュー、プロパティ エクスプローラーのボックスが含まれる。画面上にはまだデータがない。" lightbox="media/how-to-move-regions/explorer-blank.png":::

接続を確認するには、 **[クエリの実行]** ボタンを選択して既定のクエリを実行します。これにより、 **[グラフ エクスプローラー]** ボックスのグラフにすべてのツインとリレーションシップが表示されます。

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="ウィンドウの右上隅にある [クエリの実行] ボタンが強調表示されている。" lightbox="media/how-to-move-regions/run-query.png":::

この記事の後半で、Azure Digital Twins Explorer を再び使用して、これらのアイテムをターゲット リージョンの新しいインスタンスに再アップロードします。このため、Azure Digital Twins Explorer は実行したままでかまいません。

### <a name="download-models-twins-and-graph"></a>モデル、ツイン、グラフのダウンロード

次に、ソリューションのモデル、ツイン、グラフをコンピューターにダウンロードします。

これらのアイテムをすべて一度にダウンロードするには、まず、 **[グラフ ビュー]** ボックスに完全なグラフが表示されていることを確認します。 完全なグラフがまだ表示されていない場合は、 **[クエリ エクスプローラー]** ボックスで `SELECT * FROM digitaltwins` の既定のクエリを再実行します。
 
その後、 **[グラフ ビュー]** ボックス内の **[グラフのエクスポート]** アイコンを選択します。

:::image type="content" source="media/how-to-move-regions/export-graph.png" alt-text="[グラフ ビュー] ボックスで強調表示されているアイコン。クラウドから外に向かう矢印を示している。" lightbox="media/how-to-move-regions/export-graph.png":::

この操作を行うと、 **[グラフ ビュー]** ボックスで **[ダウンロード]** リンクが有効になります。 これを選択して、モデル、ツイン、リレーションシップを含む、クエリ結果の JSON ベースの表現をダウンロードします。 この操作を行うと、.json ファイルがコンピューターにダウンロードされるはずです。

>[!NOTE]
>ダウンロードしたファイルのファイル拡張子が違っている場合は、拡張子を直接編集して .json に変更してみてください。
=======
Azure Digital Twins Explorer の手順に従って、[グラフとモデルをエクスポートします](how-to-use-azure-digital-twins-explorer.md#export-graph-and-models)。 これらの手順に従うことで、モデル、ツイン、リレーションシップのコードが含まれている JSON ファイルを、ご自身のマシンにダウンロードすることができます (グラフで現在使用されていないモデルを含む)。
>>>>>>> repo_sync_working_branch

## <a name="move"></a>詳細ビュー

次に、ターゲット リージョンに新しいインスタンスを作成して、インスタンスの "移動" を完了します。 その後、元のインスタンスのデータとコンポーネントを設定します。

### <a name="create-a-new-instance"></a>新しいインスタンスの作成

まず、Azure Digital Twins の新しいインスタンスをターゲット リージョンに作成します。 「[インスタンスと認証を設定する](how-to-set-up-instance-portal.md)」の手順に従います。 これらの点に留意してください。

* 別のリソース グループにある "*場合*" は、新しいインスタンスは同じ名前のままにすることができます。 元のインスタンスを含む同じリソース グループを使用する必要がある場合、新しいインスタンスには別個の名前が必要になります。
* 場所の入力を求められたら、新しいターゲット リージョンを入力します。

この手順が完了した後、対象のデータを使用して設定を続けるために、新しいインスタンスのホスト名が必要になります。 セットアップ中にホスト名をメモしなかった場合は、[これらの手順](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)に従って Azure portal から今すぐ取得してください。

次に、新しいインスタンスのデータを設定して、元のインスタンスのコピーになるようにします。

#### <a name="upload-models-twins-and-graph-with-azure-digital-twins-explorer"></a>Azure Digital Twins Explorer を使用してモデル、ツイン、グラフをアップロードする

このセクションでは、モデル、ツイン、グラフを新しいインスタンスに再アップロードできます。 モデル、ツイン、またはグラフが元のインスタンスにないか、それらを新しいインスタンスに移動したくない場合は、スキップして[次のセクション](#recreate-endpoints-and-routes)に進むことができます。

最初に、[Azure portal](https://portal.azure.com) で新しいインスタンスの **Azure Digital Twins Explorer** に移動します。 

この記事の前半で[ダウンロードした JSON ファイル](#download-models-twins-and-graph-with-azure-digital-twins-explorer)を、ご自身の新しいインスタンスにインポートします。 それには、Azure Digital Twins Explorer の手順に従って、[ファイルを Azure Digital Twins Explorer にインポート](how-to-use-azure-digital-twins-explorer.md#import-file-to-azure-digital-twins-explorer)します。 これらの手順により、ご自身の元のインスタンスからすべてのモデル、ツイン、リレーションシップを、新しいインスタンスにアップロードできます。

すべてが正常にアップロードされたことを確認するには、 **[ツイン グラフ]** タブに切り替えて戻り、 **[クエリ エクスプローラー]** パネルにある **[クエリの実行]** ボタンを選択して、すべてのツインとリレーションシップをグラフに表示する既定のクエリを実行します。 この操作を行うと、 **[モデル]** パネルのモデルの一覧も更新されます。

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="ウィンドウの右上隅にある [クエリの実行] ボタンが強調されている Azure Digital Twins Explorer のスクリーンショット。" lightbox="media/how-to-move-regions/run-query.png":::

すべてのツインとリレーションシップを表示したグラフが **[ツイン グラフ]** パネルに表示されます。 **[モデル]** パネルにもモデルの一覧が表示されます。

:::image type="content" source="media/how-to-move-regions/post-upload.png" alt-text="[モデル] ボックス内で強調表示された 2 つのモデルと、[ツイン グラフ] ボックス内で強調表示されたグラフを示す Azure Digital Twins Explorer のスクリーンショット。" lightbox="media/how-to-move-regions/post-upload.png":::

これらのビューで、モデル、ツイン、グラフがターゲット リージョンの新しいインスタンスに再アップロードされたことを確認できます。

#### <a name="recreate-endpoints-and-routes"></a>エンドポイントとルートの再作成

元のインスタンスにエンドポイントまたはルートがある場合、新しいインスタンスでそれらを再作成する必要があります。 それ以外の場合、エンドポイントまたはルートが元のインスタンスにないか、それらを新しいインスタンスに移動したくないときは、スキップして[次のセクション](#relink-connected-resources)に進むことができます。

それ以外の場合、新しいインスタンスを使用して、「[エンドポイントとルートを管理する](how-to-manage-routes.md)」の手順に従います。 これらの点に留意してください。

* エンドポイントに使用している Event Grid、Event Hubs、Service Bus リソースを再作成する必要は "*ありません*"。 詳細については、エンドポイントに関する指示の「前提条件」セクションを参照してください。 必要なのは、Azure Digital Twins インスタンスでエンドポイントを再作成することだけです。
* エンドポイントとルートの名前は別のインスタンスにスコープ設定されるため、再利用できます。
* 作成するルートに必要なフィルターがあれば、必ず追加してください。

#### <a name="relink-connected-resources"></a>接続されたリソースを再リンクする

元の Azure Digital Twins インスタンスに接続している他のアプリまたは Azure リソースがある場合、代わりに新しいインスタンスに到達するように接続を編集する必要があります。 これらのリソースには、他の Azure サービスや、Azure Digital Twins と連携するように設定した個人または会社のアプリが含まれる場合があります。

元のインスタンスに接続している他のリソースがないか、それらを新しいインスタンスに移動したくない場合は、スキップして[次のセクション](#verify)に進むことができます。

それ以外の場合は、対象のシナリオで接続されているリソースを考慮してください。 接続されているリソースを削除して再作成する必要はありません。 代わりに、そのホスト名を使用して Azure Digital Twins インスタンスに接続するポイントを編集するだけで済みます。 その後、元のではなく、新しいインスタンスのホスト名を使用するようにこれらのポイントを更新します。

編集する必要があるリソースは正確にはシナリオによって異なりますが、一般的な統合ポイントの例を次に示します。

* Azure Functions。 そのコードに元のインスタンスのホスト名が含まれている Azure 関数がある場合は、この値を新しいインスタンスのホスト名に更新して関数を再発行する必要があります。
* Event Grid、Event Hubs、または Service Bus。
* Logic Apps。
* Time Series Insights。
* Azure Maps。
* IoT Hub Device Provisioning Service。
* Azure の外部の個人または会社のアプリ。たとえば、「[クライアント アプリをコーディングする](tutorial-code.md)」で作成された、インスタンスに接続し、Azure Digital Twins API を呼び出すクライアント アプリなどです。
* Azure AD アプリ登録は再作成する必要が "*ありません*"。 [アプリ登録](./how-to-create-app-registration-portal.md)を使用して Azure Digital Twins API に接続する場合、新しいインスタンスで同じアプリ登録を再利用できます。

この手順を完了した後、ターゲット リージョンの新しいインスタンスが元のインスタンスのコピーになるはずです。

## <a name="verify"></a>確認

新しいインスタンスが正しく設定されたことを確認するには、次のツールを使用します。

* [Azure Portal](https://portal.azure.com) このポータルは、新しいインスタンスが存在し、正しいターゲット リージョンにあることを確認するのに適しています。 また、エンドポイントとルート、および他の Azure サービスへの接続を確認するのに適しています。
* [Azure Digital Twins CLI コマンド](/cli/azure/dt?view=azure-cli-latest&preserve-view=true)。 これらのコマンドは、新しいインスタンスが存在し、正しいターゲット リージョンにあることを確認するのに適しています。 また、これらはインスタンス データを確認するために使用できます。
* [Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/)。 Azure Digital Twins Explorer は、モデル、ツイン、グラフなどのインスタンス データを確認するのに適しています。
* [Azure Digital Twins API および SDK](concepts-apis-sdks.md)。 これらのリソースは、モデル、ツイン、グラフなどのインスタンス データを確認するのに適しています。 また、エンドポイントとルートの確認にも適しています。

元のインスタンスで実行していたカスタム アプリやエンドツーエンド フローを実行してみることもでき、これは、それらが新しいインスタンスで正しく動作していることを確認するのに役立ちます。

## <a name="clean-up-source-resources"></a>ソース リソースをクリーンアップする

これで、元のインスタンスのデータと接続のコピーを使用して新しいインスタンスがターゲット リージョンに設定されたので、元のインスタンスを削除することができます。

[Azure portal](https://portal.azure.com)、[Azure CLI](/cli/azure/dt?view=azure-cli-latest&preserve-view=true)、または[コントロール プレーン API](concepts-apis-sdks.md#overview-control-plane-apis) を使用できます。

Azure portal を使用してインスタンスを削除するには、ブラウザー ウィンドウで[ポータルを開き](https://portal.azure.com)、ポータルの検索バーで元の Azure Digital Twins インスタンスの名前を検索して、そこに移動します。

**[削除]** ボタンを選択し、画面の指示に従って削除を完了します。

<<<<<<< HEAD
:::image type="content" source="media/how-to-move-regions/delete-instance.png" alt-text="Azure portal の [概要] タブでの Azure Digital Twins インスタンスの詳細のビュー。[削除] ボタンが強調表示されている。":::
=======
:::image type="content" source="media/how-to-move-regions/delete-instance.png" alt-text="Azure portal の [概要] タブでの Azure Digital Twins インスタンスの詳細のスクリーンショット。[削除] ボタンが強調表示されています。":::
>>>>>>> repo_sync_working_branch
