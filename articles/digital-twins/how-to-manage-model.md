---
title: DTDL モデルの管理
titleSuffix: Azure Digital Twins
description: Azure Digital Twins 内で DTDL モデルの作成、編集、削除などの管理を行う方法について説明します。
author: baanders
ms.author: baanders
ms.date: 10/20/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 7ae5a3293b7b9ba4712c3762b18b6d9c66eab926
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131507172"
---
# <a name="manage-azure-digital-twins-models"></a>Azure Digital Twins のモデルを管理する

この記事では、Azure Digital Twins インスタンスで[モデル](concepts-models.md)を管理する方法について説明します。 管理操作には、モデルのアップロード、検証、取得、および削除が含まれます。 

## <a name="prerequisites"></a>前提条件

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

[!INCLUDE [digital-twins-developer-interfaces.md](../../includes/digital-twins-developer-interfaces.md)]

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/model-graph-panel.png" alt-text="サンプル モデル グラフが表示されている Azure Digital Twins Explorer のスクリーンショット。" lightbox="media/how-to-use-azure-digital-twins-explorer/model-graph-panel.png":::

## <a name="create-models"></a>モデルを作成する

Azure Digital Twins のモデルは DTDL で記述され、 .json ファイルとして保存されます。 また、[Visual Studio Code](https://code.visualstudio.com/) 用の [DTDL 拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl)も用意されており、構文の検証や、DTDL ドキュメントの作成を容易にするその他の機能が提供されます。

病院が部屋をデジタルで表現する例を考えてみましょう。 各部屋には、手の洗浄を監視するためのスマート ソープ ディスペンサーと、部屋の行き来を監視するセンサーが含まれています。

ソリューションに向けた最初の手順は、病院の状況を表すモデルを作成することです。 このシナリオの病室は、次のように記述できます。

:::code language="json" source="~/digital-twins-docs-samples/models/PatientRoom.json":::

> [!NOTE]
> これは、クライアントプロジェクトの一部としてアップロードされる、モデルが定義され保存されている .json ファイルのサンプル本文です。 一方、REST API 呼び出しは、上記のようなモデル定義の配列を取得します (これは .NET SDK の `IEnumerable<string>` にマップされています)。 そのため、REST API でこのモデルを直接使用するには、ブラケットで囲みます。

このモデルでは、病室の名前と固有 ID、および訪問者数と手の洗浄状態を表すプロパティが定義されています。 これらのカウンターは、モーション センサーとスマート ソープ ディスペンサーから更新され、"*手洗い率*" のプロパティを計算するために一緒に使用されます。 このモデルでは、*hasDevices* のリレーションシップも定義されています。これは、この Room モデルに基づいて [デジタル ツイン](concepts-twins-graph.md)を実際のデバイスに接続するために使用されます。

この方法に従うと、病院の病棟、ゾーン、または病院自体のモデルを定義することができます。

### <a name="validate-syntax"></a>構文を検証する

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

## <a name="upload-models"></a>モデルのアップロード

モデルを作成したら、Azure Digital Twins インスタンスにアップロードできます。

モデルをアップロードする準備が整ったら、[.NET SDK](/dotnet/api/overview/azure/digitaltwins/management?view=azure-dotnet&preserve-view=true) に次のコード スニペットを使用します。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="CreateModel":::

1 回のトランザクションで複数のモデルをアップロードすることもできます。 

SDK を使用している場合は、次のように `CreateModels` メソッドを使用して複数のモデル ファイルをアップロードできます。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="CreateModels_multi":::

[REST API](/rest/api/azure-digitaltwins/) または [Azure CLI](/cli/azure/dt?view=azure-cli-latest&preserve-view=true) を使用している場合は、1 つの JSON ファイルに複数のモデル定義をまとめてアップロードすることで、複数のモデルをアップロードすることもできます。 この場合、次の例のように、ファイル内の JSON 配列にモデルを配置する必要があります。

:::code language="json" source="~/digital-twins-docs-samples/models/Planet-Moon.json":::

アップロード時に、サービスによってモデル ファイルが検証されます。

## <a name="retrieve-models"></a>モデルの取得

Azure Digital Twins インスタンスに格納されているモデルを一覧表示したり、取得したりすることができます。 

次のような方法があります。
* 単一のモデルの取得
* すべてのモデルの取得
* モデルのメタデータと依存関係を取得する

呼び出し例を次に示します。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="GetModels":::

モデルを取得するための SDK 呼び出しは、すべて `DigitalTwinsModelData` オブジェクトを返します。 `DigitalTwinsModelData` には、名前、DTMI、モデルの作成日など、Azure Digital Twins インスタンスに格納されているモデルに関するメタデータが含まれます。 `DigitalTwinsModelData` オブジェクトには、必要に応じてモデル自体を含めることもできます。 つまり、パラメーターによっては、取得呼び出しを使用して、メタデータのみ (これは、使用可能なツールの UI の一覧を表示するシナリオなどで役に立ちます)、またはモデル全体を取得できます。

`RetrieveModelWithDependencies` 呼び出しでは、要求されたモデルだけでなく、要求されたモデルが依存しているすべてのモデルも返されます。

モデルは、必ずしもアップロードされたドキュメント形式では返されません。 Azure Digital Twins は、返される形式が意味的に同等であることだけを保証します。 

## <a name="update-models"></a>モデルを更新する

このセクションでは、モデルを更新する際の考慮事項と方法について説明します。

### <a name="before-updating-think-in-the-context-of-your-entire-solution"></a>更新する前に: ソリューション全体のコンテキストで検討する

モデルの更新を行う前に、ソリューション全体を総合的に考察し、行おうとしているモデルの変更による影響を検討することをお勧めします。 Azure Digital Twins ソリューションのモデルは相互接続されることが多いため、1 つのモデルを更新するにはいくつかの他のモデルを更新する必要があるという連鎖的な変更に注意することが重要です。 モデルの更新は、そのモデルを使用するツインに影響し、さらにイングレスおよび処理コード、クライアント アプリケーション、および自動レポートにも影響を与える可能性があります。

モデルの切り替えをスムーズに管理するための推奨事項を次に示します。
* モデルを個別のエントリと考えるのではなく、モデルとその関係を常に最新の状態に保つために、必要に応じてモデル セット全体を進化させることを検討してください。
* モデルをソース コードのように扱い、これらをソース コード管理で管理します。 モデルおよびモデルの変更に対し、ソリューション内の他のコードに適用するのと同じ厳格さと注意深さを適用します。

モデルの更新プロセスを進める準備ができたら、このセクションの残りの部分では、更新の実装に使用できる方法について説明します。

### <a name="strategies-for-updating-models"></a>モデルを更新するための方法

Azure Digital Twins インスタンスにモデルがアップロードされると、モデル インターフェイスは不変になります。つまり、モデルの従来の "編集" ができないことを意味します。 また、Azure Digital Twins では、一致するモデルがインスタンスに既に存在している間は、同じモデルを再アップロードできません。

`displayName` や `description` の更新や、プロパティの追加および削除など、モデルに変更を加える場合は、元のモデルを置き換える必要があります。

モデルを置き換える場合は、次の 2 つの方法から選択できます。
* [方法 1: 新しいモデル バージョンをアップロードする](#strategy-1-upload-new-model-version): 新しいバージョン番号を使用してモデルをアップロードし、新しいモデルを使用するようにツインを更新します。 新しいバージョンと古いバージョンのモデルは、一方を削除するまでいずれもインスタンスに存在します。
    - **この方法を使用する状況**: モデルを使用するツインの一部のみを更新する場合、またはツインがモデルに準拠した状態を維持し、モデルの切り替え中ずっと書き込み可能にしたい場合。
* [方法 2: 古いモデルを削除して再アップロードする](#strategy-2-delete-old-model-and-reupload): 元のモデルを削除し、その代わりに同じ名前と ID (DTMI 値) を使用して新しいモデルをアップロードします。 古いモデルを完全に新しいものに置き換えます。 
    - **この方法を使用する状況**: このモデルを使用するすべてのツインと、モデルの影響を受けるすべてのコードを同時に更新する場合。 ご使用のモデル更新に、モデル更新に関する破壊的変更が含まれている場合、ツインは古いモデルから新しいモデルに切り替わるしばらくの間、モデルに準拠しなくなります。つまり、新しいモデルがアップロードされ、ツインがそれに準拠するまで、更新を受け取ることができなくなります。

>[!NOTE]
> 開発以外では、モデルに破壊的変更を加えることはお勧めできません。

それぞれの方法の詳細については、次のセクションに進んでください。

### <a name="strategy-1-upload-new-model-version"></a>方法 1: 新しいモデル バージョンをアップロードする

このオプションでは、モデルの新しいバージョンを作成し、インスタンスにアップロードします。

この操作によって、以前のバージョンのモデルが上書きされることは **ない** ため、[削除する](#remove-models)までは複数のバージョンのモデルがインスタンス内に共存します。 新しいモデル バージョンと古いモデル バージョンが共存しているため、ツインは新しいバージョンのモデルまたは古いバージョンのいずれかを使用できます。つまり、新しいバージョンのモデルをアップロードしても、既存のツインに自動的には影響しません。 既存のツインは古いモデル バージョンのインスタンスとして残ります。これらのツインを修正することで、新しいモデル バージョンに更新できます。

この方法を使用するには、次の手順に従います。

#### <a name="1-create-and-upload-new-model-version"></a>1. 新しいモデル バージョンを作成してアップロードする 

既存のモデルの新しいバージョンを作成するには、元のモデルの DTDL から開始します。 変更するフィールドを更新、追加、または削除します。

次に、このモデルにモデルの新しいバージョンとしてマークを付けるために、モデルの `id` フィールドを更新します。 モデル ID の最後のセクション (`;` の後) は、モデル番号を表します。 ここで、このモデルがより更新されたバージョンであることを示すために、`id` 値の最後にある数値を現在のバージョン番号よりも大きい数値に増やします。

たとえば、以前のモデル ID が次の場合:

```json
"@id": "dtmi:com:contoso:PatientRoom;1",
```

このモデルのバージョン 2 は次のようになります。

```json
"@id": "dtmi:com:contoso:PatientRoom;2",
```

次に、この新しいバージョンのモデルをインスタンスに[アップロード](#upload-models)します。 

このバージョンのモデルは、インスタンスで使用可能となり、デジタル ツインに使用できるようになります。 これによって、以前のバージョンのモデルが上書きされることは **ない** ため、複数のバージョンのモデルがインスタンス内に共存します。

#### <a name="2-update-graph-elements-as-needed"></a>2. 必要に応じてグラフ要素を更新する

次に、古いモデルではなく新しいモデル バージョンを使用するようにインスタンスの **ツインおよびリレーションシップ** を更新します。

次の手順を使用して、[ツインの更新](how-to-manage-twin.md#update-a-digital-twins-model)と[リレーションシップの更新](how-to-manage-graph.md#update-relationships)を行うことができます。 ツインのモデルを更新するためのパッチ操作は、次のようになります。

:::code language="json" source="~/digital-twins-docs-samples/models/patch-model-1.json":::

>[!IMPORTANT]
>ツインを更新する場合、**同じパッチ** を使用して、モデル ID の (新しいモデル バージョンへの) 更新と、ツインで変更する必要があるすべてのフィールドの更新を行い、ツインを新しいモデルに準拠させる必要があります。

また、このモデルを参照するインスタンス内の **リレーションシップ** およびその他の **モデル** を更新して、それらが新しいモデル バージョンを参照するようにすることが必要な場合もあります。 この目的を達成するには、別のモデル更新操作を行う必要があります。そのため、このセクションの冒頭に戻り、更新が必要なすべてのモデルに対してプロセスを繰り返します。

#### <a name="3-optional-decommission-or-delete-old-model-version"></a>3. (省略可能) 古いモデル バージョンを使用停止にするまたは削除する

古いモデル バージョンを今後使用しない場合、古いモデルを[使用停止](#decommissioning)にすることができます。 この操作により、モデルはインスタンス内に存在し続けますが、新しいデジタル ツインを作成するために使用することはできません。

また、インスタンス内に残す必要がない場合は、古いモデルを完全に[削除](#deletion)することもできます。

上のリンク先のセクションには、モデルの使用停止と削除に関するコード例と考慮事項が含まれています。

### <a name="strategy-2-delete-old-model-and-reupload"></a>方法 2: 古いモデルを削除して再アップロードする

モデルのバージョンを増やす代わりに、モデルを完全に削除し、編集したモデルをインスタンスに再アップロードすることができます。

Azure Digital Twins では、古いモデルが以前アップロードされていたことが記憶されないため、このアクションは、完全に新しいモデルをアップロードする操作に似たものになります。 モデルを使用するグラフ内のツインは、新しい定義が使用可能になると自動的にそちらに切り替わります。 新しい定義が古い定義とどのように異なるかに応じて、これらのツインには、削除される定義と一致するが、新しい定義では有効でないプロパティとリレーションシップが存在する場合があります。そのため、有効な状態を維持するために修正プログラムを適用することが必要な場合もあります。

この方法を使用するには、次の手順に従います。

### <a name="1-delete-old-model"></a>1. 古いモデルを削除する

Azure Digital Twins では、2 つのモデルが同じ ID を持つことが許可されないため、まず元のモデルをインスタンスから削除します。 

>[!NOTE]
> (継承またはコンポーネントを介して) このモデルに依存する他のモデルがある場合は、モデルを削除する前に、それらの参照を削除する必要があります。 これらの依存モデルを最初に更新して参照を一時的に削除したり、依存モデルを削除して後の手順で再アップロードしたりすることができます。

次の手順を使用して、[元のモデルを削除](#deletion)します。 このアクションにより、そのモデルを使用していたツインは、存在しなくなったモデルを現在使用しているため、一時的に "孤立" 状態になります。 この状態は、更新されたモデルを再アップロードする次の手順で修復されます。

### <a name="2-create-and-upload-new-model"></a>2. 新しいモデルを作成してアップロードする

まず、元のモデルの DTDL から開始します。 変更するフィールドを更新、追加、または削除します。

次に、初めてアップロードされる新しいモデルの場合と同様に、インスタンスに[モデルをアップロードします](#upload-models)。

### <a name="3-update-graph-elements-as-needed"></a>3. 必要に応じてグラフ要素を更新する

古いモデルの代わりの新しいモデルがアップロードされたので、インスタンス内のキャッシュの有効期限が切れてリセットされると、グラフ内のツインは新しいモデル定義の使用を自動的に開始します。 グラフのサイズによっては、**このプロセスには 10 から 15 分、あるいはそれ以上かかる場合もあります**。 その後、モデルの新しいプロパティと変更されたプロパティにアクセスできるようになり、削除されたプロパティにはアクセスできなくなります。

>[!NOTE]
> 元のモデルを削除するために他の依存モデルを先に削除している場合は、キャッシュがリセットされた後に再アップロードしてください。 依存モデルを更新して元のモデルへの参照を一時的に削除した場合は、これらを再び更新して、参照を元に戻すことができます。

次に、インスタンス内の **ツインとリレーションシップ** を更新して、そのプロパティが新しいモデルで定義されたプロパティと一致するようにします。 この手順が完了する前も、モデルと一致しないツインは引き続き読み取り可能ですが、書き込みできません。 有効なモデルのないツインの状態の詳細については、「[モデルなしのツイン](#after-deletion-twins-without-models)」を参照してください。

新しいモデルのツインとリレーションシップを更新して、再度書き込み可能にするには、次の 2 つの方法があります。
* 必要に応じてツインとリレーションシップにパッチを適用し、新しいモデルに適合するようにします。 次の手順を使用して、[ツインの更新](how-to-manage-twin.md#update-a-digital-twin)と[リレーションシップの更新](how-to-manage-graph.md#update-relationships)を行うことができます。
    - **プロパティを追加した場合**: ツインとリレーションシップが新しい値を持つように更新することは必須でありません。新しい値がないツインも引き続き有効なツインであるためです。 新しいプロパティの値を追加する任意の方法でパッチを適用することができます。
    - **プロパティを削除した場合**: 新しいモデルで無効になるプロパティを削除するために、ツインにパッチを適用する必要があります。
    - **プロパティを更新した場合**: ツインが新しいモデルに対して有効になるようにするために、変更されたプロパティの値を更新するパッチを適用する必要があります。
* モデルを使用するツインとリレーションシップを削除し、再作成します。 次の手順を使用して、[ツインを削除](how-to-manage-twin.md#delete-a-digital-twin)し、[ツインを再作成](how-to-manage-twin.md#create-a-digital-twin)して、[リレーションシップを削除](how-to-manage-graph.md#delete-relationships)し、[リレーションシップを再作成](how-to-manage-graph.md#create-relationships)します。
    - この操作は、モデルに多くの変更を加え、それに一致するよう既存のツインを更新するのが難しい場合に行うことができます。 ただし、多くのリレーションシップによって相互接続されている多数のツインがある場合は、再作成が複雑になる可能性があります。

## <a name="remove-models"></a>モデルの削除

モデルは、次の 2 つの方法のいずれかでサービスから削除できます。
* **使用停止**:モデルが使用停止になると、新しいデジタル ツインを作成するために使用できなくなります。 このモデルを既に使用している既存のデジタル ツインは影響を受けないため、プロパティの変更やリレーションシップの追加や削除などの操作で更新できます。
* **削除**: この操作により、ソリューションからモデルが完全に削除されます。 このモデルを使用していたすべてのツインは、有効なモデルに関連付けられなくなったため、モデルがまったくないかのように扱われます。 これらのツインは引き続き読み取ることができますが、別のモデルに再割り当てされるまで、更新を行うことはできません。

これらの操作は別の機能であり、互いに影響を与えることはありませんが、一緒に使用してモデルを段階的に削除することもできます。 

### <a name="decommissioning"></a>使用停止

モデルの使用を停止するには、SDK の [DecommissionModel](/dotnet/api/azure.digitaltwins.core.digitaltwinsclient.decommissionmodel?view=azure-dotnet&preserve-view=true) メソッドを使用します。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="DecommissionModel":::

REST API 呼び出し [DigitalTwinModels Update](/rest/api/digital-twins/dataplane/models/digitaltwinmodels_update) を使用して、モデルの使用を停止することもできます。 `decommissioned` プロパティは、この API 呼び出しで置き換えられる唯一のプロパティです。 JSON Patch ドキュメントは次のようになります。

:::code language="json" source="~/digital-twins-docs-samples/models/patch-decommission-model.json":::

モデルの使用停止状態は、モデル取得 API によって返された `ModelData` レコードに含まれます。

### <a name="deletion"></a>削除

インスタンス内のすべてのモデルを一度に削除することも、個別に実行することもできます。

すべてのモデルを同時に削除する方法の例については、GitHub の [Azure Digital Twins のエンドツーエンド サンプル](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/CommandLoop.cs) リポジトリを参照してください。 *CommandLoop.cs* ファイルには、インスタンス内のすべてのモデルを削除するコードを含む `CommandDeleteAllModels` 関数が含まれています。

個々のモデルを削除するには、このセクションの残りの部分の手順と考慮事項に従ってください。

#### <a name="before-deletion-deletion-requirements"></a>削除前: 削除の要件

一般的に、設定はいつでも削除できます。

例外は、`extends` リレーションシップまたはコンポーネントとして、他のモデルが依存しているモデルです。 たとえば、ConferenceRoom モデルが Room モデルを拡張し、コンポーネントとして ACUnit モデルを持っている場合、ConferenceRoom によってそれらの参照が削除されるまで、Room または ACUnit を削除することはできません。 

これを行うには、依存モデルを更新して依存関係を削除するか、依存モデルを完全に削除します。

#### <a name="during-deletion-deletion-process"></a>削除中: 削除プロセス

モデルが直ちに削除するための要件を満たしている場合でも、ツインが残されている場合に意図しない結果が生じないように、まずいくつかの手順を実行することをお勧めします。 プロセスの管理に役立つ手順を次に示します。
1. まず、モデルの使用を停止します
2. 使用停止前の最後の数分間に送信されたツイン作成要求をサービスが処理したことを確認するため、数分待ちます
3. モデル別にツインをクエリして、現在使用停止モデルを使用しているすべてのツインを表示します
4. 不要になった場合はツインを削除するか、必要に応じて新しいモデルにパッチします。 また、なにもせず、モデルを削除した後にモデルなしのツインにすることも選択できます。 この状態の影響については、次のセクションを参照してください。
5. さらに数分待って、変更がすべて適用されたことを確実にします
6. モデルを削除します 

モデルを削除するには、次のように [DeleteModel](/dotnet/api/azure.digitaltwins.core.digitaltwinsclient.deletemodel?view=azure-dotnet&preserve-view=true) SDK 呼び出しを使用します。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="DeleteModel":::

また、[DigitalTwinModels Delete](/rest/api/digital-twins/dataplane/models/digitaltwinmodels_delete) REST API 呼び出しでモデルを削除することもできます。

#### <a name="after-deletion-twins-without-models"></a>削除後: モデルなしのツイン

モデルを削除すると、モデルを使用していたすべてのデジタル ツインがモデルなしと見なされるようになります。 この状態のすべてのツインの一覧を表示できるクエリは存在しません。ただし、削除されたモデルによってツインにクエリを実行して、どのツインが影響を受けているかを把握することは "*できます*"。

ここでは、モデルを持たないツインで実行できることとできないことの概要を示します。

**可能** な操作:
* ツインのクエリ
* プロパティの読み取り
* 外部に対するリレーションシップの読み取り
* 外部からの関係の追加および削除 (つまり、他のツインはこのツイン *に対して* リレーションシップを形成できます)。
  - リレーションシップ定義内の `target` には、削除されたモデルの DTMI が引き続き反映されます。 ここでは、ターゲットが定義されていないリレーションシップを使用することもできます。      
* リレーションシップの削除
* ツインを削除する

**不可能** なこと:
* 外部へのリレーションシップを編集します (つまり、*このツインから他のツインへのリレーションシップ*)
* プロパティの編集

#### <a name="after-deletion-reuploading-a-model"></a>削除後: モデルの再アップロード

モデルを削除した後で、削除したものと同じ ID を持つ新しいモデルを後でアップロードすることを決定できます。 その場合は、次のようになります。
* ソリューション ストアの観点から見ると、この操作はまったく新しいモデルをアップロードすることと同じです。 古いバージョンがアップロードされたことをサービスは覚えていません。   
* 削除されたモデルを参照しているグラフにツインが残っている場合は、孤立していない状態になります。このモデル ID は、新しい定義で再び有効になります。 ただし、モデルの新しい定義が削除されたモデル定義と異なる場合、これらのツインには、削除された定義と一致し、新しい定義では無効なプロパティとリレーションシップが含まれている可能性があります。

Azure Digital Twins ではこの状態を防ぐことができないため、ツインがモデル定義の切り替え後も有効なままになるように、適切なパッチを適用するように注意してください。

## <a name="next-steps"></a>次のステップ

モデルに基づいてデジタル ツインを作成して管理する方法を説明します。
* [デジタル ツインを管理する](how-to-manage-twin.md)