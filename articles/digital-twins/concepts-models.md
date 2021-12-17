---
title: DTDL モデル
titleSuffix: Azure Digital Twins
description: Azure Digital Twins でカスタム モデルを使用して環境内のエンティティを記述する方法と、これらのモデルをデジタル ツイン定義言語 (DTDL) を使用して定義する方法について説明します。
author: baanders
ms.author: baanders
ms.date: 10/7/2021
ms.topic: conceptual
ms.service: digital-twins
ms.custom: fasttrack-edit
ms.openlocfilehash: 79931aeb1e2f6fc37ea2cf4030a6b35dc55e6a26
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2021
ms.locfileid: "130130975"
---
# <a name="learn-about-twin-models-and-how-to-define-them-in-azure-digital-twins"></a>ツイン モデルについて、および Azure Digital Twins で定義する方法について説明します。

Azure Digital Twins の重要な特性は、独自のボキャブラリを定義し、ビジネスの自己定義用語でツイン グラフを構築できることです。 この機能は、ユーザー提供の **モデル** を通じて提供されます。 モデルは、世界を説明するための名詞と考えることができます。 Azure Digital Twins のモデルは、JSON-LD ベースの **Digital Twin Definition language (DTDL)** で表現されます。 

モデルは、オブジェクト指向プログラミング言語の **クラス** に似ており、実際の作業環境における 1 つの特定の概念のデータ シェイプを定義します。 モデルには名前 (*Room* や *TemperatureSensor* など) があり、プロパティ、テレメトリまたはイベント、環境内でこの型のエンティティに何ができるかを説明するコマンドなどの要素が含まれています。 後で、これらのモデルを使用して、この型の説明と一致する特定のエンティティを表す[デジタル ツイン](concepts-twins-graph.md)を作成します。

## <a name="digital-twin-definition-language-dtdl-for-models"></a>モデル用の Digital Twin Definition Language (DTDL)

Azure Digital Twins のモデルは、Digital Twins Definition Language (DTDL) を使用して定義されます。 

DTDL の完全な言語仕様については、GitHub: [Digital Twins Definition Language (DTDL) - Version 2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) を参照してください。 このページには、独自の DTDL モデルを作成し始める際に役立つ詳細な DTDL リファレンスと例が含まれています。

DTDL は JSON-LD に基づいており、プログラミング言語に依存しません。 DTDL は Azure Digital Twins 専用ではなく、[IoT プラグ アンド プレイ](../iot-develop/overview-iot-plug-and-play.md)などの他の IoT サービスのデバイス データを表すためにも使用されます。 Azure Digital Twins は DTDL **バージョン 2** を使用します (Azure Digital Twins での DTDL バージョン 1 の使用は非推奨となりました)。 

この記事の残りの部分では、Azure Digital Twins で言語を使用する方法について説明します。

### <a name="azure-digital-twins-dtdl-implementation-specifics"></a>Azure Digital Twins DTDL の実装の仕様

DTDL を使用するすべてのサービスで、DTDL の機能がまったく同じに実装されるわけではありません。 たとえば、IoT プラグ アンド プレイではグラフ用の DTDL 機能は使用されず、Azure Digital Twins では現在、DTDL コマンドを実装していません。 

DTDL モデルに Azure Digital Twins との互換性を与えるには、これらの要件を満たす必要があります。

* モデルの最上位レベルの DTDL 要素は、すべて "*インターフェイス*" 型である必要があります。 この要件の理由は、Azure Digital Twins モデル API で、インターフェイスまたはインターフェイスの配列のいずれかを表す、JSON オブジェクトを受け取ることができるためです。 その結果、最上位レベルでは他の DTDL 要素型を使用できません。
* Azure Digital Twins の DTDL では、"*コマンド*" を定義しないでください。
* Azure Digital Twins では、単一レベルのコンポーネントの入れ子のみを許可します。これは、コンポーネントとして使用されているインターフェイス自体にはコンポーネントを含められないことを意味します。 
* インターフェイスは、他の DTDL インターフェイスの内部にインラインで定義することはできません。それらは、独自の ID を備えた個別の最上位レベルのエンティティとして定義する必要があります。 その後、別のインターフェイスがそのインターフェイスをコンポーネントとして含めるか、継承を通じて含めようとする場合は、その ID を参照できます。

また、Azure Digital Twins では、プロパティやリレーションシップの `writable` 属性も監視しません。 この属性は DTDL 仕様に従って設定できますが、Azure Digital Twins では使用しません。 代わりに、これらの属性は常に、Azure Digital Twins サービスに対する一般的な書き込みアクセス許可を持つ外部クライアントによって書き込み可能として扱われます。

## <a name="model-overview"></a>モデルの概要

### <a name="elements-of-a-model"></a>モデルの要素

モデル定義内では、最上位レベルのコード項目は **インターフェイス** です。 この種類によって、モデル全体がカプセル化され、モデルの残りの部分はインターフェイス内で定義されます。 

DTDL モデルのインターフェイスには、以下の各フィールドをゼロ個、1 個、または複数個、含めることができます。
* **Property** - プロパティは、エンティティの状態を表すデータ フィールドです (多くのオブジェクト指向プログラミング言語のプロパティと同様)。 プロパティはバッキング ストレージを備えていて、いつでも読み取ることができます。 詳細については、以下の「[プロパティとテレメトリ](#properties-and-telemetry)」を参照してください。
* **Telemetry** - テレメトリ フィールドは測定値またはイベントを表し、多くの場合、デバイスのセンサーの読み取りを説明するために使用されます。 プロパティとは異なり、テレメトリはデジタル ツインに格納されません。これは、発生時に処理される必要がある一連の期限付きデータ イベントです。 詳細については、以下の「[プロパティとテレメトリ](#properties-and-telemetry)」を参照してください。
* **Relationship** - リレーションシップを使用すると、あるデジタル ツインと他のデジタル ツインの関係性を表すことができます。 リレーションシップは、さまざまなセマンティックな意味を表す可能性があります。たとえば、*contains* ("フロアに部屋が含まれる")、*cools* ("hvac によって部屋が冷やされる")、*isBilledTo* ("コンプレッサーがユーザーに請求される") などです。 リレーションシップにより、ソリューションは相互に関連するエンティティのグラフを提供できます。 リレーションシップには、独自のプロパティを含めることもできます。 詳細については、以下の「[リレーションシップ](#relationships)」を参照してください。
* **Component** - コンポーネントを使用すると、必要に応じてモデル インターフェイスを他のインターフェイスの組み合わせとして構築できます。 コンポーネントの例として、"*電話*" のモデルの定義に使用される *frontCamera* インターフェイス (およびもう 1 つのコンポーネント インターフェイスである *backCamera*) があります。 まず、*frontCamera* のインターフェイスを独自のモデルであるかのように定義します。その後、*Phone* を定義するときに、それを参照します。

    コンポーネントを使用するのは、ソリューションの不可欠な要素でありながら、個別の ID を必要とせず、ツイン グラフで独立して作成、削除、および再配置する必要がないものを記述する場合です。 エンティティがツイン グラフ内で独立した存在になるようにするには、それらを異なるモデルの個別のデジタル ツインとして表現し、「**リレーションシップ**」で接続します。
    
    >[!TIP] 
    >コンポーネントは、モデル インターフェイス内の関連するプロパティをグループ化するために、組織に使用することもできます。 この状況では、各コンポーネントをインターフェイス内の名前空間または "フォルダー" と考えることができます。

    詳細については、以下の「[コンポーネント](#components)」を参照してください。


> [!NOTE]
> [DTDL の仕様](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)では、**Commands** も定義されています。これは、デジタル ツインに対して実行できるメソッドです (リセット コマンドや、ファンをオンまたはオフに切り替えるためのコマンドなど)。 ただし、"*Azure Digital Twins では現在、コマンドがサポートされていません*"。

### <a name="model-code"></a>モデル コード

ツインの型モデルは、任意のテキスト エディターで記述できます。 DTDL 言語は、JSON 構文に従います。そのため、モデルは json という拡張子で保存する必要があります。 JSON 拡張子を使用すると、多くのプログラミング テキスト エディターで、DTDL ドキュメントの基本的な構文チェックと強調表示ができるようになります。 また、[Visual Studio Code](https://code.visualstudio.com/) では、[DTDL 拡張子](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl)も使用できます。

モデルのフィールドは次のとおりです。

| フィールド | 説明 |
| --- | --- |
| `@id` | モデルの識別子。 `dtmi:<domain>:<unique-model-identifier>;<model-version-number>` の形式でなければなりません。 |
| `@type` | 記述されている情報の種類を識別します。 インターフェイスの場合、型は *Interface* です。 |
| `@context` | JSON ドキュメントの[コンテキスト](https://niem.github.io/json/reference/json-ld/context/)を設定します。 モデルは `dtmi:dtdl:context;2` を使用する必要があります。 |
| `displayName` | [省略可能] モデルのわかりやすい名前を定義するオプションを提供します。 |
| `contents` | 残りのすべてのインターフェイス データは、属性定義の配列としてここに配置されます。 各属性には、記述するインターフェイス情報の種類を特定するために、`@type` (**プロパティ**、**テレメトリ**、**コマンド**、**リレーションシップ**、または **コンポーネント**) を指定する必要があります。また、実際の属性を定義する一連のプロパティ (たとえば、**プロパティ** を定義するための `name` および `schema`) を指定する必要もあります。 |

#### <a name="example-model"></a>モデル例

このセクションでは、DTDL インターフェイスとして記述された基本的なモデルの例を示します。 

このモデルでは、ID に 1 つの **プロパティ** を持つ Home が記述されています。 また、Home モデルは、Floor モデルとの **リレーションシップ** も定義します。これは、Home ツインが特定の Floor ツインに接続されていることを示すために使用できます。

:::code language="json" source="~/digital-twins-docs-samples-getting-started/models/basic-home-example/IHome.json":::

## <a name="properties-and-telemetry"></a>プロパティとテレメトリ

このセクションでは、DTDL モデルの **プロパティ** と **テレメトリ** について詳しく説明します。

プロパティの一部として表示される可能性があるフィールドの包括的な一覧については、[DTDL v2 仕様の「プロパティ」](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#property)を参照してください。テレメトリの一部として表示される可能性があるフィールドの包括的な一覧については、[DTDL v2 仕様の「テレメトリ」](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#telemetry)を参照してください。

### <a name="difference-between-properties-and-telemetry"></a>プロパティとテレメトリの違い

Azure Digital Twins の DTDL の **プロパティ** と **テレメトリ** を概念的に区別するためのガイダンスを次に示します。
* **プロパティ** には、バッキング ストレージがあることが想定されています。これは、プロパティをいつでも読み取って、その値を取得できることを意味します。 プロパティが書き込み可能であれば、プロパティに値を格納することもできます。  
* **テレメトリ** は、イベントのストリームに似たものであり、存続期間の短い一連のデータ メッセージです。 イベントのリッスンおよびその発生時に実行するアクションを設定していないと、後でイベントをトレースできません。 後でそこに戻って読み取ることはできません。 
  - C# の用語では、テレメトリは C# イベントに似ています。 
  - IoT の用語では、テレメトリは通常、デバイスによって送信される 1 つの測定値です。

**テレメトリ** は、IoT デバイスで多く使用されます。多くのデバイスには、生成される測定値を格納する能力もその必要性もないためです。 代わりに、"テレメトリ" イベントのストリームとして送信されます。 この場合、テレメトリ フィールドの最新の値について、いつでもデバイスに対してクエリを実行できるわけではありません。 デバイスからのメッセージをリッスンし、メッセージが到着したときにアクションを実行する必要があります。 

そのため、Azure Digital Twins でモデルを設計する際は、ほとんどの場合 **プロパティ** を使用して、ツインをモデル化することになると考えられます。 こうすることで、バッキング ストレージが得られ、データ フィールドの読み取りとクエリを行うことができます。

多くの場合、テレメトリとプロパティは連携して、デバイスからのデータのイングレスを処理します。 Azure Digital Twins へのすべてのイングレスは [API](concepts-apis-sdks.md) を通じて行われるため、通常はイングレス関数を使用してデバイスからテレメトリまたはプロパティのイベントを読み取り、それに応じて Azure Digital Twins でプロパティを設定します。 

Azure Digital Twins API からテレメトリ イベントを発行することもできます。 他のテレメトリと同様に、これは存続期間の短いイベントであり、リスナーによる処理が必要です。

### <a name="schema"></a>スキーマ

DTDL によると、**プロパティ** および **テレメトリ** 属性のスキーマは、標準プリミティブ型の `integer`、`double`、`string`、および `boolean` と、`dateTime`、`duration` などのその他の型にすることができます。 

プリミティブ型のほか、プロパティおよびテレメトリ フィールドは、これらの[複合型](#complex-object-type-example)を含むことができます。
* `Object`
* `Map`
* `Enum`
* (**テレメトリ** のみ) `Array`

また、[セマンティック型](#semantic-type-example)にすることもできます。この型を使用すると、値に単位の注釈を付けることができます。

### <a name="basic-property-and-telemetry-examples"></a>基本的なプロパティとテレメトリの例

DTDL モデルの **プロパティ** の基本的な例を次に示します。 この例では、Home の ID プロパティを示しています。

:::code language="json" source="~/digital-twins-docs-samples-getting-started/models/basic-home-example/IHome.json" highlight="7-11":::

DTDL モデルの **テレメトリ** フィールドの基本的な例を次に示します。 この例では、センサーの温度テレメトリを示しています。

:::code language="json" source="~/digital-twins-docs-samples-getting-started/models/basic-home-example/ISensor.json" highlight="7-11":::

### <a name="complex-object-type-example"></a>複合 (オブジェクト) 型の例

プロパティとテレメトリは、`Object` 型などの複合型にすることができます。

次の例は、アドレスのプロパティを持つ Home モデルの別のバージョンを示しています。 `address` はオブジェクトであり、番地、市区町村、都道府県、および郵便番号の独自のフィールドを持っています。

:::code language="json" source="~/digital-twins-docs-samples-getting-started/models/advanced-home-example/IHome.json" highlight="8-31":::

### <a name="semantic-type-example"></a>セマンティック型の例

セマンティック型を使用すると、値を単位付きで表現できます。 プロパティとテレメトリは、DTDL でサポートされているセマンティック型で表すことができます。 DTDL のセマンティック型とサポートされている値の詳細については、「[DTDL v2 仕様のセマンティック型](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#semantic-types)」に関する記事を参照してください。

次の例は、温度のセマンティック型テレメトリと湿度のセマンティック型プロパティを持つセンサー モデルを示しています。

:::code language="json" source="~/digital-twins-docs-samples-getting-started/models/advanced-home-example/ISensor.json" highlight="7-18":::

## <a name="relationships"></a>リレーションシップ

このセクションでは、DTDL モデルの **リレーションシップ** について詳しく説明します。

リレーションシップの一部として表示される可能性のあるフィールドの包括的な一覧については、[DTDL v2 仕様の「リレーションシップ」](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#relationship)を参照してください。

### <a name="basic-relationship-example"></a>基本的なリレーションシップの例

DTDL モデルのリレーションシップの基本的な例を次に示します。 この例では、Floor モデルへの接続を可能にする Home モデルのリレーションシップを示します。

:::code language="json" source="~/digital-twins-docs-samples-getting-started/models/basic-home-example/IHome.json" highlight="12-18":::

>[!NOTE]
>リレーションシップについては、`@id` はオプションのフィールドです。 `@id` が指定されていない場合、デジタル ツイン インターフェイス プロセッサは 1 を割り当てます。

### <a name="targeted-and-non-targeted-relationships"></a>対象および対象外のリレーションシップ

リレーションシップは、**ターゲット** を指定するか、指定せずに定義することができます。 ターゲットは、リレーションシップが到達できるツインの種類を指定します。 たとえば、Home モデルが Floor ツインであるツインとのみ *rel_has_floors* リレーションシップを持つことができるように指定するターゲットを含めることができます。 

場合によっては、特定のターゲットなしでリレーションシップを定義して、リレーションシップがさまざまな種類のツインに接続できるようにする必要がある場合があります。

ターゲットを持たない DTDL モデルのリレーションシップの例を次に示します。 この例では、リレーションシップは Room に含まれる可能性があるセンサーを定義するためのものであり、リレーションシップは任意の種類に接続できます。

:::code language="json" source="~/digital-twins-docs-samples-getting-started/models/advanced-home-example/IRoom.json" range="2-27" highlight="20-25":::

### <a name="properties-of-relationships"></a>リレーションシップのプロパティ

DTDL を使用すると、**リレーションシップ** に独自のプロパティを含めることもできます。 DTDL モデル内でリレーションシップを定義する際に、リレーションシップに独自の `properties` フィールドを含めることができます。このフィールドで、リレーションシップ固有の状態を記述するカスタム プロパティを定義できます。

次の例は、別のバージョンの Home モデルを示しています。`rel_has_floors` リレーションシップには、関連する Floor が最後に占有された時間を表すプロパティがあります。

:::code language="json" source="~/digital-twins-docs-samples-getting-started/models/advanced-home-example/IHome.json" highlight="39-45":::

## <a name="components"></a>Components

このセクションでは、DTDL モデルの **コンポーネント** について詳しく説明します。

コンポーネントの一部として表示される可能性のあるフィールドの包括的な一覧については、[DTDL v2 仕様の「コンポーネント」](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#component)を参照してください。

### <a name="basic-component-example"></a>基本的なコンポーネントの例

DTDL モデルのコンポーネントの基本的な例を次に示します。 この例では、コンポーネントとしてサーモスタット モデルを使用する Room モデルを示します。

:::code language="json" source="~/digital-twins-docs-samples-getting-started/models/advanced-home-example/IRoom.json" highlight="15-19, 28-41":::

このソリューション内の他のモデルにもサーモスタットが含まれている必要がある場合は、Room の場合と同様に、独自の定義でコンポーネントと同じサーモスタット モデルを参照することができます。

> [!IMPORTANT]
> コンポーネントの参照が見つかるようにするには、コンポーネント インターフェイス (上記の例ではサーモスタット) を、それを使用するすべてのインターフェイス (上の例では Room) と同じ配列で定義する必要があります。

## <a name="model-inheritance"></a>モデルの継承

場合によっては、モデルをさらに特殊化したい場合があります。 たとえば、汎用的なモデル Room と、特殊化したバリアント ConferenceRoom および Gym を作成すると便利な場合があります。 特殊化を表現するために、**DTDL では継承がサポートされます**。 インターフェイスは、1 つ以上の他のインターフェイスから継承できます。 これを行うには、モデルに `extends` フィールドを追加します。

`extends` セクションは、インターフェイス名、またはインターフェイス名の配列です (拡張インターフェイスで複数の親モデルを継承できます)。 1 つの親は、複数の拡張インターフェイスの基本モデルとして機能できます。

次の例では、Home モデルを前の DTDL 例からより大きな「コア」モデルのサブタイプとして再イメージ化しています。 親モデル (Core) が先に定義され、次に `extends` を使用してその上に子モデル (Home) が構築されます。

:::code language="json" source="~/digital-twins-docs-samples-getting-started/models/advanced-home-example/ICore.json":::

:::code language="json" source="~/digital-twins-docs-samples-getting-started/models/advanced-home-example/IHome.json" range="1-8" highlight="6":::

この場合、Core は ID と名前を Home に提供します。 他のモデルでは、Core モデルを拡張してこれらのプロパティを取得することもできます。 同じ親インターフェイスを拡張する Room モデルを次に示します。

:::code language="json" source="~/digital-twins-docs-samples-getting-started/models/advanced-home-example/IRoom.json" range="2-9" highlight="6":::

継承が適用されると、拡張インターフェイスは継承チェーン全体のすべてのプロパティを公開します。

拡張インターフェイスでは、親インターフェイスの定義は変更できません。定義の追加のみ行うことができます。 また、いずれかの親インターフェイスで既に定義されている機能を再定義することもできません (機能が同じになるように定義される場合でも)。 たとえば、親インターフェイスで `double` プロパティの *mass* を定義している場合、拡張インターフェイスに *mass* の宣言を含めることはできません (それが同様に `double` である場合でも)。

## <a name="modeling-best-practices"></a>モデリングのベスト プラクティス

環境内のエンティティを反映するようにモデルを設計するときには、先を考えること、および設計に対する[クエリ](concepts-query-language.md)の影響を考慮することが有益です。 グラフのトラバーサルから大きな結果セットが生成されない方法で、プロパティを設計することができます。 また、1 つのクエリで回答される必要があるリレーションシップを、単一レベルのリレーションシップとしてモデル化することもできます。

### <a name="validating-models"></a>モデルの検証

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

## <a name="modeling-tools"></a>モデリング ツール

モデルとオントロジをさらに簡単に処理できるように、いくつかのサンプルが用意されています。 それらは、こちらのリポジトリ ([Digital Twins Definition Language (DTDL) のツール](https://github.com/Azure/opendigitaltwins-tools)) にあります。

このセクションでは、サンプルの現在のセットについて詳しく説明します。

### <a name="model-uploader"></a>モデルのアップローダー 

モデルの作成、拡張、または選択が完了したら、それらをお使いのソリューションで使用できるように、Azure Digital Twins インスタンスにアップロードできます。 そのようにするには、「[DTDL モデルの管理](how-to-manage-model.md#upload-models)」で説明しているように、[Azure Digital Twins API](concepts-apis-sdks.md) を使用します。

ただし、アップロードするモデルが多数ある場合 (またはモデルに多数の相互依存性があり、それにより個々のアップロードの順序付けが複雑になる場合)、[Azure Digital Twins のモデル アップローダーのサンプル](https://github.com/Azure/opendigitaltwins-tools/tree/master/ADTTools#uploadmodels)を使用して、多数のモデルを一度にアップロードできます。 サンプルで示されている手順に従って、このプロジェクトを構成し、独自のインスタンスにモデルをアップロードするために使用します。

### <a name="model-visualizer"></a>モデルのビジュアライザー 

Azure Digital Twins インスタンスにモデルをアップロードした後、[Azure Digital Twins Model Visualizer](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/AdtModelVisualizer) を使用して、Azure Digital Twins インスタンスでモデルを表示できます。これには、継承やモデルのリレーションシップも含まれます。 このサンプルは、現在ドラフト状態です。 弊社は、デジタル ツインの開発コミュニティに対して、このサンプルを拡張して寄与するように奨励しています。 

## <a name="next-steps"></a>次のステップ

* 業界標準のオントロジに基づいてモデルを作成する方法について学習してください。[オントロジとは?](concepts-ontologies.md)

* API 操作によるモデルの管理について詳しく学習してください。[DTDL モデルを管理する](how-to-manage-model.md)

* 「[デジタル ツインとツイン グラフ](concepts-twins-graph.md)」でモデルを使用してデジタル ツインを作成する方法について確認する

