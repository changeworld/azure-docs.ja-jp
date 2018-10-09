---
title: Azure 仮想マシンのシリアル コンソール | Microsoft Docs
description: Azure 仮想マシンの双方向シリアル コンソール。
services: virtual-machines-windows
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/07/2018
ms.author: harijay
ms.openlocfilehash: e1884048d0f02de1b3a354bc4dac2b3e98dcccc9
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2018
ms.locfileid: "47412859"
---
# <a name="virtual-machine-serial-console"></a>仮想マシンのシリアル コンソール


Azure の仮想マシンのシリアル コンソールでは、Windows 仮想マシンのテキスト ベースのコンソールにアクセスできます。 このシリアル接続では、仮想マシンの COM1 シリアル ポートに接続し、仮想マシンのネットワークまたはオペレーティング システムの状態に左右されずに仮想マシンにアクセスできます。 現在、仮想マシンのシリアル コンソールへのアクセスは Azure portal からのみ可能であり、仮想マシンに対する VM 共同作成者以上のアクセス権を持つユーザーだけが使用できます。 

Linux VM のシリアル コンソールのドキュメントを参照するには、[ここをクリック](serial-console-linux.md)してください。

> [!Note] 
> 仮想マシンのシリアル コンソールは、グローバル Azure リージョンで一般公開されています。 現時点では、シリアル コンソールは Azure Government と Azure China Cloud で利用できません。

 

## <a name="prerequisites"></a>前提条件 

* リソース管理デプロイ モデルを使用している必要があります。 クラシック デプロイはサポートされていません。 
* 仮想マシンの[ブート診断](boot-diagnostics.md)が有効になっている必要があります。 

    ![](../media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

* シリアル コンソールを使用するアカウントには、VM の[共同作成者ロール](../../role-based-access-control/built-in-roles.md)と[ブート診断](boot-diagnostics.md)ストレージ アカウントが必要です。 
* シリアル コンソールにアクセスしている仮想マシンには、パスワードベースのアカウントも必要です。 このアカウントは、VM アクセス拡張機能の[パスワードのリセット](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password)機能を使用して作成することができます。次のスクリーンショットを参照してください。

    ![](../media/virtual-machines-serial-console/virtual-machine-serial-console-reset-password.png)

## <a name="get-started-with-serial-console"></a>シリアル コンソールを使ってみる
仮想マシンのシリアル コンソールには、[Azure portal](https://portal.azure.com) からのみアクセスできます。 ポータルを使用して仮想マシンのシリアル コンソールにアクセスする手順を次に示します。

  1. Azure ポータルを開きます
  2. 左側のメニューで [Virtual Machines] を選択します。
  3. 一覧で VM をクリックします。 VM の概要ページが開きます。
  4. 下へスクロールして [サポート + トラブルシューティング] セクションを表示し、[シリアル コンソール] オプションをクリックします。 シリアル コンソールで新しいウィンドウが開き、接続が開始されます。


## <a name="enable-serial-console-in-custom-or-older-images"></a>カスタム イメージまたは古いイメージでシリアル コンソールを有効にする
Azure の新しい Windows Server イメージでは、既定で [Special Administrative Console](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) (SAC) が有効になります。 SAC は Windows のサーバー バージョンではサポートされていますが、クライアント バージョン (Windows 10、Windows 8、Windows 7 など) では使用できません。 2018 年 2 月以前に作成された Windows 仮想マシンのシリアル コンソールを有効にするには、次の手順を実行します。 

1. リモート デスクトップを介して Windows 仮想マシンに接続します。
2. 管理コマンド プロンプトで次のコマンドを実行します。 
* `bcdedit /ems {current} on`
* `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
3. システムを再起動して、SAC コンソールを有効にします。

![](/media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

必要に応じて、SAC をオフラインでも有効にすることができます。

1. SAC を構成する Windows ディスクをデータ ディスクとして既存の VM に接続します。 
2. 管理コマンド プロンプトで次のコマンドを実行します。 
* `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
* `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

### <a name="how-do-i-know-if-sac-is-enabled"></a>SAC が有効になっているかどうかを知る方法

[SAC] (https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) が有効になっていない場合、シリアル コンソールに SAC プロンプトは表示されません。 VM の正常性情報が表示される場合もありますが、それ以外は空白になります。 2018 年 2 月より前に作成された Windows Server イメージを使用している場合、SAC が有効になっていない可能性があります。

## <a name="enable-the-windows-boot-menu-in-serial-console"></a>シリアル コンソールの Windows ブート メニューの有効化 

Windows ブート ローダーのプロンプトを有効にしてシリアル コンソールに表示する必要がある場合は、ブート構成データに次のオプションを追加します。 詳細については、[bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set) に関する記事をご覧ください。

1. リモート デスクトップを介して Windows 仮想マシンに接続します。
2. 管理コマンド プロンプトで次のコマンドを実行します。 
* `bcdedit /set {bootmgr} displaybootmenu yes`
* `bcdedit /set {bootmgr} timeout 5`
* `bcdedit /set {bootmgr} bootems yes`
3. システムを再起動して、ブート メニューを有効にします。

## <a name="use-serial-console-for-nmi-calls-in-windows-vms"></a>Windows VM での NMI 呼び出しにシリアル コンソールを使用する
マスク不可能割り込み (NMI) は、仮想マシン上のソフトウェアで無視されない信号を作成するために設計されています。 従来より、NMI は、特定の応答時間を要したシステム上でのハードウェアの問題を監視するために使用されてきました。  今日、プログラマーおよびシステム管理者は、NMI をハングされたシステムのデバッグやトラブルシューティングのためのメカニズムとして、頻繁に使用しています。

シリアル コンソールは、以下に示すコマンド バーのキーボード アイコンを使用して、NMI を Azure 仮想マシンに送信するために使用できます。 NMI が配信されると、以降は、仮想マシン構成によってシステムの応答方法が制御されます。 NMI を受信したときにメモリ ダンプをクラッシュして作成するように Windows を構成できます。

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-nmi.png) <br>

NMI を受信したときにクラッシュ ダンプを作成するように Windows を構成する方法については、「[How to generate a complete crash dump file or a kernel crash dump file by using an NMI on a Windows-based system](https://support.microsoft.com/en-us/help/927069/how-to-generate-a-complete-crash-dump-file-or-a-kernel-crash-dump-file)」(Windowsベースのシステムで NMI を使用して完全なクラッシュ ダンプ ファイルまたはカーネル クラッシュ ダンプ ファイルを生成する方法) を参照してください。

## <a name="disable-serial-console"></a>シリアル コンソールを無効にする
既定では、すべてのサブスクリプションは、すべての VM に対してシリアル コンソールのアクセスが有効になっています。 VM レベルまたはサブスクリプション レベルのいずれかで、シリアル コンソールを無効にすることができます。

> [!Note]       
> あるサブスクリプションに対してシリアル コンソールを有効または無効にするには、そのサブスクリプションの書き込み権限が必要です。 これには管理者ロールと所有者ロールが含まれますが、それらに限定されません。 カスタム ロールにも、書き込み権限が与えられることがあります。

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
コンソールのアクセス パスワードはログに記録されませんが、コンソール内で実行されるコマンドにパスワード、シークレット、ユーザー名、またはその他の形式の個人を特定できる情報 (PII) が含まれていたり、出力されたりした場合、それらの情報は、シリアル コンソールのスクロールバック機能の実装の一部として、表示される他のすべてのテキストと共に仮想マシンのブート診断ログに書き込まれます。 これらのログは循環型であり、診断ストレージ アカウントに対する読み取りアクセス許可を持つユーザーだけがアクセスできます。ただし、シークレットや PII が含まれている可能性のあるものにはリモート デスクトップを使用するというベスト プラクティスに従うことをお勧めします。 

### <a name="concurrent-usage"></a>同時使用
ユーザーがシリアル コンソールに接続しているときに、別のユーザーがその同じ仮想マシンへのアクセスを要求し、その要求が成功した場合、最初のユーザーが立ち上がって物理コンソールから離れ、新しいユーザーがそこに座るのと同様に、最初のユーザーが切断され、2 番目のユーザーが接続されます。

>[!CAUTION] 
これは、切断されたユーザーはログアウトされないことを意味します。 (SIGHUP または同様のメカニズムを使用して) 切断時に強制的にログアウトする機能は、まだロードマップにあります。 Windows では SAC で自動タイムアウトが有効になっていますが、Linux ではターミナルのタイムアウト設定を構成できます。 

## <a name="common-scenarios-for-accessing-serial-console"></a>シリアル コンソールにアクセスする一般的なシナリオ 
シナリオ          | シリアル コンソールでのアクション                
:------------------|:-----------------------------------------
不適切なファイアウォール規則 | シリアル コンソールにアクセスし、Windows ファイアウォール規則を修正します。 
ファイル システムの破損/チェック | シリアル コンソールにアクセスし、ファイルシステムを復旧します。 
RDP 構成の問題 | シリアル コンソールにアクセスし、設定を変更します。 作業を開始するには、[RDP のドキュメント](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access)をご覧ください。
システムのネットワーク ロックダウン| ポータル経由でシリアル コンソールにアクセスし、システムを管理します。 一部のネットワーク コマンドは、[シリアル コンソールの CMD と PowerShell に関するドキュメント](serial-console-cmd-ps-commands.md)に記載されています。 
ブートローダーの操作 | シリアル コンソールから BCD にアクセスします。 作業を開始するには、[シリアル コンソールに表示するブート メニューの有効化](#enabling-boot-menu-to-show-in-the-serial-console)に関するセクションをご覧ください。 

## <a name="accessibility"></a>アクセシビリティ
アクセシビリティは、Azure シリアル コンソールの重点事項です。 そのため、視覚や聴覚に障碍があるユーザーや、マウスを使用できない可能性があるユーザーがシリアル コンソールにアクセスできるようにしました。

### <a name="keyboard-navigation"></a>キーボード ナビゲーション
Aure portal 内でシリアル コンソール インターフェイスをナビゲートするには、キーボードの `tab` キーを使用します。 現在の場所が画面上で強調表示されます。 シリアル コンソール ブレードのフォーカスを解除するには、キーボードの `Ctrl + F6` キーを押します。

### <a name="use-serial-console-with-a-screen-reader"></a>スクリーン リーダーでシリアル コンソールを使用する
シリアル コンソールには、スクリーン リーダーのサポートが組み込まれています。 スクリーン リーダーを有効にしてナビゲートすると、現在選択されているボタンの代替テキストをスクリーン リーダーで読み上げることができます。

## <a name="errors"></a>Errors
ほとんどのエラーは実際には一時的なものであり、接続の再試行によってこれらに対処します。 次の表にエラーと対応策を示します

Error                            |   対応策 
:---------------------------------|:--------------------------------------------|
Unable to retrieve boot diagnostics settings for '<VMNAME>' \('<VMNAME>' のブート診断設定を取得できません。\) To use the serial console, ensure that boot diagnostics is enabled for this VM. (シリアル コンソールを使用するには、この VM のブート診断が有効になっていることを確認してください。) | VM の[ブート診断](boot-diagnostics.md)が有効になっていることを確認します。 
The VM is in a stopped deallocated state. (VM は停止済み (割り当て解除) 状態です。) Start the VM and retry the serial console connection. (VM を起動し、シリアル コンソール接続を再試行してください。) | シリアル コンソールにアクセスするには、仮想マシンが起動済み状態である必要があります。
You do not have the required permissions to use this VM serial console.\(この VM のシリアル コンソールを使用するために必要なアクセス許可がありません。\) Ensure you have at least VM Contributor role permissions.\(少なくとも VM 共同作成者ロールのアクセス許可があることを確認してください。\)| シリアル コンソールにアクセスするには、特定のアクセス許可が必要です。 詳細については、[アクセス要件](#prerequisites)をご覧ください。
Unable to determine the resource group for the boot diagnostics storage account '<STORAGEACCOUNTNAME>'. (ブート診断ストレージ アカウント '<STORAGEACCOUNTNAME>' のリソース グループを特定できません。) Verify that boot diagnostics is enabled for this VM and you have access to this storage account.\(この VM のブート診断が有効になっており、このストレージ アカウントにアクセスできることを確認してください。\) | シリアル コンソールにアクセスするには、特定のアクセス許可が必要です。 詳細については、[アクセス要件](#prerequisites)をご覧ください。
この VM のブート診断ストレージ アカウントにアクセスする際に、'許可されていません' という応答が発生した。 | ブート診断にアカウントのファイアウォールがないことを確認してください。 シリアル コンソールが機能するには、アクセス可能なブート診断ストレージ アカウントが必要です。
Web ソケットが閉じているか、開けませんでした。 | 場合によっては、`*.console.azure.com` をホワイトリストに登録する必要があります。 より詳しいものの時間もかかるアプローチとしては、[Microsoft Azure データセンターの IP 範囲](https://www.microsoft.com/en-us/download/details.aspx?id=41653)をホワイトリストに追加するというものがありますが、この IP 範囲はかなり規則的に変化します。
Windows VM に接続したときに、正常性情報だけが表示される| これは、Windows イメージ用に Special Administrative Console が有効になっていない場合に表示されます。 Windows VM で SAC を手動で有効にする方法については、[Windows 用シリアル コンソールへのアクセス](#access-serial-console-for-windows)に関するページを参照してください。 詳細については、[Windows 正常性シグナル](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md)に関するページを参照してください。

## <a name="known-issues"></a>既知の問題 
Microsoft は、シリアル コンソールには問題がいくつかあることを認識しています。 そのような問題と軽減手順を以下に示します。

問題                             |   対応策 
:---------------------------------|:--------------------------------------------|
接続バナーの後に Enter キーを押しても、ログイン プロンプトが表示されない | [Enter キーを押しても何も実行されない](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md)問題に関するページを参照してください。 この問題は、Windows がシリアル ポートに正常に接続できない原因となるカスタム VM、堅牢化されたアプライアンス、または GRUB 構成を実行している場合に発生する可能性があります。
カーネル デバッグを有効にすると、SAC プロンプトで入力できない | VM に RDP で接続して、管理者特権でのコマンド プロンプトから `bcdedit /debug {current} off` を実行します。 RDP で接続できない場合は、代わりに OS ディスクを別の Azure VM に接続して、データ ディスクとしてアタッチされている間に `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off` を使用して変更し、ディスクを戻すこともできます。
元のコンテンツに反復する文字が含まれる場合、SAC の PowerShell に貼り付けると 3 文字目が生成される | 回避策は、現在のセッションから PSReadLine モジュールをアンロードすることです。 `Remove-Module PSReadLine` を実行し、現在のセッションから PSReadLine モジュールをアンロードします。この操作では、モジュールが削除されたり、アンインストールされたりすることはありません。
一部のキーボード入力で、不適切な SAC 出力が生成される (例: `[A`、`[3~`) | [VT100](https://aka.ms/vtsequences) エスケープ シーケンスは SAC プロンプトでサポートされていません。
非常に長い文字列を貼り付けると機能しない | シリアル コンソールでは、ターミナルに貼り付けられる文字列の長さが 2048 文字に制限されます。 これはシリアル ポートの帯域幅に対する過負荷を防止するためです。

## <a name="frequently-asked-questions"></a>よく寄せられる質問 

**Q.フィードバックを送信するにはどうすればよいですか?**

A. https://aka.ms/serialconsolefeedback にアクセスして、問題点としてフィードバックをお送りください。 (あまりお勧めしませんが) azserialhelp@microsoft.com、または http://feedback.azure.com の仮想マシン カテゴリでフィードバックをお送りいただくこともできます。

**Q.シリアル コンソールでは、コピー/貼り付けが可能ですか。**

A. はい。可能です。 Ctrl + Shift + C キーでコピーし、Ctrl + Shift + V キーでターミナルに貼り付けます。

**Q.私のサブスクリプションのシリアル コンソールは誰が有効にしたり、無効にしたりできますか。**

A. あるサブスクリプション レベルでシリアル コンソールを有効または無効にするには、そのサブスクリプションの書き込み権限が必要です。 書き込み権限が与えられているロールには、管理者ロールと所有者ロールがありますが、それらに限定されません。 カスタム ロールにも、書き込み権限が与えられることがあります。

**Q.私の VM のシリアル コンソールには誰がアクセスできますか。**

A. VM のシリアル コンソールにアクセスするには、VM に対して共同作成者レベル以上のアクセス権限が必要です。 

**Q.シリアル コンソールに何も表示されません。どうすれば良いでしょうか。**

A. シリアル コンソールのアクセスに関して、おそらく、イメージが間違って構成されています。 イメージを構成してシリアル コンソールを有効にする方法については、[カスタム イメージまたは古いイメージでのシリアル コンソールの有効化](#Enable-Serial-Console-in-custom-or-older-images)に関するページを参照してください。

**Q.シリアル コンソールは仮想マシン スケール セットに利用できますか。**

A. 現時点では、仮想マシン スケール セット インスタンスのシリアル コンソールにはアクセスできません。

## <a name="next-steps"></a>次の手順
* Windows SAC で使用できる CMD および PowerShell コマンドの詳細なガイドについては、[こちら](serial-console-cmd-ps-commands.md)を参照してください。
* シリアル コンソールは、[Linux](serial-console-linux.md) VM でも使用できます。
* ブート診断の詳細については、[こちら](boot-diagnostics.md)を参照してください。