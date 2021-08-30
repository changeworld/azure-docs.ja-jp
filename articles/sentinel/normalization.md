---
title: 正規化と Azure Sentinel 情報モデル (ASIM) | Microsoft Docs
description: この記事では、Azure Sentinel が Azure Sentinel 情報モデル (ASIM) を使用してさまざまなソースからデータを正規化する方法について説明します
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2021
ms.author: bagol
ms.openlocfilehash: c8bf2fd28a1b5adee4d028c3dc11b771d48ef295
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2021
ms.locfileid: "122179330"
---
# <a name="normalization-and-the-azure-sentinel-information-model-asim"></a>正規化と Azure Sentinel 情報モデル (ASIM)

Azure Sentinel は、多くのソースからデータを取り込みます。 さまざまなデータ型とテーブルを組み合わせて使用するには、各データ型とテーブルを理解し、各型またはスキーマの分析ルール、ブック、およびハンティング クエリに対して一意のセットを記述、使用する必要があります。 データ型がファイアウォール デバイスなどの共通要素を共有している場合でも、個別のルール、ブック、クエリが必要な場合があります。 調査とハンティング中は、さまざまな種類のデータを関連付けるのも困難な場合があります。

Azure Sentinel 情報モデル (ASIM)は、次のように、様々なソースを統一された正規のビューで扱うためのシームレスなエクスペリエンスを提供します。

- ソースに依存しないコンテンツとソリューションを許可します。
- Azure Sentinel ワークスペース内データの分析利用を簡素化します。
- パフォーマンスへの影響を最小限に抑えながら、クエリ時の解析を使用します。


この記事では、Azure Sentinel 情報モデルと、正規化されていないデータを情報モデルの正規化されたスキーマに変換する正規化パーサーを作成する方法について説明します。 また、正規化されたスキーマを使用するコンテンツを開発し、既存のコンテンツを変換して正規化されたスキーマを使用することもできます。

> [!NOTE]
> Azure Sentinel 情報モデルは、[Open Source Security Events Metadata (OSSEM)](https://ossemproject.com/intro.html) の共通情報モデルと連携して、正規化されたテーブル間での予測可能なエンティティの相関を実現します。 OSSEM は、多様なデータ ソースとオペレーティング システムからのセキュリティ イベント ログのドキュメントと標準化に主に重点を置いたコミュニティ主導のプロジェクトです。 さらに、このプロジェクトでは、データ エンジニアがデータの正規化手順時に使用できる Common Information Model (CIM) を提供しているため、セキュリティ アナリストが多様なデータソースで、データをクエリし、分析できます。
>
> 詳細については、「[OSSEM のリファレンス ドキュメント](https://ossemproject.com/cdm/guidelines/entity_structure.html)」を参照してください。
>
## <a name="azure-sentinel-information-model-components"></a>Azure Sentinel Information Model コンポーネント

Azure Sentinel Information Model には、次のコンポーネントが含まれています。

|コンポーネント  |説明  |
|---------|---------|
|**正規化されたスキーマ**     |   統合機能を構築する際に使用できる予測可能なイベント種類の標準セットについて説明します。 <br><br>各スキーマは、イベントを表すフィールド、正規化された列の名前付け規則と、フィールド値の標準形式を定義します。 <br><br> 現在、ASIM では次のスキーマが定義されています。<br> - [ネットワーク セッション](normalization-schema.md)<br> - [DNS アクティビティ](dns-normalization-schema.md)<br> - [プロセス イベント](process-events-normalization-schema.md)<br> - [認証イベント](authentication-normalization-schema.md)<br> - [レジストリ イベント](registry-event-normalization-schema.md)<br> - [ファイル アクティビティ](file-event-normalization-schema.md) |
|**パーサー**     |  [KQL 関数](/azure/data-explorer/kusto/query/functions/user-defined-functions)を使用して、既存のデータを正規化されたスキーマにマップします。 <br><br>Microsoft が開発した正規化パーサーを、[Azure Sentinel GitHub パーサー フォルダー](https://github.com/Azure/Azure-Sentinel/tree/master/Parsers) からデプロイします。 正規化されたパーサーは、**ASim** から始まるサブフォルダーに存在します。       |
|**正規化されたスキーマごとのコンテンツ**     |    これには、分析ルール、ブック、ハンティング クエリなどが含まれます。 正規化されたスキーマごとのコンテンツは、ソース固有のコンテンツを作成することなく、正規化されたデータに対して機能します。     |

<br>

次の図は、正規化されていないデータを正規化されたコンテンツに変換し、Azure Sentinel で使用する様子を示しています。 たとえば、カスタムの製品固有の正規化されていないテーブルから始め、パーサーと正規化スキーマを使用して、そのテーブルを正規化されたデータに変換できます。 Microsoft とカスタム分析、ルール、ブック、クエリの両方で正規化されたデータを使用します。

 :::image type="content" source="media/normalization/sentinel-information-model-components.png" alt-text="Azure Sentinel での非正規から正規へのデータ変換フローと使用方法":::

### <a name="azure-sentinel-information-model-terminology"></a>Azure Sentinel Information Model の用語

Azure Sentinel Information Model では、次の用語を使用します。

|用語  |説明  |
|---------|---------|
|**レポート デバイス**     |   レコードを Azure Sentinel に送信するシステム。 このシステムは、送信されるレコードのサブジェクト システムではない可能性があります。      |
|**レコード**     |レポート デバイスから送信されるデータの単位。 レコードは、`log`、`event`、`alert` と呼ばれることが多いですが、他の種類のデータであることもあります。         |
|**コンテンツ**、または **コンテンツ項目**     |Azure Sentinel で使用可能な、異なる、カスタマイズ可能な、またはユーザーが作成した成果物です。 これらの成果物には、分析ルール、ハンティング クエリ、ブックなどが含まれます。 コンテンツ項目は、そのような成果物の 1 つです。|

<br>

## <a name="normalized-schemas"></a>正規化されたスキーマ

### <a name="schema-concepts"></a>スキーマの概念

スキーマは、アクティビティを表すフィールドのセットです。 クエリで正規化されたスキーマのフィールドを使用すると、クエリがすべての正規化されたソースで確実に動作します。

スキーマ参照では、各スキーマを構成するフィールドの概要が示されます。 次の概念は、スキーマ参照ドキュメントを理解し、スキーマがカバーしていない情報がソースに含まれる場合に、正規化された方法でスキーマを拡張するのに役立ちます。


|概念  |説明  |
|---------|---------|
|**フィールド名**     |   各スキーマの中核となるのは、そのフィールド名です。 フィールド名は次のグループに属します。 <br><br>- すべてのスキーマに共通のフィールド <br>- スキーマに固有のフィールド <br>- スキーマに含むエンティティ (ユーザーなど) を表すフィールド。 エンティティ [ を表すフィールドは、スキーマ ](#entities) 間で類似しています。 <br><br>ドキュメント化されたスキーマに表示されないフィールドがソースにある場合、一貫性を維持するために正規化されます。 追加のフィールドがエンティティを表す場合は、エンティティ フィールドのガイドラインに基づいて正規化されます。 それ以外の場合、スキーマは、すべてのスキーマ間で一貫性を保つように努めます。<br><br> たとえば、DNS サーバーのアクティビティ ログはユーザー情報を提供しませんが、エンドポイントからの DNS アクティビティ ログにはユーザー情報が含まれる場合があります。これは、ユーザー エンティティのガイドラインに従って正規化できます。      |
|**フィールドの型**     |  スキーマの各フィールドには型があります。 Log Analytics ワークスペースには、限られたデータ型のセットがあります。 そのため、Azure Sentinel は多くのスキーマ フィールドに論理型を使用します。Log Analytics ではこれを強制していませんが、スキーマの互換性のために必要です。 論理フィールド型を使用すると、値とフィールド名の両方がソース間で一貫性を保ちます。  <br><br>詳細については、「[論理型](#logical-types)」を参照してください。     |
|**Field クラス**     |フィールドには、パーサーによってフィールドを実装しなければならない場合に定義するクラスがいくつか含まれます。 <br><br>-    **必須** フィールドはすべてのパーサーで表示されなければなりません。 ソースでこの値の情報が提供されていない場合、またはデータを追加できない場合、正規化されたスキーマを参照するほとんどのコンテンツ項目はサポートされません。<br>-  **推奨** フィールドがあれば正規化してください。 ただし、すべてのソースで使用できるとはいえ、正規化されたスキーマを参照するコンテンツ項目では可用性を考慮する必要があります。 <br>-  **省略可能な** フィールド (使用可能な場合) は、正規化するか、元の形式で残します。 通常、最小限のパーサーでは、パフォーマンス上の理由から正規化されません。    |
|**エンティティ**     | イベントは、ユーザー、ホスト、プロセス、ファイルなどのエンティティを中心に進化します。各エンティティでは、それを記述するために複数のフィールドが必要になる場合があります。 たとえば、ホストには名前と IP アドレスを指定できます。 <br><br>1 つのレコードに、ソース ホストと宛先ホストの両方など、同じ種類の複数のエンティティが含まれる場合があります。 <br><br>この Azure Sentinel 情報モデルでは、エンティティを一貫して記述する方法を定義し、エンティティを使用してスキーマを拡張できます。 <br><br>たとえば、ネットワーク セッション スキーマにはプロセス情報が含まれますが、一部のイベント ソースは追加できるプロセス情報を提供します。 詳細は、「[エンティティ](#entities)」を参照してください。 |
|**エイリアス**     |  場合によっては、ユーザーによってフィールドの名前が異なると予想される場合があります。 たとえば、DNS の用語では、`query` という名前のフィールドが必要ですが、より一般的にはドメイン名が保持されます。 エイリアスは、指定された値に対して複数の名前を許可することで、あいまいさの問題を解決します。 エイリアス クラスは、エイリアスのフィールドと同じクラスです。       |

<br>

### <a name="logical-types"></a>論理型

スキーマの各フィールドには型があります。 `string`、`int`、`datetime`、`dynamic`などの Azure Log Analytics 型が組み込まれているものもあります。 他のフィールドには論理型があります。これは、フィールド値を正規化する方法を表します。

|データ型  |物理型  |形式と値  |
|---------|---------|---------|
|**Boolean**     |   Bool      |    ブール値を表す数値または文字列形式ではなく、ネイティブの KQL ブールデータ型を使用します。     |
|**列挙**     |  String       |   フィールドに対して明示的に定義された値のリストです。 スキーマ定義には、受け入れ可能な値が一覧表示されます。      |
|**日付/時刻**     |  インジェスト メソッドの機能に応じて、降順の優先順位で次の物理表現を使用します。 <br><br>- Log Analytics 組み込みの datetime 型 <br>- Log Analytics の datetime の数値表現を使用した整数フィールド。 <br>- Log Analytics の datetime の数値表現を使用した文字列フィールド <br>- サポートされている [Log Analytics の日付/時刻形式](/azure/data-explorer/kusto/query/scalar-data-types/datetime) を格納する文字列フィールド。       |  [Log Analytics の日付と時刻の表現](/azure/kusto/query/scalar-data-types/datetime)は、Unix の時刻表現と性質は似ていますが異なります。 詳細については、「[変換ガイドライン](/azure/kusto/query/datetime-timespan-arithmetic)」を参照してください。 <br><br>**注**: 該当する場合は、タイム ゾーンを調整する必要があります。 |
|**MAC アドレス**     |  String       | コロン 16 進表記        |
|**IP アドレス**     |String         |    Azure Sentinel スキーマは、個別の IPv4 アドレスと IPv6 アドレスを持つ必要があります。 どの IP アドレス フィールドにも、IPv4 アドレスか IPv6 アドレスのどちらかを含めることができます。 <br><br>- **IPv4** はドット付き 10 進数で表記します☆  <br>- **IPv6** を8 ヘクステットの短い形式で表記します☆<br><br>次に例を示します。<br>`192.168.10.10` (IPv4)<br>`FEDC:BA98:7654:3210:FEDC:BA98:7654:3210` (IPv6)<br>`1080::8:800:200C:417A` (IPv6 短い形式)     |
|**FQDN**        |   string      |    ドット表記を使用した完全修飾ドメイン名 (例: `docs.microsoft.com`) |
|**Country (国)**     |   String      |    次の優先順位に従って [ISO 3166-1](https://www.iso.org/iso-3166-country-codes.html) を使用した文字列: <br><br> - アルファ 2 コード (例: 北米は `US`) <br> - アルファ 3 コード (例: 北米は `USA`) <br>- 短い名前<br><br>コードの一覧は、[国際標準機関 (ISO) Web サイト](https://www.iso.org/obp/ui/#search)で確認できます|
|**リージョン**     | String        |   ISO 3166-2 を使用した都道府県名<br><br>コードの一覧は、[国際標準機関 (ISO) Web サイト](https://www.iso.org/obp/ui/#search)で確認できます|
|**City (市)**     |  String       |         |
|**Longitude (経度)**     | Double        |  ISO 6709 座標表現 (符号付き 10 進数)       |
|**Latitude (緯度)**     | Double        |    ISO 6709 座標表現 (符号付き 10 進数)     |
|**MD5**     |    String     |  32 ヘックス文字       |
|**SHA1**     |   String      | 40 ヘックス文字        |
|**SHA256**     | String        |  64 ヘックス文字       |
|**SHA512**     |   String      |  128 ヘックス文字       |

<br>

### <a name="common-fields"></a>共通フィールド

次のフィールドは、すべての ASIM スキーマに共通です。 スキーマごとに詳細が異なる状況をサポートするために、一般的なフィールドをここに示し、スキーマごとに一覧表示します。☆ 例えば、**EventType** フィールドの値はスキーマごとに異なり、**EventSchemaVersion** フィールドの値も異なる可能性があります。 

| フィールド               | クラス       | 型       |  説明        |
|---------------------|-------------|------------|--------------------|
| <a name="timegenerated"></a>**TimeGenerated** | 組み込み | DATETIME | イベントがレポート デバイスによって生成された時刻。|
| **_ResourceId**   | 組み込み |  guid     | レポート デバイスまたはサービスの Azure リソース ID。Syslog、CEF、WEF を使用して転送されたイベントの場合はログ フォワーダー リソース ID。 |
| **EventMessage**        | 省略可能    | String     |     レコードに含まれるか、レコードから生成された一般的なメッセージまたは説明です。   |
| **EventCount**          | Mandatory   | 整数型    |     レコードによって記述されるイベントの数。 <br><br>この値は、ソースが集計に対応しており、1 つのレコードが複数のイベントを表す場合があるときに使用されます。 <br><br>その他のソースの場合は、`1` に設定します。   |
| **EventStartTime**      | Mandatory   | 日付/時刻  |      ソースが集計に対応しており、レコードが複数のイベントを表す場合、このフィールドでは最初にイベントが生成された時間を指定します。 <br><br>それ以外の場合、このフィールドは [TimeGenerated](#timegenerated) フィールドの別名になります。 |
| **EventEndTime**        | Mandatory   | エイリアス      |      [TimeGenerated](#timegenerated) フィールドの別名。    |
|  <a name=eventtype></a>**EventType**           | Mandatory   | Enumerated |    レコードによってレポートされる操作を記述します。 各スキーマには、このフィールドに対して有効な値の一覧が文書化されています。 |
| **EventSubType** | 省略可能 | Enumerated | [EventType](#eventtype) フィールドでレポートされた操作を細分化して記述します。 各スキーマには、このフィールドに対して有効な値の一覧が文書化されています。 |
| <a name="eventresult"></a>**EventResult** | Mandatory | Enumerated | 以下の値のいずれかです。**Success**、**Partial**、**Failure**、**NA** (該当なし)。<br> <br>値は、異なる用語を使用してソース レコードに指定できます。これらの値に正規化する必要があります。 また、ソースは [EventResultDetails](#eventresultdetails) フィールドのみを提供する場合があり、これを分析して EventResult 値を導き出す必要があります。<br><br>例: `Success`|
| <a name=eventresultdetails></a>**EventResultDetails** | Mandatory | エイリアス | [**EventResult**](#eventresult) でレポートされた結果の理由または詳細。 各スキーマには、このフィールドに対して有効な値の一覧が文書化されています。<br><br>例: `NXDOMAIN`|
| **EventOriginalUid**    | 省略可能    | String     |   元のレコードの一意の ID (ソースによって提供されている場合)。<br><br>例: `69f37748-ddcd-4331-bf0f-b137f1ea83b`|
| **EventOriginalType**   | 省略可能    | String     |   元のイベントの種類または ID (ソースによって提供されている場合)。 たとえば、このフィールドは、元の Windows イベント ID を格納するために使用されます。<br><br>例: `4624`|
| <a name ="eventproduct"></a>**EventProduct**        | Mandatory   | String     |             イベントを生成している製品。 <br><br>例: `Sysmon`<br><br>**注**: このフィールドはソース レコードでは使用できない場合があります。 その場合、このフィールドはパーサーによって設定される必要があります。           |
| **EventProductVersion** | 省略可能    | String     | イベントを生成している製品のバージョン。 <br><br>例: `12.1`      |
| **EventVendor**         | Mandatory   | String     |           イベントを生成している製品のベンダー。 <br><br>例: `Microsoft`  <br><br>**注**: このフィールドはソース レコードでは使用できない場合があります。 その場合、このフィールドはパーサーによって設定される必要があります。  |
| **EventSchemaVersion**  | Mandatory   | String     |    スキーマのバージョン。 各スキーマは、その現在のバージョンを文書化します。         |
| **EventReportUrl**      | 省略可能    | String     | あるリソースのイベントに指定された、そのイベントに関する追加情報を提供する URL。|
| **Dvc** | Mandatory       | String     |               イベントが発生したデバイスの一意の識別子。 <br><br>このフィールドの別名は、[DvcId](#dvcid)、[DvcHostname](#dvchostname)、または [DvcIpAddr](#dvcipaddr) フィールドになる場合があります。 明確なデバイスがないクラウド リソースの場合は、[Event Product](#eventproduct) フィールドと同じ値を使用します。           |
| <a name ="dvcipaddr"></a>**DvcIpAddr**           | 推奨 | IP アドレス |         イベントが発生したデバイスの IP アドレス。  <br><br>例: `45.21.42.12`    |
| <a name ="dvchostname"></a>**DvcHostname**         | 推奨 | Hostname (ホスト名)   |               イベントが発生したデバイスのホスト名。 <br><br>例: `ContosoDc.Contoso.Azure`               |
| <a name ="dvcid"></a>**DvcId**               | 省略可能    | String     |  イベントが発生したデバイスの一意の ID。 <br><br>例: `41502da5-21b7-48ec-81c9-baeea8d7d669`   |
| **DvcMacAddr**          | 省略可能    | MAC        |   イベントが発生したデバイスの MAC アドレス。  <br><br>例: `00:1B:44:11:3A:B7`       |
| **DvcOs**               | 省略可能    | String     |         イベントが発生したデバイスで実行されているオペレーティング システム。    <br><br>例: `Windows`    |
| **DvcOsVersion**        | 省略可能    | String     |   イベントが発生したデバイスのオペレーティング システムのバージョン。 <br><br>例: `10` |
| **AdditionalFields**    | 省略可能    | 動的    | ソースから保持する必要のある追加情報が提供される場合は、元のフィールド名をそのまま使用するか、動的な **AdditionalFields** フィールドを作成し、それに追加情報をキーと値のペアとして追加します。    |

> [!NOTE]
> Log Analytics では、セキュリティのユース ケースとあまり関連しない他のフィールドも追加します。 詳細については、「[Azure Monitor ログ内の標準列](../azure-monitor/logs/log-standard-columns.md)」を参照してください。
>


### <a name="entities"></a>エンティティ

イベントは、ユーザー、ホスト、プロセス、ファイルなどのエンティティを中心に発展しています。 エンティティ表現を使用すると、同じ型の複数のエンティティを 1 つのレコードに含めることができ、同じエンティティに対して複数の属性をサポートできます。 

エンティティの機能を有効にするために、エンティティ表現には次のガイドラインがあります。

|ガイドライン  |説明  |
|---------|---------|
|**記述子とエイリアス**     | 1 つのイベントには、ソースと宛先のホストなど、同じ種類の複数のエンティティが含まれることが多いため、特定のエンティティに関連付けられているすべてのフィールドを識別するためのプレフィックスとして *記述子* が使用されます。 <br><br>正規化を維持するために、Azure Sentinel Information Model では、少数の標準記述子を使用して、エンティティの特定のロールに最適なものを選択します。  <br><br>ある型の 1 つのエンティティがイベントに関連している場合は、記述子を使用する必要はありません。 また、記述子のない一連のフィールドは、各型で最も使用されているエンティティのエイリアスを設定します。  |
|**識別子と型**     | 正規化されたスキーマでは、各エンティティに対して複数の識別子を使用できます。これは、イベントに共存させることを想定しています。 ソース イベントに、正規化されたスキーマにマップできない他のエンティティ識別子がある場合は、ソース フォームに保持するか、`AdditionalFields` の動的フィールドを使用します。 <br><br>識別子の型情報を保持するには、必要に応じて、同じ名前と `Type` のサフィックスを持つフィールドに型を格納します。 たとえば、「 `UserIdType` 」のように入力します。         |
|**属性**     |   多くの場合、エンティティには識別子として機能しない他の属性があり、記述子で修飾することもできます。 たとえば、ソース ユーザーがドメイン情報を持っている場合、正規化されたフィールドは `SrcUserDomain` になります。      |

<br>

各スキーマは、中央エンティティとエンティティ フィールドを明示的に定義します。 次のガイドラインを考慮してください。

- 中央スキーマ フィールドについて
- スキーマで明示的に定義されていない他のエンティティまたはエンティティ フィールドを使用して、正規化された方法でスキーマを拡張する方法について説明します

#### <a name="the-user-entity"></a>ユーザー エンティティ

ユーザーに使用される記述子は、以下のシナリオで説明するように、**Actor**、**Target User**、**Updated User** です。

|アクティビティ  |完全なシナリオ  |エイリアスに使用される単一のエンティティ シナリオ  |
|---------|---------|---------|
|**ユーザーの作成**     |  **Actor** が作成されたか、**Target User** が変更されました       |  対象となる **User** が作成されました。       |
|**ユーザーの変更**     |   **Actor** が **Target User** の名前を **Updated User** に変更しました。 **Updated User** は通常、ユーザーに関連するすべての情報を保持しておらず、**Target User** と重なる部分があります。      |         |
|**ネットワーク接続**     |    送信元ホストで **Actor** として動作しているプロセスが、送信先ホストで **Target User** として動作しているプロセスと通信している場合     |         |
|**DNS 要求**     | **Actor** がDNS クエリを開始しました        |         |
|**サインイン**     |    **Actor** が **Target User** としてシステムにサインインした場合。     |サインインしている (対象となる) ユーザー         |
|**プロセス作成**     |   **Actor** (開始プロセスに関連するユーザー) がプロセス作成を開始しました。 作成されたプロセスは、**Target User** (対象プロセスに関連するユーザー) の認証情報の下で実行されます。      |  作成されたプロセスは、(対象となる) **User** の資格情報で実行されます。       |
|**電子メール**     |     **Actor** が **Target User** にメールを送信します    |         |

<br>

次の表では、ユーザーに対してサポートされている識別子について説明します。

|正規化されたフィールド  |型  |形式とサポートされる型  |
|---------|---------|---------|
|**UserId**     |    String     |   コンピューターが判読できる、英数字で、システム内のユーザーを一意に表現したもの。 <br><br>形式とサポートされる型は次のとおりです。<br>    - **SID** (Windows): `S-1-5-21-1377283216-344919071-3415362939-500`<br>    -  **UID** (Linux): `4578`<br>    -    **AADID** (Azure Active Directory): `9267d02c-5f76-40a9-a9eb-b686f3ca47aa`<br>    - **OktaId**: `00urjk4znu3BcncfY0h7`<br>    - **AWSId**: `72643944673`<br><br>    ID 型を `UserIdType` フィールドに格納します。 他の ID がある場合は、フィールド名をそれぞれ`UserSid`、`UserUid`、`UserAADID`、`UserOktaId`、`UserAwsId`に正規化することを推奨します。       |
|**ユーザー名**     |  String       |   ユーザー名 (可能な場合はドメイン情報を含む) を、以下のいずれかの形式で、以下の優先順位で記載してください。 <br> -   **Upn/Email**: `johndow@contoso.com` <br>  -    **Windows**: `Contoso\johndow` <br> -   **DN**: `CN=Jeff Smith,OU=Sales,DC=Fabrikam,DC=COM` <br>  - **Simple**: `johndow`. この形式は、ドメイン情報が使用できない場合にのみ使用してください。 <br><br> Username 型を `UsernameType` フィールドに格納します。    |

<br>

#### <a name="the-process-entity"></a>プロセス エンティティ

ユーザーに使用される記述子は、以下のシナリオで説明するように、`Acting Process`、`Target Process`、`Parent Process` です。

- **ネットワーク接続**。 **Acting Process** が、リモート システム上の **Target Process** と通信するためにネットワーク接続を開始しました。
- **DNS 要求**。  **Acting Process** が DNS クエリを開始しました。
- **サインイン**。  **Acting Process** は、自分の代わりに **Target Process** を実行するリモート システムへの署名を開始しました。
- **プロセス作成**。 **Acting Process** が **Target Process** の作成を開始したことを表します。 **Parent Process** は、Acting Process の親です。

次の表では、プロセスに対してサポートされている識別子について説明します。

|正規化されたフィールド  |型  |形式とサポートされる型  |
|---------|---------|---------|
|**Id**     |    String     |   OS によって割り当てられたプロセス ID。      |
|**Guid**     |  String       |   OS によって割り当てられたプロセス GUID。 GUID は一般的にシステムの再起動間で一意ですが、ID は再利用される場合が多いです。   |
|**Path**     |    String     |   ディレクトリとファイル名を含む、プロセスの完全なパス名。       |
|**名前**     |  エイリアス       |  プロセス名はパスのエイリアスです。   |

<br>

詳細は、「[Azure Sentinel プロセス イベント正規化スキーマ リファレンス (パブリック プレビュー)](process-events-normalization-schema.md)」を参照してください。

#### <a name="the-device-entity"></a>デバイス エンティティ

正規化スキーマは、可能な限りユーザーの直感に従うので、シナリオに応じてさまざまな方法でデバイスを処理します。

- イベント コンテキストがソース デバイスとターゲット デバイスを意味する場合、`Src` と `Target` の記述子が使用されます。 このような場合、`Dvc` 記述子はレポート デバイスに使用されます。

- ローカル OS イベントなどの単一のデバイス イベントの場合、`Dvc` 記述子が使用されます。

- イベントで別のゲートウェイ デバイスが参照され、値がレポート デバイスと異なる場合は、`Gateway` 記述子が使用されます。

デバイスの処理ガイドラインでは、次のようにさらに明確にされています。

- **ネットワーク接続**。 **ソース デバイス** (`Src`) から **ターゲット デバイス** (`Target`) への接続が確立されました。 この接続は、(レポート) **デバイス** (`Dvc`) によってレポートされました。
- **プロキシ処理されたネットワーク接続**。 **ソース デバイス** (`Src`) から **ターゲット デバイス** (`Target`) への接続が、**ゲートウェイ デバイス** (`Gateway`) を介して確立されました。 接続は、(レポート) **デバイス** によってレポートされました。
- **DNS 要求**。  **ソース デバイス** (`Src`) から DNS クエリが開始されました。
- **サインイン**   **ソース デバイス** (`Src`) から **ターゲット デバイス** (`Target`) 上のリモート システムへのサインインが開始されました。
- **プロセス**   **デバイス** (`Dvc`) でプロセスが開始されました。

次の表では、デバイスに対してサポートされている識別子について説明します。

|正規化されたフィールド  |型  |形式とサポートされる型  |
|---------|---------|---------|
|**hostname**     |    String     |        |
|**FQDN**     |  String       |   完全修飾ドメイン名   |
|**IpAddr**     |    IP アドレス     |   デバイスには複数の IP アドレスが含まれますが、イベントには通常、1 つの識別 IP アドレスがあります。 例外は、関連する 2 つの IP アドレスを持つゲートウェイ デバイスです。 ゲートウェイ デバイスの場合は、`UpstreamIpAddr` と `DownstreamIpAddr` を使用します。      |
|**HostId**     |  String       |     |

<br>

> [!NOTE]
> `Domain` はデバイスの一般的な属性ですが、完全な識別子ではありません。
>

詳細は、「[Azure Sentinel Authentication 正規化スキーマ リファレンス (パブリック プレビュー)](authentication-normalization-schema.md)」を参照してください。

#### <a name="entity-mapping-example"></a>エンティティ マッピングの例

このセクションでは [Windows イベント 4624](/windows/security/threat-protection/auditing/event-4624) を例として、Azure Sentinel でイベント データがどのように正規化されるかを説明します。

このイベントには、次のエンティティがあります。

|Microsoft の用語  |Original イベント フィールド プレフィックス |ASIM フィールド プレフィックス  |説明  |
|---------|---------|---------|---------|
|**件名**     | `Subject`        |   `Actor`      |  サインインの成功に関する情報をレポートしたユーザー。      |
|**新しいログオン**     |    `Target`     |  `TargetUser`       |  サインインが実行されたユーザー。       |
|**Process**     |    -     |     `ActingProcess`    |   サインインを試行したプロセス。      |
|**ネットワーク情報**     |   -      |   `Src`      |     サインイン試行が実行されたマシン。    |

<br>

これらのエンティティに基づいて [Windows イベント 4624](/windows/security/threat-protection/auditing/event-4624) は次のように正規化されます (一部のフィールドは省略可能です)。

|正規化されたフィールド  |元のフィールド  |例の値  |メモ  |
|---------|---------|---------|---------|
|**ActorUserId**     |  SubjectUserSid       |  S-1-5-18        |        |
|**ActorUserIdType**     |  -       | SID        |         |
|**ActorUserName**     |   SubjectDomainName\ SubjectUserName      |  WORKGROUP\WIN-GG82ULGC9GO$       |  2 つのフィールドを連結して構築       |
|**ActorUserNameType**     |   -      |   Windows      |         |
|**ActorSessionId**     | SubjectLogonId        |  0x3e7       |         |
|**TargetUserId**     |   TargetUserSid      |    S-1-5-21-1377283216-344919071-3415362939-500     |         |
|**UserId**     |    TargetUserSid     |   alias      |         |
|**TargetUserIdType**     |   -      |    SID     |         |
|**TargetUserName**     | TargetDomainName\ TargerUserName        |   Administrator\WIN-GG82ULGC9GO$      |   2 つのフィールドを連結して構築      |
|**ユーザー名**     |  TargetDomainName\ TargerUserName       |  alias       |         |
|**TargetUserNameType**     | -        |  Windows       |         |
|**TargetSessionId**     |   TargetLogonId      |  0x8dcdc       |         |
|**ActingProcessName**     |  ProcessName       |  C:\\Windows\\System32\\svchost.exe       |         |
|**ActingProcessId**     |    ProcessId     |     0x44c    |         |
|**SrcHostname**     |    WorkstationName     | Windows        |         |
|**SrcIpAddr**     |  IpAddress       |     127.0.0.1    |         |
|**SrcPortNumber**     |  IpPort       |  0       |         |
|**TargetHostname**     |   Computer      |  WIN-GG82ULGC9GO           |         |
|**hostname**     |     Computer    |     エイリアス    |         |

<br>

## <a name="parsers"></a>パーサー

Azure Sentinel では、解析はクエリ時に行われます。 パーサーは [KQL ユーザー定義関数](/azure/data-explorer/kusto/query/functions/user-defined-functions)として構築され、**CommonSecurityLog**、カスタム ログテーブル、Syslog などの既存のテーブルのデータを正規化されたスキーマに変換します。 パーサーをワークスペース関数として保存すると、他の Azure Sentinel テーブルと同じように使用できます。

パーサーには 2 つのレベルがあります。上の [Azure Sentinel Information Model コンポーネント](#azure-sentinel-information-model-components)の図に示されているように、**ソースに依存しない** パーサーと **ソース固有の** パーサーです。

- **ソースに依存しないパーサー** は、同じスキーマに正規化されたすべてのソースを結合し、正規化されたフィールドを使用してすべてのソースを照会するために使用できます。 ソースに依存しないパーサーの名前は `im<schema>` で、`<schema>` は提供する特定のスキーマを表しています。

    たとえば、次のクエリはソースに依存しない DNS パーサーを使用して、`ResponseCodeName`、`SrcIpAddr`、`TimeGenerated` の正規化フィールドを使用して DNS イベントを照会します。

    ```kusto
    imDns
      | where isnotempty(ResponseCodeName)
      | where ResponseCodeName =~ "NXDOMAIN"
      | summarize count() by SrcIpAddr, bin(TimeGenerated,15m)
    ```

    ソースに依存しないパーサーは、`union` KQL 演算子を使用して、複数のソース固有の正規化されたパーサーを組み合わせることができます。 ソース固有の正規化されたパーサーの名前は `vim<schema><vendor><product>` です。 したがって、`imDns` パーサーは次のようになります。

    ```kusto
    union isfuzzy=true
    vimDnsEmpty,
    vimDnsCiscoUmbrella,
    vimDnsInfobloxNIOS,
    vimDnsMicrosoftOMS
    ```

- **ソース固有** の正規化パーサーをソースに依存しないパーサーに追加すると、ソースに依存しないパーサーを使用する組み込みクエリにカスタム ソースを含めることができます。

    ソース固有のパーサーを使用すると、カスタム データの分析、ブック、洞察などの組み込みコンテンツからすぐに値を取得できます。

    ソース固有のパーサーを個別に使用することもできます。 たとえば、Infoblox 固有のブックでは、`vimDnsInfobloxNIOS` パーサーを使用します。
### <a name="writing-parsers"></a>パーサーの記述

パーサーは、ワークスペース関数として保存された KQL クエリです。 保存すると、組み込みテーブルのように使用できます。 パーサー クエリには、以下のパーツが含まれています。

**Filter** > **Parse** > **Prepare fields**

#### <a name="filtering"></a>フィルター処理

多くの場合、テーブルには複数の種類のイベントが含まれます。 次に例を示します。
* Syslog テーブルには、複数のソースからのデータが含まれています。
* カスタム テーブルには、複数のイベントの種類を提供し、さまざまなスキーマに適合する 1 つのソースの情報を含めることができます。

したがって、パーサーはまず、ターゲット スキーマに関連するレコードのみをフィルター処理する必要があります。

KQL でのフィルタリングは、`where` 演算子を使って行われます。 例えば、**Sysmon event 1** はプロセス作成をレポートしており、**ProcessEvent** スキーマに正規化されなければなりません。 **Sysmon event 1** イベントは `Event` テーブルの一部であり、以下のフィルターを使用する必要があります。

```kusto
Event | where Source == "Microsoft-Windows-Sysmon" and EventID == 1
```

パーサーのパフォーマンスを確保するために、次のフィルター選択に関する推奨事項を確認してください。

-   解析されたフィールドではなく、常に組み込みのフィルター処理を行います。 解析されたフィールドを使用してフィルター処理する方が簡単な場合もありますが、パフォーマンスに大きな影響を与えます。
-   最適なパフォーマンスを提供する演算子を使用します。 特に、`==`、`has` と、`startswith` を参照してください。 また、`contains` や `matches regex` などの演算子を使うと、パフォーマンスが劇的に向上します。

パフォーマンスのフィルター選択に関する推奨事項は、常に従うのが困難な場合もあります。 例えば、`has` を使うと `contains` よりも精度は低下します。 他のケースでは、`SyslogMessage` のような組み込みフィールドの一致は、`DvcAction` のような抽出されたフィールドの比較よりも精度が低くなります。 このような場合は、組み込みフィールドに対してパフォーマンス最適化演算子を使用して事前にフィルター処理し、解析後により正確な条件を使用してフィルターを繰り返すことを推奨します。

例として、次の [Infoblox DNS](https://github.com/Azure/Azure-Sentinel/tree/master/Parsers/ASimDns/ARM/Infoblox) パーサーのスニペットを参照してください。 パーサーは、まず SyslogMessage フィールド `has` に単語 `client` があるかどうかを確認します。 ただし、この用語は、メッセージ内の別の場所で使用される場合があります。 そのため、`Log_Type` フィールドを解析した後、パーサーは `client` という単語がフィールドの値に確実に存在することを再度確認します。

```kusto
Syslog | where ProcessName == "named" and SyslogMessage has "client"
…
      | extend Log_Type = tostring(Parser[1]),
      | where Log_Type == "client"
```

> [!NOTE]
> パーサーでは、パーサーを使用して時間をフィルター処理するので、時間でフィルター処理することはできません。
> 

#### <a name="parsing"></a>解析

クエリで関連するレコードを選択すると、そのレコードの解析が必要になる場合があります。 通常、イベント情報の多くを 1 つのテキスト フィールドに伝える場合は、解析が必要です。

解析を実行する KQL 演算子を、以下にパフォーマンスが最適化されている順に示します。 最初の方法では最適なパフォーマンスが得られますが、最後のパフォーマンスは最も最適化されていません。

|演算子  |説明  |
|---------|---------|
|[split](/azure/data-explorer/kusto/query/splitfunction)     |    区切り記号で区切られた値の文字列を解析します     |
|[parse_csv](/azure/data-explorer/kusto/query/parsecsvfunction)     |     CSV (コンマ区切り値) 行として書式設定された値の文字列を解析します。    |
|[parse](/azure/data-explorer/kusto/query/parseoperator)     |    パターンを使用して、任意の文字列から複数の値を解析します。これは、パフォーマンス向上のための単純なパターンでも、正規表現でもかまいません。     |
|[extract_all](/azure/data-explorer/kusto/query/extractallfunction)     | 正規表現を使用して、任意の文字列から単一の値を解析します。 `parse` が正規表現を使用している場合、`extract_all` と同様の性能を発揮します。        |
|[extract](/azure/data-explorer/kusto/query/extractfunction)     |    正規表現を使用して、任意の文字列から単一の値を抽出します。 <br><br>1 つの値が必要な場合、`extract` を使用すると、`parse` や `extract_all` よりもパフォーマンスが向上します。 しかし、同じソース文字列に対して `extract` の複数のアクティブ化を使用することは、1 つの `parse` または `extract_all` に比べて著しく効率が悪いため、避けるべきです。      |
|[parse_json](/azure/data-explorer/kusto/query/parsejsonfunction)  | JSON 形式で設定された文字列の値を解析します。 JSON の一部の値だけが必要な場合は、`parse`、`extract`、`extract_all` を使用するとパフォーマンスが向上します。        |
|[parse_xml](/azure/data-explorer/kusto/query/parse-xmlfunction)     |    XML 形式で設定された文字列の値を解析します。 XML の一部の値だけが必要な場合は、`parse`、`extract`、`extract_all` を使用するとパフォーマンスが向上します。     |

<br>

解析フェーズでは文字列の解析に加えて、以下のような元の値の処理が必要になる場合があります。

- **書式設定と型変換**。 抽出されたソース フィールドは、ターゲット スキーマ フィールドに合うように書式設定しなければならない場合があります。 たとえば、日付と時刻を表す文字列を datetime フィールドに変換することが必要な場合もあります。     このような場合には、`todatetime` や `tohex` などの機能が有効です。

- **値の検索**。 抽出されたソース フィールドの値は、ターゲット スキーマ フィールドに指定された値のセットにマッピングされる必要があります。 たとえば、いくつかのソースは DNS 応答コードを数値でレポートしますが、スキーマでは、より一般的なテキストの応答コードが要求されます。 少数の値をマッピングするには、関数 `iff` と `case` が便利です。

    たとえば、Microsoft DNS パーサーは、次のように、`iff` ステートメントを使用して、イベント ID と応答コードに基づいて `EventResult` フィールドを割り当てます。

    ```kusto
    extend EventResult = iff(EventId==257 and ResponseCode==0 ,'Success','Failure')
    ```

    複数の値の場合は、同じ DNS パーサーで示されているように、`datatable` と `lookup` を使用します。

    ```kusto
    let RCodeTable = datatable(ResponseCode:int,ResponseCodeName:string) [ 0, 'NOERROR', 1, 'FORMERR'....];
    ...
     | lookup RCodeTable on ResponseCode
     | extend EventResultDetails = case (
         isnotempty(ResponseCodeName), ResponseCodeName, 
         ResponseCode between (3841 .. 4095), 'Reserved for Private Use', 
         'Unassigned')
    ```

> [!NOTE]
> この変換では、`lookup` のみを使用することはできません。複数の値が `Reserved for Private Use`、`Unassigned` にマッピングされるため、クエリでは検索とケースの両方が使用されます。 それでも、クエリですべての値に `case` を使用するよりもはるかに効率的です。
>

#### <a name="prepare-fields-in-the-result-set"></a>結果セットのフィールドを準備する

パーサーは、正規化されたフィールドが使用されるのを確認するために、結果セット内のフィールドを準備する必要があります。 ガイドラインとして、正規化されていない元のフィールドは、混乱を招く場合など、説得力のある理由がない限り、結果セットから削除することはできません。

フィールドを準備するには、次の KQL 演算子を使用します。

|演算子  | 説明  | パーサーで使用する場合  |
|---------|---------|---------|
|**extend**     | 計算フィールドを作成し、レコードに追加します        |  `Extend` は、正規化されたフィールドが元のデータから解析または変換される場合に使用されます。 詳細については、上記「[解析](#parsing)」のセクションを参照してください。     |
|**project-rename**     | フィールドの名前を変更します        |     フィールドが元のイベントに存在し、名前変更だけが必要な場合は、`project-rename` を使用します。 <br><br>名前が変更されたフィールドは組み込みフィールドと同様に動作し、フィールドに対する操作のパフォーマンスがはるかに向上します。   |
|**project-away**     |      フィールドを削除します。   |結果セットから削除したい特定のフィールドには `project-away` を使用します。         |
|**project**     |  以前に存在したフィールド、またはステートメントの一部として作成されたフィールドを選択します。 その他のフィールドはすべて削除します。       | パーサーでは、正規化されていない他のフィールドを削除してはいけないため、パーサーでの使用は推奨されません。 <br><br>解析時に使用される一時的な値など、特定のフィールドを削除する必要がある場合は、`project-away` を使用して結果から削除します。      |

<br>

### <a name="handle-parsing-variants"></a>解析バリアントを処理する

多くの場合、イベント ストリーム内のイベントには、さまざまな解析ロジックを必要とするバリアントが含まれます。 

多くの場合、異なる解析ロジックを必要とするイベントの別のバリアントを処理する、異なるサブパーサーからパーサーを構築する必要があります。 これらのサブパーサーは、それ自体がクエリとなり、`union` 演算子で統合されます。 この方法は便利ですが、パーサーのパフォーマンスに大きな影響を与えるため、推奨 *しません*。

バリアントを処理する場合は、次のガイドラインに従います。

|シナリオ  |処理  |
|---------|---------|
|異なるバリアントは *異なる* イベント タイプを表し、一般的に異なるスキーマにマッピングされます     |  個別のパーサーを使用する       |
|異なるバリアントは、*同じ* イベント タイプを表していますが、構造は異なります。     |   解析前にイベントを区別するメソッドがある場合など、バリアントが既知の場合は、[Infoblox DNS パーサー](https://github.com/Azure/Azure-Sentinel/tree/master/Parsers/ASimDns/ARM/Infoblox)で示されているように、`case` 演算子を使用して、実行する正しい `extract_all` を選択し、フィールド マッピングを行います。      |
|`union` がやむを得ない場合     |  `union` を使用する場合は、必ず次のガイドラインを使用してください。<br><br>- 各サブクエリの組み込みフィールドを使用して事前にフィルター処理します。 <br>- フィルターが相互に排他的である必要があります。 <br>- あまり重要ではない情報を解析し、サブクエリの数を減らすことを検討してください。       |

<br>

### <a name="deploy-parsers"></a>パーサーをデプロイする

パーサーを手動でデプロイするには、Azure Monitor の **[ログ]** ページにコピーして、変更を保存します。 このメソッドはテストする際に役立ちます。 詳細については、「[関数を作成する](../azure-monitor/logs/functions.md)」を参照してください。

ただし、多数のパーサーをデプロイするには、ARM テンプレートを使用することをお勧めします。 たとえば、ソースに依存しないパーサーといくつかのソース固有のパーサーを含む完全な正規化ソリューションをデプロイする場合や、ソースのスキーマごとに複数のパーサーをデプロイする場合は、ARM テンプレートを使用できます。

詳細は、「[ジェネリック パーサーの ARM テンプレート](https://github.com/Azure/Azure-Sentinel/tree/master/Tools/ARM-Templates/ParserQuery)」を参照してください。 このテンプレートを開始点として使用し、テンプレートのデプロイ プロセス中に関連するポイントにパーサーを貼り付け、パーサーをデプロイします。 たとえば、[DNS パーサーの ARM テンプレート](https://github.com/Azure/Azure-Sentinel/tree/master/Parsers/ASimDns/ARM)を参照してください。

> [!TIP]
> ARM テンプレートにはさまざまなリソースを含め、パーサーをコネクタ、分析ルール、またはウォッチリストと共にデプロイして、いくつかの便利なオプションを指定できます。 たとえば、パーサーは、そのパーサーと共にデプロイされるウォッチリストを参照できます。
>

### <a name="use-parsers-in-normalized-content"></a>正規化されたコンテンツでパーサーを使用する

Microsoft Azure Sentinelには、ソースに依存しない正規化パーサーを使用する分析ルール、ハンティング クエリ、ブックが含まれます。

- 正規化された組み込みのコンテンツを、Azure Sentinel ギャラリーと[ソリューション](sentinel-solutions-catalog.md)で検索します。

- 正規化されたコンテンツを自分で作成するか、正規化されたデータを使用するために既存のコンテンツを変更します。

####  <a name="identify-built-in-normalized-content"></a>組み込みの正規化されたコンテンツを識別する

各スキーマのドキュメントには、各正規化されたスキーマで動作するコンテンツ項目の一覧が含まれています。 スキーマ コンテンツは定期的に更新され、次のガイドラインを使用します。

-   **正規化されたスキーマに焦点を当てたコンテンツ項目** には、名前の一部としてスキーマが含まれます。 例えば、[正規化された DNS スキーマ](dns-normalization-schema.md)に着目した解析ルールの名前には、`(Normalized DNS)` という接尾辞がついています。

-   **他のデータ型の間で正規化されたスキーマを考慮するコンテンツ項目** は、サフィックスによってマークされません。 このような場合は、GitHub で正規化されたスキーマ パーサーの名前を検索して、すべてを特定してください。

#### <a name="modifying-your-content-to-use-normalized-data"></a>正規化されたデータを使用するためにコンテンツを変更する

カスタム コンテンツで正規化を使用するには:

- クエリに関連するソースに依存しないパーサーを使用するには、クエリを変更します。
- 正規化されたスキーマ フィールド名を使用するために、クエリのフィールド名を変更します。
- 該当する場合は、クエリのフィールドの正規化された値を使用する条件を変更します。

たとえば、Infoblox DNS サーバーから送信された DNS イベントで動作する **逆引き検索回数が多いと観測されたレア クライアント** DNS 分析ルールを考えてみましょう。

```kusto
let threshold = 200;
InfobloxNIOS
| where ProcessName =~ "named" and Log_Type =~ "client"
| where isnotempty(ResponseCode)
| where ResponseCode =~ "NXDOMAIN"
| summarize count() by Client_IP, bin(TimeGenerated,15m)
| where count_ > threshold
| join kind=inner (InfobloxNIOS
    | where ProcessName =~ "named" and Log_Type =~ "client"
    | where isnotempty(ResponseCode)
    | where ResponseCode =~ "NXDOMAIN"
    ) on Client_IP
| extend timestamp = TimeGenerated, IPCustomEntity = Client_IP
```

次のバージョンはソースに依存しないバージョンです。正規化を使用して、DNS クエリ イベントを提供するソースに対して同じ検出を提供します。

```kusto
let threshold = 200;
imDns
| where isnotempty(ResponseCodeName)
| where ResponseCodeName =~ "NXDOMAIN"
| summarize count() by SrcIpAddr, bin(TimeGenerated,15m)
| where count_ > threshold
| join kind=inner (imDns
    | where isnotempty(ResponseCodeName)
    | where ResponseCodeName =~ "NXDOMAIN"
    ) on SrcIpAddr
| extend timestamp = TimeGenerated, IPCustomEntity = SrcIpAddr
```

正規化されたソースに依存しないバージョンには、次の違いがあります。

- `imDns` 正規化されたパーサーは、Infoblox パーサーの代わりに使用されます。

- `imDns` は DNS クエリ イベントのみをフェッチするため、Infoblox バージョンの `where ProcessName =~ "named" and Log_Type =~ "client"` で行われるようなイベント種類の確認は必要ありません。

- `ResponseCode`、`Client_IP` の代わりに`ResponseCodeName`、`SrcIpAddr` のフィールドが使用されます。

#### <a name="enable-normalized-content-to-use-your-custom-data"></a>カスタム データを使用するために正規化されたコンテンツを有効にする

正規化を使用すると、独自のコンテンツと組み込みコンテンツをカスタム データと一緒に使用できます。

たとえば、DNS クエリ アクティビティ ログを受信するカスタム コネクタがある場合は、次の方法で DNS クエリ アクティビティ ログで正規化された DNS コンテンツを利用できます。

-   カスタム コネクタ用の[正規化パーサーを作成](#parsers)します。 ベンダー `Yyy` の製品 `Xxx` のためのパーサーであれば、パーサー名は `vimDnsYyyXxx` でなければなりません。

-   `imDns` のソースに依存しないパーサーを修正して、`union` ステートメントのパーサーのリストに追加することで、自分のパーサーも含めることができます。 次に例を示します。

    ```kusto
    union isfuzzy=true 
    vimDnsEmpty, 
    vimDnsCiscoUmbrella, 
    vimDnsInfobloxNIOS, 
    vimDnsMicrosoftOMS,
    vimDnsYyyXxx
    ```

## <a name="next-steps"></a>次のステップ

この記事では、Azure Sentinel と Azure Sentinel 情報モデルの正規化について説明します。

詳細については、次を参照してください。

- [Azure Sentinel ネットワーク正規化スキーマ リファレンス](normalization-schema.md)
- [Azure Sentinel DNS 正規化スキーマ リファレンス](dns-normalization-schema.md)
- [Azure Sentinel ファイル イベント正規化スキーマ リファレンス (パブリック プレビュー)](file-event-normalization-schema.md)
- [Azure Sentinel プロセス イベント正規化スキーマ リファレンス (パブリック プレビュー)](process-events-normalization-schema.md)
- [Azure Sentinel 認証正規化スキーマ リファレンス (パブリック プレビュー)](authentication-normalization-schema.md)