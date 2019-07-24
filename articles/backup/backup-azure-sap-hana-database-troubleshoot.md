---
title: Azure Backup を使用した SAP HANA データベースのバックアップ中のエラーのトラブルシューティング | Microsoft Docs
description: このガイドでは、Azure Backup を使用した SAP HANA データベースのバックアップ試行時の一般的なエラーをトラブルシューティングする方法について説明します。
services: backup
author: pvrk
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: pullabhk
ms.openlocfilehash: 33f1b4674aad35d55ab014c45cd73753533931cb
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514173"
---
# <a name="troubleshoot-back-up-of-sap-hana-server-on-azure"></a>Azure での SAP HANA サーバーのバックアップのトラブルシューティングを行う

この記事では、Azure Virtual Machines で SAP HANA データベースを保護する場合のトラブルシューティング情報を提供します。 トラブルシューティングに進む前に、アクセス許可と設定に関するいくつかの要点を理解しましょう。

## <a name="understanding-pre-requisites"></a>前提条件について

[前提条件](backup-azure-sap-hana-database.md#prerequisites)の一部として、適切なアクセス許可を設定するために HANA がインストールされている仮想マシンで事前登録スクリプトを実行する必要があります。

### <a name="setting-up-permissions"></a>アクセス許可の設定

事前登録スクリプトでは、次のことが行われます。

1. HANA システムで AZUREWLBACKUPHANAUSER を作成し、以下にリストされている、必要なロールとアクセス許可を追加します。
    - DATABASE ADMIN - 復元中に新しい DB を作成する
    - CATALOG READ – バックアップ カタログを読み取る
    - SAP_INTERNAL_HANA_SUPPORT – いくつかのプライベート テーブルにアクセスする
2. HANA プラグインの Hdbuserstore にキーを追加し、すべての操作 (データベースの照会、バックアップの構成、バックアップの実行、復元の実行) を行います
   
   - キーの作成を確認するには、SIDADM 資格情報を使って HANA マシン内で HDBSQL コマンドを実行します。

    ``` hdbsql
    hdbuserstore list
    ```
    
    コマンド出力では、‘AZUREWLBACKUPHANAUSER’ というユーザーと共にキー {SID}{DBNAME} が表示されるはずです。

> [!NOTE]
> パス “/usr/sap/{SID}/home/.hdb/” の下に一意の SSFS ファイル セットがあることを確認してください。 このパスの下にはフォルダーが 1 つだけあるはずです。

### <a name="setting-up-backint-parameters"></a>BackInt パラメーターの設定

バックアップ用のデータベースが選択されると、Azure Backup サービスでは、データベース レベルで backInt パラメーターが構成されます。

- [catalog_backup_using_backint:true]
- [enable_accumulated_catalog_backup:false]
- [parallel_data_backup_backint_channels:1]
- [log_backup_timeout_s:900)]
- [backint_response_timeout:7200]

> [!NOTE]
> これらのパラメーターがホスト レベルで存在しないことを確認してください。 ホスト レベルのパラメーターではこれらのパラメーターがオーバーライドされ、予期したものとは異なる動作になる可能性があります。

## <a name="understanding-common-user-errors"></a>一般的なユーザー エラーについて

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

| エラー メッセージ | 考えられる原因 | 推奨される操作 |
|---|---|---|
| HANA システムへの接続に失敗しました。システムが稼働中であることを確認してください。| HANA DB が停止しているため、Azure Backup サービスを HANA に接続できません。 または、HANA は実行されていますが、Azure Backup サービスの接続が許可されていません | HANA DB/サービスが停止しているかどうかを確認します。 HANA DB/サービスが稼働している場合は、[ここ](#setting-up-permissions)で説明されているように、すべてのアクセス許可が設定されているかどうかを確認します。 キーが欠落している場合は、事前登録スクリプトを再実行して新しいキーを作成します。 |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| エラー メッセージ | 考えられる原因 | 推奨される操作 |
|---|---|---|
| 無効な Backint 構成が検出されました。 保護を停止し、データベースを再構成してください。| backInt パラメーターが Azure Backup に対して正しく指定されていません。 | パラメーターが[ここ](#setting-up-backint-parameters)で説明されているようになっていることを確認します。 パラメーターに基づく backInt がホストに存在する場合は、それらを削除します。 パラメーターはホストに存在しないが、データベース レベルで手動で変更されている場合は、それらを上述のとおり、適切な値に戻します。 または、Azure portal から 'データを保持して保護を停止' し、もう一度 'バックアップを再開' します。|
