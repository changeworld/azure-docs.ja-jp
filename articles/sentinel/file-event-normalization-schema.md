---
title: Microsoft Sentinel ファイル イベント正規化スキーマ リファレンス | Microsoft Docs
description: この記事では、Microsoft Sentinel ファイル イベント正規化スキーマについて説明します。
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
ms.openlocfilehash: 3b8f06a5700dd4bb0ec4c75223ffe9680515215f
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132721159"
---
# <a name="microsoft-sentinel-file-event-normalization-schema-reference-public-preview"></a>Microsoft Sentinel ファイル イベント正規化スキーマ リファレンス (パブリック プレビュー)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

ファイル イベント正規化スキーマは、ファイルまたはドキュメントの作成、変更、削除などのファイル アクティビティを記述するために使用されます。 このようなイベントは、オペレーティング システム、Azure Files などのファイル ストレージ システム、Microsoft SharePoint などのドキュメント管理システムによって報告されます。

Microsoft Sentinel での正規化の詳細については、「[正規化と Advanced SIEM Information Model (ASIM)](normalization.md)」を参照してください。

> [!IMPORTANT]
> 現在、ファイル イベント正規化スキーマはプレビュー段階です。 この機能は、サービス レベル アグリーメントなしで提供されており、運用環境のワークロード用には推奨されていません。
>
> [Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)には、ベータ版、プレビュー版、またはまだ一般提供されていない Azure 機能に適用される追加の法律条項が含まれています。
>

## <a name="parsers"></a>パーサー

Microsoft Sentinel には、次の組み込みの製品固有のファイル イベント パーサーが用意されています。

- **Sysmon ファイル アクティビティ イベント** (イベント 11、23、26)。Log Analytics エージェントまたは Azure Monitor エージェントを使用して収集されます。
- **Microsoft Office 365 SharePoint と OneDrive のイベント**。Office アクティビティ コネクタを使用して収集されます。
- **Microsoft 365 Defender for Endpoint のファイル イベント**
- **Azure Storage** (Blob、File、Queue、Table Storage を含む)。

組み込みのすべてのパーサーを 1 つにまとめる、ソースに依存しないパーサーを使用して、構成済みのソース全体にわたって分析が実行されるようにするには、クエリでテーブル名として imFileEvent を使用します。

[Microsoft Sentinel の GitHub リポジトリ](https://aka.ms/AzSentinelFileEvent)から、[ソースに依存しないパーサーとソース固有のパーサー](normalization-about-parsers.md)をデプロイします。

## <a name="add-your-own-normalized-parsers"></a>独自の正規化されたパーサーを追加する


ファイル イベント情報モデルにカスタム パーサーを実装するときは、`imFileEvent<vendor><Product` の構文を使用して KQL 関数に名前を付けます。

KQL 関数をソースに依存しないパーサー `imFileEvent` に追加して、ファイル イベント モデルが使用されるあらゆるコンテンツで、新しいパーサーも使用されるようにします。

## <a name="normalized-content-for-file-activity-data"></a>ファイル アクティビティ データの正規化されたコンテンツ

ファイル アクティビティ ASIM スキーマのサポートには、正規化されたファイル アクティビティ パーサーを使用した次の組み込み分析規則のサポートも含まれています。 Microsoft Sentinel GitHub リポジトリへのリンクは下に参照として掲載していますが、これらのルールは [Microsoft Sentinel Analytics ルール ギャラリー](detect-threats-built-in.md)でも見つけることができます。 リンクされた GitHub ページを使用して、一覧のルールに関連するハンティング クエリをコピーします。


- [SUNBURST and SUPERNOVA backdoor hashes (Normalized File Events) (SUNBURST と SUPERNOVA のバックドア ハッシュ (正規化されたファイル イベント))](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimFileEvent/imFileESolarWindsSunburstSupernova.yaml)
- [Exchange Server Vulnerabilities Disclosed March 2021 IoC Match (2021 年 3 月に公開された Exchange Server の脆弱性の IoC 一致)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ExchangeServerVulnerabilitiesMarch2021IoCs.yaml)
- [HAFNIUM UM Service writing suspicious file (疑わしいファイルを書き込む HAFNIUM UM サービス)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/HAFNIUMUmServiceSuspiciousFile.yaml)
- [NOBELIUM - Domain, Hash and IP IOCs - May 2021 (NOBELIUM - ドメイン、ハッシュ、IP IOC - 2021 年 3 月)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/NOBELIUM_IOCsMay2021.yaml)
- [SUNSPOT log file creation (SUNSPOT ログ ファイルの作成)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/SUNSPOTLogFile.yaml)
- [Known ZINC Comebacker and Klackring malware hashes (既知の ZINC Comebacker および Klackring マルウェア ハッシュ)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ZincJan272021IOCs.yaml)

詳細については、「[脅威を検出するためのカスタム分析ルールを作成する](detect-threats-custom.md)」を参照してください。


## <a name="schema-details"></a>スキーマの詳細

ファイル イベント情報モデルは、[OSSEM プロセス エンティティ スキーマ](https://github.com/OTRF/OSSEM/blob/master/docs/cdm/entities/file.md)に対応しています。

### <a name="log-analytics-fields"></a>Log Analytics のフィールド

次のフィールドは、レコードごとに Log Analytics によって生成され、カスタム コネクタの作成時にオーバーライドできます。

| フィールド         | 型     | 考察 (Discussion)      |
| ------------- | -------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| <a name="timegenerated"></a>**TimeGenerated** | DATETIME | イベントがレポート デバイスによって生成された時刻。|
| **_ResourceId**   | guid     | レポート デバイスまたはサービスの Azure リソース ID。Syslog、CEF、WEF を使用して転送されたイベントの場合はログ フォワーダー リソース ID。 |
| **Type** | String | レコードがフェッチされた元のテーブル。 このフィールドは、同じイベントを複数のチャネルを通じて異なるテーブルで受信できるときに、同じ EventVendor 値と EventProduct 値を設定する場合に役に立ちます。<br><br>たとえば、Sysmon イベントを、イベント テーブルにも WindowsEvent テーブルにも収集できます。 |
| | | |

> [!NOTE]
> Log Analytics では、セキュリティのユース ケースとあまり関連しない他のフィールドも追加します。 詳細については、「[Azure Monitor ログ内の標準列](../azure-monitor/logs/log-standard-columns.md)」を参照してください。
>

## <a name="event-fields"></a>イベント フィールド

イベント フィールドは、すべてのスキーマに共通であり、アクティビティ自体とレポート デバイスを記述します。

| フィールド               | クラス       | 型       |  説明       |
|---------------------|-------------|------------|--------------------|
| **EventMessage**        | オプション    | String     |     レコードに含まれるか、レコードから生成された一般的なメッセージまたは説明。   |
| **EventCount**          | Mandatory   | Integer    |     レコードによって記述されるイベントの数。 <br><br>この値は、ソースが集計に対応しており、1 つのレコードが複数のイベントを表す場合があるときに使用されます。 <br><br>その他のソースの場合は、`1` に設定します。   |
| **EventStartTime**      | Mandatory   | 日付/時刻  |      ソースが集計に対応しており、レコードが複数のイベントを表す場合、このフィールドでは最初にイベントが生成された時間を指定します。 それ以外の場合、このフィールドは [TimeGenerated](#timegenerated) フィールドの別名になります。 |
| **EventEndTime**        | Mandatory   | エイリアス      |      [TimeGenerated](#timegenerated) フィールドの別名。    |
| **EventType**           | Mandatory   | Enumerated |    レコードによって報告される操作を記述します。 <br><br>ファイル レコードの場合、次のような値がサポートされます。 <br><br>- `FileCreated`<br>- `FileModified`<br>- `FileDeleted`<br>- `FileRenamed`<br>- `FileCopied`<br>- `FileMoved`<br>- `FolderCreated`<br>- `FolderDeleted` |
| **EventResult**         | Mandatory   | Enumerated |  イベントの結果を記述し、次のサポートされている値の 1 つに正規化されます。 <br><br>- `Success`<br>- `Partial`<br>- `Failure`<br>- `NA` (該当なし) <br><br>ソースから [EventOriginalResultDetails](#eventoriginalresultdetails) フィールドの値のみが提供される場合があり、**EventResult** の値を取得するには分析が行われる必要があります。 |
| <a name="eventoriginalresultdetails"></a>**EventOriginalResultDetails**  | オプション    | String     | イベントの結果を記述します。 <br><br>**注**: この値は正規化されず、ソースによって提供されたままの元の値を対象とします。 現在、ファイル イベント正規化スキーマには、*EventResultDetails* のような関連する正規化されたフィールドはありません。 |
| **EventOriginalUid**    | オプション    | String     | 元のレコードの一意の ID (ソースによって提供されている場合)。<br><br>例: `69f37748-ddcd-4331-bf0f-b137f1ea83b`|
| **EventOriginalType**   | オプション    | String     | 元のイベントの種類または ID (ソースによって提供されている場合)。<br><br>例: `4663`|
| <a name ="eventproduct"></a>**EventProduct**       | Mandatory   | String     | イベントを生成している製品。 <br><br>例: `Sysmon`<br><br>**注**: このフィールドはソース レコードでは使用できない場合があります。 その場合、このフィールドはパーサーによって設定される必要があります。           |
| **EventProductVersion** | オプション    | String     | イベントを生成している製品のバージョン。 <br><br>例: `12.1`<br><br>**注**: このフィールドはソース レコードでは使用できない場合があります。 その場合、このフィールドはパーサーによって設定される必要があります。           |
| **EventVendor**         | Mandatory   | String     | イベントを生成している製品のベンダー。 <br><br>例: `Microsoft`  <br><br>**注**: このフィールドはソース レコードでは使用できない場合があります。 その場合、このフィールドはパーサーによって設定される必要があります。  |
| **EventSchemaVersion**  | Mandatory   | String     | スキーマのバージョン。 ここに記載されているスキーマのバージョンは `0.1` です         |
| **EventReportUrl**      | オプション    | String     | あるリソースのイベントに指定された、そのイベントに関する追加情報を提供する URL。|
| **Dvc**                 | エイリアス       | String     | イベントが発生したデバイスの一意の識別子。 <br><br>例: `ContosoDc.Contoso.Azure`<br><br>このフィールドは、[DvcId](#dvcid)、[DvcHostname](#dvchostname)、または [DvcIpAddr](#dvcipaddr) フィールドの別名になる場合があります。 明確なデバイスがないクラウド リソースの場合は、[EventProduct](#eventproduct) フィールドと同じ値を使用します。            |
| <a name ="dvcipaddr"></a>**DvcIpAddr**   | 推奨 | IP アドレス | ファイル イベントが発生したデバイスの IP アドレス。  <br><br>例: `45.21.42.12`    |
| <a name ="dvchostname"></a>**DvcHostname**    | 推奨 | Hostname (ホスト名)   | ファイル イベントが発生したデバイスのホスト名。 <br><br>例: `ContosoDc.Contoso.Azure` |
| <a name ="dvcid"></a>**DvcId**  | オプション    | String     |  ファイル イベントが発生したデバイスの一意の ID。 <br><br>例: `41502da5-21b7-48ec-81c9-baeea8d7d669`   |
| **DvcMacAddr**          | オプション    | MAC        |   ファイル イベントが発生したデバイスの MAC。  <br><br>例: `00:1B:44:11:3A:B7`       |
| **DvcOs**               | オプション    | String     |         ファイル イベントが発生したデバイスで実行されているオペレーティング システム。    <br><br>例: `Windows`    |
| **DvcOsVersion**        | オプション    | String     |   ファイル イベントが発生したデバイスのオペレーティング システムのバージョン。 <br><br>例: `10` |
| **AdditionalFields**    | オプション    | 動的    | ソースから保持する価値のある追加情報が提供される場合は、元のフィールド名をそのまま使用するか、動的な **AdditionalFields** フィールドを作成し、それに追加情報をキー値ペアとして追加します。    |
| | | | |



### <a name="file-event-specific-fields"></a>ファイル イベント固有のフィールド

以下の表に示すフィールドはファイル イベントに固有ですが、他のスキーマのフィールドに類似しており、同様の名前付け規則に従います。

ファイル イベント スキーマでは、ファイル アクティビティの中心である次のエンティティを参照します。

- **Actor**。 ファイル アクティビティを開始したユーザー
- **ActingProcess**。 ファイル アクティビティを開始するためにアクターによって使用されるプロセス
- **TargetFile**。 操作が実行されたファイル
- **ソース ファイル (SrcFile)** 。 操作前のファイル情報を格納します。

これらのエンティティ間の関係は、次のように示すと最もわかりやすくなります。**アクター** が **実行プロセス** を使用してファイル操作を実行し、**ソース ファイル** を **ターゲット ファイル** に変更します。 

例: `JohnDoe` (**アクター**) が `Windows File Explorer` (**実行プロセス**) を使用して `new.doc` (**ソース ファイル**) の名前を `old.doc` (**ターゲット ファイル**) に変更する。

| フィールド          | クラス        | 型       | 説明   |
|---------------|--------------|------------|-----------------|
| **ActingProcessCommandLine** |オプション  |String  | 実行プロセスを実行するために使用するコマンド ライン。 <br><br>例: `"choco.exe" -v` |
|**ActingProcessGuid** |オプション     | String     |  実行プロセスの生成された一意識別子 (GUID)。 システム間でプロセスを識別できます。  <br><br> 例: `EF3BD0BD-2B74-60C5-AF5C-010000001E00`            |
| **ActingProcessId**| Mandatory    | String        | 実行プロセスのプロセス ID (PID)。<br><br>例: `48610176`           <br><br>**注**: さまざまなシステムに対応するために、型は "*文字列*" として定義されますが、Windows と Linux ではこの値は数値である必要があります。 <br><br>Windows または Linux のマシンを使用しており、かつ別の型を使用した場合は、必ず値を変換してください。 たとえば、16 進数の値を使用した場合は、10 進数の値に変換します。    |
| <a name="actingprocessname"></a>**ActingProcessName**              | オプション     | String     |   実行プロセスの名前。 この名前は通常、プロセスの仮想アドレス空間にマップされる初期コードとデータを定義するために使用される、イメージまたは実行可能ファイルから派生します。<br><br>例: `C:\Windows\explorer.exe`  |
|**Process**| エイリアス| | [ActingProcessName](#actingprocessname) の別名|
| <a name="actoruserid"></a>**ActorUserId**    | 推奨  | String     |   **アクター** の一意の ID。 具体的な ID は、イベントが生成されるシステムによって異なります。 詳細については、「[ユーザー エンティティ](normalization-about-schemas.md#the-user-entity)」を参照してください。  <br><br>例: `S-1-5-18`    |
| **ActorUserIdType**| 推奨  | String     |  [ActorUserId](#actoruserid) フィールドに格納されている ID の種類。 詳細については、「[ユーザー エンティティ](normalization-about-schemas.md#the-user-entity)」を参照してください。 <br><br>例: `SID`         |
| <a name="actorusername"></a>**ActorUsername**  | Mandatory    | String     | イベントを開始したユーザーのユーザー名。 <br><br>例: `CONTOSO\WIN-GG82ULGC9GO$`     |
| **ActorUsernameType**              | Mandatory    | Enumerated |   [ActorUsername](#actorusername) フィールドに格納されているユーザー名の種類を指定します。 詳細については、「[ユーザー エンティティ](normalization-about-schemas.md#the-user-entity)」を参照してください。 <br><br>例: `Windows`       |
|**User** | エイリアス| | [ActorUsername](#actorusername) フィールドの別名。 <br><br>例: `CONTOSO\dadmin`|
| **ActorUserType**|オプション | Enumerated| **アクター** の種類。 サポートされている値は次のとおりです。 <br><br>- `Regular`<br>- `Machine`<br>- `Admin`<br>- `System`<br>- `Application`<br>- `Service Principal`<br>- `Other` <br><br>**注**: ソースから [ActorOriginalUserType](#actororiginalusertype) フィールドの値のみが提供される場合があり、**ActorUserType** の値を取得するには分析が行われる必要があります。|
|<a name="actororiginalusertype"></a>**ActorOriginalUserType** |オプション |String | レポート デバイスによって提供される、**アクター** のユーザーの種類。 |
|**HttpUserAgent** |オプション | String |HTTP または HTTPS を使用してリモート システムによって操作が開始されると、ユーザー エージェントが使用されます。<br><br>次に例を示します。<br>`Mozilla/5.0 (Windows NT 10.0; Win64; x64)`<br>`AppleWebKit/537.36 (KHTML, like Gecko)`<br>` Chrome/42.0.2311.135`<br>`Safari/537.36 Edge/12.246`|
| **NetworkApplicationProtocol**| オプション|String | 操作がリモート システムによって開始されると、この値は OSI モデルで使用されるアプリケーション層プロトコルになります。 <br><br>このフィールドは列挙されず、あらゆる値が許容されます。推奨される値には、`HTTP`、`HTTPS`、`SMB`、`FTP`、`SSH` などが挙げられます<br><br>例: `SMB`|
|**SrcIpAddr** |推奨 |IP アドレス | 操作がリモート システムによって開始されたときは、このシステムの IP アドレス。<br><br>例: `185.175.35.214`|
| **SrcFileCreationTime**|省略可能 |日付/時刻 |ソース ファイルが作成された時刻。 |
|**SrcFileDirectory** | オプション| String| ソース ファイルのフォルダーまたは場所。 このフィールドは、最終要素のない [SrcFilePath](#srcfilepath) フィールドに類似しています。 <br><br>**注**: 値がログ ソースにある場合は、パーサーによってこの値が提供されることがあり、フル パスから抽出される必要はありません。|
| **SrcFileExtension**|オプション | String|ソース ファイルの拡張子。 <br><br>**注**: 値がログ ソースにある場合は、パーサーによってこの値が提供されることがあり、フル パスから抽出される必要はありません。|
|**SrcFileMimeType** |オプション |Enumerated |    ソース ファイルの MIME タイプまたはメディア タイプ。 サポートされている値の一覧は、[IANA メディア タイプ](https://www.iana.org/assignments/media-types/media-types.xhtml) リポジトリで確認できます。 |
|**SrcFileName** |オプション |String | パスまたは場所のないソース ファイルの名前。ただし、該当する場合は拡張子が付きます。 このフィールドは、[SrcFilePath](#srcfilepath) フィールドの最終要素に類似しています。 <br><br>**注**: 値がログ ソースにある場合は、パーサーによってこの値が提供されることがあり、フル パスから抽出される必要はありません。|
| <a name="srcfilepath"></a>**SrcFilePath**| Mandatory|String |フォルダーまたは場所、ファイル名、拡張子が含まれる、ソース ファイルの正規化されたフル パス。 <br><br>詳細については、「[パス構造](#path-structure)」を参照してください。<br><br>例: `/etc/init.d/networking` |
|**SrcFilePathType** |Mandatory | Enumerated| [SrcFilePath](#srcfilepath) の型。 詳細については、「[パス構造](#path-structure)」を参照してください。|
|**SrcFileMD5**|省略可能 |MD5 | ソース ファイルの MD5 ハッシュ。 <br><br>例:           `75a599802f1fa166cdadb360960b1dd0` |
|**SrcFileSHA1**|省略可能 |SHA1 |ソース ファイルの SHA-1 ハッシュ。<br><br>例:<br>`d55c5a4df19b46db8c54`<br>`c801c4665d3338acdab0` |
|**SrcFileSHA256** | オプション|SHA256 |ソース ファイルの SHA-256 ハッシュ。 <br><br>例:<br> `e81bb824c4a09a811af17deae22f22dd`<br>`2e1ec8cbb00b22629d2899f7c68da274`|
|**SrcFileSHA512** |省略可能 | SHA512|ソース ファイルの SHA-512 ハッシュ。 |
|**SrcFileSize**| オプション|Integer | ソース ファイルのサイズ (バイト単位)。|
|**TargetFileCreationTime** | オプション|日付/時刻 |ターゲット ファイルが作成された時刻。 |
|**TargetFileDirectory** | オプション|String |ターゲット ファイルのフォルダーまたは場所。 このフィールドは、最終要素のない [TargetFilePath](#targetfilepath) フィールドに類似しています。 <br><br>**注**: 値がログ ソースにある場合は、パーサーによってこの値が提供されることがあり、フル パスから抽出される必要はありません。|
|**TargetFileExtension** |オプション |String | ターゲット ファイルの拡張子。<br><br>**注**: 値がログ ソースにある場合は、パーサーによってこの値が提供されることがあり、フル パスから抽出される必要はありません。|
| **TargetFileMimeType**|オプション | Enumerated| ターゲット ファイルの MIME タイプまたはメディア タイプ。 許可されている値の一覧は、[IANA メディア タイプ](https://www.iana.org/assignments/media-types/media-types.xhtml) リポジトリで確認できます。|
| **TargetFileName**|オプション |String |パスまたは場所のないターゲット ファイルの名前。ただし、該当する場合は拡張子が付きます。 このフィールドは、[TargetFilePath](#targetfilepath) フィールドの最終要素に類似しています。<br><br>**注**: 値がログ ソースにある場合は、パーサーによってこの値が提供されることがあり、フル パスから抽出される必要はありません。|
|<a name="targetfilepath"></a>**TargetFilePath** | Mandatory| String| フォルダーまたは場所、ファイル名、拡張子が含まれる、ターゲット ファイルの正規化されたフル パス。 詳細については、「[パス構造](#path-structure)」を参照してください。 <br><br>**注**: レコードにフォルダーや場所の情報が含まれていない場合は、ここにのみファイル名を格納してください。 <br><br>例: `C:\Windows\System32\notepad.exe`|
|**TargetFilePathType** | Mandatory|Enumerated | [TargetFilePath](#targetfilepath) の型。 詳細については、「[パス構造](#path-structure)」を参照してください。    |
|**FilePath** |エイリアス | | [TargetFilePath](#targetfilepath) フィールドの別名。|
| **TargetFileMD5**| オプション| MD5|ターゲット ファイルの MD5 ハッシュ。 <br><br>例: `75a599802f1fa166cdadb360960b1dd0` |
|**TargetFileSHA1** |省略可能 |SHA1 |ターゲット ファイルの SHA-1 ハッシュ。 <br><br>例:<br> `d55c5a4df19b46db8c54`<br>`c801c4665d3338acdab0`|
|**TargetFileSHA256** | 省略可能|SHA256 |ターゲット ファイルの SHA-256 ハッシュ。 <br><br>例:<br> `e81bb824c4a09a811af17deae22f22dd`<br>`2e1ec8cbb00b22629d2899f7c68da274`  |
| **TargetFileSHA512**| 省略可能| SHA512|ソース ファイルの SHA-512 ハッシュ。 |
|**Hash**|エイリアス | |利用可能な最善のターゲット ファイルのハッシュの別名。 |
|**TargetFileSize** |オプション | Integer|ターゲット ファイルのサイズ (バイト単位)。 |
| **TargetUrl**|オプション | String|HTTP または HTTPS を使用して操作が開始されているときは、使用されている URL。 <br><br>例: `https://onedrive.live.com/?authkey=...` |
| | | | |



## <a name="path-structure"></a>パス構造

パスは、次のいずれかの形式と一致するように正規化される必要があります。 値が正規化される形式は、それぞれの **FilePathType** フィールドに反映されます。

|Type  |例  |Notes  |
|---------|---------|---------|
|**Windows ローカル**     |   `C:\Windows\System32\notepad.exe`      |      Windows のパス名は大文字と小文字が区別されないため、この型は値の大文字と小文字が区別されないことを示します。   |
|**Windows 共有**     |      `\\Documents\My Shapes\Favorites.vssx`   | Windows のパス名は大文字と小文字が区別されないため、この型は値の大文字と小文字が区別されないことを示します。        |
|**Unix**     |  `/etc/init.d/networking`       |     Unix のパス名は大文字と小文字が区別されるため、この型は値の大文字と小文字が区別されることを示します。  <br><br>- AWS S3 には、この型を使用します。 バケット名とキー名を連結してパスを作成します。 <br><br>- Azure Blob Storage のオブジェクト キーには、この型を使用します。   |
|**URL**     |  `https://1drv.ms/p/s!Av04S_*********we`       | ファイル パスが URL として利用できるときに使用します。 URL は *http* または *https* に限定されず、FTP 値を含むあらゆる値が有効です。 |
|     |         |         |



## <a name="next-steps"></a>次のステップ

詳細については、次を参照してください。

- [Microsoft Sentinel での正規化](normalization.md)
- [Microsoft Sentinel 認証正規化スキーマ リファレンス (パブリック プレビュー)](authentication-normalization-schema.md)
- [Microsoft Sentinel DNS 正規化スキーマ リファレンス](dns-normalization-schema.md)
- [Microsoft Sentinel ネットワーク正規化スキーマ リファレンス](./network-normalization-schema.md)
- [Microsoft Sentinel プロセス イベント正規化スキーマ リファレンス (パブリック プレビュー)](process-events-normalization-schema.md)
- [Microsoft Sentinel レジストリ イベント正規化スキーマ リファレンス (パブリック プレビュー)](registry-event-normalization-schema.md)
