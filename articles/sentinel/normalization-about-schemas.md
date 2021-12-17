---
title: Advanced SIEM Information Model (ASIM) スキーマ | Microsoft Docs
description: この記事では、Advanced SIEM Information Model (ASIM) スキーマについてと、ASIM がさまざまなソースから統一されたプレゼンテーションにデータを正規化する上で、それがどう役立つのかについて説明します
services: sentinel
cloud: na
documentationcenter: na
author: oshezaf
manager: rkarlin
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: ofshezaf
ms.custom: ignite-fall-2021
ms.openlocfilehash: 006f5fbf005d2079fac66de2faf22e429f139d95
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132712815"
---
# <a name="advanced-siem-information-model-asim-schemas-public-preview"></a>Advanced SIEM Information Model (ASIM) スキーマ (パブリック プレビュー)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[ASIM](normalization.md) スキーマは、アクティビティを表すフィールドのセットです。 クエリで正規化されたスキーマのフィールドを使用すると、クエリがすべての正規化されたソースで確実に動作します。

スキーマ参照では、各スキーマを構成するフィールドの概要が示されます。 現在、ASIM では次のスキーマが定義されています。

 - [ネットワーク セッション](normalization-schema.md)
 - [DNS アクティビティ](dns-normalization-schema.md)
 - [DHCP アクティビティ](dhcp-normalization-schema.md)
 - [プロセス イベント](process-events-normalization-schema.md)
 - [認証イベント](authentication-normalization-schema.md)
 - [レジストリ イベント](registry-event-normalization-schema.md)
 - [ファイル アクティビティ](file-event-normalization-schema.md)

> [!IMPORTANT]
> ASIM は現在、プレビューの段階です。 [Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)には、ベータ版、プレビュー版、またはまだ一般提供されていない Azure 機能に適用される追加の法律条項が含まれています。
>

## <a name="schema-concepts"></a>スキーマの概念

次の概念は、スキーマのリファレンス ドキュメントを理解し、スキーマがカバーしていない情報がデータに含まれる場合に、正規化された方法でスキーマを拡張するのに役立ちます。


|概念  |説明  |
|---------|---------|
|**フィールド名**     |   各スキーマの中核となるのは、そのフィールド名です。 フィールド名は次のグループに属します。 <br><br>- すべてのスキーマに共通のフィールド <br>- スキーマに固有のフィールド <br>- スキーマに含むエンティティ (ユーザーなど) を表すフィールド。 エンティティ [ を表すフィールドは、スキーマ ](#entities) 間で類似しています。 <br><br>ドキュメント化されたスキーマに表示されないフィールドがソースにある場合、一貫性を維持するために正規化されます。 追加のフィールドがエンティティを表す場合は、エンティティ フィールドのガイドラインに基づいて正規化されます。 それ以外の場合、スキーマは、すべてのスキーマ間で一貫性を保つように努めます。<br><br> たとえば、DNS サーバーのアクティビティ ログはユーザー情報を提供しませんが、エンドポイントからの DNS アクティビティ ログにはユーザー情報が含まれる場合があります。これは、ユーザー エンティティのガイドラインに従って正規化できます。      |
|**フィールドの型**     |  スキーマの各フィールドには型があります。 Log Analytics ワークスペースには、限られたデータ型のセットがあります。 そのため、Microsoft Sentinel は多くのスキーマ フィールドで論理型を使います。Log Analytics ではこれを強制していませんが、スキーマの互換性のためには必要です。 論理フィールド型を使用すると、値とフィールド名の両方がソース間で一貫性を保ちます。  <br><br>詳細については、「[論理型](#logical-types)」を参照してください。     |
|**Field クラス**     |フィールドには、パーサーによってフィールドを実装しなければならない場合に定義するクラスがいくつか含まれます。 <br><br>-    **必須** フィールドはすべてのパーサーで表示されなければなりません。 ソースでこの値の情報が提供されていない場合、またはデータを追加できない場合、正規化されたスキーマを参照するほとんどのコンテンツ項目はサポートされません。<br>-  **推奨** フィールドがあれば正規化してください。 ただし、すべてのソースで使用できるとはいえ、正規化されたスキーマを参照するコンテンツ項目では可用性を考慮する必要があります。 <br>-  **省略可能な** フィールド (使用可能な場合) は、正規化するか、元の形式で残します。 通常、最小限のパーサーでは、パフォーマンス上の理由から正規化されません。    |
|**エンティティ**     | イベントは、ユーザー、ホスト、プロセス、ファイルなどのエンティティを中心に進化します。各エンティティでは、それを記述するために複数のフィールドが必要になる場合があります。 たとえば、ホストには名前と IP アドレスを指定できます。 <br><br>1 つのレコードに、ソース ホストと宛先ホストの両方など、同じ種類の複数のエンティティが含まれる場合があります。 <br><br>Advanced SIEM Information Model によって、エンティティを一貫して記述する方法が定義され、エンティティではスキーマの拡張が考慮されます。 <br><br>たとえば、ネットワーク セッション スキーマにはプロセス情報が含まれますが、一部のイベント ソースは追加できるプロセス情報を提供します。 詳細は、「[エンティティ](#entities)」を参照してください。 |
|**エイリアス**     |  場合によっては、ユーザーによってフィールドの名前が異なると予想される場合があります。 たとえば、DNS の用語では、`query` という名前のフィールドが必要ですが、より一般的にはドメイン名が保持されます。 エイリアスは、指定された値に対して複数の名前を許可することで、あいまいさの問題を解決します。 エイリアス クラスは、エイリアスのフィールドと同じクラスです。<br><br>Log Analytics ではエイリアス化がサポートされていないことに注意してください。 エイリアスパーサーを実装するには、`extend` 演算子を使用して元の値のコピーを作成します。        |
| | |

## <a name="logical-types"></a>論理型

スキーマの各フィールドには型があります。 `string`、`int`、`datetime`、`dynamic`などの Azure Log Analytics 型が組み込まれているものもあります。 他には、論理型を持つフィールドもあります。これは、フィールド値を正規化すべき方法を表します。

|データ型  |物理型  |形式と値  |
|---------|---------|---------|
|**Boolean**     |   Bool      |    ブール値を表す数値または文字列形式ではなく、組み込みの KQL `bool` データ型を使います。     |
|**列挙**     |  String       |   フィールドに対して明示的に定義された値のリストです。 スキーマ定義には、受け入れ可能な値が一覧表示されます。      |
|**日付/時刻**     |  インジェスト メソッドの機能に応じて、降順の優先順位で次の物理表現を使用します。 <br><br>- Log Analytics 組み込みの datetime 型 <br>- Log Analytics の datetime の数値表現を使用した整数フィールド。 <br>- Log Analytics の datetime の数値表現を使用した文字列フィールド <br>- サポートされている [Log Analytics の日付/時刻形式](/azure/data-explorer/kusto/query/scalar-data-types/datetime) を格納する文字列フィールド。       |  [Log Analytics の日付と時刻の表現](/azure/kusto/query/scalar-data-types/datetime)は、Unix の時刻表現と性質は似ていますが異なります。 詳細については、「[変換ガイドライン](/azure/kusto/query/datetime-timespan-arithmetic)」を参照してください。 <br><br>**注**: 該当する場合は、タイム ゾーンを調整する必要があります。 |
|**MAC アドレス**    |  String       | コロン 16 進表記        |
|**IP アドレス**     |String         |    Microsoft Sentinel スキーマは、IPv4 アドレスと IPv6 アドレスを別々に保持しません。 どの IP アドレス フィールドにも、IPv4 アドレスか IPv6 アドレスのどちらかを含めることができます。 <br><br>ドット 10 進表記の - **IPv4**<br>- **IPv6** を8 ヘクステットの短い形式で表記します☆<br><br>次に例を示します。<br>- **IPv4**: `192.168.10.10` <br>- **IPv6**: `FEDC:BA98:7654:3210:FEDC:BA98:7654:3210`<br>- **IPv6 短い形式**: `1080::8:800:200C:417A`     |
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
| | | |

## <a name="common-fields"></a><a name="common"></a>共通フィールド

一部のフィールドは、すべての ASIM スキーマに共通です。 各スキーマによって、共通フィールドの一部を特定のスキーマのコンテキストで使うためのガイドラインが追加されることがあるので、注意してください。 たとえば、**EventType** フィールドに使用できる値はスキーマごとに異なる可能性があり、**EventSchemaVersion** フィールドの値も同様に異なる可能性があります。

次のフィールドは、レコードごとに Log Analytics によって生成され、[カスタム コネクタを作成する](create-custom-connector.md)ときにオーバーライドできます。

| フィールド         | 型     | 考察 (Discussion)      |
| ------------- | -------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| <a name="timegenerated"></a>**TimeGenerated** | DATETIME | イベントがレポート デバイスによって生成された時刻。|
| **_ResourceId**   | guid     | レポート デバイスまたはサービスの Azure リソース ID。Syslog、CEF、WEF を使用して転送されたイベントの場合はログ フォワーダー リソース ID。 |
| **Type** | String | レコードがフェッチされた元のテーブル。 このフィールドは、同じイベントを異なるテーブルに対して複数のチャネルを通じて受信でき、同じ [EventVendor](#eventvendor) 値と [EventProduct](#eventproduct) 値を持つ場合に便利です。<br><br>たとえば、Sysmon イベントは、`Event` テーブルまたは `WindowsEvent` テーブルのいずれかに収集できます。 |
| | | |

> [!NOTE]
> Log Analytics では、セキュリティのユース ケースとあまり関連しない他のフィールドも追加します。 詳細については、「[Azure Monitor ログ内の標準列](../azure-monitor/logs/log-standard-columns.md)」を参照してください。
>


次のフィールドは、すべてのスキーマに対して ASIM によって定義されます。

| フィールド               | クラス       | 型       |  説明        |
|---------------------|-------------|------------|--------------------|
| **EventMessage**        | オプション    | String     |     レコードに含まれるか、レコードから生成された一般的なメッセージまたは説明。   |
| **EventCount**          | Mandatory   | Integer    |     レコードによって記述されるイベントの数。 <br><br>この値は、ソースが集計に対応しており、1 つのレコードが複数のイベントを表す場合があるときに使用されます。 <br><br>その他のソースの場合は、`1` に設定します。   |
| **EventStartTime**      | Mandatory   | 日付/時刻  |      ソースが集計に対応しており、レコードが複数のイベントを表す場合、このフィールドでは最初にイベントが生成された時間を指定します。 <br><br>それ以外の場合、このフィールドは [TimeGenerated](#timegenerated) フィールドの別名になります。 |
| **EventEndTime**        | Mandatory   | エイリアス      |      [TimeGenerated](#timegenerated) フィールドの別名。    |
|  <a name="eventtype"></a>**EventType**           | Mandatory   | Enumerated |    レコードによってレポートされる操作を記述します。 各スキーマには、このフィールドに対して有効な値の一覧が文書化されています。 |
| **EventSubType** | オプション | Enumerated | [EventType](#eventtype) フィールドでレポートされた操作を細分化して記述します。 各スキーマには、このフィールドに対して有効な値の一覧が文書化されています。 |
| <a name="eventresult"></a>**EventResult** | Mandatory | Enumerated | 以下の値のいずれかです。**Success**、**Partial**、**Failure**、**NA** (該当なし)。<br> <br>ソース レコードでは、異なる用語を使用して値が指定されている場合があります。それを、これらの値に正規化する必要があります。 また、ソースは [EventResultDetails](#eventresultdetails) フィールドのみを提供する場合があり、これを分析して EventResult 値を導き出す必要があります。<br><br>例: `Success`|
| <a name="eventresultdetails"></a>**EventResultDetails** | Mandatory | エイリアス | [**EventResult**](#eventresult) でレポートされた結果の理由または詳細。 各スキーマには、このフィールドに対して有効な値の一覧が文書化されています。<br><br>例: `NXDOMAIN`|
| **EventOriginalUid**    | オプション    | String     |   元のレコードの一意の ID (ソースによって提供されている場合)。<br><br>例: `69f37748-ddcd-4331-bf0f-b137f1ea83b`|
| **EventOriginalType**   | オプション    | String     |   元のイベントの種類または ID (ソースによって提供されている場合)。 たとえば、このフィールドは、元の Windows イベント ID を格納するために使用されます。<br><br>例: `4624`|
| <a name="eventproduct"></a>**EventProduct**        | Mandatory   | String     |             イベントを生成している製品。 <br><br>例: `Sysmon`<br><br>**注**: このフィールドはソース レコードでは使用できない場合があります。 その場合、このフィールドはパーサーによって設定される必要があります。           |
| **EventProductVersion** | オプション    | String     | イベントを生成している製品のバージョン。 <br><br>例: `12.1`      |
| <a name="eventvendor"></a>**EventVendor**         | Mandatory   | String     |           イベントを生成している製品のベンダー。 <br><br>例: `Microsoft`  <br><br>**注**: このフィールドはソース レコードでは使用できない場合があります。 その場合、このフィールドはパーサーによって設定される必要があります。  |
| **EventSchemaVersion**  | Mandatory   | String     | スキーマのバージョン。 各スキーマは、その現在のバージョンを文書化します。         |
| **EventReportUrl**      | オプション    | String     | あるリソースのイベントに提供された、そのイベントに関する追加情報を提供する URL。|
| <a name="dvc"></a>**Dvc** | Mandatory       | String     | イベントが発生した、またはイベントを報告したデバイスの一意の識別子 (スキーマによって異なります)。 <br><br>このフィールドは、[DvcFQDN](#dvcfqdn)、[DvcId](#dvcid)、[DvcHostname](#dvchostname)、または [DvcIpAddr](#dvcipaddr) フィールドの別名である可能性があります。 明確なデバイスがないクラウド ソースの場合は、[EventProduct](#eventproduct) フィールドと同じ値を使用します。            |
| <a name ="dvcipaddr"></a>**DvcIpAddr**           | 推奨 | IP アドレス | イベントが発生した、またはイベントを報告したデバイス (スキーマによって異なります) の IP アドレス。 <br><br>例: `45.21.42.12`    |
| <a name ="dvchostname"></a>**DvcHostname**         | 推奨 | Hostname (ホスト名)   | イベントが発生した、またはイベントを報告したデバイス (スキーマによって異なります) のホスト名。 <br><br>例: `ContosoDc.Contoso.Azure`               |
| <a name="dvcdomain"></a>**DvcDomain** | 推奨 | String | イベントが発生した、またはイベントを報告したデバイス (スキーマによって異なります) のドメイン。<br><br>例: `Contoso` |
| <a name="dvcdomaintype"></a>**DvcDomainType** | 推奨 | Enumerated | [DvcDomain](#dvcdomain) の種類 (既知の場合)。 次の値を指定できます。<br>- `Windows` (`contoso\mypc` などの場合)<br>- `FQDN` (`docs.microsoft.com` などの場合)<br><br>**注**: [DvcDomain](#dvcdomain) フィールドが使用されている場合、このフィールドは必須です。 |
| <a name="dvcfqdn"></a>**DvcFQDN** | オプション | String | イベントが発生した、またはイベントを報告したデバイス (スキーマによって異なります) のホスト名。 <br><br> 例: `Contoso\DESKTOP-1282V4D`<br><br>**注**: このフィールドでは、従来の FQDN 形式と Windows のドメイン\ホスト名形式の両方がサポートされています。 [DvcDomainType](#dvcdomaintype) フィールドには、使用されている形式が反映されます。  |
| <a name ="dvcid"></a>**DvcId**               | オプション    | String     | イベントが発生した、またはイベントを報告したデバイス (スキーマによって異なります) の一意の ID。 <br><br>例 : `41502da5-21b7-48ec-81c9-baeea8d7d669`   |
| **DvcIdType** | オプション | Enumerated | [DvcId](#dvcid) の種類 (既知の場合)。 次の値を指定できます。<br> - `AzureResourceId`<br>- `MDEid`<br><br>複数の ID を使用できる場合は、リストの最初のものを使用し、他のものはそれぞれフィールド名 **DvcAzureResourceId** および **DvcMDEid** を使用して格納します。<br><br>**注**: [DvcId](#dvcid) フィールドが使用されている場合、このフィールドは必須です。 |
| **DvcMacAddr**          | オプション    | MAC        |   イベントが発生したデバイスの MAC アドレス。  <br><br>例: `00:1B:44:11:3A:B7`       |
| **DvcZone** | オプション | String | イベントが発生した、またはイベントを報告したネットワーク (スキーマによって異なります)。 ゾーンは、レポート デバイスによって定義されます。<br><br>例: `Dmz` |
| **DvcOs**               | オプション    | String     |         イベントが発生したデバイスで実行されているオペレーティング システム。    <br><br>例: `Windows`    |
| **DvcOsVersion**        | オプション    | String     |   イベントが発生したデバイスのオペレーティング システムのバージョン。 <br><br>例: `10` |
| **DvcAction** | オプション | String | レポート セキュリティ システムについて、該当する場合にシステムによって実行されるアクション。 <br><br>例: `Blocked` |
| <a name="additionalfields"></a>**AdditionalFields**    | オプション    | 動的    | ソースから保持する必要のある追加情報が提供される場合は、元のフィールド名をそのまま使用するか、動的な **AdditionalFields** フィールドを作成し、それに追加情報をキーと値のペアとして追加します。    |
| | | | |

> [!NOTE]
> Log Analytics では、セキュリティのユース ケースとあまり関連しない他のフィールドも追加します。 詳細については、「[Azure Monitor ログ内の標準列](../azure-monitor/logs/log-standard-columns.md)」を参照してください。
>


## <a name="entities"></a>エンティティ

イベントは、ユーザー、ホスト、プロセス、ファイルなどのエンティティを中心に発展しています。 エンティティ表現を使用すると、同じ型の複数のエンティティを 1 つのレコードに含めることができ、同じエンティティに対して複数の属性をサポートできます。

エンティティの機能を有効にするために、エンティティ表現には次のガイドラインがあります。

|ガイドライン  |説明  |
|---------|---------|
|**記述子とエイリアス**     | 1 つのイベントには、ソースと宛先のホストなど、同じ種類の複数のエンティティが含まれることが多いため、特定のエンティティに関連付けられているすべてのフィールドを識別するためのプレフィックスとして *記述子* が使用されます。 <br><br>正規化を維持するために、Advanced SIEM Information Model には少数の標準記述子が使われ、エンティティの特定のロールに最適なものが選ばれます。  <br><br>ある型の 1 つのエンティティがイベントに関連している場合は、記述子を使用する必要はありません。 また、記述子のない一連のフィールドは、各型で最も使用されているエンティティのエイリアスを設定します。  |
|**識別子と型**     | 正規化されたスキーマでは、各エンティティに対して複数の識別子を使用できます。これは、イベントに共存させることを想定しています。 ソース イベントに、正規化されたスキーマにマップできない他のエンティティ識別子がある場合は、ソース フォームに保持するか、`AdditionalFields` の動的フィールドを使用します。 <br><br>識別子の型情報を保持するには、必要に応じて、同じ名前と `Type` のサフィックスを持つフィールドに型を格納します。 たとえば、「 `UserIdType` 」のように入力します。         |
|**属性**     |   多くの場合、エンティティには識別子として機能しない他の属性があり、記述子で修飾することもできます。 たとえば、ソース ユーザーがドメイン情報を持っている場合、正規化されたフィールドは `SrcUserDomain` になります。      |
| | |

各スキーマは、中央エンティティとエンティティ フィールドを明示的に定義します。 以下のガイドラインでは、中央スキーマ フィールドについてと、スキーマで明示的に定義されていない他のエンティティやエンティティ フィールドを使い、正規化された方法でスキーマを拡張する方法について説明します。

### <a name="the-user-entity"></a>ユーザー エンティティ

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
| | | |

次の表では、ユーザーに対してサポートされている識別子について説明します。

|正規化されたフィールド  |型  |形式とサポートされる型  |
|---------|---------|---------|
|**UserId**     |    String     |   コンピューターが判読できる、英数字で、システム内のユーザーを一意に表現したもの。 <br><br>形式とサポートされる型は次のとおりです。<br>    - **SID** (Windows): `S-1-5-21-1377283216-344919071-3415362939-500`<br>    -  **UID** (Linux): `4578`<br>    -    **AADID** (Azure Active Directory): `9267d02c-5f76-40a9-a9eb-b686f3ca47aa`<br>    - **OktaId**: `00urjk4znu3BcncfY0h7`<br>    - **AWSId**: `72643944673`<br><br>    ID 型を `UserIdType` フィールドに格納します。 他の ID がある場合は、フィールド名をそれぞれ`UserSid`、`UserUid`、`UserAADID`、`UserOktaId`、`UserAwsId`に正規化することを推奨します。       |
|**ユーザー名**     |  String       |   ユーザー名 (可能な場合はドメイン情報を含む) を、以下のいずれかの形式で、以下の優先順位で記載してください。 <br> -   **Upn/Email**: `johndow@contoso.com` <br>  -    **Windows**: `Contoso\johndow` <br> -   **DN**: `CN=Jeff Smith,OU=Sales,DC=Fabrikam,DC=COM` <br>  - **Simple**: `johndow`. この形式は、ドメイン情報が使用できない場合にのみ使用してください。 <br><br> Username 型を `UsernameType` フィールドに格納します。    |
| | | |


### <a name="the-process-entity"></a>プロセス エンティティ

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
| | | |


詳細については、「[Microsoft Sentinel プロセス イベント正規化スキーマ リファレンス (パブリック プレビュー)](process-events-normalization-schema.md)」を参照してください。

### <a name="the-device-entity"></a>デバイス エンティティ

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
| | | |


> [!NOTE]
> `Domain` はデバイスの一般的な属性ですが、完全な識別子ではありません。
>

詳細については、「[Microsoft Sentinel 認証正規化スキーマ リファレンス (パブリック プレビュー)](authentication-normalization-schema.md)」を参照してください。

### <a name="sample-entity-mapping"></a>エンティティ マッピングの例

このセクションでは、[Windows イベント 4624](/windows/security/threat-protection/auditing/event-4624) を例として、Microsoft Sentinel でイベント データがどのように正規化されるかを説明します。

このイベントには、次のエンティティがあります。

|Microsoft の用語  |Original イベント フィールド プレフィックス |ASIM フィールド プレフィックス  |説明  |
|---------|---------|---------|---------|
|**件名**     | `Subject`        |   `Actor`      |  サインインの成功に関する情報をレポートしたユーザー。      |
|**新しいログオン**     |    `Target`     |  `TargetUser`       |  サインインが実行されたユーザー。       |
|**Process**     |    -     |     `ActingProcess`    |   サインインを試行したプロセス。      |
|**ネットワーク情報**     |   -      |   `Src`      |     サインイン試行が実行されたマシン。    |
| | | | |


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
| | | | |


## <a name="next-steps"></a>次の手順

この記事では、Microsoft Sentinel での正規化の概要と、Advanced SIEM Information Model について説明しています。

詳細については、次を参照してください。
- [Advanced SIEM Information Model の概要](normalization.md)
- [Advanced SIEM Information Model のパーサー](normalization-about-parsers.md)
- [Advanced SIEM Information Model のコンテンツ](normalization-content.md)