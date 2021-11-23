---
title: Microsoft Sentinel SAP ソリューション - セキュリティ コンテンツ リファレンス | Microsoft Docs
description: Microsoft Sentinel SAP ソリューションで提供される組み込みのセキュリティ コンテンツについて説明します。
author: batamig
ms.author: bagold
ms.service: microsoft-sentinel
ms.topic: reference
ms.custom: mvc, ignite-fall-2021
ms.date: 11/09/2021
ms.subservice: microsoft-sentinel
ms.openlocfilehash: a2512bd38ff865b27d0ea667f33f42979aa4e4bb
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132524324"
---
# <a name="microsoft-sentinel-sap-solution-security-content-reference-public-preview"></a>Microsoft Sentinel SAP ソリューション: セキュリティ コンテンツ リファレンス (パブリック プレビュー)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

この記事では、[Microsoft Sentinel SAP ソリューション](sap-deploy-solution.md#deploy-sap-security-content)で使用できるセキュリティ コンテンツについて詳しく説明します。

使用できるセキュリティ コンテンツには、組み込みのブックと組み込みの分析ルールがあります。 SAP 関連の[ウォッチリスト](watchlists.md)を追加して、検索、検出規則、脅威ハンティング、応答プレイブックで使用することもできます。

> [!IMPORTANT]
> Microsoft Azure Sentinel SAP ソリューションは、現在プレビュー段階です。 [Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)には、ベータ版、プレビュー版、またはまだ一般提供されていない Azure 機能に適用される追加の法律条項が含まれています。
>


## <a name="built-in-workbooks"></a>組み込みのブック

次の組み込みのブックを使用して、SAP データ コネクタを介して取り込まれたデータを視覚化および監視します。 SAP ソリューションをデプロイすると、 **[マイ ブック]** タブに SAP のブックが表示されます。


|ブック名  |説明  |ログ |
|---------|---------|--------- |
|<a name="sap---system-applications-and-products-workbook"></a>**SAP - Audit Log Browser (SAP - 監査ログ ブラウザー)**     |次のようなデータが表示されます。 <br><br>時系列でのユーザー サインイン、システムによって取り込まれたイベント、メッセージのクラスと ID、ABAP プログラムの実行など、全般的なシステムの正常性 <br><br>システムで発生するイベントの重大度 <br><br>システムで発生する認証イベントと承認イベント |次のログのデータが使用されます。 <br><br>[ABAPAuditLog_CL](sap-solution-log-reference.md#abap-security-audit-log) |
|**SAP - Suspicious Privileges Operations (SAP - 疑わしい特権操作)**     |  次のようなデータが表示されます。 <br><br>機密性の高い重要な割り当て <br><br>機密性の高い特権ユーザーに対して行われたアクションと変更 <br><br>ロールに加えられた変更  |次のログのデータが使用されます。 <br><br>[ABAPAuditLog_CL](sap-solution-log-reference.md#abap-security-audit-log) <br><br>[ABAPChangeDocsLog_CL](sap-solution-log-reference.md#abap-change-documents-log) |
|**SAP - Initial Access & Attempts to Bypass SAP Security Mechanisms (SAP - 初期アクセス & SAP のセキュリティ メカニズムを回避する試み)**     |  次のようなデータが表示されます。 <br><br>機密性の高いプログラム、コード、関数モジュールの実行 <br><br>構成の変更 (ログの非アクティブ化など) <br><br>デバッグ モードで行われた変更    |次のログのデータが使用されます。 <br><br>[ABAPAuditLog_CL](sap-solution-log-reference.md#abap-security-audit-log)<br><br>[ABAPTableDataLog_CL](sap-solution-log-reference.md#abap-db-table-data-log)<br><br>[Syslog](sap-solution-log-reference.md#abap-syslog) |
|**SAP - Persistency &  Data Exfiltration (SAP - 永続性 & データ流出)**     |    次のようなデータが表示されます。 <br><br>アクティブ化と非アクティブ化、および新しいサービスとサービス ハンドラーに関するデータを含む、インターネット通信フレームワーク (ICF) サービス <br><br> 関数のモジュールとプログラムの両方を含む、安全でない操作 <br><br>機密性の高いテーブルへの直接アクセス      | 次のログのデータが使用されます。 <br><br>[ABAPAuditLog_CL](sap-solution-log-reference.md#abap-security-audit-log) <br><br>[ABAPTableDataLog_CL](sap-solution-log-reference.md#abap-db-table-data-log)<br><br>[ABAPSpoolLog_CL](sap-solution-log-reference.md#abap-spool-log)<br><br>[ABAPSpoolOutputLog_CL](sap-solution-log-reference.md#apab-spool-output-log)<br><br>[Syslog](sap-solution-log-reference.md#abap-syslog) |
|     |         | |

詳細については、「[チュートリアル: データの視覚化と監視](monitor-your-data.md)」と [SAP の継続的な脅威監視のデプロイ (パブリック プレビュー)](sap-deploy-solution.md) に関するページを参照してください。

## <a name="built-in-analytics-rules"></a>組み込みの分析ルール

次の表は、Microsoft Sentinel ソリューション マーケットプレースからデプロイされる、Microsoft Sentinel SAP ソリューションに含まれている組み込みの[分析ルール](sap-deploy-solution.md#deploy-sap-security-content)の一覧です。

### <a name="built-in-sap-analytics-rules-for-initial-access"></a>初期アクセスに関わる組み込みの SAP 分析ルール

|規則名  |説明  |ソース アクション  |方針  |
|---------|---------|---------|---------|
|**SAP - 高 - 予期しないネットワークからのログイン**     |   予期しないネットワークからのサインインを特定します。 <br><br>[SAP - ネットワーク](#networks) ウォッチリストでネットワークを管理します。    |    ネットワークのいずれにも割り当てられていない IP アドレスからバックエンド システムにサインインします。 <br><br>**データ ソース**: SAPcon - 監査ログ    |   初期アクセス      |
|**SAP - 高 - SPNego 攻撃**     | SPNego 再生攻撃を特定します。       |  **データ ソース**: SAPcon - 監査ログ | 影響、横移動  |
|**SAP - 中 - ブルート フォース攻撃**     |     バックエンド システムで失敗したサインイン試行に従って、SAP システムでのブルート フォース攻撃を特定します。    |   スケジュールされた期間中における同じ IP アドレスから複数のシステムまたはクライアントへのサインインを試行します。 <br><br>**データ ソース**: SAPcon - 監査ログ      | 資格情報アクセス        |
|**SAP - 中 - 同じ IP からの複数のログオン**     |  スケジュールされた期間中における同じ IP アドレスからの複数のユーザーのサインインを特定します。   <br><br>**サブユース ケース**: [永続性](#built-in-sap-analytics-rules-for-persistency)    |    同じ IP アドレスで複数のユーザーを使用してサインインします。 <br><br>**データ ソース**: SAPcon - 監査ログ | 初期アクセス        |
|**SAP - 中 - ユーザーによる複数のログオン**     | スケジュールされた期間中における複数のターミナルからの同じユーザーのサインインを特定します。  <br><br>監査 SAL 方式でのみ使用できます (SAP バージョン 7.5 以降)。      |   同じユーザーを使用して、異なる IP アドレスによってサインインします。   <br><br>**データ ソース**: SAPcon - 監査ログ   |  攻撃前、資格情報アクセス、初期アクセス、コレクション <br><br>**サブユース ケース**: [永続性](#built-in-sap-analytics-rules-for-persistency)      |
|**SAP - 情報 - ライフサイクル - SAP ノートがシステムに実装された**     |   システムでの SAP ノートの実装を特定します。 | SNOTE または TCI を使用して SAP ノートを実装します。 <br><br>**データ ソース**: SAPcon - 変更要求      | -  |
| | | | |

### <a name="built-in-sap-analytics-rules-for-data-exfiltration"></a>データ流出に関わる組み込みの SAP 分析ルール

|規則名  |説明  |ソース アクション  |方針  |
|---------|---------|---------|---------|
|**SAP - 中 - 承認されていないサーバーの FTP**     |承認されていないサーバーの FTP 接続を特定します。 | FTP_CONNECT 関数モジュールを使用するなどして、新しい FTP 接続を作成します。 <br><br>**データ ソース**: SAPcon - 監査ログ   |  探索、初期アクセス、コマンドと制御      |
|**SAP - 中 - 安全でない FTP サーバー構成**     |FTP 許可リストが空であるかプレースホルダーが含まれる場合など、安全でない FTP サーバー構成を特定します。 | `SAPFTP_SERVERS_V` メンテナンス ビューを使用して、`SAPFTP_SERVERS` テーブルにプレースホルダーを含む値を管理しないでください。 (SM30) <br><br>**データ ソース**: SAPcon - 監査ログ   |  初期アクセス、コマンドと制御      |
|**SAP - 中 - 複数ファイルのダウンロード**     |特定の時間範囲内における 1 ユーザーによる複数ファイルのダウンロードを特定します。 | Excel 用 SAPGui、リストなどを使用して複数のファイルをダウンロードします。 <br><br>**データ ソース**: SAPcon - 監査ログ   |  コレクション、データ流出、資格情報アクセス       |
|**SAP - 中 - 複数のスプールの実行**     |特定の時間範囲内における 1 ユーザーによる複数のスプールを特定します。 | 1 ユーザーにより、いずれかの種類の複数のスプール ジョブを作成して実行します。 (SP01) <br><br>**データ ソース**: SAPcon - スプール ログ、SAPcon - 監査ログ   |  コレクション、データ流出、資格情報アクセス       |
|**SAP - 中 - 複数のスプール出力の実行**     |特定の時間範囲内における 1 ユーザーによる複数のスプールを特定します。 | 1 ユーザーにより、いずれかの種類の複数のスプール ジョブを作成して実行します。 (SP01) <br><br>**データ ソース**: SAPcon - スプール出力ログ、SAPcon - 監査ログ   |  コレクション、データ流出、資格情報アクセス       |
|**SAP - 中 - 機密性のあるテーブルへの RFC ログオンによる直接アクセス**     |RFC サインインによる汎用テーブル アクセスを特定します。 <br><br> [SAP - 機密性の高いテーブル](#tables) ウォッチリストでテーブルを管理します。<br><br> **注**: 運用システムの場合のみに関連します。   | SE11、SE16、または SE16N を使用してテーブルの内容を開きます。<br><br>**データ ソース**: SAPcon - 監査ログ   |  コレクション、データ流出、資格情報アクセス     |
|**SAP - 中 - スプール引き継ぎ**     |他のユーザーによって作成されたスプール要求を出力するユーザーを特定します。 | あるユーザーを使用してスプール要求を作成してから、別のユーザーを使用してそれを出力します。 <br><br>**データ ソース**: SAPcon - スプール ログ、SAPcon - スプール出力ログ、SAPcon - 監査ログ   |  コレクション、データ流出、コマンドと制御      |
|**SAP - 低 - 動的 RFC 転送先**     |   動的な転送先を使用する RFC の実行を特定します。 <br><br>**サブユース ケース**: [SAP のセキュリティ メカニズムを回避する試み](#built-in-sap-analytics-rules-for-attempts-to-bypass-sap-security-mechanisms)| 動的な転送先 (cl_dynamic_destination) を使用する ABAP レポートを実行します。 たとえば、DEMO_RFC_DYNAMIC_DEST です。   <br><br>**データ ソース**: SAPcon - 監査ログ      |    コレクション、データ流出     |
|**SAP - 低 - ダイアログ ログオンによる機密性の高いテーブルへの直接アクセス**     |   ダイアログ サインインを使用した汎用テーブル アクセスを特定します。      |  `SE11`/`SE16`/`SE16N` を使用してテーブルの内容を開きます。 <br><br>**データ ソース**: SAPcon - 監査ログ      |    探索     |
| | | | |

### <a name="built-in-sap-analytics-rules-for-persistency"></a>永続性に関わる組み込みの SAP 分析ルール

|規則名  |説明  |ソース アクション  |方針  |
|---------|---------|---------|---------|
|**SAP - 高 - ICF サービスのアクティブ化または非アクティブ化**     | ICF サービスのアクティブ化または非アクティブ化を特定します。       |  SICF を使用してサービスをアクティブ化します。<br><br>**データ ソース**: SAPcon - テーブル データ ログ | コマンドと制御、横移動、永続化  |
|**SAP - 高 - テストされた関数モジュール**     |  関数モジュールのテストを特定します。       |   `SE37` / `SE80` を使用して関数モジュールをテストします。  <br><br>**データ ソース**: SAPcon - 監査ログ    |   コレクション、防御回避、横移動      |
|  **SAP - 高 - HANA DB - ユーザー管理アクション**   | ユーザー管理アクションを特定します。        |  データベース ユーザーを作成、更新、または削除します。 <br><br>**データ ソース**: Linux エージェント - Syslog*       |Privilege Escalation (特権昇格)         |
|**SAP - 高 - 新しい ICF サービス ハンドラー**     | ICF ハンドラーの作成を特定します。       |  SICF を使用して、新しいハンドラーをサービスに割り当てます。<br><br>**データ ソース**: SAPcon - 監査ログ | コマンドと制御、横移動、永続化  |
|**SAP - 高 - 新しい ICF サービス**     | ICF サービスの作成を特定します。       |  SICF を使用してサービスを作成します。<br><br>**データ ソース**: SAPcon - テーブル データ ログ | コマンドと制御、横移動、永続化  |
|**SAP - 中 - 古いまたは安全でない関数モジュールの実行**     |古いまたは安全でない ABAP 関数モジュールの実行を特定します。 <br><br>[SAP - 古い関数モジュール](#modules) ウォッチリストで古い関数を管理します。 バックエンドでの `EUFUNC` テーブルのテーブル ログの変更を確実にアクティブ化します。 (SE13)<br><br> **注**: 運用システムの場合のみに関連します。  | SE37 を使用して、古い、または安全でない関数モジュールを直接実行します。 <br><br>**データ ソース**: SAPcon - テーブル データ ログ   | 探索、コマンドと制御 |
|**SAP - 中 - 古い/安全でないプログラムの実行**     |古いまたは安全でない ABAP プログラムの実行を特定します。 <br><br> [SAP - 古いプログラム](#programs) ウォッチリストで古いプログラムを管理します。<br><br> **注**: 運用システムの場合のみに関連します。  | SE38、SA38、または SE80 を使用するかバックグラウンド ジョブを使用して、プログラムを直接実行します。  <br><br>**データ ソース**: SAPcon - 監査ログ   | 探索、コマンドと制御 |
|**SAP - 低 - ユーザーによる複数のパスワード変更**     |   ユーザーによる複数のパスワード変更を特定します。      |   ユーザー パスワードの変更 <br><br>**データ ソース**: SAPcon - 監査ログ      |    資格情報アクセス     |
| | | | |


### <a name="built-in-sap-analytics-rules-for-attempts-to-bypass-sap-security-mechanisms"></a>SAP のセキュリティ メカニズムを回避する試みに関わる組み込みの SAP 分析ルール

|規則名  |説明  |ソース アクション  |方針  |
|---------|---------|---------|---------|
|**SAP - 高 - クライアント構成の変更**     |     クライアント ロールや変更記録モードなど、クライアント構成の変更を特定します。    |  `SCC4` トランザクション コードを使用してクライアント構成の変更を行います。 <br><br>**データ ソース**: SAPcon - 監査ログ  |    防御回避、データ流出、永続化     |
|**SAP - 高 - デバッグ アクティビティ中にデータが変更された**     | デバッグ アクティビティ中のランタイム データの変更を特定します。  <br><br>**サブユース ケース**: [永続性](#built-in-sap-analytics-rules-for-persistency)       | 1. デバッグをアクティブ化します ("/h")。 <br>2. 変更するフィールドを選択し、その値を更新します。<br><br>**データ ソース**: SAPcon - 監査ログ        |  実行、横移動       |
|**SAP - 高 - セキュリティ監査ログの非アクティブ化**     | セキュリティ監査ログの非アクティブ化を特定します。        |  `SM19/RSAU_CONFIG` を使用してセキュリティ監査ログを無効にします。 <br><br>**データ ソース**: SAPcon - 監査ログ |   データ流出、防御回避、永続化      |
|**SAP - 高 - 機密性の高い ABAP プログラムの実行**     |機密性の高い ABAP プログラムの直接実行を特定します。 <br><br>[SAP - 機密性の高い ABAP プログラム](#programs) ウォッチリストで ABAP プログラムを管理します。        | `SE38`/`SA38`/`SE80` を使用してプログラムを直接実行します。 <br> <br>**データ ソース**: SAPcon - 監査ログ      |     データ流出、横移動、実行    |
|**SAP - 高 - 機密性の高いトランザクション コードの実行**     | 機密性の高いトランザクション コードの実行を特定します。 <br><br>[SAP - 機密性の高いトランザクション コード](#transactions) ウォッチリストでトランザクション コードを管理します。       |  機密性の高いトランザクション コードを実行します。 <br><br>**データ ソース**: SAPcon - 監査ログ      |    探索、実行     |
|**SAP - 高 - 機密性の高い関数モジュールの実行**     | 機密性の高い ABAP 関数モジュールの実行を特定します。 <br><br>**サブユース ケース**: [永続性](#built-in-sap-analytics-rules-for-persistency)<br><br>**注**: 運用システムの場合のみに関連します。 <br><br>[SAP - 機密性の高い関数モジュール](#modules) ウォッチリストで機密性の高い関数を管理し、EUFUNC テーブルのバックエンドでのテーブル ログの変更を確実にアクティブ化します。 (SE13)     | 機密性の高い関数モジュールを、SE37 を使用して直接実行します。 <br><br>**データ ソース**: SAPcon - テーブル データ ログ | 探索、コマンドと制御 
|**SAP - 高 - HANA DB - 監査証跡ポリシーの変更**     |  HANA DB 監査証跡ポリシーの変更を特定します。       |     セキュリティ定義の既存の監査ポリシーを作成または更新します。 <br> <br>**データ ソース**: Linux エージェント - Syslog    |  横移動、防御回避、永続化       |
|**SAP - 高 - HANA DB - 監査証跡の非アクティブ化**     |    HANA DB 監査ログの非アクティブ化を特定します。     |    HANA DB セキュリティ定義で監査ログを非アクティブにします。 <br><br>**データ ソース**: Linux エージェント - Syslog     |  永続化、横移動、防御回避       |
|**SAP - 高 - 機密性の高い関数モジュールの RFC 実行**     | 関連の検出で使用される機密性の高い関数モデル。    <br><br>[SAP - 機密性の高い関数モジュール](#module) ウォッチリストで関数モジュールを管理します。   |      RFC を使用して関数モジュールを実行します。  <br><br>**データ ソース**: SAPcon - 監査ログ |  探索、横移動、検出       |
|**SAP - 高 - システム構成の変更**     | システム構成の変更を特定します。        |   `SE06` トランザクション コードを使用して、システム変更オプションまたはソフトウェア コンポーネントの変更を調整します。<br><br>**データ ソース**: SAPcon - 監査ログ |データ流出、防御回避、永続化   |
|**SAP - 中 - デバッグ アクティビティ**     |  すべてのデバッグ関連アクティビティを特定します。 <br><br>**サブユース ケース**: [永続性](#built-in-sap-analytics-rules-for-persistency)      |システムでのデバッグのアクティブ化 ("/h")、アクティブ プロセスのデバッグ、ソース コードへのブレークポイントの追加などを行います。 <br><br>**データ ソース**: SAPcon - 監査ログ |   探索      |
|**SAP - 中 - セキュリティ監査ログの構成の変更**     |  セキュリティ監査ログの構成の変更を特定します       |   `SM19`/`RSAU_CONFIG` を使用して、フィルター、状態、記録モードなどのセキュリティ監査ログの構成を変更します。 <br><br>**データ ソース**: SAPcon - 監査ログ      |    永続化、データ流出、防御回避     |
|**SAP - 中 - トランザクションのロック解除**     |トランザクションのロック解除を特定します。         |     `SM01`/`SM01_DEV`/`SM01_CUS` を使用してトランザクション コードをロック解除します。 <br><br>**データ ソース**: SAPcon - 監査ログ   |  永続化、実行       |
|**SAP - 低 - 動的 ABAP プログラム**     | 動的な ABAP プログラミングの実行を特定します。 たとえば、ABAP コードが動的に作成、変更、または削除された場合です。 <br><br> [SAP - ABAP 生成のトランザクション](#transactions) ウォッチリストで、除外されたトランザクション コードを管理します。 | ABAP プログラム生成コマンド (INSERT REPORT など) を使用する ABAP レポートを作成してから、そのレポートを実行します。  <br><br>**データ ソース**: SAPcon - 監査ログ      |    探索、コマンドと制御、影響     |
| | | | |

### <a name="built-in-sap-analytics-rules-for-suspicious-privileges-operations"></a>疑わしい特権操作に関わる組み込みの SAP 分析ルール

|規則名  |説明  |ソース アクション  |方針  |
|---------|---------|---------|---------|
|**SAP - 高 - 機密性の高い特権ユーザーの変更**     |  機密性の高い特権ユーザーの変更を特定します。    <br> <br>[SAP - 特権ユーザー](#users) ウォッチリストで特権ユーザーを管理します。 |   `SU01` を使用して、ユーザーの詳細/認可を変更します。 <br><br>**データ ソース**: SAPcon - 監査ログ     |  特権エスカレーション、資格情報アクセス       |
|**SAP - 高 - HANA DB - 管理者認可の割り当て**     |   管理者特権またはロールの割り当てを特定します。      |  ユーザーに管理者ロールまたは特権を割り当てます。  <br><br>**データ ソース**: Linux エージェント - Syslog     | Privilege Escalation (特権昇格)        |
|**SAP - 高 - ログインした機密性の高い特権ユーザー**     |    機密性の高い特権ユーザーのダイアログ サインインを特定します。 <br><br>[SAP - 特権ユーザー](#users) ウォッチリストで特権ユーザーを管理します。    |  `SAP*` または別の特権ユーザーを使用してバックエンド システムにサインインします。  <br><br>**データ ソース**: SAPcon - 監査ログ     |   初期アクセス、資格情報アクセス      |
|  **SAP - 高 - 機密性の高い特権ユーザーが他のユーザーに変更を行っている**   |   機密性の高い特権ユーザーによる他のユーザーへの変更を特定します。       | SU01 を使用して、ユーザーの詳細/認可を変更します。  <br><br>**データ ソース**: SAPcon - 監査ログ     |   特権エスカレーション、資格情報アクセス       |
|**SAP - 高 - 機密性の高いユーザーのパスワード変更とログイン**     | 特権ユーザーのパスワード変更を特定します。      |  特権ユーザーのパスワードを変更し、システムにサインインします。 <br>[SAP - 特権ユーザー](#users) ウォッチリストで特権ユーザーを管理します。<br><br>**データ ソース**: SAPcon - 監査ログ | 影響、コマンドと制御、特権エスカレーション |
|**SAP - 高 - ユーザーが新しいユーザーを作成して使用する**     | 他のユーザーを作成して使用しているユーザーを特定します。  <br><br>**サブユース ケース**: [永続性](#built-in-sap-analytics-rules-for-persistency)      |  SU01 を使用してユーザーを作成し、その新しく作成したユーザーと同じ IP アドレスを使用してサインインします。<br><br>**データ ソース**: SAPcon - 監査ログ | 探索、攻撃前、初期アクセス  |
|**SAP - 高 - ユーザーが他のユーザーをロック解除して使用する**     |他のユーザーによってロック解除され使用されているユーザーを特定します。   <br><br>**サブユース ケース**: [永続性](#built-in-sap-analytics-rules-for-persistency)    |  SU01 を使用してユーザーをロック解除し、そのロック解除されたユーザーおよび同じ IP アドレスを使用してサインインします。<br><br>**データ ソース**: SAPcon - 監査ログ、SAPcon - ドキュメント変更ログ | 探索、攻撃前、初期アクセス、横移動  |
|**SAP - 中 - 機密性の高いプロファイルの割り当て**     |  ユーザーに対する機密性の高いプロファイルの新たな割り当てを特定します。 <br><br>[SAP - 機密性の高いプロファイル](#profiles) ウォッチリストで、機密性の高いプロファイルを管理します。      |    `SU01` を使用してユーザーにプロファイルを割り当てます。 <br><br>**データ ソース**: SAPcon - ドキュメント変更ログ    |  Privilege Escalation (特権昇格)       |
|**SAP - 中 - 機密性の高いロールの割り当て**     |    ユーザーに対する機密性の高いロールの新たな割り当てを特定します。     <br><br>[SAP - 機密性の高いロール](#roles) ウォッチリストで機密性の高いロールを管理します。|  `SU01` / `PFCG` を使用してユーザーにロールを割り当てます。 <br><br>**データ ソース**: SAPcon - ドキュメント変更ログ、監査ログ     |   Privilege Escalation (特権昇格)      |
|**SAP - 中 - クリティカル認可の割り当て - 新しい認可値**     | 新しいユーザーに対するクリティカル認可オブジェクト値の割り当てを特定します。  <br><br>[SAP - クリティカル認可オブジェクト](#objects) ウォッチリストでクリティカル認可オブジェクトを管理します。      |  `PFCG` を使用して、新しい認可オブジェクトを割り当てるか、ロール内の既存のものを更新します。 <br><br>**データ ソース**: SAPcon - ドキュメント変更ログ      |     Privilege Escalation (特権昇格)    |
|**SAP - 中 - クリティカル認可の割り当て - 新しいユーザー割り当て**     |  新しいユーザーに対するクリティカル認可オブジェクト値の割り当てを特定します。 <br><br>[SAP - クリティカル認可オブジェクト](#objects) ウォッチリストでクリティカル認可オブジェクトを管理します。    |    `SU01`/`PFCG` を使用して、クリティカル認可値を保持するロールに新しいユーザーを割り当てます。 <br><br>**データ ソース**: SAPcon - ドキュメント変更ログ    |  Privilege Escalation (特権昇格)       |
|**SAP - 中 - 機密性の高いロールの変更**     |機密性の高いロールの変更を特定します。 <br><br> [SAP - 機密性の高いロール](#roles) ウォッチリストで機密性の高いロールを管理します。       |  PFCG を使用してロールを変更します。 <br><br>**データ ソース**: SAPcon - ドキュメント変更ログ、SAPcon - 監査ログ   |  影響、特権エスカレーション、永続化    |
| | | | |

## <a name="available-watchlists"></a>使用可能なウォッチリスト

次の表に、Microsoft Sentinel SAP ソリューションで使用できる[ウォッチリスト](sap-deploy-solution.md#deploy-sap-security-content)と各ウォッチリストのフィールドを一覧で示します。

これらのウォッチリストにより、Microsoft Sentinel SAP Continuous Threat Monitoring ソリューションの構成が提供されます。 [SAP ウォッチリスト](https://github.com/Azure/Azure-Sentinel/tree/master/Solutions/SAP/Analytics/Watchlists)は Microsoft Sentinel GitHub リポジトリで入手できます。

|ウォッチリスト名  |説明とフィールド  |
|---------|---------|
|<a name="objects"></a>**SAP - クリティカル認可オブジェクト**     |  割り当てを制御する必要があるクリティカル認可オブジェクト。     <br><br>- **AuthorizationObject**: `S_DEVELOP`、`S_TCODE`、または `Table TOBJ` などの SAP 認可オブジェクト <br>- **AuthorizationField**: `OBJTYP`や `TCD` などの SAP 認可フィールド    <br>- **AuthorizationValue**: `DEBUG` などの SAP 認可フィールド値       <br>- **ActivityField:** : SAP アクティビティ フィールド。 ほとんどの場合、この値は `ACTVT` になります。 **Activity** のない、または **Activity** フィールドのみがある認可オブジェクトの場合、`NOT_IN_USE` が埋め込まれます。        <br>- **Activity**: 認可オブジェクトに従った SAP アクティビティ。`01`: 作成、`02`: 変更、`03`: 表示など。      <br>- **Description**: クリティカル認可オブジェクトのわかりやすい説明。 |
|**SAP - 除外されたネットワーク** | 除外されたネットワークの内部メンテナンス (たとえば、Web ディスパッチャー、ターミナル サーバーなどを無視する場合)。 <br><br>-**Network**: ネットワーク IP アドレスまたは範囲 (`111.68.128.0/17` など)。 <br>-**Description**: ネットワークに関するわかりやすい説明。|
|**SAP 除外されたユーザー** |システムにサインインしていて、無視する必要があるシステム ユーザー。 たとえば、同じユーザーによる複数のサインインに関するアラートです。 <br><br>- **User**: SAP ユーザー <br>-**Description**: ユーザーに関するわかりやすい説明。 |
|<a name="networks"></a>**SAP - ネットワーク**     |  未承認のログインを識別するための内部およびメンテナンス ネットワーク。      <br><br>- **Network**: ネットワーク IP アドレスまたは範囲 (`111.68.128.0/17` など)     <br>- **Description**: ネットワークに関するわかりやすい説明。|
|<a name="users"></a>**SAP - 特権ユーザー**     |   追加制限の対象となる特権ユーザー。  <br><br>- **User**: ABAP ユーザー (`DDIC` や `SAP` など) <br>- **Description**: ユーザーに関するわかりやすい説明。 |
|<a name= "programs"></a>**SAP - 機密性の高い ABAP プログラム**     |      実行を制御する必要がある機密性の高い ABAP プログラム (レポート)。   <br><br>- **ABAPProgram**: ABAP プログラムまたはレポート (`RSPFLDOC` など)     <br>- **Description**: プログラムに関するわかりやすい説明。|
|<a name="module"></a>**SAP - 機密性の高い関数モジュール**     |   未承認のログインを識別するための内部およびメンテナンス ネットワーク。      <br><br>- **FunctionModule**: ABAP 関数モジュール (`RSAU_CLEAR_AUDIT_LOG` など)       <br>- **Description**: モジュールに関するわかりやすい説明。     |
|<a name="profiles"></a>**SAP - 機密性の高いプロファイル**     |  割り当てを制御する必要がある機密性の高いプロファイル。     <br><br>- **Profile**: SAP 認可プロファイル (`SAP_ALL` や `SAP_NEW` など)      <br>- **Description**: プロファイルに関するわかりやすい説明。|
|<a name="tables"></a>**SAP - 機密性の高いテーブル**     |  アクセスを制御する必要がある機密性の高いテーブル。  <br><br>- **Table**: ABAP 辞書テーブル (`USR02` や `PA008` など) <br>- **Description**: テーブルに関するわかりやすい説明。 |
|<a name="roles"></a>**SAP - 機密性の高いロール**     |  割り当てを制御する必要がある機密性の高いロール。    <br><br>- **Role**: SAP 認可ロール (`SAP_BC_BASIS_ADMIN` など)  <br>- **Description**: ロールに関するわかりやすい説明。 |
|<a name="transactions"></a>**SAP - 機密性の高いトランザクション**     |     実行を制御する必要がある機密性の高いトランザクション。  <br><br>- **TransactionCode:** SAP トランザクション コード (`RZ11` など) <br>- **Description**: コードに関するわかりやすい説明。 |
|<a name="systems"></a>**SAP - システム**     |    ロールと使用法に応じた、SAP システムのランドスケープについて記述します。<br><br>- **SystemID**: SAP システム ID (SYSID) <br>- **SystemRole:** SAP システム ロール。`Sandbox`、`Development`、`Quality Assurance`、`Training`、`Production` のいずれかの値 <br>- **SystemUsage:** SAP システムの使用法。`ERP`、`BW`、`Solman`、`Gateway`、`Enterprise Portal` のいずれかの値        |
|<a name="users"></a>**SAP - 除外されたユーザー**     |  ログインしていて、無視する必要があるシステム ユーザー (ユーザーによる複数のログオンのアラートの場合など)。 <br><br>- **User**: SAP ユーザー  <br>- **Description**: ユーザーに関するわかりやすい説明  |
|<a name="networks"></a>**SAP - 除外されたネットワーク**     |  Web ディスパッチャー、ターミナル サーバーなどを無視するために、内部の除外されたネットワークを管理します。  <br><br>- **Network**: ネットワーク IP アドレスまたは範囲 (`111.68.128.0/17` など)  <br>- **Description**: ネットワークに関するわかりやすい説明 |
|<a name="modules"></a>**SAP - 古い関数モジュール**     | 実行を制御する必要がある古い関数モジュール。    <br><br>- **FunctionModule**: ABAP 関数モジュール (TH_SAPREL など)  <br>- **Description**: 関数モジュールに関するわかりやすい説明 |
|<a name="programs"></a>**SAP - 古いプログラム**     | 実行を制御する必要がある古い ABAP プログラム (レポート)。  <br><br>- **ABAPProgram**:ABAP プログラム (TH_ RSPFLDOC など)  <br>- **Description**: ABAP プログラムに関するわかりやすい説明 |
|<a name="transactions"></a>**SAP - ABAP 生成のトランザクション**     |  実行を制御する必要がある ABAP 生成のトランザクション。 <br><br>- **TransactionCode:** トランザクション コード (SE11 など)。  <br>- **Description**: トランザクション コードに関するわかりやすい説明  |
|<a name="servers"></a>**SAP - FTP サーバー**     |  未承認の接続を識別するための FTP サーバー。    <br><br>- **Client**: 100 など。  <br>- **FTP_Server_Name**: FTP サーバー名 (http://contoso.com/ など) <br>-**FTP_Server_Port**: FTP サーバー ポート (22 など)。 <br>- **Description** FTP サーバーに関するわかりやすい説明 |
| | |


## <a name="next-steps"></a>次のステップ

詳細については、次を参照してください。

- [SAP 用 Microsoft Sentinel ソリューションをデプロイする](sap-deploy-solution.md)
- [Microsoft Azure Sentinel SAP ソリューションのログ リファレンス](sap-solution-log-reference.md)
- [SNC を使用して Microsoft Azure Sentinel SAP データ コネクタを展開する](sap-solution-deploy-snc.md)
- [エキスパートの構成オプション、オンプレミス デプロイ、SAPControl のログ ソース](sap-solution-deploy-alternate.md)
- [Microsoft Sentinel SAP ソリューションの詳細な SAP 要件](sap-solution-detailed-requirements.md)
- [Microsoft Azure Sentinel SAP ソリューションのデプロイのトラブルシューティング](sap-deploy-troubleshoot.md)
