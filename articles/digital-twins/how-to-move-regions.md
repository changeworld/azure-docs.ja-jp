---
title: インスタンスを別の Azure リージョンに移動する
titleSuffix: Azure Digital Twins
description: Azure Digital Twins インスタンスを Azure リージョン間で移動する方法について説明します。
author: baanders
ms.author: baanders
ms.date: 08/26/2020
ms.topic: how-to
ms.custom: subject-moving-resources
ms.service: digital-twins
ms.openlocfilehash: e2cb8ee282666d7a9a567ca04762b26de3b3b9bd
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2020
ms.locfileid: "89443043"
---
# <a name="move-an-azure-digital-twins-instance-to-a-different-azure-region"></a>Azure Digital Twins インスタンスを別の Azure リージョンに移動する

Azure Digital Twins インスタンスをリージョン間で移動する必要がある場合、現在のプロセスでは、**新しいリージョンでリソースを再作成**してから、元のリソースを削除します。 このプロセスの終わりには、更新された場所を除いて最初のものと同じである新しい Azure Digital Twins インスタンスを操作することになります。

この記事では、新しいインスタンスを元のインスタンスと一致させるために必要なすべてをコピーして、完全な移動を実行する方法についてのガイダンスを提供します。

このプロセスには、次の手順が含まれます。
1. 準備:元のモデル、ツイン、グラフをダウンロードします。
2. 移動:新しい Azure Digital Twins インスタンスを新しいリージョンに作成します。
3. 移動:新しい Azure Digital Twins インスタンスにデータを再入力します。
    - 元のモデル、ツイン、グラフをアップロードします。
    - エンドポイントとルートを再作成します。
    - 接続されたリソースを再リンクします。
4. ソース リソースのクリーンアップ: 元のインスタンスを削除します。

## <a name="prerequisites"></a>前提条件

Azure Digital Twins インスタンスを再作成する前に、元のインスタンスのコンポーネントを確認し、再作成する必要があるすべての要素を明確に把握しておくことをお勧めします。

考慮する必要があるいくつかの質問を次に示します。
* どのような**モデル**がインスタンスにアップロードされているか? いくつあるか?
* インスタンス内の**ツイン**は何か? いくつあるか?
* インスタンス内の**グラフ**の一般的な形状はどのようなものか? リレーションシップはいくつあるか?
* どのような**エンドポイント**がインスタンスにあるか?
* どのような**ルート**がインスタンスにあるか? フィルターはあるか?
* インスタンスはどこで**他の Azure サービスに接続**するか? 一般的な統合ポイントの例を次に示します。
    - Event Grid、Event Hub、または Service Bus
    - Azure Functions
    - Logic Apps
    - Time Series Insights
    - Azure Maps
    - デバイス プロビジョニング サービス (DPS)
* インスタンスに接続する**個人または会社のアプリ**には、他にどのようなものがあるか?

この情報は、[Azure portal](https://portal.azure.com)、[Azure Digital Twins の API と SDK](how-to-use-apis-sdks.md)、[Azure Digital Twins CLI コマンド](how-to-use-cli.md)、または [Azure Digital Twins (ADT) エクスプローラー](https://docs.microsoft.com/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) サンプルを使用して収集できます。

## <a name="prepare"></a>準備

このセクションでは、元のインスタンスから**元のモデル、ツイン、グラフをダウンロード**することによってインスタンスを再作成するための準備をします。 これを行うために、この記事では、[Azure Digital Twins (ADT) Explorer](https://docs.microsoft.com/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) サンプルを使用しています。

>[!NOTE]
>モデルやグラフを含むファイルがインスタンスに既に存在している場合があります。 その場合、すべてを再度ダウンロードする必要はありません。不足しているものや、これらのファイルを最初にアップロードした後に変更された可能性があるもの (新しいデータで更新された可能性があるツインなど) のみをダウンロードしてください。

### <a name="limitations-of-adt-explorer"></a>ADT Explorer の制限事項

[Azure Digital Twins (ADT) Explorer サンプル](https://docs.microsoft.com/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/)は、グラフの視覚的な表現をサポートするクライアント アプリのサンプルであり、インスタンスとの視覚的な対話機能を備えています。 この記事では、これを使用して、モデル、ツイン、グラフをダウンロードして、後で再アップロードする方法について説明します。

ただし、これは**サンプル**であって、完全なツールではないことにご注意ください。 ストレス テストが行われておらず、大きなサイズのグラフを処理するように構築されていませんでした。 そのため、次のようなすぐに使えるサンプルの制限事項に留意してください。
* このサンプルは現在、最大 1,000 ノードと 2,000 のリレーションシップのグラフ サイズでのみテストされています
* このサンプルは、断続的なエラーが発生した場合の再試行はサポートしません
* このサンプルでは、アップロードされたデータが不完全かどうかについて、ユーザーに通知を行いません
* このサンプルでは、メモリなどの使用可能なリソースを超える非常に大きなグラフによって発生したエラーは処理されません

お使いのグラフのサイズをこのサンプルで処理できない場合は、他の Azure Digital Twins 開発者ツールを使用してグラフをエクスポートおよびインポートできます。
* [Azure Digital Twins CLI コマンド](how-to-use-cli.md)
* [Azure Digital Twins API および SDK](how-to-use-apis-sdks.md)

### <a name="set-up-adt-explorer-application"></a>ADT エクスプローラー アプリケーションの設定

ADT Explorer の設定を進めるには、まず、サンプル アプリケーション コードをダウンロードして設定し、コンピューター上で実行します。 

サンプルにはこちらからアクセスします: [Azure Digital Twins (ADT) エクスプローラー](https://docs.microsoft.com/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/)。 *[Download ZIP]\(ZIP のダウンロード\)* ボタンをクリックして、このサンプル コードの *.ZIP* ファイルを _**ADT_Explorer.zip**_ としてご自分のマシンにダウンロードしてください。 ファイルを解凍します。

次に、コンピューター上で実行するために ADT エクスプローラーのアクセス許可を設定します。 これを行うには、Azure Digital Twins クイックスタートの「[*ADT エクスプローラーのアクセス許可を設定する*](quickstart-adt-explorer.md#set-adt-explorer-permissions)」セクションの手順に従います。

最後に、ADT エクスプローラーを実行し、元の Azure Digital Twins インスタンスに接続するように構成します。 クイックスタートの「[*ADT エクスプローラーを実行して構成する*](quickstart-adt-explorer.md#run-and-configure-adt-explorer)」セクションの手順に従います。

これで、コンピューター上のブラウザーで ADT エクスプローラー サンプル アプリが実行されます。 このサンプルが、元の Azure Digital Twins インスタンスに接続していることを確認します。

:::image type="content" source="media/how-to-move-regions/explorer-blank.png" alt-text="localhost:3000 で実行中のアプリがブラウザー ウィンドウに表示されている。このアプリは ADT エクスプローラーと呼ばれ、クエリ エクスプローラー、モデル ビュー、グラフ ビュー、プロパティ エクスプローラーのボックスが存在する。画面上にはまだデータが入力されていない。" lightbox="media/how-to-move-regions/explorer-blank.png":::

接続を確認するには、 *[クエリの実行]* ボタンをクリックして既定のクエリを実行します。このクエリにより、 *[グラフ エクスプローラー]* ボックスのグラフにすべてのツインとリレーションシップが表示されます。

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="localhost:3000 で実行中のアプリがブラウザー ウィンドウに表示されている。このアプリは ADT エクスプローラーと呼ばれ、クエリ エクスプローラー、モデル ビュー、グラフ ビュー、プロパティ エクスプローラーのボックスが存在する。画面上にはまだデータが入力されていない。" lightbox="media/how-to-move-regions/run-query.png":::

この記事の後半で、ADT エクスプローラーを再び使用して、これらのアイテムをターゲット リージョンの新しいインスタンスに再アップロードします。そのため、ADT エクスプローラーは実行したままでかまいません。

### <a name="download-models-twins-and-graph"></a>モデル、ツイン、グラフのダウンロード

次に、ソリューションのモデル、ツイン、グラフをコンピューターにダウンロードします。

これらすべてを一度にダウンロードするには、まず、完全なグラフが *[グラフ ビュー]* ボックスに表示されていることを確認します (これを行うには、 *[クエリ エクスプローラー]* ボックスで `SELECT * FROM digitaltwins` の既定のクエリを再実行します)。
 
次に、 *[グラフ ビュー]* ボックス内の *[グラフのエクスポート]* アイコンをクリックします。

:::image type="content" source="media/how-to-move-regions/export-graph.png" alt-text="localhost:3000 で実行中のアプリがブラウザー ウィンドウに表示されている。このアプリは ADT エクスプローラーと呼ばれ、クエリ エクスプローラー、モデル ビュー、グラフ ビュー、プロパティ エクスプローラーのボックスが存在する。画面上にはまだデータが入力されていない。" lightbox="media/how-to-move-regions/export-graph.png":::

これにより、 *[グラフ ビュー]* で *[ダウンロード]* リンクが有効になります。 これを選択して、モデル、ツイン、リレーションシップなど、クエリ結果の JSON ベースの表現をダウンロードします。 これにより、 *.json* ファイルがコンピューターにダウンロードされます。

>[!NOTE]
>ダウンロードしたファイルのファイル拡張子が違っている場合は、拡張子を直接編集して *.json* に変更してみてください。

## <a name="move"></a>詳細ビュー

次に、新しいインスタンスをターゲット リージョンに作成し、元のインスタンスのデータとコンポーネントを新しいインスタンスに入力することによって、インスタンスの "移動" を完了します。

### <a name="create-a-new-instance"></a>新しいインスタンスの作成

まず、**Azure Digital Twins の新しいインスタンスをターゲット リージョンに作成**します。 これを行うには、「[*方法: インスタンスと認証を設定する*](how-to-set-up-instance-scripted.md)」の手順に従いますが、次のことに注意してください。
* 別のリソース グループにある**場合**は、新しいインスタンスは同じ名前のままにすることができます。 元のインスタンスを含む同じリソース グループを使用する必要がある場合、新しいインスタンスには別個の名前が必要になります。
* 場所の入力を求められたら、ターゲットの新しいリージョンを入力します。
* アプリの登録を再作成する**必要はありません**。 新しいインスタンスでは、既存のものと同じアプリ登録を再利用できます。
    - [スクリプト](how-to-set-up-instance-scripted.md)による設定の記事を使用している場合、プロンプトが表示されたときに新しい名前を入力する代わりに、既存のアプリ登録の詳細を再入力できます。
    - [ポータル](how-to-set-up-instance-portal.md)または [CLI](how-to-set-up-instance-cli.md) からの手動設定の記事を使用している場合、*Azure Digital Twins インスタンスの作成*および*ユーザーのアクセス許可の設定*の各手順の後に停止できます。 *クライアント アプリケーションのアクセス許可の設定*を続行する必要はありません。

これが完了した後は、対象のデータを使用して新しいインスタンスの設定を続けるために、新しいインスタンスの**ホスト名**が必要になります。 セットアップ中にこの情報をメモしていない場合、[以下の手順](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)に従って Azure portal から今すぐ取得できます。

### <a name="repopulate-old-instance"></a>古いインスタンスへのデータ再入力

次に、元のインスタンスのコピーになるように新しいインスタンスを設定します。

#### <a name="upload-original-models-twins-and-graph-using-adt-explorer"></a>ADT エクスプローラーを使用して元のモデル、ツイン、グラフをアップロードする

このセクションでは、モデル、ツイン、グラフを新しいインスタンスに再アップロードできます。 モデル、ツイン、またはグラフが元のインスタンスにないか、それらを新しいインスタンスに移動したくない場合は、スキップして[次のセクション](#recreate-endpoints-and-routes)に進むことができます。

それ以外の場合、続行するには、**ADT エクスプローラー**を実行しているブラウザー ウィンドウに戻り、以下の手順に従います。

##### <a name="connect-to-the-new-instance"></a>新しいインスタンスへの接続

ADT エクスプローラーは現在、元の Azure Digital Twins インスタンスに接続しています。 ウィンドウの上部にある *[サインイン]* ボタンをクリックして、新しいインスタンスを指すように接続を切り替えます。 

:::image type="content" source="media/how-to-move-regions/sign-in.png" alt-text="localhost:3000 で実行中のアプリがブラウザー ウィンドウに表示されている。このアプリは ADT エクスプローラーと呼ばれ、クエリ エクスプローラー、モデル ビュー、グラフ ビュー、プロパティ エクスプローラーのボックスが存在する。画面上にはまだデータが入力されていない。" lightbox="media/how-to-move-regions/sign-in.png":::

アプリの登録を再利用しているので、必要なのは *ADT URL* を置き換えることだけです。 この値を *https://{新しいインスタンスのホスト名}* に変更します。

*[接続]* をクリックします。 Azure の資格情報を使用して再度ログインするか、インスタンスに対する同意をこのアプリケーションに付与するか、あるいはその両方を行うように求められる場合があります。

##### <a name="upload-models-twins-and-graph"></a>モデル、ツイン、グラフのアップロード

次に、以前にダウンロードしたソリューション コンポーネントを新しいインスタンスにアップロードします。

**モデル、ツイン、グラフ**をアップロードするには、 *[グラフ ビュー]* ボックスにある *[グラフのインポート]* アイコンをクリックします。 このオプションは、(グラフで現在使用されていないモデルも含めて) これら 3 つのコンポーネントすべてを一度にアップロードします。

:::image type="content" source="media/how-to-move-regions/import-graph.png" alt-text="localhost:3000 で実行中のアプリがブラウザー ウィンドウに表示されている。このアプリは ADT エクスプローラーと呼ばれ、クエリ エクスプローラー、モデル ビュー、グラフ ビュー、プロパティ エクスプローラーのボックスが存在する。画面上にはまだデータが入力されていない。" lightbox="media/how-to-move-regions/import-graph.png":::

ファイル選択ボックスで、ダウンロードしたグラフに移動します。 グラフの *.json* ファイルを選択して *[開く]* をクリックします。

数秒後、ADT エクスプローラーで *[インポート]* ビューが開き、読み込みの対象となるグラフのプレビューが表示されます。

グラフのアップロードを確認するには、 *[グラフ ビュー]* の右上隅にある *[保存]* アイコンをクリックします。

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/graph-preview-save.png" alt-text="localhost:3000 で実行中のアプリがブラウザー ウィンドウに表示されている。このアプリは ADT エクスプローラーと呼ばれ、クエリ エクスプローラー、モデル ビュー、グラフ ビュー、プロパティ エクスプローラーのボックスが存在する。画面上にはまだデータが入力されていない。" lightbox="media/how-to-move-regions/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

ADT エクスプローラーにより、(ツインとリレーションシップを含む) モデルとグラフが新しい Azure Digital Twins インスタンスにアップロードされます。 アップロードされたモデル、ツイン、リレーションシップの数を示す成功メッセージが表示されます。

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/import-success.png" alt-text="localhost:3000 で実行中のアプリがブラウザー ウィンドウに表示されている。このアプリは ADT エクスプローラーと呼ばれ、クエリ エクスプローラー、モデル ビュー、グラフ ビュー、プロパティ エクスプローラーのボックスが存在する。画面上にはまだデータが入力されていない。" lightbox="media/how-to-move-regions/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

すべてが正常にアップロードされたことを確認するには、 *[グラフ エクスプローラー]* ボックスにある *[クエリの実行]* ボタンをクリックして、すべてのツインとリレーションシップをグラフに表示する既定のクエリを実行します。 これにより、 *[モデル ビュー]* のモデルの一覧も更新されます。

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="localhost:3000 で実行中のアプリがブラウザー ウィンドウに表示されている。このアプリは ADT エクスプローラーと呼ばれ、クエリ エクスプローラー、モデル ビュー、グラフ ビュー、プロパティ エクスプローラーのボックスが存在する。画面上にはまだデータが入力されていない。" lightbox="media/how-to-move-regions/run-query.png":::

すべてのツインとリレーションシップを表示したグラフが *[グラフ エクスプローラー]* ボックスに表示されます。 *[モデル ビュー]* ボックスにもモデルの一覧が表示されます。

:::image type="content" source="media/how-to-move-regions/post-upload.png" alt-text="localhost:3000 で実行中のアプリがブラウザー ウィンドウに表示されている。このアプリは ADT エクスプローラーと呼ばれ、クエリ エクスプローラー、モデル ビュー、グラフ ビュー、プロパティ エクスプローラーのボックスが存在する。画面上にはまだデータが入力されていない。" lightbox="media/how-to-move-regions/post-upload.png":::

これにより、モデル、ツイン、グラフがターゲット リージョンの新しいインスタンスに再アップロードされたことが確認できます。

#### <a name="recreate-endpoints-and-routes"></a>エンドポイントとルートの再作成

元のインスタンスに**エンドポイントやルート**がある場合、新しいインスタンスでそれらを再作成する必要があります。 エンドポイントまたはルートが元のインスタンスにないか、それらを新しいインスタンスに移動したくない場合は、スキップして[次のセクション](#re-link-connected-resources)に進むことができます。

それ以外の場合は、次に進み、「[*方法: エンドポイントとルートを管理する*](how-to-manage-routes-portal.md)」の手順に従います。このとき、新しいインスタンスを使用して、次の点に注意してください。 
* エンドポイントに使用している Event Grid、Event Hub、または Service Bus リソースを再作成する**必要はありません** (エンドポイントに関する指示の「*前提条件*」セクション)。 必要なのは、Azure Digital Twins インスタンスでエンドポイントを再作成することだけです。
* エンドポイントとルートの**名前**は別のインスタンスにスコープ設定されるため、再利用できます。
* 作成するルートに必要な**フィルター**があれば、必ず追加してください。

#### <a name="re-link-connected-resources"></a>接続されたリソースの再リンク

元の Azure Digital Twins インスタンスに接続している他のアプリまたは Azure リソースがある場合、代わりに新しいインスタンスに到達するように接続を編集する必要があります。 これには、他の Azure サービスや、Azure Digital Twins と連携するように設定した個人または会社のアプリが含まれる場合があります。

元のインスタンスに接続している他のリソースがないか、それらを新しいインスタンスに移動したくない場合は、スキップして[次のセクション](#verify)に進むことができます。

それ以外の場合、先に進むには、対象のシナリオで接続されているリソースを考慮してください。 接続しているリソースを削除して再作成する必要はありません。代わりに、その**ホスト名**を使用して Azure Digital Twins インスタンスに接続するポイントを編集し、元のインスタンスではなく新しいインスタンスのホスト名を使用するようにこれを更新する必要があるだけです。

編集する必要があるリソースは正確にはシナリオによって異なりますが、一般的な統合ポイントの例を次に示します。
* Azure Functions。 そのコードに元のインスタンスのホスト名が含まれている Azure 関数がある場合、この値を新しいインスタンスのホスト名に更新して関数を再発行する必要があります。
* Event Grid、Event Hubs、または Service Bus
* Logic Apps
* Time Series Insights
* Azure Maps
* デバイス プロビジョニング サービス (DPS)
* Azure の外部の個人または会社のアプリ。このような**クライアント アプリ**の例としては、「[*チュートリアル: クライアント アプリをコーディングする*](tutorial-code.md)」で作成した、インスタンスに接続して Azure Digital Twins API を呼び出すアプリがあります。

この手順を完了すると、ターゲット リージョンの新しいインスタンスは元のインスタンスのコピーになります。

## <a name="verify"></a>確認

新しいインスタンスが正しく設定されたことを確認するには、次のツールを使用できます。
* [**Azure portal**](https://portal.azure.com) (新しいインスタンスが存在し、正しいターゲット リージョンにあることの確認に適しています。エンドポイントとルート、および他の Azure サービスへの接続の確認にも適しています)
* [Azure Digital Twins **CLI コマンド**](how-to-use-cli.md) (新しいインスタンスが存在し、正しいターゲット リージョンにあることの確認に適しています。インスタンス データの確認にも使用できます)
* [**ADT エクスプローラー**](https://docs.microsoft.com/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) (モデル、ツイン、グラフなどのインスタンスデータの確認に適しています)
* [Azure Digital Twins の API と SDK](how-to-use-apis-sdks.md) (モデル、ツイン、グラフなどのインスタンス データの確認に適しています。エンドポイントとルートの確認にも適しています)

元のインスタンスで実行していたカスタム アプリやエンドツーエンド フローを実行してみることもでき、これは、それらが新しいインスタンスで正しく動作していることの確認に役立ちます。

## <a name="clean-up-source-resources"></a>ソース リソースをクリーンアップする

これで、元のインスタンスのデータと接続のコピーを使用して新しいインスタンスがターゲット リージョンに設定されたので、**元のインスタンスを削除**することができます。

これは [Azure portal](https://portal.azure.com)、[CLI](how-to-use-cli.md)、または[コントロール プレーン API](how-to-use-apis-sdks.md#overview-control-plane-apis) を使用して実行できます。

Azure portal を使用してインスタンスを削除するには、ブラウザー ウィンドウで[ポータルを開き](https://portal.azure.com)、ポータルの検索バーで元の Azure Digital Twins インスタンスの名前を検索して、そのインスタンスにアクセスします。

*[削除]* ボタンをクリックし、画面の指示に従って削除を完了します。

:::image type="content" source="media/how-to-move-regions/delete-instance.png" alt-text="localhost:3000 で実行中のアプリがブラウザー ウィンドウに表示されている。このアプリは ADT エクスプローラーと呼ばれ、クエリ エクスプローラー、モデル ビュー、グラフ ビュー、プロパティ エクスプローラーのボックスが存在する。画面上にはまだデータが入力されていない。":::