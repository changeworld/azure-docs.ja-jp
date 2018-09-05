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
ms.date: 08/07/2018
ms.author: harijay
ms.openlocfilehash: d4ca44268740f48702594d9c87aa568d4f8eecb6
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2018
ms.locfileid: "43122407"
---
# <a name="virtual-machine-serial-console-preview"></a>仮想マシンのシリアル コンソール (プレビュー) 


Azure の仮想マシンのシリアル コンソールでは、Linux および Windows 仮想マシンのテキスト ベースのコンソールにアクセスできます。 このシリアル接続は、仮想マシンの COM1 シリアル ポートに接続し、仮想マシンへのアクセスを提供します。仮想マシンのネットワーク/オペレーティング システムの状態とは無関係です。 現在、仮想マシンのシリアル コンソールへのアクセスは Azure Portal からのみ可能であり、仮想マシンへの VM 共同作成者以上のアクセス権を持つユーザーだけが使用できます。 

Windows VM のシリアル コンソールのドキュメントを参照するには、[ここをクリック](../windows/serial-console.md)してください。

> [!Note] 
> プレビューは、使用条件に同意することを条件に使用することができます。 詳細については、「[Microsoft Azure プレビューの追加使用条件] (https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」をご覧ください。現在、このサービスは**パブリック プレビュー**段階にあり、仮想マシンのシリアル コンソールへのアクセスは全世界の Azure リージョンで提供されています。 現時点では、シリアル コンソールは、Azure Government、Azure Germany、Azure China Cloud では利用できません。


## <a name="prerequisites"></a>前提条件 

* リソース管理デプロイ モデルを使用している必要があります。 クラシック デプロイはサポートされていません。 
* 仮想マシンの[ブート診断](boot-diagnostics.md)が有効になっている必要があります。以下のスクリーンショットを参照してください。

    ![](../media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)
    
* シリアル コンソールを使用する Azure アカウントには、VM の[共同作成者ロール](../../role-based-access-control/built-in-roles.md)と[ブート診断](boot-diagnostics.md)のストレージ アカウントが必要です。 
* シリアル コンソールにアクセスしている仮想マシンには、パスワードベースのアカウントも必要です。 このアカウントは、VM アクセス拡張機能の[パスワードのリセット](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password)機能を使用して作成することができます。次のスクリーンショットを参照してください。

    ![](../media/virtual-machines-serial-console/virtual-machine-serial-console-reset-password.png)

* Linux ディストリビューションに固有の設定については、「[Linux 用シリアル コンソールへのアクセス](#access-serial-console-for-linux)」を参照してください。



## <a name="get-started-with-serial-console"></a>シリアル コンソールを使ってみる
仮想マシンのシリアル コンソールには、[Azure portal](https://portal.azure.com) からのみアクセスできます。 ポータルを使用して仮想マシンのシリアル コンソールにアクセスする手順を次に示します。 

  1. Azure ポータルを開きます
  2. 左側のメニューで [Virtual Machines] を選択します。
  3. 一覧で VM をクリックします。 VM の概要ページが開きます。
  4. 下へスクロールして [サポート + トラブルシューティング] セクションを表示し、[シリアル コンソール (プレビュー)] をクリックします。 シリアル コンソールで新しいウィンドウが開き、接続が開始されます。

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)


> [!NOTE] 
> シリアル コンソールを使うには、パスワードが構成されたローカル ユーザーが必要です。 現時点では、SSH 公開キーだけが構成された VM は、シリアル コンソールにアクセスできません。 パスワードを持つローカル ユーザーを作成するには、[VM アクセス拡張機能](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension)に関する記事を参照して (ポータルで [パスワードのリセット] をクリックして表示することもできます)、パスワードを持つローカル ユーザーを作成します。

## <a name="disable-serial-console"></a>シリアル コンソールの無効化
既定では、すべてのサブスクリプションは、すべての VM に対してシリアル コンソールのアクセスが有効になっています。 VM レベルまたはサブスクリプション レベルのいずれかで、シリアル コンソールを無効にすることができます。

### <a name="subscription-level-disable"></a>サブスクリプション レベルの無効化
サブスクリプション全体のシリアル コンソールは、[Disable Console REST API 呼び出し](https://aka.ms/disableserialconsoleapi)で無効にできます。 API ドキュメントのページで使用できる "使ってみる" 機能を利用して、サブスクリプションのシリアル コンソールを有効または無効にすることができます。 `subscriptionId` を入力し、`default` フィールドに "default" と入力して、[実行] をクリックします。 Azure CLI コマンドはまだ利用できませんが、後日利用可能になる予定です。 [こちらで REST API 呼び出しを試してみてください](https://aka.ms/disableserialconsoleapi)。

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-rest-api-try-it.png)

または、Cloud Shell (bash コマンドが表示される) で以下の一連のコマンドを使用して、サブスクリプションのシリアル コンソールの無効化と有効化、および無効化状態の表示ができます。 

* サブスクリプションのシリアル コンソールの無効化状態を取得するには、以下を実行します。
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"')) 

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s | jq .properties
    ```
* サブスクリプションのシリアル コンソールを無効にするには、以下を実行します。
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"')) 

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/disableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```
* サブスクリプションのシリアル コンソールを有効にするには、以下を実行します。
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"')) 

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/enableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```

### <a name="vm-level-disable"></a>VM レベルの無効化
特定の VM のシリアル コンソールは、その VM のブート診断設定を無効にすることで無効にできます。 Azure portal からブート診断をオフにするだけで、その VM のシリアル コンソールは無効になります。

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
壊れた FSTAB ファイル | `Enter` キーを押して続行し、テキスト エディターを使用して fstab ファイルを修正します。 このためには、シングル ユーザー モードにすることが必要な場合があります。 [fstab の問題の修正方法](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors)に関するページ、および「[Using Serial Console to access GRUB and Single User Mode (GRUB とシングル ユーザー モードへのアクセスにシリアル コンソールを使用する)](serial-console-grub-single-user-mode.md)」を参照してください。 | Linux 
不適切なファイアウォール規則 | シリアル コンソールにアクセスし、iptables または Windows ファイアウォール規則を修正します。 | Linux/Windows 
ファイル システムの破損/チェック | シリアル コンソールにアクセスし、ファイルシステムを復旧します。 | Linux/Windows 
SSH/RDP の構成の問題 | シリアル コンソールにアクセスし、設定を変更します。 | Linux/Windows 
システムのネットワーク ロックダウン| ポータル経由でシリアル コンソールにアクセスし、システムを管理します。 | Linux/Windows 
ブートローダーの操作 | シリアル コンソールから GRUB/BCD にアクセスします。 開始するには、「[Using Serial Console to access GRUB and Single User Mode (GRUB とシングル ユーザー モードへのアクセスにシリアル コンソールを使用する)](serial-console-grub-single-user-mode.md)」を参照してください。 | Linux/Windows 

## <a name="access-serial-console-for-linux"></a>Linux 用シリアル コンソールへのアクセス
シリアル コンソールが正常に機能するには、シリアル ポートでコンソール メッセージを読み書きするように、ゲスト オペレーティング システムを構成する必要があります。 ほとんどの [Azure での動作保証済み Linux ディストリビューション](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)は、既定で構成済みのシリアル コンソールを備えています。 Azure portal の [シリアル コンソール] セクションをクリックするだけで、コンソールにアクセスできます。 

ディストリビューション      | シリアル コンソール アクセス
:-----------|:---------------------
Red Hat Enterprise Linux    | Azure で利用できる Red Hat Enterprise Linux イメージは、コンソール アクセスが既定で有効になっています。 
CentOS      | Azure で利用できる CentOS イメージは、コンソール アクセスが既定で有効になっています。 
Ubuntu      | Azure で利用できる Ubuntu イメージは、コンソール アクセスが既定で有効になっています。
CoreOS      | Azure で利用できる CoreOS イメージは、コンソール アクセスが既定で有効になっています。
SUSE        | Azure で利用できる新しい SLES イメージは、コンソール アクセスが既定で有効になっています。 前のバージョン (10 以前) の SLES を Azure で使っている場合は、[こちらのサポート技術情報記事](https://www.novell.com/support/kb/doc.php?id=3456486)に従ってシリアル コンソールを有効にします。 
Oracle Linux        | Azure で利用できる Oracle Linux イメージは、コンソール アクセスが既定で有効になっています。
カスタム Linux イメージ     | カスタム Linux VM イメージのシリアル コンソールを有効にするには、/etc/inittab でコンソール アクセスを有効にして、ttyS0 でターミナルを実行します。 inittab ファイルにこれを追加する例を次に示します: `S0:12345:respawn:/sbin/agetty -L 115200 console vt102`。 カスタム イメージを適切に作成するための詳細については、[Azure 上での Linux VHD の作成とアップロード](https://aka.ms/createuploadvhd)に関する記事を参照してください。

## <a name="errors"></a>Errors
ほとんどのエラーは実際には一時的なものであり、多く場合、シリアル コンソール接続の再試行によって解決できます。 次の表にエラーと対応策を示します。

Error                            |   対応策 
:---------------------------------|:--------------------------------------------|
Unable to retrieve boot diagnostics settings for '<VMNAME>' \('<VMNAME>' のブート診断設定を取得できません。\) To use the serial console, ensure that boot diagnostics is enabled for this VM. (シリアル コンソールを使用するには、この VM のブート診断が有効になっていることを確認してください。) | VM の[ブート診断](boot-diagnostics.md)が有効になっていることを確認します。 
The VM is in a stopped deallocated state. (VM は停止済み (割り当て解除) 状態です。) Start the VM and retry the serial console connection. (VM を起動し、シリアル コンソール接続を再試行してください。) | シリアル コンソールにアクセスするには、仮想マシンが起動済み状態である必要があります。
You do not have the required permissions to use this VM the serial console. (この VM のシリアル コンソールを使用するために必要なアクセス許可がありません。) Ensure you have at least VM Contributor role permissions. (少なくとも VM 共同作成者ロールのアクセス許可があることを確認してください。)| シリアル コンソールにアクセスするには、特定のアクセス許可が必要です。 詳細については、[アクセス要件](#prerequisites)をご覧ください。
Unable to determine the resource group for the boot diagnostics storage account '<STORAGEACCOUNTNAME>'. (ブート診断ストレージ アカウント '<STORAGEACCOUNTNAME>' のリソース グループを特定できません。) Verify that boot diagnostics is enabled for this VM and you have access to this storage account.\(この VM のブート診断が有効になっており、このストレージ アカウントにアクセスできることを確認してください。\) | シリアル コンソールにアクセスするには、特定のアクセス許可が必要です。 詳細については、[アクセス要件](#prerequisites)をご覧ください。
Web ソケットが閉じているか、開けませんでした。 | 場合によっては、`*.console.azure.com` をホワイトリストに登録する必要があります。 より詳しいものの時間もかかるアプローチとしては、[Microsoft Azure データセンターの IP 範囲](https://www.microsoft.com/en-us/download/details.aspx?id=41653)をホワイトリストに追加するというものがありますが、この IP 範囲はかなり規則的に変化します。
## <a name="known-issues"></a>既知の問題 
シリアル コンソールへのアクセスは現在プレビュー段階にあるため、Microsoft は既知の問題の解決に取り組んでいます。 これらの問題と回避策の一覧を次に示します。

問題                           |   対応策 
:---------------------------------|:--------------------------------------------|
仮想マシン スケール セット インスタンスのシリアル コンソールに関するオプションがない |  プレビュー時点では、仮想マシン スケール セット インスタンスのシリアル コンソールへのアクセスはサポートされていません。
接続バナーの後に Enter キーを押しても、ログイン プロンプトが表示されない | [Enter キーを押しても何も実行されない](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md)問題に関するページを参照してください。 この問題は、Linux がシリアル ポートに正常に接続できない原因となるカスタム VM、堅牢化されたアプライアンス、または GRUB 構成を実行している場合に発生する可能性があります。
この VM のブート診断ストレージ アカウントにアクセスする際に、'許可されていません' という応答が発生した。 | ブート診断にアカウントのファイアウォールがないことを確認してください。 シリアル コンソールが機能するには、アクセス可能なブート診断ストレージ アカウントが必要です。


## <a name="frequently-asked-questions"></a>よく寄せられる質問 
**Q.フィードバックを送信するにはどうすればよいですか?**

A. https://aka.ms/serialconsolefeedback にアクセスして、問題点としてフィードバックをお送りください。 (あまりお勧めしませんが) azserialhelp@microsoft.com、または http://feedback.azure.com の仮想マシン カテゴリでフィードバックをお送りいただくこともできます。

**Q.シリアル コンソールにアクセスできません。サポート ケースはどこで提出できますか?**

A. このプレビュー機能は、Azure プレビューの使用条件の対象となります。 このサポートは、前述のチャネルを介して適切に処理されます。 

## <a name="next-steps"></a>次の手順
* シリアル コンソールを使用して、[GRUB を起動し、シングル ユーザー モードに入る](serial-console-grub-single-user-mode.md)
* [NMI および SysRq 呼び出し](serial-console-nmi-sysrq.md)のためにシリアル コンソールを使用する
* シリアル コンソールは、[Windows](../windows/serial-console.md) VM でも使用可能
* [ブート診断](boot-diagnostics.md)の詳細を表示する