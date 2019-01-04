---
title: VMware VM と物理サーバーの Azure へのディザスター リカバリーのためにモビリティ サービスをインストールする | Microsoft Docs
description: Azure Site Recovery を使用して VMware VM と物理サーバーを Azure にディザスター リカバリーするために、モビリティ サービス エージェントをインストールする方法について説明します。
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: 30b177578464653499cdcde8cacf65defa5548ef
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2018
ms.locfileid: "52846914"
---
# <a name="install-the-mobility-service-for-disaster-recovery-of-vmware-vms-and-physical-servers"></a>VMware VM と物理サーバーのディザスター リカバリーのためにモビリティ サービスをインストールする

[Azure Site Recovery](site-recovery-overview.md) を使用して VMware VM と物理サーバーのためのディザスター リカバリーを設定するとき、オンプレミスの VMware VM と物理サーバーごとに [Site Recovery モビリティ サービス](vmware-physical-mobility-service-overview.md)をインストールします。  このモビリティ サービスによって、コンピューター上のデータ書き込みがキャプチャされ、Site Recovery プロセス サーバーに転送されます。

## <a name="install-on-windows-machine"></a>Windows マシンへのインストール

保護する各 Windows マシンで、次のようにします。

1. マシンとプロセス サーバー間にネットワーク接続が存在することを確認します。 プロセス サーバーを別に設定していない場合は、既定で、構成サーバー上で実行されます。
1. プロセス サーバーがコンピューターへのアクセスに使用できるアカウントを作成します。 このアカウントには管理者権限 (ローカルまたはドメイン) が必要です 。 このアカウントは、プッシュ インストールとエージェントの更新にのみ使用します。
2. ドメイン アカウントを使用しない場合は、次のようにローカル コンピューターでリモート ユーザー アクセス コントロールを無効にします。
    - HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System レジストリ キーに、新しい DWORD 値**LocalAccountTokenFilterPolicy** を追加します。 値を **1** に設定します。
    -  これをコマンド プロンプトから行うには、次のコマンドを実行します。  
   `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d
3. 保護対象のマシンの Windows ファイアウォールで、**[Windows ファイアウォールを介したアプリまたは機能を許可]** を選択します。 **[ファイルとプリンターの共有]** と **[Windows Management Instrumentation (WMI)]** を有効にします。 コンピューターがドメインに属している場合は、グループ ポリシー オブジェクト (GPO) を使用してファイアウォールの設定を構成できます。

   ![ファイアウォールの設定](./media/vmware-azure-install-mobility-service/mobility1.png)

4. CSPSConfigtool で作成したアカウントを追加します。 これを行うには、構成サーバーにサインインします。
5. **cspsconfigtool.exe** を開きます。 これはデスクトップにショートカットがあり、%ProgramData%\home\svsystems\bin フォルダーに保存されています。
6. **[アカウントの管理]** タブの **[アカウントの追加]** を選択します。
7. 作成したアカウントを追加します。
8. コンピューターのレプリケーションを有効にするときに使用する資格情報を入力します。

## <a name="install-on-linux-machine"></a>Linux マシンにインストールする

保護する各 Linux マシンで、次のようにします。

1. Linux マシンとプロセス サーバー間にネットワーク接続が存在することを確認します。
2. プロセス サーバーがコンピューターへのアクセスに使用できるアカウントを作成します。 アカウントは、ソース Linux サーバーの **root** ユーザーである必要があります  このアカウントは、プッシュ インストールと更新にのみ使用します。
3. ソース Linux サーバーの /etc/hosts ファイルに、ローカル ホスト名を、すべてのネットワーク アダプターに関連付けられた IP アドレスにマップするエントリが存在することを確認します。
4. レプリケート対象のコンピューターに最新の openssh、openssh-server、openssl の各パッケージをインストールします。
5. SSH (Secure Shell) が有効になっており、ポート 22 で実行中であることを確認します。
4. sshd_config ファイルで SFTP サブシステムとパスワード認証を有効にします。 これを行うには、**root** としてサインインします。
5. **/etc/ssh/sshd_config** ファイルで、**PasswordAuthentication** で始まる行を見つけます。
6. この行のコメントを解除し、値を **yes** に変更します。
7. **Subsystem** で始まる行を見つけ、その行のコメントを解除します。

      ![Linux](./media/vmware-azure-install-mobility-service/mobility2.png)

8. **sshd** サービスを再起動します。
9. CSPSConfigtool で作成したアカウントを追加します。 これを行うには、構成サーバーにサインインします。
10. **cspsconfigtool.exe** を開きます。 これはデスクトップにショートカットがあり、%ProgramData%\home\svsystems\bin フォルダーに保存されています。
11. **[アカウントの管理]** タブの **[アカウントの追加]** を選択します。
12. 作成したアカウントを追加します。
13. コンピューターのレプリケーションを有効にするときに使用する資格情報を入力します。

## <a name="next-steps"></a>次の手順

モビリティ サービスがインストールされたら、Azure portal で、**[+ レプリケート]** を選択し、これらの VM の保護を開始します。 [VMware VM](vmware-azure-enable-replication.md) および[物理サーバー](physical-azure-disaster-recovery.md#enable-replication)でレプリケーションを有効にする方法についてさらに学習します。


