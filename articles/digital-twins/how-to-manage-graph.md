---
title: ツイン グラフとリレーションシップを管理する
titleSuffix: Azure Digital Twins
description: リレーションシップを使って接続することでデジタル ツインのグラフを管理する方法について説明します。
author: baanders
ms.author: baanders
ms.date: 9/13/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 5c02ad72ace0568f7be1062993adaf154bb31cbf
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128624313"
---
# <a name="manage-a-graph-of-digital-twins-using-relationships"></a>リレーションシップを使ってデジタル ツインのグラフを管理する

Azure Digital Twins の中核は、環境全体を表す[ツイン グラフ](concepts-twins-graph.md)です。 ツイン グラフは、**リレーションシップ** を介して接続された個々のデジタル ツインで構成されています。 

機能する [Azure Digital Twins インスタンス](how-to-set-up-instance-portal.md)があり、クライアント アプリで[認証](how-to-authenticate-client.md)コードを設定すると、Azure Digital Twins インスタンス内のデジタル ツインとそのリレーションシップを作成、変更、削除することができるようになります。

この記事では、リレーションシップとグラフ全体の管理に焦点を当てます。個々のデジタル ツインの操作については、「[デジタル ツインを管理する](how-to-manage-twin.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

[!INCLUDE [digital-twins-developer-interfaces.md](../../includes/digital-twins-developer-interfaces.md)]

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

:::image type="content" source="media/concepts-azure-digital-twins-explorer/azure-digital-twins-explorer-demo.png" alt-text="サンプルのモデルとツインが表示されている Azure Digital Twins Explorer のスクリーンショット。" lightbox="media/concepts-azure-digital-twins-explorer/azure-digital-twins-explorer-demo.png":::


## <a name="create-relationships"></a>リレーションシップの作成

リレーションシップには、さまざまなデジタル ツインが相互にどのように接続されるかが記述され、それによってツイン グラフの基礎が形成されます。

リレーションシップは、`CreateOrReplaceRelationshipAsync()` 呼び出しを使用して作成されます。 

リレーションシップを作成するには、以下を指定する必要があります。
* ソース ツイン ID (以下のコード サンプルの `srcId`):リレーションシップが発生したツインの ID。
* ターゲット ツイン ID (以下のコード サンプルの `targetId`):リレーションシップの対象のツインの ID。
* リレーションシップ名 (以下のコード サンプルの `relName`):_contains_ のような一般的な種類のリレーションシップ。
* リレーションシップ ID (以下のコード サンプルの `relId`):このリレーションシップの具体的な名前 (_Relationship1_ など)。

リレーションシップ ID は、指定されたソース ツイン内で一意である必要があります。 グローバルに一意である必要はありません。
たとえば、ツイン Foo の場合、それぞれのリレーションシップ ID は一意である必要があります。 ただし、別のツインである Bar は、Foo リレーションシップの同じ ID と一致する発信リレーションシップを持つことができます。

次のコード サンプルは、Azure Digital Twins インスタンスにリレーションシップを作成する方法を示しています。 より大きなプログラムのコンテキストで使用される可能性のあるカスタム メソッド内で SDK 呼び出し (強調表示) を使用しています。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="CreateRelationshipMethod" highlight="13":::

このカスタム関数を呼び出して、次の方法で _包含_ 関係を作成できます。 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseCreateRelationship":::

複数のリレーションシップを作成する場合は、同じメソッドの呼び出しを繰り返して、異なるリレーションシップの種類を引数に渡すことができます。 

ヘルパー クラス `BasicRelationship` の詳細については、[Azure Digital Twins API および SDK](concepts-apis-sdks.md#serialization-helpers) に関するページを参照してください。

### <a name="create-multiple-relationships-between-twins"></a>ツイン間でリレーションシップの作成

リレーションシップは次のいずれかに分類できます。 

* 発信リレーションシップ:他のツインに接続するために外側を指す、このツインに属するリレーションシップ。 `GetRelationshipsAsync()` メソッドは、ツインの発信リレーションシップを取得するために使用されます。
* 受信リレーションシップ:"受信" リンクを作成するためにこのツインを指す、他のツインに属するリレーションシップ。 `GetIncomingRelationshipsAsync()` メソッドは、ツインの受信リレーションシップを取得するために使用されます。

2 つのツイン間のリレーションシップの数には制限がありません。ツイン間には、必要な数のリレーションシップを含めることができます。 

これは、2 つのツイン間に、さまざまな種類のリレーションシップを同時に複数表すことができることを意味します。 たとえば、ツイン A とツイン B の間に、"*格納された*" リレーションシップと "*製造された*" リレーションシップを含めることができます。

必要に応じて、同じ 2 つのツイン間に、同じ種類のリレーションシップのインスタンスを複数作成することもできます。 この例では、リレーションシップのリレーションシップ ID が異なる限り、ツイン A はツイン B と 2 つの異なる "*格納された*" リレーションシップを持つことができます。

## <a name="list-relationships"></a>リレーションシップの一覧表示

### <a name="list-properties-of-a-single-relationship"></a>1 つのリレーションシップのプロパティを一覧表示する

リレーションシップ データはいつでも任意の型に逆シリアル化できます。 リレーションシップへの基本的なアクセスには、`BasicRelationship` 型を使用します。 また、`BasicRelationship` ヘルパー クラスを使用すると、リレーションシップで定義されたプロパティに `IDictionary<string, object>` を介してアクセスできます。 プロパティを一覧表示するには、次のコードを使用します。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_other.cs" id="ListRelationshipProperties":::

### <a name="list-outgoing-relationships-from-a-digital-twin"></a>デジタル ツインからの出力方向のリレーションシップを一覧表示する

グラフ内の特定のツインの "**発信**" リレーションシップの一覧にアクセスするには、次のように `GetRelationships()` メソッドを使用できます。 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="GetRelationshipsCall":::

このメソッドでは、呼び出しの同期または非同期のバージョンのどちらを使用するかに応じて、`Azure.Pageable<T>` または `Azure.AsyncPageable<T>` が返されます。

リレーションシップの一覧を取得する例を次に示します。 より大きなプログラムのコンテキストで使用される可能性のあるカスタム メソッド内で SDK 呼び出し (強調表示) を使用しています。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FindOutgoingRelationshipsMethod" highlight="8":::

これで、このカスタム メソッドを呼び出して、このようなツインの発信リレーションシップを確認できます。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseFindOutgoingRelationships":::

取得したリレーションシップを使用すると、返されたリレーションシップから `target` フィールドを読み取り、そのフィールドを `GetDigitalTwin()` の次の呼び出しの ID として使用することで、グラフ内の他のツインに移動できます。

### <a name="list-incoming-relationships-to-a-digital-twin"></a>デジタル ツインとの受信リレーションシップを一覧表示する

Azure Digital Twins には、特定のツインとのすべての **受信** リレーションシップを検索するための API もあります。 この SDK は、逆方向のナビゲーションの場合やツインを削除するときに便利です。

>[!NOTE]
> `IncomingRelationship` の呼び出しからは、リレーションシップ全体は返されません。 `IncomingRelationship` クラスの詳細については、その[リファレンス ドキュメント](/dotnet/api/azure.digitaltwins.core.incomingrelationship?view=azure-dotnet&preserve-view=true)を参照してください。

前のセクションのコード サンプルは、ツインからの発信リレーションシップの検索に重点を置いていました。 次の例は同じような構造になってますが、代わりにツインへの "*受信*" リレーションシップを検索します。 この例でも、より大きなプログラムのコンテキストで使用される可能性のあるカスタム メソッド内で SDK 呼び出し (強調表示) を使用しています。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FindIncomingRelationshipsMethod" highlight="8":::

これで、このカスタム メソッドを呼び出して、このようなツインの受信リレーションシップを確認できます。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseFindIncomingRelationships":::

### <a name="list-all-twin-properties-and-relationships"></a>ツインのすべてのプロパティとリレーションシップを一覧表示する

ツインへの発信と受信のリレーションシップを一覧表示する上記のメソッドを使用すると、ツインのプロパティとその両方の種類のリレーションシップを含む完全なツインの情報を出力するメソッドを作成できます。 この目的のために上記のカスタム メソッドを組み合わせる方法を示すカスタム メソッドの例を次に示します。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FetchAndPrintMethod":::

これで、このようにこのカスタム関数を呼び出すことができます。 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseFetchAndPrint":::

## <a name="update-relationships"></a>リレーションシップの更新

リレーションシップは、`UpdateRelationship` メソッドを使用して更新します。 

>[!NOTE]
>このメソッドは、リレーションシップの **プロパティ** を更新するためのものです。 リレーションシップのソース ツインまたはターゲット ツインを変更する必要がある場合は、[リレーションシップを削除](#delete-relationships)し、新しいツインを使用して[再作成](#create-relationships)する必要があります。

クライアント呼び出しに必要なパラメーターは次のとおりです。
- ソース ツインの ID (リレーションシップの発生元のツイン)。
- 更新するリレーションシップの ID です。
- 更新するプロパティおよび新しい値が含まれている [JSON パッチ](http://jsonpatch.com/)のドキュメント。

このメソッドの使用方法を示すサンプル コード スニペットを次に示します。 この例では、より大きなプログラムのコンテキストで使用される可能性のあるカスタム メソッド内で SDK 呼び出し (強調表示) を使用しています。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UpdateRelationshipMethod" highlight="6":::

プロパティを更新するための情報を含む JSON パッチ ドキュメントを渡して、このカスタム メソッドを呼び出す例を次に示します。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseUpdateRelationship":::

## <a name="delete-relationships"></a>リレーションシップの削除

最初のパラメーターには、ソース ツイン (リレーションシップの発生元のツイン) を指定します。 もう一方のパラメーターはリレーションシップ ID です。 リレーションシップ ID はツインのスコープ内でのみ一意であるため、ツイン ID とリレーションシップ ID の両方が必要です。

このメソッドの使用方法を示すサンプル コードを次に示します。 この例では、より大きなプログラムのコンテキストで使用される可能性のあるカスタム メソッド内で SDK 呼び出し (強調表示) を使用しています。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="DeleteRelationshipMethod" highlight="5":::

これで、このカスタム メソッドを呼び出して、このようなリレーションシップを削除できます。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseDeleteRelationship":::

## <a name="create-graph-from-a-csv-file"></a>CSV ファイルからグラフを作成する

実際の使用例では、多くの場合、別のデータベース、または場合によってはスプレッドシートまたは CSV ファイルに格納されたデータからツイン階層が作成されます。 このセクションでは、CSV ファイルからデータを読み取り、そこからツイン グラフを作成する方法について説明します。

デジタル ツインのセットとリレーションシップが説明されている次のデータ テーブルがあるとします。

|  モデル ID    | ツイン ID (一意である必要があります) | [リレーションシップ名]  | ターゲット ツイン ID  | ツインの init データ |
| --- | --- | --- | --- | --- |
| dtmi:example:Floor;1    | Floor1 | contains | Room1 | |
| dtmi:example:Floor;1    | Floor0 | contains | Room0 | |
| dtmi:example:Room;1    | Room1 | | | {"Temperature":80} |
| dtmi:example:Room;1    | Room0 | | | {"Temperature":70} |

このデータを Azure Digital Twins に取り込む 1 つの方法は、テーブルを CSV ファイルに変換することです。 テーブルが変換されたら、コードを記述して、ファイルをコマンドに解釈し、ツインとリレーションシップを作成できます。 次のコード サンプルは、CSV ファイルからのデータの読み取り、および Azure Digital Twins でのツイン グラフの作成を示しています。

次のコードでは、CSV ファイルは *data.csv* と呼ばれ、Azure Digital Twins インスタンスの **ホスト名** を表すプレースホルダーがあります。 また、このサンプルでは、プロジェクトに追加できるパッケージをいくつか使用すると、このプロセスを実行しやすくなります。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graphFromCSV.cs":::

## <a name="runnable-twin-graph-sample"></a>実行可能なツインのグラフのサンプル

次の実行可能なコード スニペットでは、この記事のリレーションシップ操作を使用して、デジタル ツインとリレーションシップからツイン グラフを作成します。

### <a name="set-up-sample-project-files"></a>サンプル プロジェクト ファイルの設定

このスニペットでは、[Room.json](https://raw.githubusercontent.com/Azure-Samples/digital-twins-samples/master/AdtSampleApp/SampleClientApp/Models/Room.json) と [Floor.json](https://raw.githubusercontent.com/Azure-Samples/digital-twins-samples/master/AdtSampleApp/SampleClientApp/Models/Floor.json) という 2 つのサンプル モデル定義を使用します。 コードで使用する **モデル ファイルをダウンロード** するには、これらのリンクを使用して GitHub 上のファイルに直接アクセスします。 次に、画面上の任意の場所を右クリックし、ブラウザーの右クリック メニューから **[名前を付けて保存]** を選択して、[名前を付けて保存] のウィンドウでファイルを **Room.json** および **Floor.json** で保存します。

次に、Visual Studio または任意のエディターで、**新しいコンソール アプリ プロジェクト** を作成します。

次に、実行可能なサンプルの **次のコードをプロジェクトにコピー** します。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs":::

### <a name="configure-project"></a>プロジェクトを構成する

次に、以下の手順を実行してプロジェクト コードを構成します。
1. 以前ダウンロードした **Room.json** および **Floor.json** ファイルをプロジェクトに追加し、コード内部の `<path-to>` プレースホルダーを置き換えて、プログラムに検索する場所を指示します。
1. プレースホルダー `<your-instance-hostname>` を Azure Digital Twins インスタンスのホスト名に置き換えます。
1. Azure Digital Twins を操作するために必要な 2 つの依存関係をプロジェクトに追加します。 1 つ目は [.NET 用 Azure Digital Twins SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) 用のパッケージであり、2 つ目では Azure に対する認証に役立つツールが提供されます。

      ```cmd/sh
      dotnet add package Azure.DigitalTwins.Core
      dotnet add package Azure.Identity
      ```

サンプルを直接実行する場合は、ローカルの資格情報も設定する必要があります。 次のセクションでは、このプロセスについて説明します。
[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](../../includes/digital-twins-local-credentials-outer.md)]

### <a name="run-the-sample"></a>サンプルを実行する

セットアップが完了したので、これでサンプル コード プロジェクトを実行できます。

このプログラムのコンソール出力は次のようになります。 

:::image type="content" source="./media/how-to-manage-graph/console-output-twin-graph.png" alt-text="ツインの受信および発信リレーションシップを含めたツインの詳細が表示されているコンソール出力のスクリーンショット。" lightbox="./media/how-to-manage-graph/console-output-twin-graph.png":::

> [!TIP]
> ツイン グラフは、ツイン間にリレーションシップを作成する概念です。 ツイン グラフの視覚的表現を表示する場合は、この記事の "[視覚化](how-to-manage-graph.md#visualization)" に関するセクションを参照してください。 

## <a name="next-steps"></a>次のステップ

Azure Digital Twins ツイン グラフのクエリについて確認します。
* [クエリ言語](concepts-query-language.md)
* [ツイン グラフにクエリを実行する](how-to-query-graph.md)