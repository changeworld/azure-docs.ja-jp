---
title: Azure Site Recovery を使用して VMware VM と物理サーバーを Azure にディザスター リカバリーする間の構成サーバーでの問題のトラブルシューティング | Microsoft Docs
description: この記事では、Azure Site Recovery による VMware VM と物理サーバーの Azure へのディザスター リカバリーのために構成サーバーをデプロイする場合のトラブルシューティング情報を提供します。
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: 4faadc27648b0d944e61a4d390313a35b4ba8bfa
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2018
ms.locfileid: "52837734"
---
# <a name="troubleshoot-configuration-server-issues"></a>構成サーバーの問題のトラブルシューティング

この記事は、[Azure Site Recovery](site-recovery-overview.md) 構成サーバーをデプロイおよび管理するときの問題のトラブルシューティングに役立ちます。 構成サーバーは、Site Recovery を使用してオンプレミスの VMware VM と物理サーバーを Azure にディザスター リカバリーするように設定するときに使用されます。 

## <a name="installation-failures"></a>インストール エラー

| **エラー メッセージ** | **推奨される操作** |
|--------------------------|------------------------|
|エラー   アカウントを読み込めませんでした。 エラー:System.IO.IOException:Unable to read data from the transport connection when installing and registering the CS server (CS サーバーをインストールおよび登録するときに転送接続からデータを読み取れません)| コンピューターで TLS 1.0 が有効になっていることを確認します。 |

## <a name="registration-failures"></a>登録エラー

登録エラーは、%ProgramData%\ASRLogs フォルダーのログを使用してデバッグできます。

登録エラーは **%ProgramData%\ASRLogs** フォルダーのログを確認してデバッグできます。

| **エラー メッセージ** | **推奨される操作** |
|--------------------------|------------------------|
|**09:20:06**:InnerException.Type:SrsRestApiClientLib.AcsException,InnerException.<br>メッセージ:ACS50008:SAML token is invalid. (SAML トークンが無効です。)<br>トレース ID:1921ea5b-4723-4be7-8087-a75d3f9e1072<br>関連付け ID:62fea7e6-2197-4be4-a2c0-71ceb7aa2d97><br>タイムスタンプ:**2016-12-12 14:50:08Z<br>** | システム クロックの時刻と現地時刻に 15 分以上の差がないことを確認します。 インストーラーを再実行して登録を完了します。|
|**09:35:27** :DRRegistrationException while trying to get all disaster recovery vault for the selected certificate: : (選択された証明書のすべてのディザスター リカバリー コンテナーを所得使用として DRRegistrationException が発生しました)Threw Exception.Type:Microsoft.DisasterRecovery.Registration.DRRegistrationException, Exception.Message:ACS50008:SAML token is invalid. (SAML トークンが無効です。)<br>Trace ID: e5ad1af1-2d39-4970-8eef-096e325c9950<br>Correlation ID: abe9deb8-3e64-464d-8375-36db9816427a<br>タイムスタンプ:**2016-05-19 01:35:39Z**<br> | システム クロックの時刻と現地時刻に 15 分以上の差がないことを確認します。 インストーラーを再実行して登録を完了します。|
|06:28:45:Failed to create certificate<br>06:28:45:Setup cannot proceed. A certificate required to authenticate to Site Recovery cannot be created. Rerun Setup | ローカル管理者としてセットアップを実行していることを確認します。 |
