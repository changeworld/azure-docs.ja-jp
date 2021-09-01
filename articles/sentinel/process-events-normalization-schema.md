---
title: Azure Sentinel プロセス イベント正規化スキーマ リファレンス | Microsoft Docs
description: この記事では、Azure Sentinel プロセス イベント正規化スキーマについて説明します。
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
ms.topic: reference
ms.date: 06/22/2021
ms.author: bagol
ms.openlocfilehash: f23cc4fbba1c923e2425626861acde18b20705d5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121725122"
---
# <a name="azure-sentinel-process-event-normalization-schema-reference-public-preview"></a>Azure Sentinel プロセス イベント正規化スキーマ リファレンス (パブリック プレビュー)

プロセス イベント正規化スキーマは、プロセスを実行および終了するオペレーティング システムのアクティビティを記述するために使用されます。 このようなイベントは、オペレーティング システムと、EDR (エンドポイント検出と応答) システムなどのセキュリティ システムによって報告されます。 OSSEM で定義されているプロセスは、プログラムの実行中のインスタンスを表す包含および管理オブジェクトです。 プロセスは実行されないことに注意してください。 代わりに、プロセスでは、コードを実行するスレッドを管理します。

Azure Sentinel での正規化の詳細については、「[正規化と Azure Sentinel 情報モデル (ASIM)](normalization.md)」を参照してください。

> [!IMPORTANT]
> プロセス イベント正規化スキーマは、現在パブリック プレビュー中です。
> この機能はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="parsers"></a>パーサー

Azure Sentinel には、次の組み込みの製品固有のプロセス イベント パーサーが用意されています。

- **セキュリティ イベント プロセスの作成 (イベント 4688)** 。Log Analytics エージェントまたは Azure Monitor エージェントを使用して収集されます
- **セキュリティ イベント プロセスの終了 (イベント 4689)** 。Log Analytics エージェントまたは Azure Monitor エージェントを使用して収集されます
- **Sysmon プロセスの作成 (イベント 1)** 。Log Analytics エージェントまたは Azure Monitor エージェントを使用して収集されます
- **Sysmon プロセスの終了 (イベント 5)** 。Log Analytics エージェントまたは Azure Monitor エージェントを使用して収集されます
- **Microsoft 365 Defender for Endpoints プロセスの作成**

一覧表示されたすべてのパーサーを統一し、構成済みのソース全体で必ず分析する、ソースに依存しないパーサーを使用するには、クエリで次のテーブル名を使用します。

- プロセス作成情報を必要とするクエリでは **imProcessCreate**。 これが最も一般的なケースです。
- プロセス終了情報を必要とするクエリでは **imProcessTerminate**。
- プロセス作成と終了の両方の情報を必要とするクエリでは **imProcessEvents**。 このような場合、`EventType` フィールドを使用するとイベントを区別できます。それぞれ `ProcessCreate` または `ProcessTerminate` に設定されます。 プロセス終了イベントには、通常、プロセス作成イベントよりもはるかに少ない情報が含まれます。

[Azure Sentinel の GitHub リポジトリ](https://aka.ms/AzSentinelProcessEvents)から、[ソースに依存しないパーサーとソース固有のパーサー](normalization.md#parsers)をデプロイします。

## <a name="add-your-own-normalized-parsers"></a>独自の正規化されたパーサーを追加する

[プロセス イベント](normalization.md#the-process-entity)情報モデルのカスタム パーサーを実装する場合は、構文 `imProcess<Type><vendor><Product>` を使用して KQL 関数に名前を付けます。ここで、`Type` は、`Create`、`Terminate`、またはパーサーで作成と終了の両方のイベントを実装する場合は `Event` です。

KQL 関数を `imProcess<Type>` と `imProcess` のソースに依存しないパーサーに追加して、[ファイル イベント](normalization.md#the-process-entity) モデルが使用されるあらゆるコンテンツで、新しいパーサーも使用されるようにします。

## <a name="normalized-content-for-process-activity-data"></a>プロセス アクティビティ データの正規化されたコンテンツ

次の Azure Sentinel のコンテンツは、Azure Sentinel 情報モデルを使用して正規化されたあらゆるプロセス アクティビティで機能します。

- **分析ルール**:

    - Probable AdFind Recon Tool の使用 (正規化されたプロセス イベント)
    - Base64 でエンコードされた Windows プロセスのコマンド ライン (正規化されたプロセス イベント)
    - ごみ箱内のマルウェア (正規化されたプロセス イベント)
    - NOBELIUM - vbscript の疑わしい rundll32.exe の実行 (正規化されたプロセス イベント)
    - SUNBURST の疑わしい SolarWinds 子プロセス (正規化されたプロセス イベント)

    詳細については、「[脅威を検出するためのカスタム分析規則を作成する](detect-threats-custom.md)」をご覧ください。

-   **ハンティング クエリ**:
    - Cscript スクリプトの毎日の概要の内訳 (正規化されたプロセス イベント)
    - ユーザーとグループの列挙 (正規化されたプロセス イベント)
    - Exchange PowerShell スナップインの追加 (正規化されたプロセス イベント)
    - メールボックスのエクスポートとエクスポートの削除を行うホスト (正規化されたプロセス イベント)
    - Invoke-PowerShellTcpOneLine の使用 (正規化されたプロセス イベント)
    - Base64 の Nishang リバース TCP シェル (正規化されたプロセス イベント)
    - 一般的でない/ドキュメントに記載されていないコマンド ライン スイッチを使用して作成されたユーザーの概要 (正規化されたプロセス イベント)
    - Powercat のダウンロード (正規化されたプロセス イベント)
    - PowerShell のダウンロード (正規化されたプロセス イベント)
    - 特定のホストのプロセスのエントロピ (正規化されたプロセス イベント)
    - SolarWinds インベントリ (正規化されたプロセス イベント)
    - Adfind ツールを使用した疑わしい列挙型 (正規化されたプロセス イベント)
    - 一般的でないプロセス - 下位 5% (正規化されたプロセス イベント)
    - Windows システムのシャットダウン/再起動 (正規化されたプロセス イベント)
    - Certutil (LOLBins と LOLScripts、正規化されたプロセス イベント)
    - Rundll32 (LOLBins と LOLScripts、正規化されたプロセス イベント)

    詳細については、「[Azure Sentinel で脅威を検出する](hunting.md)」を参照してください。

## <a name="schema-details"></a>スキーマの詳細

プロセス イベント情報モデルは、[OSSEM プロセス エンティティ スキーマ](https://github.com/OTRF/OSSEM/blob/master/docs/cdm/entities/process.md)に対応しています。

### <a name="log-analytics-fields"></a>Log Analytics のフィールド

次のフィールドは、レコードごとに Log Analytics によって生成され、カスタム コネクタの作成時にオーバーライドできます。

| フィールド         | 型     | 考察 (Discussion)      |
| ------------- | -------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| <a name="timegenerated"></a>**TimeGenerated** | DATETIME | イベントがレポート デバイスによって生成された時刻。|
| **_ResourceId**   | guid     | レポート デバイスまたはサービスの Azure リソース ID、または Syslog、CEF、または WEF を使用して転送されたイベントの場合はログ フォワーダー リソース ID。 |
| | | |

> [!NOTE]
> Log Analytics では、セキュリティのユース ケースとあまり関連しない他のフィールドも追加します。 詳細については、「[Azure Monitor ログ内の標準列](../azure-monitor/logs/log-standard-columns.md)」を参照してください。
>

## <a name="event-fields"></a>イベント フィールド

イベント フィールドは、すべてのスキーマに共通であり、アクティビティ自体とレポート デバイスを記述します。

| フィールド               | クラス       | 型       |  説明        |
|---------------------|-------------|------------|--------------------|
| **EventMessage**        | 省略可能    | String     |     レコードに含まれるか、レコードから生成された一般的なメッセージまたは説明です。   |
| **EventCount**          | Mandatory   | 整数型    |     レコードによって記述されるイベントの数。 <br><br>この値は、ソースが集計に対応しており、1 つのレコードが複数のイベントを表す場合があるときに使用されます。 <br><br>その他のソースの場合は、`1` に設定します。   |
| **EventStartTime**      | Mandatory   | 日付/時刻  |      ソースが集計に対応しており、レコードが複数のイベントを表す場合、このフィールドでは最初にイベントが生成された時間を指定します。 それ以外の場合、このフィールドは [TimeGenerated](#timegenerated) フィールドの別名になります。 |
| **EventEndTime**        | Mandatory   | エイリアス      |      [TimeGenerated](#timegenerated) フィールドの別名。    |
| **EventType**           | Mandatory   | Enumerated |    レコードによって報告される操作を記述します。 <br><br>プロセス レコードの場合、次のような値がサポートされます。 <br>- `ProcessCreated` <br>- `ProcessTerminated` |
| **EventResult**         | Mandatory   | Enumerated |  イベントの結果を記述し、次のサポートされている値の 1 つに正規化されます。 <br><br>- `Success`<br>- `Partial`<br>- `Failure`<br>- `NA` (適用外) <br><br>ソースから **EventResultDetails** フィールドの値のみが提供される場合があり、**EventResult** の値を取得するには分析が行われる必要があります。<br><br>プロセス イベントで報告されるのは一般に成功だけであることに注意してください。 |
| **EventOriginalUid**    | 省略可能    | String     |   元のレコードの一意の ID (ソースによって提供されている場合)。<br><br>例: `69f37748-ddcd-4331-bf0f-b137f1ea83b`|
| **EventOriginalType**   | 省略可能    | String     |   元のイベントの種類または ID (ソースによって提供されている場合)。<br><br>例: `4688`|
| <a name ="eventproduct"></a>**EventProduct**        | Mandatory   | String     |             イベントを生成している製品。 <br><br>例: `Sysmon`<br><br>**注**: このフィールドはソース レコードでは使用できない場合があります。 その場合、このフィールドはパーサーによって設定される必要があります。           |
| **EventProductVersion** | 省略可能    | String     | イベントを生成している製品のバージョン。 <br><br>例: `12.1`      |
| **EventVendor**         | Mandatory   | String     |           イベントを生成している製品のベンダー。 <br><br>例: `Microsoft`  <br><br>**注**: このフィールドはソース レコードでは使用できない場合があります。 その場合、このフィールドはパーサーによって設定される必要があります。  |
| **EventSchemaVersion**  | Mandatory   | String     |    スキーマのバージョン。 ここに記載されているスキーマのバージョンは `0.1` です         |
| **EventReportUrl**      | 省略可能    | String     | あるリソースのイベントに指定された、そのイベントに関する追加情報を提供する URL。|
| **Dvc** | Mandatory       | String     |                イベントが発生したデバイスの一意の識別子。 <br><br>このフィールドの別名は、[DvcId](#dvcid)、[DvcHostname](#dvchostname)、または [DvcIpAddr](#dvcipaddr) フィールドになる場合があります。 明確なデバイスがないクラウド リソースの場合は、[Event Product](#eventproduct) フィールドと同じ値を使用します。            |
| <a name ="dvcipaddr"></a>**DvcIpAddr**           | 推奨 | IP アドレス |         プロセス イベントが発生したデバイスの IP アドレス。  <br><br>例: `45.21.42.12`    |
| <a name ="dvchostname"></a>**DvcHostname**         | 推奨 | Hostname (ホスト名)   |               プロセス イベントが発生したデバイスのホスト名。 <br><br>例: `ContosoDc.Contoso.Azure`               |
| <a name ="dvcid"></a>**DvcId**               | 省略可能    | String     |  プロセス イベントが発生したデバイスの一意の ID。 <br><br>例: `41502da5-21b7-48ec-81c9-baeea8d7d669`   |
| **DvcMacAddr**          | 省略可能    | MAC        |   プロセス イベントが発生したデバイスの MAC。  <br><br>例: `00:1B:44:11:3A:B7`       |
| **DvcOs**               | 省略可能    | String     |         プロセス イベントが発生したデバイスで実行されているオペレーティング システム。    <br><br>例: `Windows`    |
| **DvcOsVersion**        | 省略可能    | String     |   プロセス イベントが発生したデバイスのオペレーティング システムのバージョン。 <br><br>例: `10` |
| **AdditionalFields**    | 省略可能    | 動的    | ソースから保持する必要のある追加情報が提供される場合は、元のフィールド名をそのまま使用するか、動的な **AdditionalFields** フィールドを作成し、それに追加情報をキーと値のペアとして追加します。    |
| | | | |

### <a name="process-event-specific-fields"></a>プロセス イベント固有のフィールド

下の表に示すフィールドはプロセス イベントに固有ですが、他のスキーマのフィールドに類似しており、同様の名前付け規則に従います。

プロセス イベント スキーマでは、プロセスの作成と終了のアクティビティの中心である次のエンティティを参照します。

- **Actor**。 プロセスの作成または終了を開始したユーザー。
- **ActingProcess**。 プロセスの作成または終了を開始するために Actor によって使用されるプロセス。
- **TargetProcess**。 新しいプロセス。
- **TargetUser**。 新しいプロセスを作成するために使用される資格情報を持つユーザー。
- **ParentProcess**。 Actor プロセスを開始したプロセス。

| フィールド          | クラス        | 型       | 説明   |
|---------------|--------------|------------|-----------------|
| **User**           | エイリアス        |            | [TargetUsername](#targetusername) の別名。 <br><br>例: `CONTOSO\dadmin`     |
| **Process**        | エイリアス        |            | [TargetProcessName](#targetprocessname) の別名 <br><br>例: `C:\Windows\System32\rundll32.exe`|
| **CommandLine**    | エイリアス        |            |     [TargetProcessCommandLine](#targetprocesscommandline) の別名  |
| **Hash**           | エイリアス        |            |       利用可能な最善のハッシュの別名。 |
| <a name="actorusername"></a>**ActorUsername**  | Mandatory    | String     | イベントを開始したユーザーのユーザー名。 <br><br>例: `CONTOSO\WIN-GG82ULGC9GO$`     |
| **ActorUsernameType**              | Mandatory    | Enumerated |   [ActorUsername](#actorusername) フィールドに格納されているユーザー名の種類を指定します。 詳細については、「[ユーザー エンティティ](normalization.md#the-user-entity)」を参照してください。 <br><br>例: `Windows`       |
| <a name="actoruserid"></a>**ActorUserId**    | 推奨  | String     |   Actor の一意の ID。 具体的な ID は、イベントが生成されるシステムによって異なります。 詳細については、「[ユーザー エンティティ](normalization.md#the-user-entity)」を参照してください。  <br><br>例: `S-1-5-18`    |
| **ActorUserIdType**| 推奨  | String     |  [ActorUserId](#actoruserid) フィールドに格納されている ID の種類。 詳細については、「[ユーザー エンティティ](normalization.md#the-user-entity)」を参照してください。 <br><br>例: `SID`         |
| **ActorSessionId** | 省略可能     | String     |   Actor のログイン セッションの一意の ID。  <br><br>例: `999`<br><br>**注**: さまざまなシステムに対応するために、型は "*文字列*" として定義されますが、Windows ではこの値は数値である必要があります。 <br><br>Windows マシンを使用しており、かつ別の型を使用した場合は、必ず値を変換してください。 たとえば、16 進数の値を使用した場合は、10 進数の値に変換します。   |
| **ActingProcessCommandLine**       | 省略可能     | String     |   実行プロセスを実行するために使用するコマンド ライン。 <br><br>例: `"choco.exe" -v`    |
| **ActingProcessName**              | 省略可能     | string     |   実行プロセスの名前。 この名前は通常、プロセスの仮想アドレス空間にマップされる初期コードとデータを定義するために使用される、イメージまたは実行可能ファイルから派生します。<br><br>例: `C:\Windows\explorer.exe`  |
| **ActingProcessFileCompany**       | 省略可能     | String     |           実行プロセス イメージ ファイルを作成した会社。  <br><br> 例: `Microsoft`    |
| **ActingProcessFileDescription**   | 省略可能     | String     |  実行プロセス イメージ ファイルのバージョン情報に埋め込まれた説明。 <br><br>例: `Notepad++ : a free (GPL) source code editor` |
| **ActingProcessFileProduct**       | 省略可能     | String     |実行プロセス イメージ ファイルのバージョン情報からの製品名。 <br><br> 例: `Notepad++`           |
| **ActingProcessFileVersion**       | 省略可能     | String     |               実行プロセス イメージ ファイルのバージョン情報からの製品バージョン。 <br><br>例: `7.9.5.0`   |
| **ActingProcessFileInternalName**  | 省略可能     | String     |      実行プロセス イメージ ファイルのバージョン情報からの製品内部ファイル名。 |
| **ActingProcessFileOriginalName** | 省略可能     | String     |実行プロセス イメージ ファイルのバージョン情報からの製品の元のファイル名。       <br><br> 例: `Notepad++.exe` |
| **ActingProcessIsHidden**          | 省略可能     | ブール型    |      実行プロセスが非表示モードかどうかを示します。  |
| **ActingProcessInjectedAddress**   | 省略可能     | String     |      責任ある実行プロセスが格納されているメモリ アドレス。           |
| **ActingProcessId**| Mandatory    | INT        | 実行プロセスのプロセス ID (PID)。<br><br>例: `48610176`           <br><br>**注**: さまざまなシステムに対応するために、型は "*文字列*" として定義されますが、Windows と Linux ではこの値は数値である必要があります。 <br><br>Windows または Linux のマシンを使用しており、かつ別の型を使用した場合は、必ず値を変換してください。 たとえば、16 進数の値を使用した場合は、10 進数の値に変換します。    |
| **ActingProcessGuid**              | 省略可能     | string     |  実行プロセスの生成された一意識別子 (GUID)。 システム間でプロセスを識別できます。  <br><br> 例: `EF3BD0BD-2B74-60C5-AF5C-010000001E00`            |
| **ActingProcessIntegrityLevel**    | 省略可能     | String     |       すべてのプロセスには、トークンで表される整合性レベルがあります。 整合性レベルは、保護またはアクセスのプロセス レベルを決定します。 <br><br> Windows では、整合性レベルとして **low**、**medium**、**high**、**system** を定義します。 標準ユーザーには **medium** の整合性レベルが指定され、管理者特権ユーザーには **high** の整合性が指定されます。 <br><br> 詳細については、[必須の整合性コントロール - Win32 アプリ](/windows/win32/secauthz/mandatory-integrity-control)に関するページを参照してください。 |
| **ActingProcessMD5**               | 省略可能     | String     |実行プロセス イメージ ファイルの MD5 ハッシュ。  <br><br>例: `75a599802f1fa166cdadb360960b1dd0`|
| **ActingProcessSHA1**              | 省略可能     | SHA1       | 実行プロセス イメージ ファイルの SHA-1 ハッシュ。             <br><br>  例: `d55c5a4df19b46db8c54c801c4665d3338acdab0`  |
| **ActingProcessSHA256**            | 省略可能     | SHA256     | 実行プロセス イメージ ファイルの SHA-256 ハッシュ。    <br><br> 例: <br> `e81bb824c4a09a811af17deae22f22dd`<br>`2e1ec8cbb00b22629d2899f7c68da274`   |
| **ActingProcessSHA512**            | 省略可能     | SHA521     |       実行プロセス イメージ ファイルの SHA-512 ハッシュ。       |
| **ActingProcessIMPHASH**           | 省略可能     | String     |       実行プロセスで使用されるすべてのライブラリ DLL のインポート ハッシュ。    |
| **ActingProcessCreationTime**      | 省略可能     | DateTime   |       実行プロセスが開始された日時。 |
| **ActingProcessTokenElevation**    | 省略可能     | String     | 実行プロセスに適用されたユーザー アクセス制御 (UAC) 特権の昇格の有無を示すトークン。   <br><br>例: `None`|
| **ActingProcessFileSize**          | 省略可能     | Long       |      処理プロセスを実行したファイルのサイズ。   |
| **ParentProcessName**              | 省略可能     | string     | 親プロセスの名前。 この名前は通常、プロセスの仮想アドレス空間にマップされる初期コードとデータを定義するために使用される、イメージまたは実行可能ファイルから派生します。<br><br>例: `C:\Windows\explorer.exe` |
| **ParentProcessFileCompany**       | 省略可能     | String     |親プロセス イメージ ファイルを作成した会社の名前。            <br><br>    例: `Microsoft`   |
| **ParentProcessFileDescription**   | 省略可能     | String     |  親プロセス イメージ ファイルのバージョン情報からの説明。    <br><br>例: `Notepad++ : a free (GPL) source code editor`|
| **ParentProcessFileProduct**       | 省略可能     | String     |親プロセス イメージ ファイルのバージョン情報からの製品名。    <br><br>  例: `Notepad++`  |
| **ParentProcessFileVersion**       | 省略可能     | String     | 親プロセス イメージ ファイルのバージョン情報からの製品バージョン。    <br><br> 例: `7.9.5.0` |
| **ParentProcessIsHidden**          | 省略可能     | ブール型    |   親プロセスが非表示モードかどうかを示します。  |
| **ParentProcessInjectedAddress**   | 省略可能     | String     |    責任ある親プロセスが格納されているメモリ アドレス。           |
| **ParentProcessId**| Mandatory    | 整数 (integer)    | 親プロセスのプロセス ID (PID)。   <br><br>     例: `48610176`    |
| **ParentProcessGuid**              | 省略可能     | String     |  親プロセスの生成された一意識別子 (GUID)。  システム間でプロセスを識別できます。    <br><br> 例: `EF3BD0BD-2B74-60C5-AF5C-010000001E00` |
| **ParentProcessIntegrityLevel**    | 省略可能     | String     |   すべてのプロセスには、トークンで表される整合性レベルがあります。 整合性レベルは、保護またはアクセスのプロセス レベルを決定します。 <br><br> Windows では、整合性レベルとして **low**、**medium**、**high**、**system** を定義します。 標準ユーザーには **medium** の整合性レベルが指定され、管理者特権ユーザーには **high** の整合性が指定されます。 <br><br> 詳細については、[必須の整合性コントロール - Win32 アプリ](/windows/win32/secauthz/mandatory-integrity-control)に関するページを参照してください。 |
| **ParentProcessMD5**               | 省略可能     | MD5        | 親プロセス イメージ ファイルの MD5 ハッシュ。  <br><br>例: `75a599802f1fa166cdadb360960b1dd0`|
| **ParentProcessSHA1**              | 省略可能     | SHA1       | 親プロセス イメージ ファイルの SHA-1 ハッシュ。       <br><br> 例: `d55c5a4df19b46db8c54c801c4665d3338acdab0`   |
| **ParentProcessSHA256**            | 省略可能     | SHA256     |親プロセス イメージ ファイルの SHA-256 ハッシュ。      <br><br>  例: <br> `e81bb824c4a09a811af17deae22f22dd`<br>`2e1ec8cbb00b22629d2899f7c68da274` |
| **ParentProcessSHA512**            | 省略可能     | SHA512     |    親プロセス イメージ ファイルの SHA-512 ハッシュ。       |
| **ParentProcessIMPHASH**           | 省略可能     | String     |    親プロセスで使用されるすべてのライブラリ DLL のインポート ハッシュ。    |
| **ParentProcessTokenElevation**    | 省略可能     | String     |親プロセスに適用されたユーザー アクセス制御 (UAC) 特権の昇格の有無を示すトークン。     <br><br>  例: `None` |
| **ParentProcessCreationTime**      | 省略可能    | DateTime   |    親プロセスが開始された日時。 |
| <a name="targetusername"></a>**TargetUsername** | プロセス作成イベントの場合は必須です。 | String     | ターゲット ユーザーのユーザー名。  <br><br>例: `CONTOSO\WIN-GG82ULGC9GO$`      |
| **TargetUsernameType**             | プロセス作成イベントの場合は必須です。   | Enumerated | [TargetUsername](#targetusername) フィールドに格納されているユーザー名の種類を指定します。 詳細については、「[ユーザー エンティティ](normalization.md#the-user-entity)」を参照してください。          <br><br>  例: `Windows`        |
|<a name="targetuserid"></a> **TargetUserId**   | 推奨 | String     | ターゲット ユーザーの一意の ID。 具体的な ID は、イベントが生成されるシステムによって異なります。 詳細については、「[ユーザー エンティティ](normalization.md#the-user-entity)」を参照してください。            <br><br> 例: `S-1-5-18`    |
| **TargetUserIdType**               | 推奨 | String     | [TargetUserId](#targetuserid) フィールドに格納されているユーザー ID の種類。 詳細については、「[ユーザー エンティティ](normalization.md#the-user-entity)」を参照してください。            <br><br> 例: `SID`  |
| **TargetUserSessionId**            | 省略可能     | String     |ターゲット ユーザーのログイン セッションの一意の ID。 <br><br>例: `999`          <br><br>**注**: さまざまなシステムに対応するために、型は "*文字列*" として定義されますが、Windows ではこの値は数値である必要があります。 <br><br>Windows または Linux のマシンを使用しており、かつ別の型を使用した場合は、必ず値を変換してください。 たとえば、16 進数の値を使用した場合は、10 進数の値に変換します。     |
| <a name="targetprocessname"></a>**TargetProcessName**              | Mandatory    | string     |ターゲット プロセスの名前。 この名前は通常、プロセスの仮想アドレス空間にマップされる初期コードとデータを定義するために使用される、イメージまたは実行可能ファイルから派生します。   <br><br>     例: `C:\Windows\explorer.exe`     |
| **TargetProcessFileCompany**       | 省略可能     | String     |ターゲット プロセス イメージ ファイルを作成した会社の名前。   <br><br>   例: `Microsoft` |
| **TargetProcessFileDescription**   | 省略可能     | String     | ターゲット プロセス イメージ ファイルのバージョン情報の説明。   <br><br>例: `Notepad++ : a free (GPL) source code editor` |
| **TargetProcessFileProduct**       | 省略可能     | String     |ターゲット プロセス イメージ ファイルのバージョン情報の製品名。  <br><br>  例: `Notepad++`  |
| **TargetProcessFileSize**          | 省略可能     | String     |    イベントを処理するプロセスを実行したファイルのサイズ。 |
| **TargetProcessFileVersion**       | 省略可能     | String     |ターゲット プロセス イメージ ファイルのバージョン情報の製品バージョン。   <br><br>  例: `7.9.5.0` |
| **TargetProcessFileInternalName**  |    省略可能          | String  |   ターゲット プロセスのイメージ ファイルのバージョン情報の製品内部ファイル名。 |
| **TargetProcessFileOriginalName** |       省略可能       | String   |   ターゲット プロセスのイメージ ファイルのバージョン情報からの製品の元のファイル名。 |
| **TargetProcessIsHidden**          | 省略可能     | ブール型    |   ターゲット プロセスが非表示モードかどうかを示します。  |
| **TargetProcessInjectedAddress**   | 省略可能     | String     |    責任あるターゲット プロセスが格納されているメモリ アドレス。           |
| **TargetProcessMD5**               | 省略可能     | MD5        | ターゲット プロセス イメージ ファイルの MD5 ハッシュ。   <br><br> 例: `75a599802f1fa166cdadb360960b1dd0`|
| **TargetProcessSHA1**              | 省略可能     | SHA1       | ターゲット プロセス イメージ ファイルの SHA-1 ハッシュ。       <br><br>  例: `d55c5a4df19b46db8c54c801c4665d3338acdab0`   |
| **TargetProcessSHA256**            | 省略可能     | SHA256     | ターゲット プロセス イメージ ファイルの SHA-256 ハッシュ。      <br><br>  例: <br> `e81bb824c4a09a811af17deae22f22dd`<br>`2e1ec8cbb00b22629d2899f7c68da274` |
| **TargetProcessSHA512**            | 省略可能     | SHA512     |   ターゲット プロセス イメージ ファイルの SHA-512 ハッシュ。       |
| **TargetProcessIMPHASH**           | 省略可能     | String     |    ターゲット プロセスで使用されるすべてのライブラリ DLL のインポート ハッシュ。    |
| <a name="targetprocesscommandline"></a> **TargetProcessCommandLine**       | Mandatory    | String     | ターゲット プロセスを実行するために使用するコマンド ライン。   <br><br> 例: `"choco.exe" -v`  |
| <a name="targetprocesscurrentdirectory"></a> **TargetProcessCurrentDirectory**       | 省略可能    | String     | ターゲット プロセスが実行される現在のディレクトリ。  <br><br> 例: `c:\windows\system32`  |
| **TargetProcessCreationTime**      | Mandatory    | DateTime   |    ターゲット プロセス イメージ ファイルのバージョン情報からの製品バージョン。   |
| **TargetProcessId**| Mandatory    | String     |  ターゲット プロセスのプロセス ID (PID)。     <br><br>例: `48610176`<br><br>**注**: さまざまなシステムに対応するために、型は "*文字列*" として定義されますが、Windows と Linux ではこの値は数値である必要があります。 <br><br>Windows または Linux のマシンを使用しており、かつ別の型を使用した場合は、必ず値を変換してください。 たとえば、16 進数の値を使用した場合は、10 進数の値に変換します。         |
| **TargetProcessGuid**              | 省略可能    | String     |ターゲット プロセスの生成された一意識別子 (GUID)。 システム間でプロセスを識別できます。   <br><br>  例: `EF3BD0BD-2B74-60C5-AF5C-010000001E00`  |
| **TargetProcessIntegrityLevel**    | 省略可能    | String     |   すべてのプロセスには、トークンで表される整合性レベルがあります。 整合性レベルは、保護またはアクセスのプロセス レベルを決定します。 <br><br> Windows では、整合性レベルとして **low**、**medium**、**high**、**system** を定義します。 標準ユーザーには **medium** の整合性レベルが指定され、管理者特権ユーザーには **high** の整合性が指定されます。 <br><br> 詳細については、[必須の整合性コントロール - Win32 アプリ](/windows/win32/secauthz/mandatory-integrity-control)に関するページを参照してください。 |
| **TargetProcessTokenElevation**    | 省略可能    | String     |作成または終了されたプロセスに適用されたユーザー アクセス制御 (UAC) 特権の昇格の有無を示すトークンの種類。   <br><br>    例: `None`     |
| | | | |



## <a name="next-steps"></a>次のステップ

詳細については、次を参照してください。

- [Azure Sentinel での正規化](normalization.md)
- [Azure Sentinel 認証正規化スキーマ リファレンス (パブリック プレビュー)](authentication-normalization-schema.md)
- [Azure Sentinel DNS 正規化スキーマ リファレンス](dns-normalization-schema.md)
- [Azure Sentinel ファイル イベント正規化スキーマ リファレンス (パブリック プレビュー)](file-event-normalization-schema.md)
- [Azure Sentinel ネットワーク正規化スキーマ リファレンス](normalization-schema.md)
- [Azure Sentinel レジストリ イベント正規化スキーマ リファレンス (パブリック プレビュー)](registry-event-normalization-schema.md)
