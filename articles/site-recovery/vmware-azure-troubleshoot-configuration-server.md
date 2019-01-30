---
title: Azure Site Recovery を使用して VMware VM と物理サーバーを Azure にディザスター リカバリーする間の構成サーバーでの問題のトラブルシューティング | Microsoft Docs
description: この記事では、Azure Site Recovery を使用した VMware VM と物理サーバーの Azure へのディザスター リカバリーのために構成サーバーをデプロイする場合のトラブルシューティング情報を提供します。
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 01/14/2019
ms.author: ramamill
ms.openlocfilehash: 0eebfd8b75f428d3b8f6024ed6ee71c18c1309f6
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2019
ms.locfileid: "54435976"
---
# <a name="troubleshoot-configuration-server-issues"></a>構成サーバーの問題のトラブルシューティング

この記事は、[Azure Site Recovery](site-recovery-overview.md) 構成サーバーをデプロイおよび管理するときの問題のトラブルシューティングに役立ちます。 構成サーバーは管理サーバーとして機能します。 構成サーバーは、Site Recovery を使用してオンプレミスの VMware VM と物理サーバーを Azure にディザスター リカバリーするように設定するときに使用されます。 以降のセクションでは、新しい構成サーバーを追加するときや構成サーバーを管理するときに発生する可能性がある最も一般的なエラーについて説明します。

## <a name="registration-failures"></a>登録エラー

ソース マシンは、モビリティ エージェントをインストールするときに構成サーバーに登録されます。 この手順中に発生したエラーはすべて、以下のガイドラインに従ってデバッグできます。

1. C:\ProgramData\ASR\home\svsystems\var\configurator_register_host_static_info.log ファイルを開きます  (ProgramData フォルダーは非表示のフォルダーである場合があります。 ProgramData フォルダーが表示されない場合は、エクスプローラーの **[表示]** タブにある **[表示/非表示]** セクションで、**[Hidden items]\(非表示の項目\)** チェック ボックスをオンにします)。エラーは、複数の問題が原因で発生する可能性があります。

2. 文字列「**No Valid IP Address found (有効な IP アドレスが見つかりません)**」を検索します。 この文字列が見つかった場合:
    1. 要求されているホスト ID がソース マシンのホスト ID と同じであることを確認します。
    2. 物理 NIC に 1 つ以上の IP アドレスが割り当てられていることを確認します。 構成サーバーへのエージェントの登録が成功するためには、ソース マシンの物理 NIC に 1 つ以上の有効な IP v4 アドレスが割り当てられている必要があります。
    3. ソース マシン上で次のいずれかのコマンドを実行して、ソース マシンのすべての IP アドレスを取得します。
      - Windows の場合: `> ipconfig /all`
      - Linux の場合: `# ifconfig -a`

3. 文字列「**No Valid IP Address found (有効な IP アドレスが見つかりません)**」が見つからない場合は、文字列「**Reason=>NULL (理由 =>NULL)**」を検索します。 このエラーは、ソース マシンが空のホストを使用して構成サーバーに登録された場合に発生します。 この文字列が見つかった場合:
    - 問題を解決した後で、「[ソース マシンを構成サーバーに登録する](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server)」に記載されたガイドラインに従って、登録を手動で再試行してください。

4. 文字列「**Reason=>NULL (理由 =>NULL)**」が見つからない場合は、ソース マシン上で C:\ProgramData\ASRSetupLogs\UploadedLogs\ASRUnifiedAgentInstaller.log ファイルを開きます  (ProgramData フォルダーは非表示のフォルダーである場合があります。 ProgramData フォルダーが表示されない場合は、エクスプローラーの **[表示]** タブにある **[表示/非表示]** セクションで、**[Hidden items]\(非表示の項目\)** チェック ボックスをオンにします)。エラーは、複数の問題が原因で発生する可能性があります。 

5. 文字列「**post request:(7) - Couldn't connect to server (POST 要求: (7) - サーバーに接続できませんでした)**」を検索します。 この文字列が見つかった場合:
    1. ソース マシンと構成サーバーの間におけるネットワークの問題を解決します。 ping、traceroute、Web ブラウザーなどのネットワーク ツールを使用して、ソース マシンから構成サーバーにアクセスできることを確認します。 ソース マシンがポート 443 経由で構成サーバーにアクセスできることを確認します。
    2. ソース マシン上のファイアウォール規則によって、ソース マシンと構成サーバー間の接続がブロックされていないかどうかを調べます。 接続の問題がある場合は、お客様のネットワーク管理者と協力して解決してください。
    3. 「[ウイルス対策プログラムからの Azure Site Recovery フォルダーの除外](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)」の一覧に記載されているフォルダーがウイルス対策プログラムから除外されていることを確認します。
    4. ネットワークの問題を解決したら、「[ソース マシンを構成サーバーに登録する](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server)」に記載されたガイドラインに従って、登録を再試行してください。

6. 文字列「**post request:(7) - Couldn't connect to server (POST 要求: (7) - サーバーに接続できませんでした)**」が見つからない場合は、同じログ ファイル内で文字列「**request:(60) - Peer certificate cannot be authenticated with given CA certificates (要求: (60) - ピア証明書は与えられた CA 証明書で認証できません)**」を検索します。 このエラーは、構成サーバーの証明書の有効期限が切れているか、またはソース マシンで TLS 1.0 以降の SSL プロトコルがサポートされていないことが原因で発生する可能性があります。 また、ファイアウォールによってソース マシンと構成サーバー間の SSL 通信がブロックされている場合にも発生する可能性があります。 この文字列が見つかった場合: 
    1. 解決するには、ソース マシン上の Web ブラウザーを使用して構成サーバーの IP アドレスに接続します。 URI https:\/\/<configuration server IP address\>:443/ を使用します。 ソース マシンがポート 443 経由で構成サーバーにアクセスできることを確認します。
    2. ソース マシンから構成サーバーにアクセスするためにソース マシン上のファイアウォール規則を追加または削除する必要がないかどうかを調べます。 使用されている可能性のあるファイアウォール ソフトウェアにはさまざまな種類があるため、必要なすべてのファイアウォール構成の一覧をここで示すことはできません。 接続の問題がある場合は、お客様のネットワーク管理者と協力して解決してください。
    3. 「[ウイルス対策プログラムからの Azure Site Recovery フォルダーの除外](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)」の一覧に記載されているフォルダーがウイルス対策プログラムから除外されていることを確認します。  
    4. 問題を解決したら、「[ソース マシンを構成サーバーに登録する](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server)」に記載されたガイドラインに従って、登録を再試行してください。

7. Linux では、<INSTALLATION_DIR\>/etc/drscout.conf 内のプラットフォームの値が破損していると登録に失敗します。 この問題を特定するには、/var/log/ua_install.log ファイルを開きます。 文字列「**Aborting configuration as VM_PLATFORM value is either null or it is not VmWare/Azure (VM_PLATFORM の値が null であるか、VmWare/Azure でないため、構成を中止しています)**」を検索します。 プラットフォームは **VmWare** または **Azure** に設定する必要があります。 drscout.conf ファイルが破損している場合は、[モビリティ エージェントをアンインストール](vmware-physical-mobility-service-overview.md#uninstall-the-mobility-service)してから再インストールすることをお勧めします。 アンインストールに失敗した場合は、次の手順を実行します。
    1. Installation_Directory/uninstall.sh ファイルを開き、**StopServices** 関数の呼び出しをコメントアウトします。
    2. Installation_Directory/Vx/bin/uninstall.sh ファイルを開き、**stop_services** 関数の呼び出しをコメントアウトします。
    3. Installation_Directory/Fx/uninstall.sh ファイルを開き、Fx サービスの停止を試行するセクション全体をコメントアウトします。
    4. モビリティ エージェントを[アンインストール](vmware-physical-mobility-service-overview.md#uninstall-the-mobility-service)します。 アンインストールが成功したら、システムを再起動して、モビリティ エージェントの再インストールを試みます。

## <a name="installation-failure-failed-to-load-accounts"></a>インストールの失敗: アカウントを読み込めませんでした

このエラーは、モビリティ エージェントをインストールして構成サーバーに登録するときにサービスがトランスポート接続からデータを読み取れない場合に発生します。 この問題を解決するには、お客様のソース マシン上で TLS 1.0 を有効にします。

## <a name="vcenter-discovery-failures"></a>vCenter の検出エラー

VCenter の検出のエラーを解決するには、その vCenter サーバーがバイパス リストのプロキシ設定に追加されていることを確認します。このアクティビティを実行するには、

- システム ユーザーのコンテンツにアクセスするために、[こちら](https://aka.ms/PsExec)から PsExec ツールをダウンロードします。
- コマンド ライン psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe" を実行して、インターネット エクスプローラーでシステム ユーザーのコンテンツを開きます。
- IE にプロキシ設定を追加し、tmanssvc サービスを再開します。
- DRA プロキシ設定を構成するために、cd C:\Program Files\Microsoft Azure Site Recovery Provider を実行します。
- 次に、DRCONFIGURATOR.EXE /configure /AddBypassUrls を実行します (「[構成サーバーをデプロイする](vmware-azure-deploy-configuration-server.md#configure-settings)」の **vCenter Server/vSphere ESXi Server を構成する**手順の間に提供された vCenter Server の IP アドレス/FQDN を追加します)。

## <a name="change-the-ip-address-of-the-configuration-server"></a>構成サーバーの IP アドレスの変更

構成サーバーの IP アドレスは変更しないことを強くお勧めします。 構成サーバーに割り当てられているすべての IP アドレスが静的 IP アドレスであるようにしてください。 DHCP IP アドレスは使用しないでください。

## <a name="acs50008-saml-token-is-invalid"></a>ACS50008:SAML トークンが無効です

このエラーを回避するには、お客様のシステム クロックの時刻と現地時刻に 15 分以上の差がないようにします。 インストーラーを再実行して登録を完了します。

## <a name="failed-to-create-a-certificate"></a>証明書を作成できませんでした

Site Recovery の認証に必要な証明書を作成できません。 ローカル管理者として設定を実行していることを確認してから、設定を再実行します。

## <a name="register-source-machine-with-configuration-server"></a>ソース マシンを構成サーバーに登録する

### <a name="if-the-source-machine-runs-windows"></a>ソース マシン上で Windows が実行されている場合

ソース マシン上で次のコマンドを実行します。

```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <configuration server IP address> /PassphraseFilePath <passphrase file path>
  ```

設定 | 詳細
--- | ---
使用法 | UnifiedAgentConfigurator.exe  /CSEndPoint <configuration server IP address\> /PassphraseFilePath <passphrase file path\>
エージェント構成ログ | %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log の下にあります。
/CSEndPoint | 必須パラメーターです。 構成サーバーの IP アドレスを指定します。 任意の有効な IP アドレスを使用します。
/PassphraseFilePath |  必須。 パスフレーズの場所。 任意の有効な UNC またはローカル ファイル パスを使用します。

### <a name="if-the-source-machine-runs-linux"></a>ソース マシン上で Linux が実行されている場合

ソース マシン上で次のコマンドを実行します。

```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <configuration server IP address> -P /var/passphrase.txt
  ```

設定 | 詳細
--- | ---
使用法 | cd /usr/local/ASR/Vx/bin<br /><br /> UnifiedAgentConfigurator.sh -i <configuration server IP address\> -P <passphrase file path\>
-i | 必須パラメーターです。 構成サーバーの IP アドレスを指定します。 任意の有効な IP アドレスを使用します。
-P |  必須。 パスフレーズが保存されているファイルの完全なファイル パス。 任意の有効なフォルダーを使用します。

