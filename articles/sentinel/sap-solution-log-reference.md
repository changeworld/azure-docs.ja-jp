---
title: Microsoft Azure Sentinel SAP ソリューション - 利用可能なログのリファレンス | Microsoft Docs
description: Microsoft Azure Sentinel SAP ソリューションから利用できる SAP ログについて説明します。
author: batamig
ms.author: bagold
ms.topic: reference
ms.custom: mvc, ignite-fall-2021
ms.date: 11/09/2021
ms.openlocfilehash: ee74d7658be01c2859d860f4c53b7e8e2b0e1b1a
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132711832"
---
# <a name="microsoft-sentinel-sap-solution-logs-reference-public-preview"></a>Microsoft Azure Sentinel SAP ソリューションのログ リファレンス (パブリック プレビュー)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
> Microsoft Sentinel SAP ソリューションは、現在プレビュー段階です。 [Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)には、ベータ版、プレビュー版、またはまだ一般提供されていない Azure 機能に適用される追加の法律条項が含まれています。
>
> 下に示す一部のログは、既定では Microsoft Sentinel に送信されませんが、必要に応じて手動で追加できます。 詳細については、「[Microsoft Sentinel に送信される SAP ログを定義する](sap-solution-deploy-alternate.md#define-the-sap-logs-that-are-sent-to-microsoft-sentinel)」を参照してください。
>

この記事では、Microsoft Azure Sentinel SAP データ コネクタから利用できる SAP ログについて説明します。Microsoft Azure Sentinel におけるテーブル名やログの目的、詳細なログ スキーマについても取り上げます。 スキーマのフィールドの説明は、関連 [SAP ドキュメント](https://help.sap.com/)におけるフィールドの説明に準じています。

この記事は、上級 SAP ユーザーを対象にしています。

## <a name="abap-application-log"></a>ABAP アプリケーション ログ

- **Microsoft Azure Sentinel での名前**: `ABAPAppLog_CL`

- **関連する SAP ドキュメント**: [SAP ヘルプ ポータル](https://help.sap.com/viewer/56bf1265a92e4b4d9a72448c579887af/7.5.7/en-US/c769bcc9f36611d3a6510000e835363f.html)

- **ログの目的**: 必要に応じて後で再現できるよう、アプリケーション実行の進行状況を記録します。

    XBP インターフェイスの標準サービスに基づくカスタム サービスと RFC を併用することで利用できます。 このログは、クライアントごとに生成されます。

### <a name="abapapplog_cl-log-schema"></a>ABAPAppLog_CL ログ スキーマ

| フィールド                 | Description                    |
| --------------------- | ------------------------------ |
| AppLogDateTime        | アプリケーション ログの日時      |
| CallbackProgram       | コールバック プログラム               |
| CallbackRoutine       | コールバック ルーチン               |
| CallbackType          | コールバックの種類                  |
| ClientID              | ABAP クライアント ID (MANDT)         |
| ContextDDIC           | コンテキストの DDIC 構造         |
| ExternalID            | 外部ログ ID                |
| Host                  | Host                           |
| インスタンス              | 次の構文の ABAP インスタンス: `<HOST>_<SYSID>_<SYSNR>`              |
| InternalMessageSerial | アプリケーション ログ メッセージのシリアル |
| LevelofDetail         | 詳細レベル                |
| LogHandle             | アプリケーション ログのハンドル         |
| LogNumber             | ログ番号                     |
| MessageClass          | message クラス                  |
| MessageNumber         | メッセージ番号                 |
| [MessageText]           | [メッセージ テキスト]                   |
| MessageType           | メッセージの種類                   |
| Object                | アプリケーション ログ オブジェクト         |
| OperationMode         | 操作モード                 |
| ProblemClass          | 問題クラス                  |
| ProgramName           | プログラム名                   |
| SortCriterion         | 並べ替え条件                 |
| StandardText          | 標準テキスト                  |
| SubObject             | アプリケーション ログのサブオブジェクト     |
| SystemID              | システム ID                      |
| SystemNumber          | システム数                  |
| TransactionCode       | トランザクション コード               |
| User                  | User                           |
| UserChange            | ユーザーの変更                    |
| | |



## <a name="abap-change-documents-log"></a>ABAP 変更文書ログ

- **Microsoft Azure Sentinel での名前**: `ABAPChangeDocsLog_CL`

- **関連する SAP ドキュメント**: [SAP ヘルプ ポータル](https://help.sap.com/viewer/6f51f5216c4b10149358d088a0b7029c/7.01.22/en-US/b8686150ed102f1ae10000000a44176f.html)

- **ログの目的**: 次の情報を記録します。

    - 変更文書におけるビジネス データ オブジェクトに対する SAP NetWeaver Application Server (AS) ABAP ログの変更。

    - SAP システム内のその他のエンティティ (ユーザー データ、ロール、アドレスなど)。

    標準サービスに基づくカスタム サービスと RFC を併用することで利用できます。 このログは、クライアントごとに生成されます。

### <a name="abapchangedocslog_cl-log-schema"></a>ABAPChangeDocsLog_CL ログ スキーマ


| フィールド                    | Description                 |
| ------------------------ | ---------------------------- |
| ActualChangeNum          | 実際の変更番号         |
| ChangedTableKey          | 変更されたテーブル キー            |
| ChangeNumber             | 変更番号                |
| ClientID                 | ABAP クライアント ID (MANDT)       |
| CreatedfromPlannedChange | 予定された変更から次の構文で作成されます: `(‘X’ , ‘ ‘)`|
| CurrencyKeyNew           | 通貨キー: 新しい値      |
| CurrencyKeyOld           | 通貨キー: 古い値      |
| FieldName                | フィールド名                   |
| FlagText                 | フラグ テキスト                    |
| Host                     | Host                         |
| インスタンス                 | 次の構文の ABAP インスタンス: `<HOST>_<SYSID>_<SYSNR>` |
| 言語                 | 言語                     |
| ObjectClass              | オブジェクト クラス (`BELEG`、`BPAR`、`PFCG`、`IDENTITY`) |
| ObjectID                 | オブジェクト ID  |
| PlannedChangeNum         | 予定された変更番号  |
| SystemID                 | システム ID    |
| SystemNumber             | システム数     |
| TableName                | テーブル名        |
| TransactionCode          | トランザクション コード   |
| TypeofChange_Header      | 変更のヘッダー タイプ (例): <br>`U` = 変更、`I` = 挿入、`E` = 単一文書の削除、`D` = 削除、`J` = 単一文書の挿入 |
| TypeofChange_Item        | 変更のアイテム タイプ (例): <br>`U` = 変更、`I` = 挿入、`E` = 単一文書の削除、`D` = 削除、`J` = 単一文書の挿入 |
| UOMNew                   | 測定単位: 新しい値  |
| UOMOld                   | 測定単位: 古い値 |
| User                     | User  |
| ValueNew                 | フィールドの内容: 新しい値 |
| ValueOld                 | フィールドの内容: 古い値 |
| バージョン                  | バージョン          |
| | |

## <a name="abap-cr-log"></a>ABAP CR ログ

- **Microsoft Azure Sentinel での名前**: `ABAPCRLog_CL`

- **関連する SAP ドキュメント**: [SAP ヘルプ ポータル](https://help.sap.com/viewer/56bf1265a92e4b4d9a72448c579887af/7.5.7/en-US/c769bcd5f36611d3a6510000e835363f.html)

- **ログの目的**: Change & Transport System (CTS) のログ、たとえば変更が加えられたディレクトリ オブジェクトやカスタマイズを含みます。

    標準テーブルと標準サービスに基づくカスタム サービスと RFC を併用することで利用できます。 このログは、すべてのクライアントにわたるデータを使用して生成されます。

> [!NOTE]
> アプリケーション ログ、変更文書、テーブルの記録に加え、Change & Transport System を使用して運用システムに行うあらゆる変更が CTS ログと TMS ログに記録されます。
>


### <a name="abapcrlog_cl-log-schema"></a>ABAPCRLog_CL ログ スキーマ

| フィールド        | 説明                       |
| ------------ | --------------------------------- |
| カテゴリ     | カテゴリ (ワークベンチ、カスタマイズ) |
| ClientID     | ABAP クライアント ID (MANDT)            |
| 説明  | 説明                       |
| Host         | Host                              |
| インスタンス     | 次の構文の ABAP インスタンス: `<HOST>_<SYSID>_<SYSNR>` |
| ObjectName   | オブジェクト名                       |
| ObjectType   | オブジェクトの種類                       |
| 所有者        | 所有者                             |
| 要求      | 変更要求                    |
| Status       | Status                            |
| SystemID     | システム ID                         |
| SystemNumber | システム数                     |
| TableKey     | テーブル キー                         |
| TableName    | テーブル名                        |
| ViewName     | ビューの名前                         |
| | |

## <a name="abap-db-table-data-log"></a>ABAP DB テーブルデータ ログ

このログを Microsoft Sentinel に送信するには、[**systemconfig.ini** ファイルに手動で追加](sap-solution-deploy-alternate.md#define-the-sap-logs-that-are-sent-to-microsoft-sentinel)する必要があります。

- **Microsoft Azure Sentinel での名前**: `ABAPTableDataLog_CL`

- **関連する SAP ドキュメント**: [SAP ヘルプ ポータル](https://help.sap.com/viewer/56bf1265a92e4b4d9a72448c579887af/7.5.7/en-US/c769bcd2f36611d3a6510000e835363f.html)

- **ログの目的**: 監査にとってきわめて重要なテーブルや監査の対象になりやすいテーブルのログ記録に対応します。

    カスタム サービスと RFC を併用することで利用できます。 このログは、すべてのクライアントにわたるデータを使用して生成されます。

### <a name="abaptabledatalog_cl-log-schema"></a>ABAPTableDataLog_CL ログ スキーマ

| フィールド            | Description                           |
| ---------------- | ------------------------------------- |
| DBLogID          | DB ログ ID                             |
| Host             | Host                                  |
| インスタンス         | 次の構文の ABAP インスタンス: `<HOST>_<SYSID>_<SYSNR>` |
| 言語         | 言語                              |
| LogKey           | ログ キー                               |
| NewValue         | フィールドの新しい値                       |
| OldValue         | フィールドの古い値                       |
| OperationTypeSQL | 操作の種類 (`Insert`、`Update`、`Delete`) |
| プログラム          | プログラム名                          |
| SystemID         | システム ID                             |
| SystemNumber     | システム数                         |
| TableField       | テーブルのフィールド                           |
| TableName        | テーブル名                            |
| TransactionCode  | トランザクション コード                      |
| UserName         | User                                  |
| VersionNumber    | バージョン番号                        |
| | |

## <a name="abap-gateway-log"></a>ABAP ゲートウェイ ログ

このログを Microsoft Sentinel に送信するには、[**systemconfig.ini** ファイルに手動で追加](sap-solution-deploy-alternate.md#define-the-sap-logs-that-are-sent-to-microsoft-sentinel)する必要があります。


- **Microsoft Azure Sentinel での名前**: `ABAPOS_GW_CL`

- **関連する SAP ドキュメント**: [SAP ヘルプ ポータル](https://help.sap.com/viewer/62b4de4187cb43668d15dac48fc00732/7.5.7/en-US/48b2a710ca1c3079e10000000a42189b.html)

- **ログの目的**: ゲートウェイのアクティビティを監視します。 SAP Control Web サービスによって利用できます。 このログは、すべてのクライアントにわたるデータを使用して生成されます。

### <a name="abapos_gw_cl-log-schema"></a>ABAPOS_GW_CL ログ スキーマ

| フィールド        | 説明      |
| ------------ | ---------------- |
| Host         | Host             |
| インスタンス     | 次の構文の ABAP インスタンス: `<HOST>_<SYSID>_<SYSNR>`   |
| [MessageText]  | [メッセージ テキスト]     |
| 重大度     | メッセージの重大度: `Debug`、`Info`、`Warning`、`Error`  |
| SystemID     | システム ID        |
| SystemNumber | システム数    |
| | |

## <a name="abap-icm-log"></a>ABAP ICM ログ

このログを Microsoft Sentinel に送信するには、[**systemconfig.ini** ファイルに手動で追加](sap-solution-deploy-alternate.md#define-the-sap-logs-that-are-sent-to-microsoft-sentinel)する必要があります。


- **Microsoft Azure Sentinel での名前**: `ABAPOS_ICM_CL`

- **関連する SAP ドキュメント**: [SAP ヘルプ ポータル](https://help.sap.com/viewer/683d6a1797a34730a6e005d1e8de6f22/7.52.4/en-US/a10ec40d01e740b58d0a5231736c434e.html)

- **ログの目的**: 受信要求と送信要求を記録し、HTTP 要求の統計情報を集計します。

    SAP Control Web サービスによって利用できます。 このログは、すべてのクライアントにわたるデータを使用して生成されます。

### <a name="abapos_icm_cl-log-schema"></a>ABAPOS_ICM_CL ログ スキーマ

| フィールド        | 説明      |
| ------------ | ---------------- |
| Host         | Host             |
| インスタンス     | 次の構文の ABAP インスタンス: `<HOST>_<SYSID>_<SYSNR>`   |
| [MessageText]  | [メッセージ テキスト]     |
| 重大度     | メッセージの重大度 (例): `Debug`、`Info`、`Warning`、`Error`   |
| SystemID     | システム ID        |
| SystemNumber | システム数    |
| | |

## <a name="abap-job-log"></a>ABAP ジョブ ログ

- **Microsoft Azure Sentinel での名前**: `ABAPJobLog_CL`

- **関連する SAP ドキュメント**: [SAP ヘルプ ポータル](https://help.sap.com/viewer/b07e7195f03f438b8e7ed273099d74f3/7.31.19/en-US/4b2bc0974c594ba2e10000000a42189c.html)

- **ログの目的**: すべてのバックグラウンド処理ジョブ ログ (SM37) を結合します。

    XBP インターフェイスの標準サービスに基づくカスタム サービスと RFC を併用することで利用できます。 このログは、すべてのクライアントにわたるデータを使用して生成されます。

### <a name="abapjoblog_cl-log-schema"></a>ABAPJobLog_CL ログ スキーマ


| フィールド               | Description                      |
| ------------------- | -------------------------------- |
| ABAPProgram         | ABAP プログラム                     |
| BgdEventParameters  | バックグラウンド イベントのパラメーター      |
| BgdProcessingEvent  | バックグラウンド処理イベント      |
| ClientID            | ABAP クライアント ID (MANDT)           |
| DynproNumber        | Dynpro 番号                    |
| GUIStatus           | GUI の状態                       |
| Host                | Host                             |
| インスタンス            | 次の構文の ABAP インスタンス (HOST_SYSID_SYSNR): `<HOST>_<SYSID>_<SYSNR>` |
| JobClassification   | ジョブ分類               |
| JobCount            | ジョブ数                        |
| JobGroup            | ジョブ グループ                        |
| JobName             | ジョブ名                         |
| JobPriority         | ジョブの優先順位                     |
| MessageClass        | message クラス                    |
| MessageNumber       | メッセージ番号                   |
| [MessageText]         | [メッセージ テキスト]                     |
| MessageType         | メッセージの種類                     |
| ReleaseUser         | ジョブのリリース ユーザー                 |
| SchedulingDateTime  | スケジューリング日時             |
| StartDateTime       | 開始日時                  |
| SystemID            | システム ID                        |
| SystemNumber        | システム数                    |
| TargetServer        | ターゲット サーバー                    |
| User                | User                             |
| UserReleaseInstance | ABAP インスタンス - ユーザー リリース     |
| WorkProcessID       | 作業プロセス ID                  |
| WorkProcessNumber   | 作業プロセス番号              |
| | |

## <a name="abap-security-audit-log"></a>ABAP セキュリティ監査ログ

- **Microsoft Azure Sentinel での名前**: `ABAPAuditLog_CL`

- **関連する SAP ドキュメント**: [SAP ヘルプ ポータル](https://help.sap.com/viewer/280f016edb8049e998237fcbd80558e7/7.5.7/en-US/4d41bec4aa601c86e10000000a42189b.html)

- **ログの目的**: 次のデータを記録します。

    - メイン ユーザー レコードの変更など、SAP システム環境に対するセキュリティ関連の変更
    - 比較的大まかなデータを提供する情報 (サインイン試行の成功と失敗など)
    - 一連のイベントの再構築を可能にする情報 (トランザクション開始の成功、失敗など)

    RFC XAL または SAL インターフェイスを介して利用できます。 SAL は、Basis 7.50 バージョン以降で提供されます。 このログは、すべてのクライアントにわたるデータを使用して生成されます。

### <a name="abapauditlog_cl-log-schema"></a>ABAPAuditLog_CL ログ スキーマ

| フィールド                      | Description                     |
| -------------------------- | ------------------------------- |
| ABAPProgramName            | プログラム名 (SAL のみ)                    |
| AlertSeverity              | アラートの重大度                  |
| AlertSeverityText          | アラートの重大度テキスト (SAL のみ)             |
| AlertValue                 | アラートの値                     |
| AuditClassID               | 監査クラス ID (SAL のみ)                 |
| ClientID                   | ABAP クライアント ID (MANDT)          |
| Computer                   | ユーザー マシン (SAL のみ)                   |
| 電子メール                      | ユーザーの電子メール                      |
| Host                       | Host                                                   |
| インスタンス                   | 次の構文の ABAP インスタンス: `<HOST>_<SYSID>_<SYSNR>`                  |
| MessageClass               | message クラス                   |
| MessageContainerID         | メッセージ コンテナー ID (XAL のみ)            |
| MessageID                  | メッセージ ID (`‘AU1’,’AU2’…` など)                     |
| [MessageText]                | [メッセージ テキスト]                    |
| MonitoringObjectName       | MTE モニター オブジェクト名 (XAL のみ)        |
| MonitorShortName           | MTE モニターの短い名前 (XAL のみ)          |
| SAPProcesType              | システム ログ: SAP プロセス タイプ (SAL のみ)    |
| B* - バックグラウンド処理 |                                 |
| D* - ダイアログ処理     |                                 |
| U* - 更新タスク         |                                 |
| SAPWPName                  | システム ログ: 作業プロセス番号 (SAL のみ) |
| SystemID                   | システム ID                       |
| SystemNumber               | システム数                   |
| TerminalIPv6               | ユーザー マシン IP (SAL のみ) |
| TransactionCode            | トランザクション コード (SAL のみ) |
| User                       | User                            |
| Variable1                  | メッセージ変数 1              |
| Variable2                  | メッセージ変数 2              |
| Variable3                  | メッセージ変数 3              |
| Variable4                  | メッセージ変数 4              |
| | |

## <a name="abap-spool-log"></a>ABAP スプール ログ

- **Microsoft Azure Sentinel での名前**: `ABAPSpoolLog_CL`

- **関連する SAP ドキュメント**: [SAP ヘルプ ポータル](https://help.sap.com/viewer/290ce8983cbc4848a9d7b6f5e77491b9/7.52.1/en-US/4eae791c40f72045e10000000a421937.html)

- **ログの目的**: スプール要求 (SP01) の履歴を含む SAP 印刷のメイン ログとして機能します。 (SP01)。

    標準テーブルに基づくカスタム サービスと RFC を併用することで利用できます。 このログは、すべてのクライアントにわたるデータを使用して生成されます。

### <a name="abapspoollog_cl-log-schema"></a>ABAPSpoolLog_CL ログ スキーマ

| フィールド                               | Description                                |
| ----------------------------------- | ------------------------------------------ |
| ArchiveStatus                       | アーカイブ状態                             |
| ArchiveType                         | アーカイブの種類                               |
| ArchivingDevice                     | アーカイブ デバイス                           |
| AutoRereoute                        | 自動再ルーティング                              |
| ClientID                            | ABAP クライアント ID (MANDT)                     |
| CountryKey                          | 国キー                                |
| DeleteSpoolRequestAuto              | スプール要求の自動削除                  |
| DelFlag                             | 削除フラグ                              |
| 部署                          | 部署                                 |
| DocumentType                        | ドキュメントの種類                              |
| ExternalMode                        | 外部モード                              |
| FormatType                          | 形式の種類                                |
| Host                                | Host                                       |
| インスタンス                            | 次の構文の ABAP インスタンス: `<HOST>_<SYSID>_<SYSNR>` |
| NumofCopies                         | [部数]                           |
| OutputDevice                        | 出力デバイス                              |
| PrinterLongName                     | プリンターの長い名前                          |
| PrintImmediately                    | 即時印刷                          |
| PrintOSCoverPage                    | OSCover ページの印刷                         |
| PrintSAPCoverPage                   | SAPCover ページの印刷                        |
| 優先度                            | 優先度                                   |
| RecipientofSpoolRequest             | スプール要求の受信者                 |
| SpoolErrorStatus                    | スプール エラーの状態                         |
| SpoolRequestCompleted               | スプール要求完了                    |
| SpoolRequestisALogForAnotherRequest | スプール要求が別の要求のログになっている |
| SpoolRequestName                    | スプール要求の名前                         |
| SpoolRequestNumber                  | スプール要求番号                       |
| SpoolRequestSuffix1                 | スプール要求のサフィックス 1                      |
| SpoolRequestSuffix2                 | スプール要求のサフィックス 2                      |
| SpoolRequestTitle                   | スプール要求のタイトル                        |
| SystemID                            | システム ID                                  |
| SystemNumber                        | システム数                              |
| TelecommunicationsPartner           | 通信パートナー                 |
| TelecommunicationsPartnerE          | 通信パートナー E               |
| TemSeGeneralcounter                 | TemSe カウンター                              |
| TemseNumAddProtectionRule           | TemSe の追加保護ルール番号              |
| TemseNumChangeProtectionRule        | TemSe の変更保護ルール番号           |
| TemseNumDeleteProtectionRule        | TemSe の削除保護ルール番号           |
| TemSeObjectName                     | TemSe オブジェクト名                          |
| TemSeObjectPart                     | TemSe オブジェクト パーツ                          |
| TemseReadProtectionRule             | TemSe 読み取り保護ルール                 |
| User                                | User                                       |
| ValueAuthCheck                      | 値承認チェック                           |
| | |

## <a name="apab-spool-output-log"></a>APAB スプール出力ログ

- **Microsoft Azure Sentinel での名前**: `ABAPSpoolOutputLog_CL`

- **関連する SAP ドキュメント**: [SAP ヘルプ ポータル](https://help.sap.com/viewer/290ce8983cbc4848a9d7b6f5e77491b9/7.52.1/en-US/4eae779e40f72045e10000000a421937.html)

- **ログの目的**: スプール出力要求 (SP02) の履歴を含む SAP 印刷のメイン ログとして機能します。 (SP02)。

    標準テーブルに基づくカスタム サービスと RFC を併用することで利用できます。 このログは、すべてのクライアントにわたるデータを使用して生成されます。

### <a name="abapspooloutputlog_cl-log-schema"></a>ABAPSpoolOutputLog_CL ログ スキーマ

| フィールド                              | Description                               |
| ---------------------------------- | ----------------------------------------- |
| AppServer                          | アプリケーション サーバー                        |
| ClientID                           | ABAP クライアント ID (MANDT)                    |
| コメント                            | コメント                                   |
| CopyCount                          | コピー数                                |
| CopyCounter                        | コピー カウンター                              |
| 部署                         | 部署                                |
| ErrorSpoolRequestNumber            | エラー要求番号                      |
| FormatType                         | 形式の種類                               |
| Host                               | Host                                      |
| HostName                           | ホスト名                                 |
| HostSpoolerID                      | ホスト スプーラー ID                          |
| インスタンス                           | ABAP インスタンス                             |
| LastPage                           | 最後のページ                                 |
| NumofCopies                        | [部数]                              |
| OutputDevice                       | 出力デバイス                             |
| OutputRequestNumber                | 出力要求番号                     |
| OutputRequestStatus                | 出力要求の状態                     |
| PhysicalFormatType                 | 物理フォーマット タイプ                      |
| PrinterLongName                    | プリンターの長い名前                         |
| PrintRequestSize                   | 印刷要求のサイズ                        |
| 優先度                           | 優先度                                  |
| ReasonforOutputRequest             | 出力要求の理由                 |
| RecipientofSpoolRequest            | スプール要求の受信者                 |
| SpoolNumberofOutputReqProcessed    | 出力要求の数 - 処理済み     |
| SpoolNumberofOutputReqWithErrors   | 出力要求の数 - エラーあり   |
| SpoolNumberofOutputReqWithProblems | 出力要求の数 - 問題あり |
| SpoolRequestNumber                 | スプール要求番号                      |
| StartPage                          | スタート ページ                                |
| SystemID                           | システム ID                                 |
| SystemNumber                       | システム数                             |
| TelecommunicationsPartner          | 通信パートナー                |
| TemSeGeneralcounter                | TemSe カウンター                             |
| タイトル                              | タイトル                                     |
| User                               | User                                      |
| | |


## <a name="abap-syslog"></a>ABAP SysLog

このログを Microsoft Sentinel に送信するには、[**systemconfig.ini** ファイルに手動で追加](sap-solution-deploy-alternate.md#define-the-sap-logs-that-are-sent-to-microsoft-sentinel)する必要があります。


- **Microsoft Azure Sentinel での名前**: `ABAPOS_Syslog_CL`

- **関連する SAP ドキュメント**: [SAP ヘルプ ポータル](https://help.sap.com/viewer/56bf1265a92e4b4d9a72448c579887af/7.5.7/en-US/c769bcbaf36611d3a6510000e835363f.html)

- **ログの目的**: SAP NetWeaver Application Server (SAP NetWeaver AS) ABAP システムのエラー、警告、ユーザー ロック (既知のユーザーからのサインイン試行の失敗によるもの)、処理メッセージをすべて記録します。

    SAP Control Web サービスによって利用できます。 このログは、すべてのクライアントにわたるデータを使用して生成されます。

### <a name="abapos_syslog_cl-log-schema"></a>ABAPOS_Syslog_CL ログスキーマ


| フィールド            | Description            |
| ---------------- | ---------------------- |
| ClientID         | ABAP クライアント ID (MANDT) |
| Host             | Host                   |
| インスタンス         | 次の構文の ABAP インスタンス: `<HOST>_<SYSID>_<SYSNR> ` |
| MessageNumber    | メッセージ番号         |
| [MessageText]      | [メッセージ テキスト]           |
| 重大度         | メッセージの重大度 (`Debug`、`Info`、`Warning`、`Error` のいずれかの値)。        |
| SystemID         | システム ID              |
| SystemNumber     | システム数          |
| TransacationCode | トランザクション コード       |
| 型             | SAP プロセス タイプ       |
| User             | User                   |
| | |


## <a name="abap-workflow-log"></a>ABAP Workflow ログ

- **Microsoft Azure Sentinel での名前**: `ABAPWorkflowLog_CL`

- **関連する SAP ドキュメント**: [SAP ヘルプ ポータル](https://help.sap.com/viewer/56bf1265a92e4b4d9a72448c579887af/7.5.7/en-US/c769bcccf36611d3a6510000e835363f.html)

- **ログの目的**: SAP Business Workflow (WebFlow エンジン) では、SAP システムにおけるマッピングが済んでいないビジネス プロセスを定義できます。

    マッピングの済んでいないビジネス プロセスとしては、たとえばリリースや承認の単純な手続きのほか、基本資料を作成して関連部署を調整するなど、より複雑なビジネス プロセスが挙げられます。

    標準テーブルと標準サービスに基づくカスタム サービスと RFC を併用することで利用できます。 このログは、クライアントごとに生成されます。

### <a name="abapworkflowlog_cl-log-schema"></a>ABAPWorkflowLog_CL ログスキーマ


| フィールド               | Description                      |
| ------------------- | -------------------------------- |
| ActualAgent         | 実際のエージェント                     |
| Address             | Address                          |
| ApplicationArea     | アプリケーション領域                 |
| CallbackFunction    | コールバック関数                |
| ClientID            | ABAP クライアント ID (MANDT)           |
| CreationDateTime    | 作成日時               |
| Creator             | Creator                          |
| CreatorAddress      | 作成者のアドレス                  |
| ErrorType           | エラーの種類                       |
| ExceptionforMethod  | メソッドの例外             |
| Host                | Host                             |
| インスタンス            | 次の構文の ABAP インスタンス (HOST_SYSID_SYSNR): `<HOST>_<SYSID>_<SYSNR>` |
| 言語            | 言語                         |
| LogCounter          | ログ カウンター                      |
| MessageNumber       | メッセージ番号                   |
| MessageType         | メッセージの種類                     |
| MethodUser          | メソッドのユーザー                      |
| 優先度            | 優先度                         |
| SimpleContainer     | キーと値から成る作業項目エンティティのリストとしてパックされた単純なコンテナー。 |
| Status              | Status                           |
| SuperWI             | スーパー WI                         |
| SystemID            | システム ID                        |
| SystemNumber        | システム数                    |
| TaskID              | タスク ID                          |
| TasksClassification | タスクの分類            |
| TaskText            | タスク テキスト                        |
| TopTaskID           | トップ タスク ID                      |
| UserCreated         | User created (ユーザーが作成)                     |
| WIText              | 作業項目のテキスト                   |
| WIType              | 作業項目の種類                   |
| WorkflowAction      | ワークフロー アクション                  |
| WorkItemID          | 作業アイテム ID                     |
| | |





## <a name="abap-workprocess-log"></a>ABAP WorkProcess ログ

このログを Microsoft Sentinel に送信するには、[**systemconfig.ini** ファイルに手動で追加](sap-solution-deploy-alternate.md#define-the-sap-logs-that-are-sent-to-microsoft-sentinel)する必要があります。


- **Microsoft Azure Sentinel での名前**: `ABAPOS_WP_CL`

- **関連する SAP ドキュメント**: [SAP ヘルプ ポータル](https://help.sap.com/viewer/d0739d980ecf42ae9f3b4c19e21a4b6e/7.3.15/en-US/46fb763b6d4c5515e10000000a1553f6.html)

- **ログの目的**: 作業プロセスのログをすべて結合します (既定値: `dev_*`)。

    SAP Control Web サービスによって利用できます。 このログは、すべてのクライアントにわたるデータを使用して生成されます。

### <a name="abapos_wp_cl-log-schema"></a>ABAPOS_WP_CL ログ スキーマ


| フィールド        | 説明         |
| ------------ | ------------------- |
| Host         | Host                |
| インスタンス     | 次の構文の ABAP インスタンス: `<HOST>_<SYSID>_<SYSNR>`   |
| [MessageText]  | [メッセージ テキスト]     |
| 重大度     | メッセージの重大度: `Debug`、`Info`、`Warning`、`Error`  |
| SystemID     | システム ID           |
| SystemNumber | システム数       |
| WPNumber     | 作業プロセス番号 |
| | |


## <a name="hana-db-audit-trail"></a>HANA DB 監査証跡

このログを Microsoft Sentinel に送信するには、[**systemconfig.ini** ファイルに手動で追加](sap-solution-deploy-alternate.md#define-the-sap-logs-that-are-sent-to-microsoft-sentinel)する必要があります。


- **Microsoft Azure Sentinel での名前**: `Syslog`

- **関連する SAP ドキュメント**: [全般](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.03/en-US/48fd6586304c4f859bf92d64d0cd8b08.html) |  [監査証跡](https://help.sap.com/viewer/b3ee5778bc2e4a089d3299b82ec762a7/2.0.03/en-US/0a57444d217649bf94a19c0b68b470cc.html)

- **ログの目的**: ユーザーの操作、つまり SAP HANA データベースにおいて試行された操作を記録します。 たとえば、機密データへの読み取りアクセスを記録、監視することができます。

    Syslog 用 Sentinel Linux エージェントによって利用できます。 このログは、すべてのクライアントにわたるデータを使用して生成されます。

### <a name="syslog-log-schema"></a>Syslog ログ スキーマ

| フィールド         | 説明  |
| ------------- | ------------ |
| Computer      | ホスト名    |
| HostIP        | ホストの IP      |
| HostName      | ホスト名    |
| ProcessID     | プロセス ID   |
| ProcessName   | プロセス名: `HDB*` |
| SeverityLevel | アラート:        |
| SourceSystem  |   ソース システム OS (`Linux`)           |
| SyslogMessage | メッセージ (未解析の監査証跡メッセージ)      |
| | |

## <a name="java-files"></a>JAVA ファイル

このログを Microsoft Sentinel に送信するには、[**systemconfig.ini** ファイルに手動で追加](sap-solution-deploy-alternate.md#define-the-sap-logs-that-are-sent-to-microsoft-sentinel)する必要があります。


- **Microsoft Azure Sentinel での名前**: `JavaFilesLogsCL`

- **関連する SAP ドキュメント**: [全般](https://help.sap.com/viewer/2f8b1599655d4544a3d9c6d1a9b6546b/7.5.9/en-US/485059dfe31672d4e10000000a42189c.html) | [Java セキュリティ監査ログ](https://help.sap.com/viewer/1531c8a1792f45ab95a4c49ba16dc50b/7.5.9/en-US/4b6013583840584ae10000000a42189c.html)

- **ログの目的**: Java ファイルベースのすべてのログ (セキュリティ監査ログを含む) と、システム (クラスターおよびサーバー プロセス)、パフォーマンス、ゲートウェイのログとを結合します。 開発者のトレース ログや既定のトレース ログも含みます。

    SAP Control Web サービスによって利用できます。 このログは、すべてのクライアントにわたるデータを使用して生成されます。

### <a name="javafileslogscl-log-schema"></a>JavaFilesLogsCL ログ スキーマ


| フィールド            | 説明          |
| ---------------- | -------------------- |
| Application      | Java アプリケーション     |
| ClientID         | クライアント ID           |
| CSNComponent     | CSN コンポーネント (`BC-XI-IBD` など) |
| DCComponent      | DC コンポーネント (`com.sap.xi.util.misc` など) |
| DSRCounter       | DSR カウンター          |
| DSRRootContentID | DSR コンテキストの GUID     |
| DSRTransaction   | DSR トランザクションの GUID |
| Host             | Host                 |
| インスタンス         | 次の構文の Java インスタンス: `<HOST>_<SYSID>_<SYSNR>` |
| 場所         | Java クラス           |
| LogName          | Java のログ名 (`Available`、`defaulttrace`、`dev*`、`security` など)
| [MessageText]      | [メッセージ テキスト]         |
| MNo              | メッセージ番号       |
| Pid              | プロセス ID           |
| プログラム          | プログラム名         |
| Session          | Session              |
| 重大度         | メッセージの重大度 (例): `Debug`、`Info`、`Warning`、`Error`     |
| 解決策         | 解決策             |
| SystemID         | システム ID            |
| SystemNumber     | システム数        |
| ThreadName       | スレッド名          |
| Thrown           | 例外をスロー     |
| TimeZone         | タイム ゾーン             |
| User             | User                 |
| | |

## <a name="next-steps"></a>次のステップ

詳細については、次を参照してください。

- [SAP 用 Microsoft Sentinel ソリューションをデプロイする](sap-deploy-solution.md)
- [Microsoft Azure Sentinel SAP ソリューションの詳細な SAP 要件](sap-solution-detailed-requirements.md)
- [SNC を使用して Microsoft Azure Sentinel SAP データ コネクタをデプロイする](sap-solution-deploy-snc.md)
- [エキスパートの構成オプション、オンプレミス デプロイ、SAPControl のログ ソース](sap-solution-deploy-alternate.md)
- [Microsoft Azure Sentinel SAP ソリューション: 組み込みのセキュリティ コンテンツ](sap-solution-security-content.md)
- [Microsoft Azure Sentinel SAP ソリューションのデプロイのトラブルシューティング](sap-deploy-troubleshoot.md)
