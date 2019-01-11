---
title: Azure Site Recovery を使用して VMware VM と物理サーバーを Azure にディザスター リカバリーする間の構成サーバーでの問題のトラブルシューティング | Microsoft Docs
description: この記事では、Azure Site Recovery による VMware VM と物理サーバーの Azure へのディザスター リカバリーのために構成サーバーをデプロイする場合のトラブルシューティング情報を提供します。
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 12/17/2018
ms.author: ramamill
ms.openlocfilehash: f5c8241907459a06f0a6206ae6865cdf3fe9ab89
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/03/2019
ms.locfileid: "53998967"
---
# <a name="troubleshoot-configuration-server-issues"></a>構成サーバーの問題のトラブルシューティング

この記事は、[Azure Site Recovery](site-recovery-overview.md) 構成サーバーをデプロイおよび管理するときの問題のトラブルシューティングに役立ちます。 構成サーバーは管理サーバーとして機能し、Site Recovery を使用してオンプレミスの VMware VM と物理サーバーを Azure にディザスター リカバリーするよう設定するために使用されます。 以降のセクションでは、新しい構成サーバーを追加するときや構成サーバーを管理するときに発生する最も一般的なエラーについて説明します。

## <a name="registration-failures"></a>登録エラー

ソース マシンは、モビリティ エージェントのインストール中に構成サーバーに登録されます。 この手順中に発生したエラーは、以下のガイドラインに従ってデバッグできます。

1. C:\ProgramData\ASR\home\svsystems\var\configurator_register_host_static_info.log ファイルに移動します。 ProgramData は隠しフォルダーである場合があります。 見つからない場合は、フォルダーを再表示してください。 エラーは、複数の問題が原因で発生する可能性があります。
2. 文字列 "No Valid IP Address found (有効な IP アドレスが見つかりません)" を検索します。 文字列が見つかった場合、
    - 要求されたホスト ID がソース マシンと同じかどうかを確認します。
    - CS へのエージェントの登録が成功するためには、ソース マシンの物理 NIC に 1 つ以上の IP アドレスが割り当てられている必要があります。
    - ソース マシン上で `> ipconfig /all` コマンド (Windows OS の場合) または `# ifconfig -a` コマンド (Linux OS の場合) を実行して、ソース マシンのすべての IP アドレスを取得します。
    - エージェントを登録するには有効な IP v4 アドレスが物理 NIC に割り当てられている必要があることに注意してください。
3. 上記の文字列が見つからない場合は、文字列 "Reason" => "NULL" ("理由" => "NULL") を検索します。 見つかった場合、
    - ソース マシンで空のホストを使用して構成サーバーに登録すると、このエラーが発生します。
    - 問題を解決した後、[ここ](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server)に記載されたガイドラインに従って、登録を手動で再試行してください。
4. 上記の文字列が見つからない場合は、ソース マシンに移動して、ログ C:\ProgramData\ASRSetupLogs\UploadedLogs\* ASRUnifiedAgentInstaller.log を確認します。ProgramData は隠しフォルダーである場合があります。 見つからない場合は、フォルダーを再表示してください。 エラーは、複数の問題が原因で発生する可能性があります。 文字列 "post request: (7) - Couldn't connect to server (POST 要求: (7) - サーバーに接続できませんでした)" を検索します。 見つかった場合、
    - ソース マシンと構成サーバーの間におけるネットワークの問題を解決します。 ping、traceroute、Web ブラウザーなどのネットワーク ツールを使用して、ソース マシンから構成サーバーに到達できることを確認します。ソース マシンがポート 443 経由で構成サーバーに到達できることを確認します。
    - ソース マシン上にソース マシンと構成サーバーの間の接続をブロックしているファイアウォール規則があるかどうかを確認します。 お客様のネットワーク管理者と協力して、接続の問題を解決してください。
    - [ここ](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)で言及されているフォルダーはウイルス対策ソフトウェアから除外されるようにします。
    - ネットワークの問題を解決した後、[ここ](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server)に記載されたガイドラインに従って、登録を再試行してください。
5. 見つからない場合は、同じログ内で、文字列 "request: (60) - Peer certificate cannot be authenticated with given CA certificates (要求: (60) - ピア証明書は与えられた CA 証明書で認証できません)" を検索します。 見つかった場合、 
    - このエラーの原因として、構成サーバーの証明書が期限切れであるか、ソース マシンが TLS 1.0 以上の SSL プロトコルをサポートしていないか、ファイアウォールによってソース マシンと構成サーバーの間の SSL 通信がブロックされていることが考えられます。
    - 解決するには、ソース マシン上の Web ブラウザーで URI https://<CSIPADDRESS>:443/ を使用して、構成サーバーの IP アドレスに接続します。 ソース マシンがポート 443 経由で構成サーバーに到達できることを確認します。
    - ソース マシン上にソース マシンと構成サーバーの間の接続をブロックしているファイアウォール規則があるかどうかを確認します。 お客様のネットワーク管理者と協力して、接続の問題を解決してください。
    - [ここ](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)で言及されているフォルダーはウイルス対策ソフトウェアから除外されるようにします。  
    - 問題を解決した後、[ここ](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server)に記載されたガイドラインに従って、登録を再試行してください。
6. Linux では、<INSTALLATION_DIR>/etc/drscout.conf のプラットフォームの値が破損していると登録に失敗します。 特定するには、ログ /var/log/ua_install.log に移動します。 文字列 "Aborting configuration as VM_PLATFORM value is either null or it is not VmWare/Azure (VM_PLATFORM の値が null であるか、VmWare/Azure でないため、構成を中止しています)" が見つかります。 プラットフォームは "VmWare" または "Azure" に設定する必要があります。 drscout.conf ファイルが破損しているため、モビリティ エージェントを[アンインストール](vmware-physical-mobility-service-overview.md#uninstall-the-mobility-service)してから再インストールすることをお勧めします。 アンインストールに失敗した場合は、次の手順に従ってください。
    - Installation_Directory/uninstall.sh ファイルを開き、*StopServices* 関数の呼び出しをコメントアウトします
    - Installation_Directory/Vx/bin/uninstall ファイルを開き、`stop_services` 関数の呼び出しをコメントアウトします
    - Installation_Directory/Fx/uninstall ファイルを開き、Fx サービスの停止を試行するセクション全体をコメントアウトします。
    - モビリティ エージェントの[アンインストール](vmware-physical-mobility-service-overview.md#uninstall-the-mobility-service)を試みます。 アンインストールが成功したら、システムを再起動して、エージェントをもう一度インストールします。

## <a name="installation-failure---failed-to-load-accounts"></a>インストールの失敗 - アカウントを読み込めませんでした

このエラーは、モビリティ エージェントをインストールして構成サーバーに登録するときにサービスがトランスポート接続からデータを読み取れない場合に発生します。 解決するには、お客様のソース マシン上で TLS 1.0 を有効にします。

## <a name="change-ip-address-of-configuration-server"></a>構成サーバーの IP アドレスの変更

構成サーバーの IP アドレスは変更しないことを強くお勧めします。 構成サーバーに割り当てられているすべての IP アドレスが DHCP IP アドレスではなく静的 IP アドレスになるようにしてください。

## <a name="acs50008-saml-token-is-invalid"></a>ACS50008:SAML トークンが無効です

このエラーを回避するには、お客様のシステム クロックの時刻と現地時刻に 15 分以上の差がないようにします。 インストーラーを再実行して登録を完了します。

## <a name="failed-to-create-certificate"></a>証明書を作成できませんでした

Site Recovery の認証に必要な証明書を作成できません。 ローカル管理者として設定を実行していることを確認してから、設定を再実行します。

## <a name="register-source-machine-with-configuration-server"></a>ソース マシンを構成サーバーに登録する

### <a name="if-source-machine-has-windows-os"></a>ソース マシンの OS が Windows の場合

ソース マシンで次のコマンドを実行します

```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
  ```
**設定** | **詳細**
--- | ---
使用法 | UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
エージェント構成ログ | %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log の下。
/CSEndPoint | 必須パラメーターです。 構成サーバーの IP アドレスを指定します。 任意の有効な IP アドレスを使用します。
/PassphraseFilePath |  必須。 パスフレーズの場所。 任意の有効な UNC またはローカル ファイル パスを使用します。

### <a name="if-source-machine-has-linux-os"></a>ソース マシンの OS が Linux の場合

ソース マシンで次のコマンドを実行します

```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
  ```
**設定** | **詳細**
--- | ---
使用法 | cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i <CSIP> -P <PassphraseFilePath>
-i | 必須パラメーターです。 構成サーバーの IP アドレスを指定します。 任意の有効な IP アドレスを使用します。
-P |  必須。 パスフレーズが保存されているファイルの完全なファイル パス。 任意の有効なフォルダーを使用します