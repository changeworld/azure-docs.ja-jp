---
title: "Azure Backup での System Center Data Protection Manager のトラブルシューティング | Microsoft Docs"
description: "System Center Data Protection Manager での問題のトラブルシューティングを行います。"
services: backup
documentationcenter: 
author: adigan
manager: shreeshd
editor: 
ms.assetid: 2d73c349-0fc8-4ca8-afd8-8c9029cb8524
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/24/2017
ms.author: pullabhk;markgal;adigan
ms.openlocfilehash: 2244a39217f54eb5906d05990f19fc8ca2fdeb0e
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2018
---
# <a name="troubleshoot-system-center-data-protection-manager"></a>System Center Data Protection Manager のトラブルシューティング

SC DPM の最新のリリース ノートは、[こちら](https://docs.microsoft.com/en-us/system-center/dpm/dpm-release-notes?view=sc-dpm-2016)で入手できます。
保護のサポート マトリックスについては、[こちら](https://docs.microsoft.com/en-us/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2016)をご覧ください。

## <a name="replica-is-inconsistent"></a>レプリカに整合性がありません

このエラーはさまざまな理由で発生する可能性があります。レプリカ作成ジョブの失敗、変更ジャーナルの問題、ボリューム レベルのフィルター ビットマップ エラー、ソース マシンの予期しないシャット ダウン、同期ログのオーバーフロー、レプリカの本当の不整合などです。 この問題を解決するには、次の手順に従ってください。
- 不整合状態を解消するには、整合性チェックを手動で実行するか、または整合性チェックを毎日実行するようにスケジュールします。
- MAB サーバーまたは System Center DPM が最新バージョンであることを確認します
- 自動整合性チェックが有効になっていることを確認します
- コマンド プロンプトからサービスを再起動してみます ("net stop dpmra" に続いて "net start dpmra")
- ネットワーク接続と帯域幅の要件が満たされていることを確認します
- ソース マシンが予期せずにシャットダウンされたかどうかを確認します
- ディスクが正常であり、レプリカに十分な領域があることを確認します
- 重複するバックアップ ジョブが同時に実行していないことを確認します

## <a name="online-recovery-point-creation-failed"></a>オンライン回復ポイントを作成できませんでした

この問題を解決するには、次の手順に従ってください。
- Azure Backup エージェントが最新バージョンであることを確認します
- 保護タスク領域に回復ポイントを手動で作成してみます
- データ ソースで整合性チェックを実行します
- ネットワーク接続と帯域幅の要件が満たされていることを確認します
- レプリカ データが不整合状態になっています。 このデータ ソースのディスク回復ポイントを作成します
- レプリカが存在し、欠落していないことを確認します
- レプリカに USN ジャーナルを作成するための十分な領域があります

## <a name="unable-to-configure-protection"></a>保護を構成できません

このエラーは、DPM サーバーが保護されるサーバーに接続できないときに表示されます。 この問題を解決するには、次の手順に従ってください。
- Azure Backup エージェントが最新バージョンであることを確認します
- DPM サーバーと保護されるサーバーの間に接続 (ネットワーク/ファイアウォール/プロキシ) があることを確認します
- SQL Server を保護している場合は、ログインのプロパティから NT AUTHORITY\SYSTEM で sysadmin が有効になっていることを確認します

## <a name="this-server-is-not-registered-to-the-vault-specified-by-the-vault-credential"></a>このサーバーは、資格情報コンテナーで指定されたコンテナーに登録されていません

このエラーは、選んだコンテナー資格情報ファイルが回復対象の System Center DPM/Azure Backup Server に関連付けられている Recovery Services コンテナーに属さないときに表示されます。 この問題を解決するには、次の手順に従ってください。
- System Center DPM/Azure Backup Server が登録されている Recovery Services コンテナーから、コンテナー資格情報ファイルをダウンロードします。
- ダウンロードした最新のコンテナー資格情報ファイルを使って、コンテナーにサーバーを登録してみます。

## <a name="either-the-recoverable-data-is-not-available-or-the-selected-server-is-not-a-dpm-server"></a>回復可能なデータがないか、選択されたサーバーが DPM サーバーではありません
このエラーは、他の System Center DPM/Azure Backup Server が Recovery Services コンテナーに登録されていない場合、サーバーがまだメタデータをアップロードしていない場合、または選んだサーバーが System Center DPM/Azure Backup Server ではない場合に表示されます。
- 他の System Center DPM/Azure Backup Server が Recovery Services コンテナーに登録されている場合、最新の Azure Backup エージェントがインストールされていることを確認します。
- 他の System Center DPM/Azure Backup Server が Recovery Services コンテナーに登録されている場合、インストール後 1 日待ってから、回復プロセスを開始します。 保護されたすべてのバックアップのメタデータを夜間ジョブがクラウドにアップロードします。 このデータは回復に利用できます。

## <a name="the-encryption-passphrase-provided-does-not-match-with-passphrase-associated-with-the-following-server"></a>入力したパスフレーズは、次のサーバーのパスフレーズと一致しません

> [!NOTE]
>暗号化パスフレーズをなくしたり忘れたりした場合、データを回復することはできません。 唯一のオプションは、パスフレーズを再生成し、今後のバックアップ データの暗号化に使うことだけです。
>
>

このエラーは、回復対象の System Center DPM/Azure Backup Server のデータからデータを暗号化する過程で使用された暗号化パスフレーズが、指定した暗号化パスフレーズに一致しない場合に表示されます。 エージェントはデータを復号できません。 そのため、回復に失敗します。 この問題を解決するには、次の手順に従ってください。
- データを回復する System Center DPM/Azure Backup Server に関連付けられている暗号化パスフレーズとまったく同じものを指定します。 
