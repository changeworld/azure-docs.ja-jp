---
title: GRUB とシングル ユーザー モード用の Azure シリアル コンソール | Microsoft Docs
description: Azure 仮想マシンでの GRUB 用シリアル コンソールの使用。
services: virtual-machines-linux
documentationcenter: ''
author: alsin
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: 059cb0cbc7e62af16dbf95693be421feebcc1ee0
ms.sourcegitcommit: 76797c962fa04d8af9a7b9153eaa042cf74b2699
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2018
ms.locfileid: "42142464"
---
# <a name="use-serial-console-to-access-grub-and-single-user-mode"></a>シリアル コンソール を使用して GRUB とシングル ユーザー モードにアクセスする
シングル ユーザー モードは、最小限の機能を持つ最小限の環境です。 バックグラウンドで実行されるサービスが少なくなるため、起動の問題やネットワークの問題を調査する場合に便利です。また、実行レベルによっては、ファイル システムが自動的にマウントされないこともあります。 これは、破損したファイル システム、壊れた fstab、またはネットワーク接続 (誤った iptables 構成) などの状況を調査する場合に便利です。

VM が起動できない場合、ディストリビューションによっては自動的にシングル ユーザー モードまたは緊急モード切り替わります。 一方、シングル ユーザー モードまたは緊急モードに自動的に切り替わる前に、追加の設定が必要な場合もあります。

シングル ユーザー モードにアクセスできるようにするには、VM 上で GRUB が有効なことを確認する必要があります。 ディストリビューションによっては、GRUB が有効なことを確認するための設定作業があります。 


## <a name="access-for-red-hat-enterprise-linux-rhel"></a>Red Hat Enterprise Linux (RHEL) へのアクセス
正常に起動できない場合、RHEL は自動的にシングル ユーザー モードに切り替わります。 ただし、シングル ユーザー モード用に root アクセス権を設定していない場合は、root パスワードがないため、ログインできません。 これには回避策があります (後述の「RHEL のシングル ユーザー モードを手動で開始する」を参照してください) が、最初は root アクセス権を設定することをお勧めします。

### <a name="grub-access-in-rhel"></a>RHEL での GRUB アクセス
RHEL には GRUB が付属しており、すぐに使用できます。 GRUB を開始するには、`sudo reboot` を使用して VM を再起動し、いずれかのキーを押します。 GRUB 画面が表示されます。

> 注: Red Hat には、再起動してレスキュー モード、緊急モード、デバッグ モード、およびルート パスワードのリセットを実行するためのドキュメントも用意されています。 アクセスするには、[こちら](https://aka.ms/rhel7grubterminal)をクリックしてください。

### <a name="set-up-root-access-for-single-user-mode-in-rhel"></a>RHEL でシングル ユーザー モードの root アクセス権を設定する
RHEL のシングル ユーザー モードを使用するには、root ユーザーを有効にする必要があります。root ユーザーは既定では無効になっています。 シングル ユーザー モードを有効にする必要がある場合は、次の手順のようにします。

1. SSH を使って RedHat システムにログインします
1. SD に切り替えます
1. root ユーザーのパスワードを有効にします 
    * `passwd root` (強力な root パスワードを設定します)
1. root ユーザーが ttyS0 経由でのみログインできることを確認します
    * `edit /etc/ssh/sshd_config`: PermitRootLogIn が no に設定されていることを確認します
    * `edit /etc/securetty file`: ttyS0 でのログインのみを許可します 

ここで、システムがシングル ユーザー モードで起動した場合、root パスワードでログインできます。

RHEL 7.4 以降または 6.9 以降の場合の代わりの方法として、GRUB プロンプトでシングル ユーザー モードを有効にできます。方法については[こちら](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single)をご覧ください。

### <a name="manually-enter-single-user-mode-in-rhel"></a>RHEL のシングル ユーザー モードを手動で開始する
前述の手順で GRUB と root アクセス権を設定しした場合は、以下の手順でシングル ユーザー モードを開始することができます。

1. VM の再起動中に Esc キーを押して GRUB を開始します
1. GRUB では、e キーを押して起動する OS を編集します (通常は最初の行です)
1. カーネル行を探します (Azure の場合は `linux16` で始まります)
1. Ctrl キーを押しながら E キーを押して行末に移動します
1. 行末に `systemd.unit=rescue.target` を追加します
    * これでシングル ユーザー モードで起動されます。 緊急モードを使用する場合は、`systemd.unit=rescue.target` ではなく `systemd.unit=emergency.target` を行末に追加します
1. Ctrl キーを押しながら X キーを押して終了し、適用された設定で再起動します
1. シングル ユーザー モードが開始される前に管理者パスワードの入力が求められます (前述の手順で作成したパスワードを入力します)    

    ![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-enter-emergency-shell.gif)

### <a name="enter-single-user-mode-without-root-account-enabled-in-rhel"></a>RHEL で root アカウントを有効にせずにシングル ユーザー モードを開始する
前述の手順を実行して root ユーザーを有効にしなかった場合でも、root パスワードをリセットできます。 次の手順を実行してください。

> 注意: SELinux を使用している場合は、ルート パスワードをリセットするときに、[こちら](https://aka.ms/rhel7grubterminal)の Red Hat のドキュメントで説明されている追加の手順を実行してください。

1. VM の再起動中に Esc キーを押して GRUB を開始します
1. GRUB では、e キーを押して起動する OS を編集します (通常は最初の行です)
1. カーネル行を探します (Azure の場合は `linux16` で始まります)
1. 行末に `rd.break` を追加し、`rd.break` の前にスペースがあることを確認します (以下の例を参照してください)
    - [こちら](https://aka.ms/rhel7rootpassword)の Red Hat のドキュメントで説明されているように、コントロールが `initramfs` から `systemd` に渡される前に起動プロセスが中断されます。
1. Ctrl キーを押しながら X キーを押して終了し、適用された設定で再起動します
1. 起動すると、読み取り専用のファイル システムで緊急モードが開始されます。 シェルに `mount -o remount,rw /sysroot` と入力して、読み取り/書き込みアクセス許可でルート ファイル システムを再マウントします
1. シングル ユーザー モードで起動したら、`chroot /sysroot` と入力して `sysroot` jail に切り替えます
1. これで root になりました。 `passwd` を使用して root パスワードをリセットしてから、前述の手順を使用してシングル ユーザー モードを開始することができます。 完了したら、`reboot -f` と入力して再起動します。

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-emergency-mount-no-root.gif)

> 注: 前述の手順を実行すると、緊急シェルが開始されるので、`fstab` の編集などのタスクも実行できます。 ただし、一般的に受け入れられている提案は、root のパスワードをリセットし、それを使用してシングル ユーザー モードを開始することです。 


## <a name="access-for-centos"></a>CentOS へのアクセス
Red Hat Enterprise Linux と同様に、CentOS のシングル ユーザー モードを使用するには、GRUB が必要であり、root ユーザーを有効にする必要があります。 

### <a name="grub-access-in-centos"></a>CentOS での GRUB アクセス
CentOS には GRUB が付属しており、すぐに使用できます。 GRUB を開始するには、`sudo reboot` を使用して VM を再起動し、いずれかのキーを押します。 GRUB 画面が表示されます。

### <a name="single-user-mode-in-centos"></a>CentOS でのシングル ユーザー モード
CentOS でシングル ユーザー モードを有効にするには、前述の RHEL の手順を実行します。

## <a name="access-for-ubuntu"></a>Ubuntu へのアクセス 
Ubuntu イメージの場合、root のパスワードは必要ありません。 システムがシングル ユーザー モードで起動した場合、資格情報を追加指定しなくても使用できます。 

### <a name="grub-access-in-ubuntu"></a>Ubuntu での GRUB アクセス
GRUB にアクセスするには、VM の起動中に Enter キーを長押しします。

### <a name="single-user-mode-in-ubuntu"></a>Ubuntu でのシングル ユーザー モード
正常に起動できない場合、Ubuntu は自動的にシングル ユーザー モードに切り替わります。 シングル ユーザー モードを手動で開始するには、次の手順を実行します。

1. GRUB から E キーを押してブート エントリ (Ubuntu エントリ) を編集します
1. `linux` で始まる行を探し、次に `ro` を探します
1. `ro` の 後に `single` を追加し、`single` の前後にはスペースを空けます
1. Ctrl キーを押しながら X キーを押してこれらの設定で再起動し、シングル ユーザー モードを開始します

## <a name="access-for-coreos"></a>CoreOS へのアクセス
CoreOS でシングル ユーザー モードを使用するには、GRUB を有効にする必要があります。 

### <a name="grub-access-in-coreos"></a>CoreOS での GRUB アクセス
GRUB にアクセスするには、VM の起動中に任意のキーを押します。

### <a name="single-user-mode-in-coreos"></a>CoreOS でのシングル ユーザー モード
正常に起動できない場合、CoreOS は自動的にシングル ユーザー モードに切り替わります。 シングル ユーザー モードを手動で開始するには、次の手順を実行します。
1. GRUB から E キーを押してブート エントリを編集します
1. `linux$` で始まる行を探します。 別の if/else 句でカプセル化された 2 があります。
1. 両方の `linux$` 行の末尾に `coreos.autologin=ttyS0` を追加します。
1. Ctrl キーを押しながら X キーを押してこれらの設定で再起動し、シングル ユーザー モードを開始します

## <a name="access-for-suse-sles"></a>SUSE SLES へのアクセス
SLES 12 SP3 以降の新しいイメージでは、システムが緊急モードで起動している場合、シリアル コンソールを使ってアクセスできます。 

### <a name="grub-access-in-suse-sles"></a>SUSE SLES での GRUB アクセス
SLES での GRUB アクセスには、YaST を使用してブートローダーを設定する必要があります。 これを行うには、次の手順を実行します。

1. SLES VM に ssh でログインし、`sudo yast bootloader` を実行します。 `tab`キー、`enter` キー、矢印キーを使用してメニュー内を移動します。 
1. `Kernel Parameters` に移動し、`Use serial console` をオンにします。 
1. Console arguments に `serial --unit=0 --speed=9600 --parity=no` を追加します

1. F10 キーを押して設定を保存し、終了します
1. GRUB を開始するには、VM を再起動し、起動シーケンス中に任意のキーを押します (GRUB が画面に表示されたままになります)
    - GRUB の既定のタイムアウトは 1 秒です。 これを変更するには、`/etc/default/grub` で `GRUB_TIMEOUT` 変数を変更します

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-sles-yast-grub-config.gif)

### <a name="single-user-mode-in-suse-sles"></a>SUSE SLES でのシングル ユーザー モード
SLES が正常に起動できない場合は、自動的に緊急シェルが開始されます。 緊急シェルを手動で開始するには、次の手順を実行します。

1. GRUB から E キーを押してブート エントリ (SLES エントリ) を編集します
1. `linux` で始まるカーネル行を探します
1. `systemd.unit=emergency.target` を行末に追加します
1. Ctrl キーを押しながら X キーを押してこれらの設定で再起動し、緊急シェルを開始します
> 緊急シェルは、_読み取り専用_ファイルシステムで開始される点に注意してください。 任意のファイルを編集する場合は、読み取り/書き込みアクセス許可でファイルシステムを再マウントする必要があります。 これを行うには、シェルに `mount -o remount,rw /` と入力します

## <a name="access-for-oracle-linux"></a>Oracle Linux へのアクセス
Red Hat Enterprise Linux と同様に、Oracle Linux のシングル ユーザー モードを使用するには、GRUB が必要であり、root ユーザーを有効にする必要があります。 

### <a name="grub-access-in-oracle-linux"></a>Oracle Linux での GRUB アクセス
Oracle Linux には GRUB が付属しており、すぐに使用できます。 GRUB を開始するには、`sudo reboot` を使用して VM を再起動し、Esc キーを押します。 GRUB 画面が表示されます。

### <a name="single-user-mode-in-oracle-linux"></a>Oracle Linux でのシングル ユーザー モード
Oracle Linux でシングル ユーザー モードを有効にするには、前述の RHEL の手順を実行します。

## <a name="next-steps"></a>次の手順
* 主要なシリアル コンソールの Linux ドキュメントのページについては、[こちら](serial-console.md)を参照してください。
* [NMI および SysRq 呼び出し](serial-console-nmi-sysrq.md)のためにシリアル コンソールを使用する
* シリアル コンソールは、[Windows](../windows/serial-console.md) VM でも使用可能
* [ブート診断](boot-diagnostics.md)の詳細を表示する