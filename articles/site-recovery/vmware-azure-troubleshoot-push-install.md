---
title: レプリケーション (VMware から Azure へ) を有効にする際のモビリティ サービスのプッシュ インストールに関する問題のトラブルシューティング | Microsoft Docs
description: Azure 仮想マシンのレプリケート時にモビリティ サービス/プッシュ インストールに関するエラーをトラブルシューティングします。
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.author: ramamill
ms.date: 09/19/2018
ms.openlocfilehash: 4c57d048f4c3222ac180355a6a700562415f601c
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2018
ms.locfileid: "49390195"
---
# <a name="troubleshoot-mobility-service-push-installation-issues"></a>モビリティ サービスのプッシュ インストールに関する問題のトラブルシューティング

モビリティ サービスのインストールは、レプリケーションを有効にする際の重要な手順です。 前提条件を満たしていて、サポート対象の構成を使用していれば、この手順は成功します。 モビリティ サービスのインストール時に直面する最も一般的な問題の原因は次のとおりです。

* 資格情報/特権のエラー
* 接続エラー
* サポート対象外のオペレーティング システム

レプリケーションを有効にすると、Azure Site Recovery はモビリティ サービス エージェントを仮想マシンにプッシュ インストールしようとします。 その一環として、構成サーバーは仮想マシンとの接続およびエージェントのコピーを試行します。 インストールを成功させるには、以下に示す段階的なトラブルシューティング ガイダンスに従ってください。

## <a name="credentials-check-errorid-95107--95108"></a>資格情報チェック (ErrorID: 95107 および 95108)

* レプリケーションを有効にする際に選択したユーザー アカウントが**有効かつ正確**なものであることを確認します。
* Azure Site Recovery には、プッシュ インストールを実行するための**管理者特権**が必要です。
  * Windows の場合は、ソース マシンに対する管理用のアクセス権 (ローカルまたはドメイン) がユーザー アカウントにあること確認します。
  * ドメイン アカウントを使用していない場合、ローカル コンピューターでリモート ユーザー アクセス制御を無効にする必要があります。
    * リモート ユーザー アクセス制御を無効にするには、HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System レジストリ キーに新しい DWORD として LocalAccountTokenFilterPolicy を追加します。 値を 1 に設定します。 この手順を実行するには、コマンド プロンプトで次のコマンドを実行します。

         `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
  * Linux でモビリティ エージェントのインストールを成功させるには、ルート アカウントを選択する必要があります。

選択したユーザー アカウントの資格情報を変更する場合は、[ここ](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)に示す手順に従ってください。

## <a name="connectivity-check-errorid-95117--97118"></a>**接続チェック (ErrorID: 95117 および 97118)**

* 構成サーバーからソース マシンに ping を実行できることを確認します。 レプリケーションを有効にする際にスケールアウト プロセス サーバーを選択した場合は、プロセス サーバーからソース マシンに ping を実行できることを確認します。
  * ソース サーバー マシンのコマンド ラインから、Telnet を使用して、次のように https ポート (既定値は 9443) を指定して構成サーバー/スケールアウト プロセス サーバーを ping し、ネットワーク接続の問題であるか、ファイアウォールのポート ブロックの問題であるかを確認します。

     `telnet <CS/ scale-out PS IP address> <port>`

  * 接続できない場合は、構成サーバー/スケールアウト プロセス サーバーで受信ポート 9443 を許可します。
  * サービス **InMage Scout VX Agent – Sentinel/Outpost** の状態を確認します。 実行されていない場合は、サービスを開始します。

* また、**Linux VM** の場合は次のことを確認します。
  * 最新の openssh、openssh-server、および openssl パッケージがインストールされているかどうかを確認します。
  * SSH (Secure Shell) が有効になっており、ポート 22 で実行中であることを確認します。
  * SFTP サービスが実行中である必要があります。 sshd_config ファイルで SFTP サブシステムとパスワード認証を有効にするには、次の手順を実行します。
    * root としてサインインします。
    * /etc/ssh/sshd_config ファイルに移動し、PasswordAuthentication で始まる行を見つけます。
    * この行のコメントを解除し、値を yes に変更します。
    * Subsystem で始まる行を見つけ、その行のコメントを解除します。
    * sshd サービスを再起動します。
* 一定の期間が経過した後に適切な応答がない場合は、接続の試行が失敗した可能性があります。または、接続されたホストが応答しなかったため接続を確立できませんでした。
* 接続/ネットワーク/ドメインに関連する問題である可能性があります。 また、DNS 名解決の問題または TCP ポート消費の問題が原因である可能性もあります。 ドメインにそのような既知の問題がないかどうかを確認してください。

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>ファイルとプリンターの共有サービス チェック (ErrorID: 95105 および 95106)

接続チェックが完了したら、ファイルとプリンターの共有サービスが仮想マシンで有効になっているかどうかを確認します。

**Windows 2008 R2 およびそれ以前のバージョン**の場合は、次の手順を実行します。

* Windows ファイアウォールを介してファイルとプリンターの共有を有効にする
  * コントロール パネルを開きます。[システムとセキュリティ]、[Windows ファイアウォール] の順に選択し、左側のウィンドウで [詳細設定] をクリックし、コンソール ツリーの [受信の規則] をクリックします。
  * [ファイルとプリンターの共有 (NB セッション受信)] 規則と [ファイルとプリンターの共有 (SMB 受信)] 規則を探します。 各規則を右クリックして、**[規則の有効化]** をクリックします。
* グループ ポリシーを使用してファイル共有を有効にする
  * [スタート] メニューに移動し、「gpmc.msc」と入力して検索します。
  * ナビゲーション ウィンドウで、[ローカル コンピューター ポリシー]、[ユーザーの構成]、[管理用テンプレート]、[Windows コンポーネント]、および [ネットワーク共有] の各フォルダーを順に開きます。
  * 詳細ウィンドウで、**[ユーザーがプロファイル内のファイルを共有できないようにします]** をダブルクリックします。 グループ ポリシー設定を無効にして、ユーザーがファイルを共有できるようにするには、[無効] をクリックします。 [OK] をクリックして変更を保存します。 詳細については、 [ここ](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10))をクリックしてください。

**より新しいバージョン**の場合は、[ここ](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery)に記載されている手順に従ってファイルとプリンターの共有を有効にします。

## <a name="windows-management-instrumentation-wmi-configuration-check"></a>Windows Management Instrumentation (WMI) の構成チェック

ファイルとプリンターのサービスのチェックが完了したら、ファイアウォール経由で WMI サービスを有効にします。

* コントロール パネルで、[セキュリティ] をクリックし、[Windows ファイアウォール] をクリックします。
* [設定の変更] をクリックし、[例外] タブをクリックします。
* [例外] ウィンドウで、Windows Management Instrumentation (WMI) のチェック ボックスをオンにして、ファイアウォール経由の WMI トラフィックを有効にします。 

また、コマンド プロンプトで、ファイアウォール経由の WMI トラフィックを有効にすることもできます。 次のコマンドを使用します。`netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes`
WMI のトラブルシューティングに関するその他の記事を次に示します。

* [基本的な WMI テスト](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [WMI のトラブルシューティング](https://msdn.microsoft.com/library/aa394603(v=vs.85).aspx)
* [WMI スクリプトと WMI サービスに関する問題のトラブルシューティング](https://technet.microsoft.com/library/ff406382.aspx#H22)

## <a name="unsupported-operating-systems"></a>サポート対象外のオペレーティング システム

問題が発生するもう 1 つの最も一般的な理由は、サポート対象外のオペレーティング システムです。 モビリティ サービスのインストールを成功させるには、サポート対象のオペレーティング システム/カーネルのバージョンを使用してください。

Azure Site Recovery でサポートされているオペレーティング システムについては、[サポート マトリックス ドキュメント](vmware-physical-azure-support-matrix.md#replicated-machines)を参照してください。

## <a name="next-steps"></a>次の手順

VMware VM のディザスター リカバリーを設定する[方法を確認する](vmware-azure-tutorial.md)