---
title: Digital Twins オブジェクト モデルと空間インテリジェンス グラフを理解する | Microsoft Docs
description: Azure Digital Twins を使用して、人、場所、デバイスの間の関係をモデル化します
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: alinast
ms.openlocfilehash: 1c2068af510cb3733ce99a6ae7b40487a8c1a015
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323806"
---
# <a name="understanding-digital-twins-object-models-and-spatial-intelligence-graph"></a>Digital Twins オブジェクト モデルと空間インテリジェンス グラフを理解する

Azure Digital Twins は Azure IoT サービスであり、物理環境および関連するデバイス、センサー、ユーザーの包括的な仮想表現を強化します。 ドメイン固有の概念を役に立つモデル内にまとめることで開発しやすくなり、モデル自体が空間インテリジェンス グラフ内の配置されます。 このような概念により、人、スペース、デバイスの間の関係と相互作用が忠実にモデル化されます。

"_Digital Twins オブジェクト モデル_" では、ドメイン固有の概念、カテゴリ、およびプロパティを記述します。 モデルは、特定のニーズに合わせてソリューションを調整しようとしているユーザーによって事前に定義されます。 同時に、これらの定義済みの Digital Twins オブジェクト モデルによって "_オントロジー_" が構成されます。 スマート ビルディングのオントロジーでは、地域、会場、フロア、オフィス、ゾーン、会議室、フォーカス ルームが記述されています。 エネルギー グリッドのオントロジーでは、さまざまな発電所、変電所、エネルギー資源、顧客が記述されています。 これらの Digital Twins オブジェクト モデルとオントロジーにより、多様なシナリオやニーズに合わせて Azure Digital Twins をカスタマイズできます。

"_Digital Twins オブジェクト モデル_" と "_オントロジー_" を配置して、"_空間グラフ_" を設定できます。 空間グラフは、IoT ソリューションに関連するスペース、デバイス、人の間のさまざまな関係の仮想表現です。 次の図は、スマート ビルのオントロジーを使用した空間グラフの例です。

![Digital Twins 空間グラフの構築][1]

<a id="model" />

空間グラフには、スペース、デバイス、センサー、ユーザーがまとめられています。 それぞれは、現実世界をモデル化する方法でリンクされています。会場 43 には 4 つのフロアがあり、それぞれに複数の異なる領域があります。 ユーザーは各自のワークステーションに関連付けられていて、グラフの一部にアクセスできます。  たとえば、管理者は空間グラフを変更する権限を持ちますが、訪問者には特定のビルのデータを見る権限しかありません。

## <a name="digital-twins-object-models"></a>Digital Twins のオブジェクト モデル

Digital Twins オブジェクト モデルでは、オブジェクトの以下の主要カテゴリがサポートされています。

- **スペース**は仮想的または物理的な場所であり、`Tenant`、`Customer`、`Region`、`Venue` などがあります。
- **デバイス**は仮想的または物理的な機器であり、`AwesomeCompany Device`、`Raspberry Pi 3` などがあります。
- **センサー**はイベントを検出するオブジェクトであり、`AwesomeCompany Temperature Sensor`、`AwesomeCompany Presence Sensor` などです。
- **ユーザー**は占有者とその特性を示します。

オブジェクトには他に次のようなカテゴリがあります。

- **リソース**はスペースに関連付けられており、通常は空間グラフ内のオブジェクトによって使用される Azure リソースを表します (例: `IoTHub`)。
- **BLOB** はオブジェクト (スペース、デバイス、センサー、ユーザーなど) に関連付けられており、MIME の種類とメタデータのファイルとして使用されます (例: `maps`、`pictures`、`manuals`)。
- **拡張型**は、特定の特性を持つエンティティを拡張する拡張可能な列挙です (例: `SpaceType`、`SpaceSubtype`)。
- **オントロジー**は、一連の拡張型を表します (例: `Default`、`Building`、`BACnet`、`EnergyGrid`)。
- **プロパティのキーと値**は、スペース、デバイス、センサー、ユーザーのカスタムの特徴です。 組み込みの特性に加えて使用できます (例: キーとして `DeltaProcessingRefreshTime`、値として `10`)。
- **ロール**は、空間グラフ内のユーザーやデバイスに割り当てられたアクセス許可のセットです (例: `Space Administrator`、`User Administrator`、`Device Administrator`)。
- **ロールの割り当て**は、空間グラフ内のロールとオブジェクトの間の関連付けです (例: ユーザーまたはサービス プリンシパルへの、空間グラフ内のスペースを管理するアクセス許可の付与)。
- **セキュリティ キー ストア**は、特定のスペース オブジェクトの下の階層内のすべてのデバイスにセキュリティ キーを提供し、デバイスが Digital Twins サービスと安全に通信できるようにします。
- **ユーザー定義関数**または **UDF** は、空間グラフ内でセンサーのテレメトリ処理をカスタマイズできるようにします。 たとえば、UDF では、センサー値の設定、センサーの測定値に基づくカスタム ロジックの実行とスペースへの出力の設定、スペースへのメタデータのアタッチ、事前定義された条件が満たされたときの通知の送信などを行うことができます。 現在は、JavaScript で UDF を記述できます。
- **マッチャー**は、特定のテレメトリ メッセージに対して実行される UDF を決定するオブジェクトです。
- **エンドポイント**は、テレメトリ メッセージと Digital Twins のイベントをルーティングできる場所です (例: `Event Hub`、`Service Bus`、`Event Grid`)。

<a id="graph" />

## <a name="spatial-intelligence-graph"></a>空間インテリジェンス グラフ

**空間グラフ**は、**Digital Twins オブジェクト モデル**で定義されているスペース、デバイス、人の階層グラフです。 空間グラフでは、"_継承_"、"_フィルター_"、"_走査_"、"_スケーラビリティ_"、"_拡張性_" がサポートされています。 ユーザーは、REST API (下記参照) のコレクションを使用して空間グラフを管理および操作できます。

自分のサブスクリプションに Digital Twins サービスをデプロイするユーザーは、ルート ノードのグローバル管理者になり、構造全体へのフル アクセスを自動的に許可されます。 このユーザーは、`Space` API を使用してグラフ内にスペースをプロビジョニングできます。 デバイスは `Device` API を使用してプロビジョニングでき、センサーは `Sensor` API を使用してプロビジョニングできます。グラフを一括してプロビジョニングするための[オープン ソース ツール](https://github.com/Azure-Samples/digital-twins-samples-csharp)も用意されています。

グラフの "_継承_" はアクセス許可とプロパティに適用され、親ノードからその下にあるすべてのノードに伝えられます。 たとえば、特定のノード上のユーザーにロールが割り当てられると、ユーザーはその特定のノードとその下のすべてのノードに対してそのロールのアクセス許可を持つようになります。 さらに、特定のノードに対して定義されている各プロパティ キーと拡張型は、そのノードの下にあるすべてのノードによって継承されます。

グラフの "_フィルター_" を使用すると、ユーザーは ID、名前、タイプ、サブタイプ、親のスペース、関連付けられているスペース、センサーのデータ型、プロパティのキーと値、走査、minLevel、maxLevel、および他の OData フィルター パラメーターによって、要求の結果を絞り込むことができます。

グラフの "_走査_" を使用すると、空間グラフの深さと幅の全体を移動することができます。 深さの場合は、ナビゲーション パラメーター `traverse`、`minLevel`、`maxLevel` を使用して、トップダウンまたはボトムアップでグラフ内を移動できます。 幅の場合は、グラフ内を移動して、親スペースに直接関連付けられている兄弟ノードまたはその子孫のいずれかを取得できます。 オブジェクトのクエリでは、GET API の `includes` パラメーターを使用して、そのオブジェクトへの関係を持つすべての関連オブジェクトを取得できます。

Azure Digital Twins ではグラフの "_スケーラビリティ_" が保証されているので、現実のワークロードを処理できます。 Digital Twins を使用して、不動産、インフラストラクチャ、デバイス、センサー、テレメトリなどの大規模なポートフォリオを表すことができます。

グラフの "_拡張性_" により、ユーザーは基になっている Digital Twins オブジェクト モデルを、新しい型とオントロジーでカスタマイズできます。 Digital Twins のデータを、拡張可能なプロパティと値で拡充することもできます。

### <a name="spatial-intelligence-graph-management-apis"></a>空間インテリジェンス グラフの Management API

[Azure portal](https://portal.azure.com) から Azure Digital Twins をデプロイすると、Management API の [Swagger](https://swagger.io/tools/swagger-ui/) URL が自動的に生成され、Azure portal の **[概要]** セクションに次の形式で表示されます。

```plaintext
https://yourInstanceName.yourLocation.azuresmartspaces.net/management/swagger
```

| カスタム属性の名前 | 置換後の文字列 |
| --- | --- |
| `yourInstanceName` | Azure Digital Twins インスタンスの名前 |
| `yourLocation` | インスタンスをホストするサーバーのリージョン |

 URL の完全な形式は次の図で使用されているものでわかります。

![Digital Twins ポータル Management API][2]

空間インテリジェンス グラフの使用について詳しくは、Azure Digital Twins Management API のプレビューをご覧ください。

> [!TIP]
> API の機能を見ることができる Swagger のプレビューが提供されています。
> [docs.westcentralus.azuresmartspaces.net/management/swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger) でホストされています。

[Swagger の使用方法](how-to-use-swagger.md)の詳細を学習してください。

すべての API 呼び出しでは、[OAuth](https://docs.microsoft.com/azure/active-directory/develop/v1-protocols-oauth-code) を使用して認証を行う必要があります。 API は、[Microsoft REST API ガイドライン規則](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md)に従います。 コレクションを返すほとんどの API は、[OData](http://www.odata.org/getting-started/basic-tutorial/#queryData) のシステム クエリ オプションをサポートしています。

## <a name="next-steps"></a>次の手順

デバイスの接続と、Azure Digital Twins サービスにテレメトリ メッセージを送信する方法については、「[デバイスの接続性とテレメトリの受信](concepts-device-ingress.md)」をご覧ください。

Management API の制限と調整については、[Azure Digital Twins API の管理と制限](concepts-service-limits.md)に関するページをご覧ください。

<!-- Images -->
[1]: media/concepts/digital-twins-spatial-graph-building.png
[2]: media/concepts/digital-twins-spatial-graph-management-api-url.png
