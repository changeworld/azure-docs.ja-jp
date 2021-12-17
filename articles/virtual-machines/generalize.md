---
title: イメージ作成前に VM を一般化する
description: VM を一般化し、イメージ作成前にマシン固有の情報を削除しました。
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/16/2021
ms.author: cynthn
ms.custom: portal
ms.openlocfilehash: e28ec0a10431316f7c5658ec006a7136f5388ff3
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131444613"
---
# <a name="remove-machine-specific-information-by-generalizing-a-vm-before-creating-an-image"></a>イメージ作成前に VM を一般化し、マシン固有の情報を削除します。

一般化されたイメージを作成する希望が特にないのであれば、[Azure Compute Gallery](shared-image-galleries.md#generalized-and-specialized-images) でイメージを作成するとき、VM を一般化する必要はありません。 ギャラリーの外でマネージド イメージを作成するとき、一般化が必要になります。

一般化でマシン固有の情報が削除され、複数の VM 作成にイメージを使用できます。 VM が一般化されたら、起動シーケンスを正しく設定できるよう、VM が一般化されたことをプラットフォームに認識させる必要があります。 VM が一般化されたら、再起動しないでください。


## <a name="linux"></a>Linux

まず Azure VM エージェントで VM のプロビジョニングを解除し、マシン固有のファイルとデータを削除します。 ソース Linux VM で `-deprovision+user` パラメーターを指定して `waagent` コマンドを実行します。 詳細については、「[Azure Linux エージェント ユーザー ガイド](./extensions/agent-linux.md)」をご覧ください。 これは、元に戻すことができない処理です。

1. SSH クライアントを使って Linux VM に接続します。
2. SSH のウィンドウで、次のコマンドを入力します。
   ```bash
    sudo waagent -deprovision+user
   ```
   > [!NOTE]
   > このコマンドはイメージとしてキャプチャする VM に対して実行します。 このコマンドでは、イメージからすべての機密情報が削除されることや、イメージが再配布に適した状態になることが保証されるわけではありません。 `+user` パラメーターにより、前回プロビジョニングされたユーザー アカウントも削除されます。 ユーザー アカウントの資格情報を VM に保持するには、`-deprovision` のみを使用します。
 
3. 「**y**」と入力して続行します。 `-force` パラメーターを追加すると、この確認手順を省略できます。
4. コマンドが完了したら、**exit** を入力して SSH クライアントを閉じます。  この時点で、VM はまだ実行されています。


その後、VM をプラットフォームで一般化済みとしてマークする必要があります。 

```azurecli-interactive
az vm generalize \
   --resource-group myResourceGroup \
   --name myVM
```

## <a name="windows"></a>Windows 

Sysprep はすべての個人アカウント情報とセキュリティ情報を削除して、マシンをイメージとして使用できるように準備します。 Sysprep については、[Sysprep の概要](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)を参照してください。

コンピューター上で実行されるサーバー ロールが Sysprep でサポートされていることを確認します。 詳細については、「[サーバーの役割の Sysprep サポート](/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles)」と「[サポートされていないシナリオ](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview#unsupported-scenarios)」を参照してください。 

> [!IMPORTANT]
> VM で Sysprep を実行すると、その VM は *一般化されている* と見なされ、再起動できなくなります。 VM の一般化プロセスは元に戻せません。 元の VM の機能を保持する場合は、[VM のコピー](./windows/create-vm-specialized.md#option-3-copy-an-existing-azure-vm)を作成し、そのコピーを一般化してください。 
>
> Sysprep では、ドライブを完全に復号化する必要があります。 VM で暗号化を有効にしている場合は、Sysprep を実行する前に暗号化を無効にしてください。
>
> 初めて VHD を Azure に仮想ハード ディスク (VHD) にアップロードする前に Sysprep を実行する予定の場合は、[VM の準備](./windows/prepare-for-upload-vhd-image.md)ができていることを確認してください。  
> 
> 

Windows VM を一般化するには、次の手順に従います。

1. Windows VM にサインインします。
   
2. 管理者としてコマンド プロンプト ウィンドウを開きます。 

3. panther ディレクトリ (C:\Windows\Panther) を削除します。 次に、ディレクトリを %windir%\system32\sysprep に変更して、`sysprep.exe` を実行します。
   
4. **[システム準備ツール]** ダイアログ ボックスで **[システムの OOBE (Out-of-Box Experience) に入る]** を選択し、 **[一般化する]** チェック ボックスを選択します。
   
5. **[シャットダウン オプション]** の **[シャットダウン]** を選択します。
   
6. **[OK]** を選択します。
   
    :::image type="content" source="windows/media/upload-generalized-managed/sysprepgeneral.png" alt-text="![Start Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)":::

6. Sysprep は完了時に VM をシャットダウンします。 VM は再起動しないでください。

> [!TIP]
> **オプション**[DISM](/windows-hardware/manufacture/desktop/dism-optimize-image-command-line-options) を使用してイメージを最適化し、VM の初回起動時間を短縮します。
>
> イメージを最適化するには、Windows エクスプローラーで VHD をダブルクリックして VHD をマウントし、`/optimize-image` パラメーターを指定して DISM を実行します。
>
> ```cmd
> DISM /image:D:\ /optimize-image /boot
> ```
> ここで D: はマウントされた VHD のパスです。
>
> `DISM /optimize-image` の実行は、VHD に行う最後の変更にします。 デプロイの前に VHD に変更を加えた場合は、`DISM /optimize-image` をもう一度実行する必要があります。

Sysprep が完了したら、仮想マシンのステータスを **[一般化]** に設定します。
   
```azurepowershell-interactive
Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized
```
