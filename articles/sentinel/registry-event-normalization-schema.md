---
title: Microsoft Sentinel レジストリ イベント正規化スキーマ リファレンス | Microsoft Docs
description: この記事では、Microsoft Sentinel レジストリ イベント正規化スキーマについて説明します。
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 99892cb92bd2a7ac4ba854b96928707801ccd1aa
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132712245"
---
# <a name="microsoft-sentinel-registry-event-normalization-schema-reference-public-preview"></a>Microsoft Sentinel レジストリ イベント正規化スキーマ リファレンス (パブリック プレビュー)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

レジストリ イベント スキーマは、Windows レジストリ エンティティの作成、変更、または削除の Windows アクティビティを記述するために使用されます。

レジストリ イベントは、Windows システムに固有のものですが、Windows を監視するさまざまなシステム (EDR (エンドポイント検出と応答) システム、Sysmon、Windows 自体など) によってレポートされます。

Microsoft Sentinel での正規化の詳細については、「[正規化と Advanced SIEM Information Model (ASIM)](normalization.md)」を参照してください。

> [!IMPORTANT]
> レジストリ イベント正規化スキーマは、現在プレビュー中です。 この機能は、サービス レベル アグリーメントなしで提供されており、運用環境のワークロード用には推奨されていません。
>
> [Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)には、ベータ版、プレビュー版、またはまだ一般提供されていない Azure 機能に適用される追加の法律条項が含まれています。
>

## <a name="parsers"></a>パーサー

Microsoft Sentinel には、次の組み込みの製品固有のレジストリ イベント パーサーが用意されています。

- **セキュリティ イベント レジストリの更新 (イベント 4657)** 。Log Analytics エージェントまたは Azure Monitor エージェントを使用して収集されます
- **Sysmon レジストリ監視イベント (イベント 12、13、および 14)** 。Log Analytics エージェントまたは Azure Monitor エージェントを使用して収集されます
- **Microsoft 365 Defender for Endpoint のレジストリ イベント**

組み込みのすべてのパーサーを 1 つにまとめる、ソースに依存しないパーサーを使用して、構成済みのソース全体にわたって分析が実行されるようにするには、クエリでテーブル名として **imRegistry** を使用します。

[Microsoft Sentinel の GitHub リポジトリ](https://aka.ms/AzSentinelRegistry)から、[ソースに依存しないパーサーとソース固有のパーサー](normalization-about-parsers.md)をデプロイします。

### <a name="add-your-own-normalized-parsers"></a>独自の正規化されたパーサーを追加する

レジストリ イベント情報モデルにカスタム パーサーを実装するときは、`imRegistry<vendor><Product>` の構文を使用して KQL 関数に名前を付けます。

KQL 関数をソースに依存しないパーサー `imRegistry` に追加して、レジストリ イベント モデルが使用されるあらゆるコンテンツでも、新しいパーサーが使用されるようにします。

## <a name="normalized-content"></a>正規化されたコンテンツ

Microsoft Sentinel には、[[IFEO レジストリ キーを使用した永続化]](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/MultipleDataSources/PersistViaIFEORegistryKey.yaml) ハンティング クエリが用意されています。 このクエリは、Advanced SIEM Information Model を使用して正規化された任意のレジストリ アクティビティ データに対して機能します。

詳細については、「[Microsoft Azure Sentinel で脅威を検出する](hunting.md)」を参照してください。

## <a name="schema-details"></a>スキーマの詳細

レジストリ　イベント情報モデルは、[OSSEM レジストリ エンティティ スキーマ](https://github.com/OTRF/OSSEM/blob/master/docs/cdm/entities/registry.md)に対応しています。

### <a name="log-analytics-fields"></a>Log Analytics のフィールド


次のフィールドは、レコードごとに Log Analytics によって生成され、カスタム コネクタの作成時にオーバーライドできます。

| フィールド         | 型     | 考察 (Discussion)      |
| ------------- | -------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| <a name="timegenerated"></a>**TimeGenerated** | DATETIME | イベントがレポート デバイスによって生成された時刻。|
| **_ResourceId**   | guid     | レポート デバイスまたはサービスの Azure リソース ID。Syslog、CEF、WEF を使用して転送されたイベントの場合はログ フォワーダー リソース ID。 |
| **Type** | String | レコードがフェッチされた元のテーブル。 このフィールドは、同じイベントを複数のチャネルを通じて異なるテーブルで受信できるときに、同じ EventVendor 値と EventProduct 値を設定する場合に役に立ちます。<br><br>たとえば、Sysmon イベントを、イベント テーブルにも WindowsEvent テーブルにも収集できます。 |


> [!NOTE]
> Log Analytics では、セキュリティのユース ケースとあまり関連しない他のフィールドも追加します。 詳細については、「[Azure Monitor ログ内の標準列](../azure-monitor/logs/log-standard-columns.md)」を参照してください。
>

### <a name="event-fields"></a>イベント フィールド


イベント フィールドは、すべてのスキーマに共通であり、アクティビティ自体とレポート デバイスを記述します。

| フィールド               | クラス       | 型       |  説明        |
|---------------------|-------------|------------|--------------------|
| **EventMessage**        | オプション    | String     |     レコードに含まれるか、レコードから生成された一般的なメッセージまたは説明。   |
| **EventCount**          | Mandatory   | Integer    |     レコードによって記述されるイベントの数。 <br><br>この値は、ソースが集計に対応しており、1 つのレコードが複数のイベントを表す場合があるときに使用されます。 <br><br>その他のソースの場合は、`1` に設定します。   |
| **EventStartTime**      | Mandatory   | 日付/時刻  |      ソースが集計に対応しており、レコードが複数のイベントを表す場合、このフィールドでは最初にイベントが生成された時間を指定します。 <br><br>それ以外の場合、このフィールドは [TimeGenerated](#timegenerated) フィールドの別名になります。 |
| **EventEndTime**        | Mandatory   | エイリアス      |      [TimeGenerated](#timegenerated) フィールドの別名。    |
| **EventType**           | Mandatory   | Enumerated |    レコードによってレポートされる操作を記述します。 <br><br>レジストリ レコードの場合、次の値がサポートされます。 <br>- `RegistryKeyCreated` <br>- `RegistryKeyDeleted`<br>- `RegistryKeyRenamed` <br>- `RegistryValueDeleted` <br>- `RegistryValueSet`|
| **EventOriginalUid**    | オプション    | String     |   元のレコードの一意の ID (ソースによって提供されている場合)。<br><br>例: `69f37748-ddcd-4331-bf0f-b137f1ea83b`|
| **EventOriginalType**   | オプション    | String     |   元のイベントの種類または ID (ソースによって提供されている場合)。<br><br>例: `4657`|
| <a name ="eventproduct"></a>**EventProduct**        | Mandatory   | String     |             イベントを生成している製品。 <br><br>例: `Sysmon`<br><br>**注**: このフィールドはソース レコードでは使用できない場合があります。 その場合、このフィールドはパーサーによって設定される必要があります。           |
| **EventProductVersion** | オプション    | String     | イベントを生成している製品のバージョン。 <br><br>例: `12.1`      |
| **EventVendor**         | Mandatory   | String     |           イベントを生成している製品のベンダー。 <br><br>例: `Microsoft`  <br><br>**注**: このフィールドはソース レコードでは使用できない場合があります。 その場合、このフィールドはパーサーによって設定される必要があります。  |
| **EventSchemaVersion**  | Mandatory   | String     |    スキーマのバージョン。 ここに記載されているスキーマのバージョンは `0.1` です         |
| **EventReportUrl**      | オプション    | String     | あるリソースのイベントに指定された、そのイベントに関する追加情報を提供する URL。|
| **Dvc** | Mandatory       | String     |               イベントが発生したデバイスの一意の識別子。 <br><br>このフィールドの別名は、[DvcId](#dvcid)、[DvcHostname](#dvchostname)、または [DvcIpAddr](#dvcipaddr) フィールドになる場合があります。 明確なデバイスがないクラウド リソースの場合は、[EventProduct](#eventproduct) フィールドと同じ値を使用します。         |
| <a name ="dvcipaddr"></a>**DvcIpAddr**           | 推奨 | IP アドレス |         レジストリ イベントが発生したデバイスの IP アドレス。  <br><br>例: `45.21.42.12`    |
| <a name ="dvchostname"></a>**DvcHostname**         | 推奨 | Hostname (ホスト名)   |               レジストリ イベントが発生したデバイスのホスト名。 <br><br>例: `ContosoDc.Contoso.Azure`               |
| <a name ="dvcid"></a>**DvcId**               | オプション    | String     |  レジストリ イベントが発生したデバイスの一意の ID。 <br><br>例: `41502da5-21b7-48ec-81c9-baeea8d7d669`   |
| **DvcMacAddr**          | オプション    | MAC        |   レジストリ イベントが発生したデバイスの MAC。  <br><br>例: `00:1B:44:11:3A:B7`       |
| **DvcOs**               | オプション    | String     |         レジストリ イベントが発生したデバイスで実行されているオペレーティング システム。    <br><br>例: `Windows`    |
| **DvcOsVersion**        | オプション    | String     |   レジストリ イベントが発生したデバイスのオペレーティング システムのバージョン。 <br><br>例: `10` |
| **AdditionalFields**    | オプション    | 動的    | ソースから保持する必要のある追加情報が提供される場合は、元のフィールド名をそのまま使用するか、動的な **AdditionalFields** フィールドを作成し、それを追加情報にキーと値のペアとして追加します。    |


### <a name="registry-event-specific-fields"></a>レジストリ イベント固有のフィールド

以下の表に示すフィールドはレジストリ イベントに固有ですが、他のスキーマのフィールドに類似しており、同様の名前付け規則に従っています。

詳細については、Windows ドキュメントの[レジストリの構造](/windows/win32/sysinfo/structure-of-the-registry)に関するページを参照してください。

| フィールド          | クラス        | 型       | 説明   |
|---------------|--------------|------------|-----------------|
|<a name="registrykey"></a>**RegistryKey**     |     Mandatory    |   String      |操作に関連付けられたレジストリ キー。標準のルート キーの名前付け規則に正規化されます。 詳細については、「[ルート キー](#root-keys)」を参照してください。<br><br>レジストリ キーは、ファイル システムのフォルダーに似ています。 <br><br>例: `HKEY_LOCAL_MACHINE\SOFTWARE\MTG`        |
|**RegistryValue**     |    推奨     |  String       |操作に関連付けられたレジストリ値。 レジストリ値は、ファイル システムのファイルに似ています。 <br><br>例: `Path`        |
|<a name="registryvaluetype"></a>**RegistryValueType**     |    推奨     |    String     | レジストリ値の型。標準形式に正規化されます。 詳細については、「[値の型](#value-types)」を参照してください。<br><br>例: `Reg_Expand_Sz`        |
|**RegistryValueData**     | 推奨       |      String   |  レジストリ値に格納されるデータ。 <br><br>例: `C:\Windows\system32;C:\Windows;`       |
|<a name="registrypreviouskey"></a>**RegistryPreviousKey**     |  推奨       |   String      |  レジストリを変更する操作での元のレジストリ キー。標準のルート キーの名前付けに正規化されます。 詳細については、「[ルート キー](#root-keys)」を参照してください。 <br><br>**注**: 操作で、値などの他のフィールドが変更された一方で、キーが同じままの場合、[RegistryPreviousKey](#registrypreviouskey) の値は [RegistryKey](#registrykey) と同じになります。<br><br>例: `HKEY_LOCAL_MACHINE\SOFTWARE\MTG`       |
|<a name="registrypreviousvalue"></a>**RegistryPreviousValue**     | 推奨        | String        | レジストリを変更する操作での元の値の型。標準形式に正規化されます。 詳細については、「[値の型](#value-types)」を参照してください。 <br><br>型が変更されていない場合、このフィールドの値は [RegistryValueType](#registryvaluetype) フィールドと同じになります。  <br><br>例: `Path`       |
|**RegistryPreviousValueType**     | 推奨        |   String      |レジストリを変更する操作での元の値の型。 <br><br>型が変更されていない場合、このフィールドの値は [RegistryValueType](#registryvaluetype) フィールドと同じになります。値は標準形式に正規化されます。 詳細については、[値の型](#value-types)に関するページを参照してください。<br><br>例: `Reg_Expand_Sz`         |
|**RegistryPreviousValueData**     | 推奨        |   String      |レジストリを変更する操作での元のレジストリ データ。 <br><br>例: `C:\Windows\system32;C:\Windows;`         |
|**User** | エイリアス | |[ActorUsername](#actorusername) フィールドの別名。 <br><br>例: `CONTOSO\ dadmin` |
|**Process**     |  エイリアス       |         |  [ActingProcessName](#actingprocessname) フィールドの別名。<br><br>例: `C:\Windows\System32\rundll32.exe`       |
| <a name="actorusername"></a>**ActorUsername**  | Mandatory    | String     | イベントを開始したユーザーのユーザー名。 <br><br>例: `CONTOSO\WIN-GG82ULGC9GO$`     |
| **ActorUsernameType**              | Mandatory    | Enumerated |   [ActorUsername](#actorusername) フィールドに格納されているユーザー名の種類を指定します。 詳細については、「[ユーザー エンティティ](normalization-about-schemas.md#the-user-entity)」を参照してください。 <br><br>例: `Windows`       |
| <a name="actoruserid"></a>**ActorUserId**    | 推奨  | String     |   Actor の一意の ID。 具体的な ID は、イベントが生成されるシステムによって異なります。 詳細については、「[ユーザー エンティティ](normalization-about-schemas.md#the-user-entity)」を参照してください。  <br><br>例: `S-1-5-18`    |
| **ActorUserIdType**| 推奨  | String     |  [ActorUserId](#actoruserid) フィールドに格納されている ID の種類。 詳細については、「[ユーザー エンティティ](normalization-about-schemas.md#the-user-entity)」を参照してください。 <br><br>例: `SID`         |
| **ActorSessionId** | オプション     | String     |   Actor のログイン セッションの一意の ID。  <br><br>例: `999`<br><br>**注**: さまざまなシステムに対応するために、型は "*文字列*" として定義されますが、Windows ではこの値は数値である必要があります。 Windows マシンを使用しており、かつソースが別の型を送信する場合は、必ず値を変換してください。 たとえば、ソースが 16 進数の値を送信する場合は、10 進数の値に変換します。   |
| <a name="actingprocessname"></a>**ActingProcessName**              | オプション     | String     |   実行プロセス イメージ ファイルのファイル名。 この名前は通常、プロセス名と見なされます。  <br><br>例: `C:\Windows\explorer.exe`  |
| **ActingProcessId**| Mandatory    | String        | 実行プロセスのプロセス ID (PID)。<br><br>例: `48610176`           <br><br>**注**: さまざまなシステムに対応するために、型は "*文字列*" として定義されますが、Windows と Linux ではこの値は数値である必要があります。 <br><br>Windows または Linux のマシンを使用しており、かつ別の型を使用した場合は、必ず値を変換してください。 たとえば、16 進数の値を使用した場合は、10 進数の値に変換します。    |
| **ActingProcessGuid**              | オプション     | String     |  実行プロセスの生成された一意識別子 (GUID)。   <br><br> 例: `EF3BD0BD-2B74-60C5-AF5C-010000001E00`            |
| **ParentProcessName**              | オプション     | String     |  親プロセス イメージ ファイルのファイル名。 この値は通常、プロセス名と見なされます。    <br><br>例: `C:\Windows\explorer.exe` |
| **ParentProcessId**| Mandatory    | String    | 親プロセスのプロセス ID (PID)。   <br><br>     例: `48610176`    |
| **ParentProcessGuid**              | オプション     | String     |  親プロセスの生成された一意識別子 (GUID)。     <br><br> 例: `EF3BD0BD-2B74-60C5-AF5C-010000001E00` |



### <a name="root-keys"></a>ルート キー

さまざまなソースで、さまざまな表現を使用してレジストリ キー プレフィックスが表されます。 [RegistryKey](#registrykey) および [RegistryPreviousKey](#registrypreviouskey) フィールドには、次の正規化されたプレフィックスを使用します。

|正規化されたキー プレフィックス  |その他の一般的な表現  |
|---------|---------|
|**HKEY_LOCAL_MACHINE**     | `HKLM`, `\REGISTRY\MACHINE`        |
|**HKEY_USERS**     | `HKU`, `\REGISTRY\USER`        |


### <a name="value-types"></a>値型

さまざまなソースで、さまざまな表現を使用してレジストリ値の型が表されます。 [RegistryValueType](#registryvaluetype) および [RegistryPreviousValueType](#registrypreviousvalue) フィールドには、次の正規化された型を使用します。


|正規化されたキー プレフィックス  |その他の一般的な表現  |
|---------|---------|
|  **Reg_None**   |    `None`, `%%1872`     |
|  **Reg_Sz**   |     `String`, `%%1873`    |
| **Reg_Expand_Sz**    | `ExpandString`, `%%1874`        |
|  **Reg_Binary**   |   `Binary`, `%%1875`      |
| **Reg_DWord**    |    `Dword`, `%%1876`     |
|  **Reg_Multi_Sz**   |  `MultiString`, `%%1879`       |
|  **Reg_QWord**   |    `Qword`, `%%1883`     |



## <a name="next-steps"></a>次のステップ

詳細については、次を参照してください。

- [Microsoft Sentinel での正規化](normalization.md)
- [Microsoft Sentinel 認証正規化スキーマ リファレンス (パブリック プレビュー)](authentication-normalization-schema.md)
- [Microsoft Sentinel の DNS 正規化スキーマ リファレンス](dns-normalization-schema.md)
- [Microsoft Sentinel ファイル イベント正規化スキーマ リファレンス (パブリック プレビュー)](file-event-normalization-schema.md)
- [Microsoft Sentinel ネットワーク正規化スキーマ リファレンス](./network-normalization-schema.md)
