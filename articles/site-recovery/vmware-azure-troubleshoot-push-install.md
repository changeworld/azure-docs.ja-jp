---
title: VMware から Azure への Azure Site Recovery のトラブルシューティング | Microsoft Docs
description: Azure 仮想マシンのレプリケート時に発生するエラーのトラブルシューティングを行います。
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.author: ramamill
ms.date: 07/06/2018
ms.openlocfilehash: 8d5db03eeebb659414ea1f554e5b34c938fd2795
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952911"
---
# <a name="troubleshoot-mobility-service-push-installation-issues"></a>モビリティ サービスのプッシュ インストールに関する問題のトラブルシューティング

この記事では、保護を有効にするためにソース サーバーに Azure Site Recovery モビリティ サービスをインストールするときに発生する可能性がある、一般的な問題のトラブルシューティング方法について説明します。

## <a name="error-78007---the-requested-operation-could-not-be-completed"></a>エラー 78007 - 要求された操作を完了できませんでした
このエラーは、複数の理由により、サービスでスローされる可能性があります。 具体的なトラブルシューティングを行うには対応するプロバイダー エラーを選択します。

* [エラー 95103](#error-95103---protection-could-not-be-enabled-ep0854) 
* [エラー 95105](#error-95105---protection-could-not-be-enabled-ep0856) 
* [エラー 95107](#error-95107---protection-could-not-be-enabled-ep0858) 
* [エラー 95108](#error-95108---protection-could-not-be-enabled-ep0859) 
* [エラー 95117](#error-95117---protection-could-not-be-enabled-ep0865) 
* [エラー 95213](#error-95213---protection-could-not-be-enabled-ep0874) 
* [エラー 95224](#error-95224---protection-could-not-be-enabled-ep0883) 
* [エラー 95265](#error-95265---protection-could-not-be-enabled-ep0902) 


## <a name="error-95105---protection-could-not-be-enabled-ep0856"></a>エラー 95105 - 保護を有効にできませんでした (EP0856)

**エラー コード** | **考えられる原因** | **エラー固有の推奨事項**
--- | --- | ---
95105 </br>**メッセージ:** ソース マシンに対するモビリティ サービスのプッシュ インストールが、エラー コード **EP0856** で失敗しました。 <br> **[ファイルとプリンターの共有]** がソース マシンで許可されていないか、プロセス サーバーとソース マシン間のネットワーク接続に問題があります。| **[ファイルとプリンターの共有]** が有効になっていません。 | Windows ファイアウォールでソース マシンの **[ファイルとプリンターの共有]** を許可します。 ソース マシンで、**[Windows ファイアウォール]** > **[ファイアウォールを介したアプリまたは機能を許可する]** の順に移動し、すべてのプロファイルで **[ファイルとプリンターの共有]** を選択します。 </br> さらに、次の前提条件を確認して、プッシュ インストールを正常に完了します。<br> 詳細については、「[WMI の問題のトラブルシューティング](#troubleshoot-wmi-issues)」を参照してください。


## <a name="error-95107---protection-could-not-be-enabled-ep0858"></a>エラー 95107 - 保護を有効にできませんでした (EP0858)

**エラー コード** | **考えられる原因** | **エラー固有の推奨事項**
--- | --- | ---
95107 </br>**メッセージ:** ソース マシンに対するモビリティ サービスのプッシュ インストールが、エラー コード **EP0858** で失敗しました。 <br> モビリティ サービスをインストールするために指定した資格情報が正しくないか、ユーザー アカウントに十分な権限がありません。 | ソース マシンにモビリティ サービスをインストールするために指定したユーザー資格情報が正しくありません。 | 構成サーバー上のソース マシンに対して指定したユーザー資格情報が正しいことを確認します。 <br> ユーザー資格情報を追加または編集するには、構成サーバーに移動して、**[Cspsconfigtool]** > **[アカウントの管理]** の順に選択します。 </br> さらに、次の[前提条件](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery)を確認して、プッシュ インストールを正常に完了します。


## <a name="error-95117---protection-could-not-be-enabled-ep0865"></a>エラー 95117 - 保護を有効にできませんでした (EP0865)

**エラー コード** | **考えられる原因** | **エラー固有の推奨事項**
--- | --- | ---
95117 </br>**メッセージ:** ソース マシンに対するモビリティ サービスのプッシュ インストールが、エラー コード **EP0865** で失敗しました。 <br> ソース マシンが実行されていないか、プロセス サーバーとソース マシン間のネットワーク接続に問題があります。 | プロセス サーバーとソース サーバー間のネットワーク接続の問題です。 | プロセス サーバーとソース サーバー間の接続を確認します。 </br> さらに、次の[前提条件](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery)を確認して、プッシュ インストールを正常に完了します。|

## <a name="error-95103---protection-could-not-be-enabled-ep0854"></a>エラー 95103 - 保護を有効にできませんでした (EP0854)

**エラー コード** | **考えられる原因** | **エラー固有の推奨事項**
--- | --- | ---
95103 </br>**メッセージ:** ソース マシンに対するモビリティ サービスのプッシュ インストールが、エラー コード **EP0854** で失敗しました。 <br> Windows Management Instrumentation (WMI) がソース マシンで許可されていないか、プロセス サーバーとソース マシン間のネットワーク接続に問題があります。| WMI が Windows ファイアウォールでブロックされています。 | Windows ファイアウォールで WMI を許可します。 **[Windows ファイアウォール]** > **[ファイアウォールを介したアプリまたは機能を許可]** の順に選択し、すべてのプロファイルで **[WMI]** を選択します。 </br> さらに、次の[前提条件](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery)を確認して、プッシュ インストールを正常に完了します。|

## <a name="error-95213---protection-could-not-be-enabled-ep0874"></a>エラー 95213 - 保護を有効にできませんでした (EP0874)

**エラー コード** | **考えられる原因** | **エラー固有の推奨事項**
--- | --- | ---
95213 </br>**メッセージ:** ソース マシン %SourceIP; に対するモビリティ サービスのインストールが、エラー コード **EP0874** で失敗しました。 <br> | ソース マシンのオペレーティング システムのバージョンがサポートされていません。 <br>| ソース マシンの OS バージョンがサポートされていることを確認します。 [サポート マトリックス](https://aka.ms/asr-os-support)を参照してください。 </br> さらに、次の[前提条件](https://aka.ms/pushinstallerror)を確認して、プッシュ インストールを正常に完了します。| 


## <a name="error-95108---protection-could-not-be-enabled-ep0859"></a>エラー 95108 - 保護を有効にできませんでした (EP0859)

**エラー コード** | **考えられる原因** | **エラー固有の推奨事項**
--- | --- | ---
95108 </br>**メッセージ:** ソース マシンに対するモビリティ サービスのプッシュ インストールが、エラー コード **EP0859** で失敗しました。 <br>| モビリティ サービスをインストールするために指定した資格情報が正しくないか、ユーザー アカウントに十分な権限がありません。 <br>| 指定された資格情報が**ルート** アカウントの資格情報であることを確認します。 ユーザー資格情報を追加または編集するには、構成サーバーに移動して、デスクトップにある **[Cspsconfigtool]** ショートカット アイコンを選択します。 **[アカウントの管理]** を選択して資格情報を追加または編集します。|

## <a name="error-95265---protection-could-not-be-enabled-ep0902"></a>エラー 95265 - 保護を有効にできませんでした (EP0902)

**エラー コード** | **考えられる原因** | **エラー固有の推奨事項**
--- | --- | ---
95265 </br>**メッセージ:** ソース マシンへのモビリティ サービスのプッシュ インストールが成功しましたが、システムの変更内容を有効にするために再起動が必要です。 <br>| 古いバージョンのモビリティ サービスが、サーバーに既にインストールされています。| 仮想マシンのレプリケーションはシームレスに継続されます。<br> 次のメンテナンス期間中にサーバーを再起動して、モビリティ サービスの新しい拡張機能のメリットを活用します。|


## <a name="error-95224---protection-could-not-be-enabled-ep0883"></a>エラー 95224 - 保護を有効にできませんでした (EP0883)

**エラー コード** | **考えられる原因** | **エラー固有の推奨事項**
--- | --- | ---
95224 </br>**メッセージ:** ソース マシン %SourceIP; に対するモビリティ サービスのプッシュ インストールが、エラー コード **EP0883** で失敗しました。 前のインストールまたは更新からのシステムの再起動が保留されています。| 古いまたは互換性のないバージョンのモビリティ サービスをアンインストールしたところ、システムが再起動しませんでした。| サーバー上にモビリティ サービスのバージョンが存在しないことを確認します。 <br> サーバーを再起動し、保護の有効化ジョブを再実行します。|

## <a name="resource-to-troubleshoot-push-installation-problems"></a>プッシュ インストールの問題をトラブルシューティングするためのリソース

#### <a name="troubleshoot-file-and-print-sharing-issues"></a>ファイルと印刷の共有の問題をトラブルシューティングする
* [グループ ポリシーを使用してファイルの共有を有効または無効にする](https://technet.microsoft.com/library/cc754359(v=ws.10).aspx)
* [Windows ファイアウォールを介してファイルおよび印刷の共有を有効にする](https://technet.microsoft.com/library/ff633412(v=ws.10).aspx)

#### <a name="troubleshoot-wmi-issues"></a>WMI の問題のトラブルシューティング
* [基本的な WMI テスト](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [WMI のトラブルシューティング](https://msdn.microsoft.com/library/aa394603(v=vs.85).aspx)
* [WMI スクリプトと WMI サービスに関する問題のトラブルシューティング](https://technet.microsoft.com/library/ff406382.aspx#H22)

## <a name="next-steps"></a>次の手順

VMware VM のディザスター リカバリーを設定する[方法を確認する](vmware-azure-tutorial.md)