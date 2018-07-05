---
title: Azure 仮想マシンのシリアル コンソール | Microsoft Docs
description: Azure 仮想マシンの双方向シリアル コンソール。
services: virtual-machines-linux
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/05/2018
ms.author: harijay
ms.openlocfilehash: 99d09455ed73b366fb3acfb414b9bd095df6319b
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2018
ms.locfileid: "36962425"
---
# <a name="virtual-machine-serial-console-preview"></a>仮想マシンのシリアル コンソール (プレビュー) 


Azure の仮想マシンのシリアル コンソールでは、Linux および Windows 仮想マシンのテキスト ベースのコンソールにアクセスできます。 このシリアル接続は、仮想マシンの COM1 シリアル ポートに接続し、仮想マシンへのアクセスを提供します。仮想マシンのネットワーク/オペレーティング システムの状態とは無関係です。 現在、仮想マシンのシリアル コンソールへのアクセスは Azure Portal からのみ可能であり、仮想マシンへの VM 共同作成者以上のアクセス権を持つユーザーだけが使用できます。 

> [!Note] 
> プレビューは、使用条件に同意することを条件に使用することができます。 詳細については、「[Microsoft Azure プレビューの追加使用条件] (https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」をご覧ください。現在、このサービスは**パブリック プレビュー**段階にあり、仮想マシンのシリアル コンソールへのアクセスは全世界の Azure リージョンで提供されています。 現時点では、シリアル コンソールは、Azure Government、Azure Germany、Azure China Cloud では利用できません。


## <a name="prerequisites"></a>前提条件 

* リソース管理デプロイ モデルを使用している必要があります。 クラシック デプロイはサポートされていません。 
* 仮想マシンの[ブート診断](boot-diagnostics.md)が有効になっている必要があります。 
* シリアル コンソールを使用するアカウントには、VM の[共同作成者ロール](../../role-based-access-control/built-in-roles.md)と[ブート診断](boot-diagnostics.md)ストレージ アカウントが必要です。 
* Linux ディストリビューションに固有の設定については、「[Linux のシリアル コンソールへのアクセス](#accessing-serial-console-for-linux)」をご覧ください。


## <a name="open-the-serial-console"></a>シリアル コンソールを開く
仮想マシンのシリアル コンソールには、[Azure Portal](https://portal.azure.com) からのみアクセスできます。 ポータルを使用して仮想マシンのシリアル コンソールにアクセスする手順を次に示します。 

  1. Azure ポータルを開きます
  2. 左側のメニューで [Virtual Machines] を選択します。
  3. 一覧で VM をクリックします。 VM の概要ページが開きます。
  4. 下へスクロールして [サポート + トラブルシューティング] セクションを表示し、[シリアル コンソール (プレビュー)] をクリックします。 シリアル コンソールで新しいウィンドウが開き、接続が開始されます。

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)


> [!NOTE] 
> シリアル コンソールを使うには、パスワードが構成されたローカル ユーザーが必要です。 現時点では、SSH 公開キーだけが構成された VM は、シリアル コンソールにアクセスできません。 パスワードを持つローカル ユーザーを作成するには、「[VM アクセス拡張機能](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension)」に従ってください。

### <a name="disable-feature"></a>機能を無効にする
特定の VM のブート診断設定を無効にすることで、その VM のシリアル コンソール機能を非アクティブ化できます。

## <a name="serial-console-security"></a>シリアル コンソールのセキュリティ 

### <a name="access-security"></a>アクセス セキュリティ 
シリアル コンソールへのアクセスは、仮想マシンへの [VM 共同作成者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)以上のアクセス権を持つユーザーに制限されます。 シリアル コンソールには [Azure Portal](https://portal.azure.com) からアクセスするため、AAD テナントに Multi-Factor Authentication が必要な場合は、シリアル コンソールへのアクセスにも MFA が必要になります。

### <a name="channel-security"></a>チャネル セキュリティ
やり取りされるすべてのデータがネットワーク上で暗号化されます。

### <a name="audit-logs"></a>監査ログ
現在、シリアル コンソールへのすべてのアクセスが、仮想マシンの[ブート診断](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics)ログに記録されます。 これらのログへのアクセスは、Azure 仮想マシン管理者が所有し、制御します。  

>[!CAUTION] 
コンソールのアクセス パスワードはログに記録されませんが、コンソール内で実行されるコマンドにパスワード、シークレット、ユーザー名、またはその他の形式の個人を特定できる情報 (PII) が含まれていたり、出力されたりした場合、それらの情報は、シリアル コンソールのスクロールバック機能の実装の一部として、表示される他のすべてのテキストと共に仮想マシンのブート診断ログに書き込まれます。 これらのログは循環型であり、診断ストレージ アカウントに対する読み取りアクセス許可を持つユーザーだけがアクセスできます。ただし、シークレットや PII が含まれている可能性のあるものには SSH コンソールを使うというベスト プラクティスに従うことをお勧めします。 

### <a name="concurrent-usage"></a>同時使用
ユーザーがシリアル コンソールに接続しているときに、別のユーザーがその同じ仮想マシンへのアクセスを要求し、その要求が成功した場合、最初のユーザーが立ち上がって物理コンソールから離れ、新しいユーザーがそこに座るのと同様に、最初のユーザーが切断され、2 番目のユーザーが接続されます。

>[!CAUTION] 
これは、切断されたユーザーはログアウトされないことを意味します。 (SIGHUP または同様のメカニズムを使用して) 切断時に強制的にログアウトする機能は、まだロードマップにあります。 Windows では SAC で自動タイムアウトが有効になっていますが、Linux ではターミナルのタイムアウト設定を構成できます。 たとえば、10 分後にセッションがタイムアウトされるようにするには、コンソールへのログオンに使っているユーザーの .bash_profile または .profile に、`export TMOUT=600` を追加するだけです。

### <a name="disable-feature"></a>機能を無効にする
特定の VM のブート診断設定を無効にすることで、その VM のシリアル コンソール機能を非アクティブ化できます。

## <a name="common-scenarios-for-accessing-serial-console"></a>シリアル コンソールにアクセスする一般的なシナリオ 
シナリオ          | シリアル コンソールでのアクション                |  OS 適用性 
:------------------|:-----------------------------------------|:------------------
壊れた FSTAB ファイル | Enter キーを押して続行し、テキスト エディターを使って fstab ファイルを修正します。 [fstab の問題の修正方法](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors)に関するページをご覧ください | Linux 
不適切なファイアウォール規則 | シリアル コンソールにアクセスし、iptables または Windows ファイアウォール規則を修正します | Linux/Windows 
ファイル システムの破損/チェック | シリアル コンソールにアクセスし、ファイル システムを復旧します | Linux/Windows 
SSH/RDP の構成の問題 | シリアル コンソールにアクセスし、設定を変更します | Linux/Windows 
システムのネットワーク ロックダウン| ポータル経由でシリアル コンソールにアクセスし、システムを管理します | Linux/Windows 
ブートローダーの操作 | シリアル コンソールから GRUB/BCD にアクセスします | Linux/Windows 

## <a name="accessing-serial-console-for-linux"></a>Linux 用シリアル コンソールへのアクセス
シリアル コンソールが正常に機能するには、シリアル ポートでコンソール メッセージを読み書きするように、ゲスト オペレーティング システムを構成する必要があります。 ほとんどの [Azure での動作保証済み Linux ディストリビューション](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)は、既定で構成済みのシリアル コンソールを備えています。 ポータルで [シリアル コンソール] セクションをクリックするだけで、コンソールにアクセスできます。 

### <a name="access-for-red-hat"></a>Red Hat へのアクセス 
Azure で利用できる Red Hat イメージは、コンソール アクセスが既定で有効になっています。 RedHat のシングル ユーザー モードを使うには、root ユーザーを有効にする必要があります。root ユーザーは既定では無効になっています。 シングル ユーザー モードを有効にする必要がある場合は、次の手順のようにします。

1. SSH を使って RedHat システムにログインします
2. root ユーザーのパスワードを有効にします 
 * `passwd root` (強力な root パスワードを設定します)
3. root ユーザーが ttyS0 経由でのみログインできることを確認します
 * `edit /etc/ssh/sshd_config`: PermitRootLogin が no に設定されていることを確認します
 * `edit /etc/securetty file`: ttyS0 でのログインのみを許可します 

ここで、システムがシングル ユーザー モードで起動した場合、root パスワードでログインできます。

RHEL 7.4 以降または 6.9 以降の場合の代わりの方法として、GRUB プロンプトでシングル ユーザー モードを有効にできます。方法については[こちら](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single)をご覧ください。

### <a name="access-for-ubuntu"></a>Ubuntu へのアクセス 
Azure で利用できる Ubuntu イメージは、コンソール アクセスが既定で有効になっています。 システムがシングル ユーザー モードで起動した場合、資格情報を追加指定しなくてもアクセスできます。 

### <a name="access-for-coreos"></a>CoreOS へのアクセス
Azure で利用できる CoreOS イメージは、コンソール アクセスが既定で有効になっています。 必要な場合は、GRUB パラメーターを変更することによってシステムはシングル ユーザー モードで起動でき、`coreos.autologin=ttyS0` を追加するとコア ユーザーはログインしてシリアル コンソールで使用できます。 

### <a name="access-for-suse"></a>SUSE へのアクセス
Azure で利用できる SLES イメージは、コンソール アクセスが既定で有効になっています。 古いバージョンの SLES を Azure で使っている場合は、[こちらのサポート技術情報記事](https://www.novell.com/support/kb/doc.php?id=3456486)に従ってシリアル コンソールを有効にします。 SLES 12 SP3 以降の新しいイメージでは、システムが緊急モードで起動している場合でも、シリアル コンソールを使ってアクセスできます。

### <a name="access-for-centos"></a>CentOS へのアクセス
Azure で利用できる CentOS イメージは、コンソール アクセスが既定で有効になっています。 シングル ユーザー モードについては、上の RedHat イメージと同様の方法に従ってください。 

### <a name="access-for-oracle-linux"></a>Oracle Linux へのアクセス
Azure で利用できる Oracle Linux イメージは、コンソール アクセスが既定で有効になっています。 シングル ユーザー モードについては、上の RedHat イメージと同様の方法に従ってください。

### <a name="access-for-custom-linux-image"></a>カスタムの Linux イメージへのアクセス
カスタム Linux VM イメージのシリアル コンソールを有効にするには、/etc/inittab でコンソール アクセスを有効にして、ttyS0 でターミナルを実行します。 inittab ファイルにこれを追加する例を次に示します。 

`S0:12345:respawn:/sbin/agetty -L 115200 console vt102` 

## <a name="errors"></a>Errors
ほとんどのエラーは実際には一時的なものであり、接続の再試行によってこれらに対処します。 次の表にエラーと対応策を示します。 

エラー                            |   対応策 
:---------------------------------|:--------------------------------------------|
Unable to retrieve boot diagnostics settings for '<VMNAME>' \('<VMNAME>' のブート診断設定を取得できません。\) To use the serial console, ensure that boot diagnostics is enabled for this VM. (シリアル コンソールを使用するには、この VM のブート診断が有効になっていることを確認してください。) | VM の[ブート診断](boot-diagnostics.md)が有効になっていることを確認します。 
The VM is in a stopped deallocated state. (VM は停止済み (割り当て解除) 状態です。) Start the VM and retry the serial console connection. (VM を起動し、シリアル コンソール接続を再試行してください。) | シリアル コンソールにアクセスするには、仮想マシンが起動済み状態である必要があります。
You do not have the required permissions to use this VM the serial console. (この VM のシリアル コンソールを使用するために必要なアクセス許可がありません。) Ensure you have at least VM Contributor role permissions. (少なくとも VM 共同作成者ロールのアクセス許可があることを確認してください。)| シリアル コンソールにアクセスするには、特定のアクセス許可が必要です。 詳細については、[アクセス要件](#prerequisites)をご覧ください。
Unable to determine the resource group for the boot diagnostics storage account '<STORAGEACCOUNTNAME>'. (ブート診断ストレージ アカウント '<STORAGEACCOUNTNAME>' のリソース グループを特定できません。) Verify that boot diagnostics is enabled for this VM and you have access to this storage account. (この VM のブート診断が有効になっており、このストレージ アカウントにアクセスできることを確認してください。) | シリアル コンソールにアクセスするには、特定のアクセス許可が必要です。詳細については、[アクセス要件](#prerequisites)をご覧ください。

## <a name="known-issues"></a>既知の問題 
シリアル コンソールへのアクセスは現在プレビュー段階にあるため、Microsoft は既知の問題の解決に取り組んでいます。これらの問題と対応策を次に示します。 

問題                           |   対応策 
:---------------------------------|:--------------------------------------------|
仮想マシン スケール セット インスタンスのシリアル コンソールに関するオプションがない |  プレビュー時点では、仮想マシン スケール セット インスタンスのシリアル コンソールへのアクセスはサポートされていません。
接続バナーの後に Enter キーを押しても、ログイン プロンプトが表示されない | [Enter キーを押しても何も実行されない](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md)問題に関するページをご覧ください。


## <a name="frequently-asked-questions"></a>よく寄せられる質問 
**Q.フィードバックを送信するにはどうすればよいですか?**

A. https://aka.ms/serialconsolefeedback にアクセスして、問題点としてフィードバックをお送りください。 (あまりお勧めしませんが) azserialhelp@microsoft.com、または http://feedback.azure.com の仮想マシン カテゴリでフィードバックをお送りいただくこともできます。

**Q. "既存のコンソールに、要求された OS の種類 'Linux' と競合する OS の種類 'Windows' があります" というエラーが表示されます。**

A. これは修正すべき既知の問題です。[Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) を bash モードで開いて再試行してください。

**Q.シリアル コンソールにアクセスできません。サポート ケースはどこで提出できますか?**

A. このプレビュー機能は、Azure プレビューの使用条件の対象となります。 このサポートは、前述のチャネルを介して適切に処理されます。 

## <a name="next-steps"></a>次の手順
* シリアル コンソールは、[Windows](../windows/serial-console.md) VM でも使用できます。
* [bootdiagnostics](boot-diagnostics.md) の詳細を確認します。
