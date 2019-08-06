---
title: Azure エージェントなしでローカルの Windows パスワードをリセットする | Microsoft Docs
description: Azure のゲスト エージェントが VM 上にインストールされていない場合または Azure のゲスト エージェントが VM 上で機能していない場合に、ローカルの Windows ユーザー アカウントのパスワードをリセットする方法
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: gwallace
editor: ''
ms.assetid: cf353dd3-89c9-47f6-a449-f874f0957013
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/25/2019
ms.author: genli
ms.openlocfilehash: 5354ebc8c25125f86a0208382d176c84372cadc1
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2019
ms.locfileid: "68369868"
---
# <a name="reset-local-windows-password-for-azure-vm-offline"></a>Azure VM のローカルの Windows パスワードをオフラインでリセットする
Azure ゲスト エージェントがインストールされている場合、[Azure Portal または Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) を使用して、Azure 内の VM のローカルの Windows パスワードをリセットできます。 これは、Azure VM のパスワードをリセットする最も一般的な方法です。 Azure のゲスト エージェントが応答しない場合やカスタム イメージのアップロード後にインストールに失敗する場合、Windows のパスワードを手動でリセットできます。 この記事では、ソース OS の仮想ディスクを別の VM に接続してローカル アカウントのパスワードをリセットする方法について説明します。 この記事に記載されている手順は、Windows ドメイン コントローラーには適用されません。 

> [!WARNING]
> この方法は、他の方法を利用できない場合のみ使用してください。 必ず最初に、[Azure Portal または Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) を使用したパスワードのリセットをお試しください。

## <a name="overview-of-the-process"></a>プロセスの概要
Azure ゲスト エージェントへのアクセス権がない場合に Azure 内の Windows VM のローカル パスワードをリセットする基本的な手順は次のとおりです。

1. ソース VM を準備します。 仮想ディスクは保持されます。

2. Azure サブスクリプション内の同じ場所にある別の VM に、ソース VM の OS ディスクを接続します。 この VM は、トラブルシューティング VM と呼ばれます。

3. トラブルシューティング VM を使用して、ソース VM の OS ディスクに構成ファイルをいくつか作成します。

4. トラブルシューティング VM から VM の OS ディスクを取り外します。

5. Resource Manager テンプレートを使用して、オリジナル仮想ディスクを使って VM を作成します。

6. 新しい VM が起動すると、作成した構成ファイルによって、必要なユーザーのパスワードが更新されます。

> [!NOTE]
> 次のプロセスを自動化することができます。
>
> - トラブルシューティング用 VM の作成
> - OS ディスクのアタッチ
> - 元の VM の再作成
> 
> これを行うには、[Azure VM の復旧スクリプト](https://github.com/Azure/azure-support-scripts/blob/master/VMRecovery/ResourceManager/README.md)を使用します。 Azure VM 復旧スクリプトを使用することを選択した場合は、「詳細な手順」セクションで次のプロセスを使用できます。
> 1. スクリプトを使用して影響を受ける VM の OS ディスクを復旧 VM にアタッチすることで、手順 1 と 2 をスキップします。
> 2. 手順 3 から 6 に従って軽減策を適用します。
> 3. スクリプトを使用して VM を再構築することで、手順 7 から 9 をスキップします。
> 4. 手順 10 と 11 に従います。

## <a name="detailed-steps-for-resource-manager"></a>Resource Manager での詳しい手順

> [!NOTE]
> この手順は、Windows ドメイン コントローラーには適用されません。 スタンドアロン サーバーまたはドメインのメンバーであるサーバーでのみ機能します。

次の手順を試す前に、[Azure Portal または Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) を使用したパスワードのリセットを必ずお試しください。 開始する前に、必ず VM のバックアップをおとりください。 

1. Azure Portal で、影響を受ける VM を削除します。 VM を削除しても削除されるのは、Azure 内の VM の参照であるメタデータのみです。 VMが削除されても、仮想ディスクは保持されます。
   
   * Azure Portal で VM を選んで、 *[削除]* をクリックします。
     
     ![既存の VM を削除する](./media/reset-local-password-without-agent/delete-vm.png)

2. トラブルシューティング VM に VM の OS ディスクを接続します。 トラブルシューティング VM は、ソース VM の OS ディスクと同じリージョン (`West US` など) にある必要があります。
   
   1. Azure Portal でトラブルシューティング VM を選びます。 *[Disks]*  |  *[Attach existing]* をクリックします。
     
     ![既存のディスクを接続する](./media/reset-local-password-without-agent/disks-attach-existing.png)
     
   2. *[VHD File]* を選択してから、ソース VM を含むストレージ アカウントを選びます。
     
     ![ストレージ アカウントを選択する](./media/reset-local-password-without-agent/disks-select-storage-account.png)
     
   3. ソース コンテナーを選びます。 ソース コンテナーは、通常 *VHD* です。
     
     ![ストレージ コンテナーを選ぶ](./media/reset-local-password-without-agent/disks-select-container.png)
     
   4. 接続する OS VHD を選びます。 *[選択]* をクリックしてプロセスを完了します。
     
     ![ソース仮想ディスクを選択する](./media/reset-local-password-without-agent/disks-select-source-vhd.png)

3. リモート デスクトップを使用してトラブルシューティング VM に接続し、ソース VM の OS ディスクが表示されていることを確認します。
   
   1. Azure Portal でトラブルシューティング VM を選択して、 *[接続]* をクリックします。

   2. ダウンロードを行う RDP ファイルを開きます。 トラブルシューティング VM のユーザー名とパスワードを入力します。

   3. エクスプローラーで、接続されているデータ ディスクを探します。 ソース VM の VHD がトラブルシューティングの VM に接続されている唯一のデータ ディスクの場合は、ソース VM の VHD が F: ドライブになっている必要があります。
     
     ![接続されたデータ ディスクを表示する](./media/reset-local-password-without-agent/troubleshooting-vm-file-explorer.png)

4. ソース VM のドライブ上の `\Windows\System32\GroupPolicy` に `gpt.ini` を作成します (gpt.ini が存在する場合は、gpt.ini.bak に名前を変更します)。
   
   > [!WARNING]
   > トラブルシューティング VM の OS ドライブである C:\Windows に誤って次のファイルを作成していないかご確認ください。 次のファイルは、データ ディスクとして接続されているソース VM の OS ドライブに作成してください。
   
   * 作成した `gpt.ini` ファイルに次の行を追加します。
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     ![gpt.ini を作成する](./media/reset-local-password-without-agent/create-gpt-ini.png)

5. `\Windows\System32\GroupPolicy\Machines\Scripts\` に `scripts.ini` を作成します。 非表示のフォルダーが表示されていることを確認します。 必要に応じて、`Machine` フォルダーまたは `Scripts` フォルダーを作成します。
   
   * 作成した `scripts.ini` ファイルに次の行を追加します。
     
     ```
     [Startup]
     0CmdLine=C:\Windows\System32\FixAzureVM.cmd
     0Parameters=
     ```
     
     ![scripts.ini を作成する](./media/reset-local-password-without-agent/create-scripts-ini.png)

6. `\Windows\System32` に次の内容を含む `FixAzureVM.cmd` を作成します。`<username>` と `<newpassword>` は実際の値に置き換えます。
   
    ```
    net user <username> <newpassword> /add
    net localgroup administrators <username> /add
    net localgroup "remote desktop users" <username> /add
    ```

    ![FixAzureVM.cmd を作成する](./media/reset-local-password-without-agent/create-fixazure-cmd.png)
   
    新しいパスワードを決めるときには、VM のパスワードの複雑さの要件を満たす必要があります。

7. Azure Portal で、トラブルシューティング VM からディスクを取り外します。
   
   1. Azure Portal でトラブルシューティング VM を選び、 *[Disks]* をクリックします。

   2. 手順 2 で接続したデータ ディスクを選んで、 *[Detach]* をクリックします。
     
     ![ディスクを取り外す](./media/reset-local-password-without-agent/detach-disk.png)

8. VM を作成する前に、ソース OS ディスクへの URI を取得します。
   
   1. Azure Portal でストレージ アカウントを選んで、 *[Blobs]* を選択します。

   2. コンテナーを選びます。 ソース コンテナーは、通常 *VHD* です。
     
     ![ストレージ アカウント BLOB を選ぶ](./media/reset-local-password-without-agent/select-storage-details.png)
     
   3. ソース VM の OS VHD を選び、 *[URL]* 名の横にある *[コピー]* をクリックします。
     
     ![ディスクの URI をコピーする](./media/reset-local-password-without-agent/copy-source-vhd-uri.png)

9. ソース VM の OS ディスクから VM を作成します。
   
   1. [この Azure Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-new-or-existing-vnet)を使って、特殊な VHD から VM を作成します。 [`Deploy to Azure`] をクリックして Azure Portal を開きます。テンプレートの情報が自動入力されています。

   2. VM の以前の設定をすべて保持する場合は、 *[Edit template]* を選んで、既存の VNet、サブネット、ネットワーク アダプター、パブリック IP のいずれかを入力します。

   3. [`OSDISKVHDURI`] パラメーター テキスト ボックスに、前の手順で取得したソース VHD の URI を貼り付けます。
     
     ![テンプレートから VM を作成する](./media/reset-local-password-without-agent/create-new-vm-from-template.png)

10. 新しい VM が起動したら、`FixAzureVM.cmd` スクリプトに指定した新しいパスワードを使って、VM に接続します。

11. リモート セッションから新しい VM までの次のファイルを削除して環境をクリーンアップします。
    
    * %Windir%\System32 から
      * FixAzureVM.cmd を削除します
    * %windir%\System32\GroupPolicy\Machine\Scripts から
      * scripts.ini を削除します
    * %windir%\System32\GroupPolicy から
      * gpt.ini を削除します (以前に gpt.ini が存在し、gpt.ini.bak に名前を変更した場合、この .bak ファイルの名前を変更して gpt.ini に戻します)

## <a name="detailed-steps-for-classic-vm"></a>クラシック VM での詳しい手順

> [!NOTE]
> この手順は、Windows ドメイン コントローラーには適用されません。 スタンドアロン サーバーまたはドメインのメンバーであるサーバーでのみ機能します。

次の手順を試す前に、[Azure Portal または Azure PowerShell](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/reset-rdp) を使用したパスワードのリセットを必ずお試しください。 開始する前に、必ず VM のバックアップをおとりください。 

1. Azure Portal で、影響を受ける VM を削除します。 VM を削除しても削除されるのは、Azure 内の VM の参照であるメタデータのみです。 VMが削除されても、仮想ディスクは保持されます。
   
   * Azure portal で VM を選んで、 *[削除]* をクリックします。
     
     ![既存の VM を削除する](./media/reset-local-password-without-agent/delete-vm-classic.png)

2. トラブルシューティング VM に VM の OS ディスクを接続します。 トラブルシューティング VM は、ソース VM の OS ディスクと同じリージョン (`West US` など) にある必要があります。
   
   1. Azure Portal でトラブルシューティング VM を選びます。 *[Disks]*  |  *[Attach existing]* をクリックします。
     
      ![既存のディスクを接続する](./media/reset-local-password-without-agent/disks-attach-existing-classic.png)
     
   2. *[VHD File]* を選択してから、ソース VM を含むストレージ アカウントを選びます。
     
      ![ストレージ アカウントを選択する](./media/reset-local-password-without-agent/disks-select-storage-account-classic.png)
     
   3. *[従来のストレージ アカウントの表示]* というチェック ボックスをオンにして、ソース コンテナーを選択します。 ソース コンテナーは、通常 *VHD* です。
     
      ![ストレージ コンテナーを選ぶ](./media/reset-local-password-without-agent/disks-select-container-classic.png)

      ![ストレージ コンテナーを選ぶ](./media/reset-local-password-without-agent/disks-select-container-vhds-classic.png)
     
   4. 接続する OS VHD を選びます。 *[選択]* をクリックしてプロセスを完了します。
     
      ![ソース仮想ディスクを選択する](./media/reset-local-password-without-agent/disks-select-source-vhd-classic.png)

   5. [OK] をクリックしてディスクをアタッチします。

      ![既存のディスクを接続する](./media/reset-local-password-without-agent/disks-attach-okay-classic.png)

3. リモート デスクトップを使用してトラブルシューティング VM に接続し、ソース VM の OS ディスクが表示されていることを確認します。

   1. Azure Portal でトラブルシューティング VM を選択して、 *[接続]* をクリックします。

   2. ダウンロードを行う RDP ファイルを開きます。 トラブルシューティング VM のユーザー名とパスワードを入力します。

   3. エクスプローラーで、接続されているデータ ディスクを探します。 ソース VM の VHD がトラブルシューティングの VM に接続されている唯一のデータ ディスクの場合は、ソース VM の VHD が F: ドライブになっている必要があります。
     
      ![接続されたデータ ディスクを表示する](./media/reset-local-password-without-agent/troubleshooting-vm-file-explorer-classic.png)

4. ソース VM のドライブ上の `\Windows\System32\GroupPolicy` に `gpt.ini` を作成します (`gpt.ini` が存在する場合は、`gpt.ini.bak` に名前を変更します)。
   
   > [!WARNING]
   > トラブルシューティング VM の OS ドライブである `C:\Windows` に誤って次のファイルを作成していないかご確認ください。 次のファイルは、データ ディスクとして接続されているソース VM の OS ドライブに作成してください。
   
   * 作成した `gpt.ini` ファイルに次の行を追加します。
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     ![gpt.ini を作成する](./media/reset-local-password-without-agent/create-gpt-ini-classic.png)

5. `\Windows\System32\GroupPolicy\Machines\Scripts\` に `scripts.ini` を作成します。 非表示のフォルダーが表示されていることを確認します。 必要に応じて、`Machine` フォルダーまたは `Scripts` フォルダーを作成します。
   
   * 作成した `scripts.ini` ファイルに次の行を追加します。

     ```
     [Startup]
     0CmdLine=C:\Windows\System32\FixAzureVM.cmd
     0Parameters=
     ```
     
     ![scripts.ini を作成する](./media/reset-local-password-without-agent/create-scripts-ini-classic.png)

6. `\Windows\System32` に次の内容を含む `FixAzureVM.cmd` を作成します。`<username>` と `<newpassword>` は実際の値に置き換えます。
   
    ```
    net user <username> <newpassword> /add
    net localgroup administrators <username> /add
    net localgroup "remote desktop users" <username> /add
    ```

    ![FixAzureVM.cmd を作成する](./media/reset-local-password-without-agent/create-fixazure-cmd-classic.png)
   
    新しいパスワードを決めるときには、VM のパスワードの複雑さの要件を満たす必要があります。

7. Azure Portal で、トラブルシューティング VM からディスクを取り外します。
   
   1. Azure Portal でトラブルシューティング VM を選び、 *[Disks]* をクリックします。
   
   2. 手順 2 で接続したデータ ディスクを選んで、 *[Detach]\(デタッチ\)* をクリックし、 *[OK]* をクリックします。

     ![ディスクを取り外す](./media/reset-local-password-without-agent/data-disks-classic.png)
     
     ![ディスクを取り外す](./media/reset-local-password-without-agent/detach-disk-classic.png)

8. ソース VM の OS ディスクから VM を作成します。
   
     ![テンプレートから VM を作成する](./media/reset-local-password-without-agent/create-new-vm-from-template-classic.png)

     ![テンプレートから VM を作成する](./media/reset-local-password-without-agent/choose-subscription-classic.png)

     ![テンプレートから VM を作成する](./media/reset-local-password-without-agent/create-vm-classic.png)

## <a name="complete-the-create-virtual-machine-experience"></a>仮想マシンの作成エクスペリエンスを完了する

1. 新しい VM が起動したら、`FixAzureVM.cmd` スクリプトに指定した新しいパスワードを使って、VM に接続します。

2. リモート セッションから新しい VM までの次のファイルを削除して環境をクリーンアップします。
    
    * `%windir%\System32` から
      * `FixAzureVM.cmd` を削除します
    * `%windir%\System32\GroupPolicy\Machine\Scripts` から
      * `scripts.ini` を削除します
    * `%windir%\System32\GroupPolicy` から
      * `gpt.ini` を削除します (以前に `gpt.ini` が存在し、`gpt.ini.bak` に名前を変更した場合、この `.bak` ファイルの名前を変更して `gpt.ini` に戻します)

## <a name="next-steps"></a>次の手順
それでもリモート デスクトップを使用して接続できない場合は、[RDP トラブルシューティング ガイド](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)をご覧ください。 [詳細な RDP トラブルシューティングガイド](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)では、具体的な手順というよりは、トラブルシューティングの手法について説明しています。 また、直接のサポートについては、[Azure のサポートに依頼](https://azure.microsoft.com/support/options/)できます。
