---
title: SAP HANA データベースのバックアップ エラーのトラブルシューティング
description: Azure Backup を使用して SAP HANA データベースをバックアップするときに発生する可能性のある一般的なエラーをトラブルシューティングする方法について説明します。
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: b4c39c631963a358dcdc9d1eafe954a85a9499ad
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/27/2019
ms.locfileid: "74554856"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>Azure での SAP HANA データベースのバックアップをトラブルシューティングする

この記事では、Azure 仮想マシン上で SAP HANA データベースをバックアップするためのトラブルシューティング情報を提供します。 現在サポートされている SAP HANA バックアップ シナリオの詳細については、「[シナリオのサポート](sap-hana-backup-support-matrix.md#scenario-support)」を参照してください。

## <a name="prerequisites-and-permissions"></a>前提条件とアクセス許可

バックアップを構成する前に、「[前提条件](tutorial-backup-sap-hana-db.md#prerequisites)」および「[アクセス許可の設定](tutorial-backup-sap-hana-db.md#setting-up-permissions)」のセクションを参照してください。

## <a name="common-user-errors"></a>一般的なユーザー エラー

###  <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection 

| エラー メッセージ      | HANA システムに接続できませんでした                             |
| ------------------ | ------------------------------------------------------------ |
| 考えられる原因    | SAP HANA インスタンスがダウンしている可能性があります。<br/>Azure Backup が HANA データベースと対話するのに必要なアクセス許可が設定されていません。 |
| 推奨される操作 | SAP HANA データベースが稼働しているかどうかを確認します。 データベースが稼働している場合は、必要なアクセス許可がすべて設定されているかどうかを確認します。 いずれかのアクセス許可がない場合は、[事前登録スクリプト](https://aka.ms/scriptforpermsonhana)を実行して、不足しているアクセス許可を追加します。 |

###  <a name="usererrorhanainstancenameinvalid"></a>UserErrorHanaInstanceNameInvalid 

| エラー メッセージ      | 指定した SAP HANA インスタンスは無効であるか、見つかりません |
| ------------------ | ------------------------------------------------------------ |
| 考えられる原因    | 単一の Azure VM 上の複数の SAP HANA インスタンスをバックアップすることはできません。 |
| 推奨される操作 | バックアップする SAP HANA インスタンス上で[事前登録スクリプト](https://aka.ms/scriptforpermsonhana)を実行します。 引き続き問題が発生する場合は、Microsoft サポートにお問い合わせください。 |

###  <a name="usererrorhanaunsupportedoperation"></a>UserErrorHanaUnsupportedOperation 

| エラー メッセージ      | 指定された SAP HANA 操作はサポートされていません             |
| ------------------ | ------------------------------------------------------------ |
| 考えられる原因    | SAP HANA 用 Azure Backup は、SAP HANA ネイティブ クライアント (Studio/ Cockpit/ DBA Cockpit) で実行される増分バックアップやアクションをサポートしていません |
| 推奨される操作 | 詳しくは、[こちら](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support)を参照してください。 |

###  <a name="usererrorhanapodoesnotsupportbackuptype"></a>UserErrorHANAPODoesNotSupportBackupType 

| エラー メッセージ      | この SAP HANA データベースは、要求されたバックアップの種類をサポートしていません |
| ------------------ | ------------------------------------------------------------ |
| 考えられる原因    | Azure Backup は、増分バックアップやスナップショットを使用したバックアップをサポートしていません |
| 推奨される操作 | 詳しくは、[こちら](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support)を参照してください。 |

###  <a name="usererrorhanalsnvalidationfailure"></a>UserErrorHANALSNValidationFailure 

| エラー メッセージ      | バックアップ ログ チェーンが壊れています                                   |
| ------------------ | ------------------------------------------------------------ |
| 考えられる原因    | ログ バックアップ先が backint からファイル システムに更新されたか、backint 実行可能ファイルが変更された可能性があります |
| 推奨される操作 | 問題を解決するには、完全バックアップをトリガーします                   |

###  <a name="usererrorincomaptiblesrctargetsystsemsforrestore"></a>UserErrorIncomaptibleSrcTargetSystsemsForRestore 

| エラー メッセージ      | 復元のソース システムとターゲット システムには互換性がありません   |
| ------------------ | ------------------------------------------------------------ |
| 考えられる原因    | 復元用ターゲット システムにソースとの互換性がありません |
| 推奨される操作 | SAP ノート [1642148](https://launchpad.support.sap.com/#/notes/1642148) を参照し、現在サポートされている復元の種類をご確認ください |

###  <a name="usererrorsdctomdcupgradedetected"></a>UserErrorSDCtoMDCUpgradeDetected 

| エラー メッセージ      | SDC から MDC へのアップグレードが検出されました                                  |
| ------------------ | ------------------------------------------------------------ |
| 考えられる原因    | SAP HANA インスタンスが、SDC から MDC にアップグレードされました。 更新すると、バックアップは失敗します。 |
| 推奨される操作 | この問題を解決するには、[SAP HANA 1.0 から 2.0 へのアップグレード](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#upgrading-from-sap-hana-10-to-20)に関するセクションに記載されている手順に従ってください |

###  <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration 

| エラー メッセージ      | 無効な backint 構成が検出されました                       |
| ------------------ | ------------------------------------------------------------ |
| 考えられる原因    | バッキング パラメーターが Azure Backup に対して正しく指定されていません |
| 推奨される操作 | 次の (backint) パラメーターが設定されているかどうかを確認します。<br/>\* [catalog_backup_using_backint:true]<br/>\* [enable_accumulated_catalog_backup:false]<br/>\* [parallel_data_backup_backint_channels:1]<br/>\* [log_backup_timeout_s:900)]<br/>\* [backint_response_timeout:7200]<br/>backint ベースのパラメーターが HOST に存在する場合は、それらを削除します。 パラメーターが HOST レベルに存在しないが、データベース レベルで手動で変更されている場合は、それらを既に説明した適切な値に戻します。 または、Azure portal から [[保護を停止してバックアップ データを保持する]](https://docs.microsoft.com/azure/backup/sap-hana-db-manage#stop-protection-for-an-sap-hana-database) を実行してから、 **[バックアップの再開]** を選択します。 |

## <a name="restore-checks"></a>復元の確認

### <a name="single-container-database-sdc-restore"></a>単一コンテナー データベース (SDC) の復元

HANA 用の単一コンテナー データベース (SDC) を別の SDC マシンに復元するときは、入力に注意してください。 データベース名は小文字で指定し、かっこで囲んだ "sdc" を後に付ける必要があります。 HANA インスタンスは大文字で表示されます。

SDC HANA インスタンス "H21" がバックアップされているとします。 バックアップ項目のページに、バックアップ項目名が **"h21(sdc)"** と表示されます。 このデータベースを別のターゲット SDC (たとえば H11) に復元する場合は、次の入力を指定する必要があります。

![SDC 復元の入力](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

以下の点に注意してください。

- 既定では、復元されるデータベース名に、バックアップ項目名 h21 (sdc) が設定されます。
- ターゲットを H11 として選択しても、復元されるデータベース名は自動的に変更されません。 **h11(sdc) となるように編集する必要があります**。 SDC に関しては、復元されるデータベース名は、小文字のターゲット インスタンス ID の後に sdc をかっこで囲んで付けたものです。
- SDC はデータベースを 1 つしか含むことができないため、復旧ポイント データによる既存のデータベース データのオーバーライドを許可するチェックボックスをオンにすることも必要です。
- Linux では大文字と小文字が区別されます。 このため、大文字と小文字の区別を維持するように注意してください。

### <a name="multiple-container-database-mdc-restore"></a>複数コンテナー データベース (MDC) の復元

HANA 用の複数コンテナー データベースの場合、標準構成は SYSTEMDB と 1 つ以上のテナント DB です。 SAP HANA インスタンス全体の復元は、SYSTEMDB とテナント DB の両方を復元することを意味します。 最初に SYSTEMDB を復元してから、テナント DB の処理に進みます。 基本的にシステム DB では、選択したターゲットのシステム情報がオーバーライドされます。 この復元では、ターゲット インスタンス内の BackInt 関連情報もオーバーライドされます。 したがって、システム DB がターゲット インスタンスに復元された後で、事前登録スクリプトを再実行する必要があります。 その後でのみ、後続のテナント DB の復元が成功します。

## <a name="upgrading-from-sap-hana-10-to-20"></a>SAP HANA 1.0 から 2.0 へのアップグレード

SAP HANA 1.0 データベースを保護しているときに、2.0 にアップグレードする場合は、次の手順を実行します。

- 古い SDC データベースのデータ保持の[保護を停止](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database)します。
- (sid と mdc) の適切な詳細を指定して、[事前登録スクリプト](https://aka.ms/scriptforpermsonhana)を再実行します。
- 拡張機能を再登録します (バックアップ -> 詳細の表示 -> 関連する Azure VM の選択 > 再登録)。
- 同じ VM に対して [DB の再検出] をクリックします。 このアクションにより、手順 2 の新しい DB が適切な詳細と共に表示されます (SDC ではなく SYSTEMDB とテナント DB)。
- これらの新しいデータベースを保護します。

## <a name="upgrading-without-an-sid-change"></a>SID を変更しないアップグレード

SID の変更が生じない OS や SAP HANA へのアップグレードは、次のように処理できます。

- そのデータベースのデータ保持の[保護を停止](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database)します
- [事前登録スクリプト](https://aka.ms/scriptforpermsonhana)を再実行します
- データベースの[保護を再開](sap-hana-db-manage.md#resume-protection-for-an-sap-hana-database)します

## <a name="next-steps"></a>次の手順

- Azure VM 上の SAP HANA データベースのバックアップに関する[よく寄せられる質問](https://docs.microsoft.com/azure/backup/sap-hana-faq-backup-azure-vm)を確認する
