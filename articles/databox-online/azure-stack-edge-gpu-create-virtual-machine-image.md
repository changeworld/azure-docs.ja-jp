---
title: Azure Stack Edge Pro GPU デバイス用のカスタム VM イメージを作成する
description: Azure Stack Edge Pro GPU デバイスに仮想マシンをデプロイするためのカスタム Windows および Linux VM イメージを作成する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 07/16/2021
ms.author: alkohli
ms.openlocfilehash: 94ffb38c71437c8f5902866620b5ac0c2467edfd
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114462917"
---
# <a name="create-custom-vm-images-for-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU デバイス用のカスタム VM イメージを作成する

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Stack Edge Pro GPU デバイスに VM をデプロイするには、Azure で VM を作成するために使用できるカスタム VM イメージを作成する必要があります。 この記事では、Azure で Windows および Linux VM 用のカスタム VM イメージを作成し、それらのイメージを Azure Storage アカウントにダウンロードまたはコピーする手順について説明します。

カスタム VM イメージを準備するために必要なワークフローがあります。 イメージ ソースの場合、Azure でサポートされている任意のサイズの Gen1 VM の固定 VHD を使用する必要があります。 VM サイズのオプションについては、「[サポートされる VM のサイズ](azure-stack-edge-gpu-virtual-machine-sizes.md#supported-vm-sizes)」を参照してください。

## <a name="prerequisites"></a>前提条件

VM イメージを作成する前に、次の前提条件を満たします。

- [AzCopy をダウンロードします](../storage/common/storage-use-azcopy-v10.md#download-azcopy)。 AzCopy を使用すると、OS ディスクを Azure ストレージ アカウントにすばやくコピーできます。

---

## <a name="create-a-custom-vm-image"></a>カスタム VM イメージの作成

カスタム VM イメージを準備する手順は、VM が Windows か Linux かによって異なります。


### <a name="windows"></a>[Windows](#tab/windows)

Windows の VM イメージを作成するには、次の手順のようにします。

1. Azure で Windows 仮想マシンを作成します。 ポータルでの手順については、「[Azure portal で Windows 仮想マシンを作成する](../virtual-machines/windows/quick-create-portal.md)」を参照してください。 PowerShell での手順については、「[チュートリアル: Azure PowerShell を使用して Windows VM を作成および管理する](../virtual-machines/windows/tutorial-manage-vm.md)」を参照してください。  

   仮想マシンは第 1 世代 VM でなければなりません。 VM イメージの作成に使用する OS ディスクは、Azure でサポートされている任意のサイズの固定サイズの VHD である必要があります。 VM サイズのオプションについては、「[サポートされる VM のサイズ](azure-stack-edge-gpu-virtual-machine-sizes.md#supported-vm-sizes)」を参照してください。

   Azure Marketplace では、固定サイズの VHD を備える任意の Windows Gen1 VM を使用できます。 動作する可能性のある Azure Marketplace イメージの一覧については、[Azure Stack Edge でよく使用される Azure Marketplace イメージ](azure-stack-edge-gpu-create-virtual-machine-marketplace-image.md#commonly-used-marketplace-images)に関するセクションを参照してください。

2. 仮想マシンを一般化します。 VM を一般化するには、[仮想マシンに接続](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-a-windows-vm)し、コマンド プロンプトを開いて、次の `sysprep` コマンドを実行します。

   ```dos
   c:\windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown /mode:vm
   ```

   > [!IMPORTANT]
   > コマンドが完了すると、VM はシャットダウンされます。 **VM を再起動しないでください。** VM を再起動すると、準備したディスクが破損します。


### <a name="linux"></a>[Linux](#tab/linux)

Linux の VM イメージを作成するには、次の手順のようにします。

1. Azure で Linux 仮想マシンを作成します。 ポータルでの手順については、「[クイック スタート: Azure portal で Linux 仮想マシンを作成する](../virtual-machines/linux/quick-create-portal.md)」を参照してください。  PowerShell での手順については、「[クイック スタート: PowerShell を使用して Azure に Linux 仮想マシンを作成する](../virtual-machines/linux/quick-create-powershell.md)」を参照してください。

   Azure Marketplace で提供されている固定サイズの VHD を使用する任意の Gen1 VM を使用して、Linux カスタム イメージを作成できます。ただし、Red Hat Enterprise Linux (RHEL) のイメージは例外で、追加の手順が必要です。 動作する可能性のある Azure Marketplace イメージの一覧については、[Azure Stack Edge でよく使用される Azure Marketplace イメージ](azure-stack-edge-gpu-create-virtual-machine-marketplace-image.md#commonly-used-marketplace-images)に関するセクションを参照してください。 RHEL イメージについてのガイダンスは、後の 「[RHEL BYOS イメージの使用](#using-rhel-byos-images)」を参照してください。

1. VM のプロビジョニングを解除します。 Azure VM エージェントを使用し、マシン固有のファイルとデータを削除します。 ソース Linux VM で `-deprovision+user` パラメーターを指定して `waagent` コマンドを実行します。 詳細については、「[Azure Linux エージェントの理解と使用](../virtual-machines/extensions/agent-linux.md)」を参照してください。

    1. [SSH クライアントを使用して Linux VM に接続します](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-a-linux-vm)。
    2. SSH のウィンドウで、次のコマンドを入力します。
       
        ```bash
        sudo waagent -deprovision+user
        ```
       > [!NOTE]
       > このコマンドはイメージとしてキャプチャする VM に対して実行します。 このコマンドでは、イメージからすべての機密情報が削除されることや、イメージが再配布に適した状態になることが保証されるわけではありません。 `+user` パラメーターにより、前回プロビジョニングされたユーザー アカウントも削除されます。 ユーザー アカウントの資格情報を VM に保持するには、`-deprovision` のみを使用します。
     
    3. 「**y**」と入力して続行します。 `-force` パラメーターを追加すると、この確認手順を省略できます。
    4. コマンドが完了したら、**exit** を入力して SSH クライアントを閉じます。  この時点で、VM はまだ実行されています。


### <a name="using-rhel-byos-images"></a>RHEL BYOS イメージの使用

Red Hat Enterprise Linux (RHEL) イメージを使用する場合は、Red Hat Enterprise Linux Bring Your Own Subscription (RHEL BYOS) イメージ (Red Hat ゴールド イメージとも呼ばれます) だけがサポートされており、VM イメージの作成に使用できます。 Azure Marketplace の標準の従量課金制 RHEL イメージは、Azure Stack Edge ではサポートされていません。

RHEL BYOS イメージを使用して VM イメージを作成するには、次の手順のようにします。

1. [Red Hat のサブスクリプション管理](https://access.redhat.com/management)にログインします。 上部のメニュー バーの [[Cloud Access Dashboard]\(クラウド アクセス ダッシュボード\)](https://access.redhat.com/management/cloud) に移動します。
1. Azure サブスクリプションを有効にします。 [詳細な手順](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/getting-started-with-ca_cloud-access)を参照してください。 サブスクリプションを有効にすると、Red Hat ゴールド イメージにアクセスできます。

1. Azure の使用条件に同意し (Azure サブスクリプションごと、イメージごとに 1 回のみ)、VM をプロビジョニングします。 [手順](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/understanding-gold-images_cloud-access)を参照してください。 

プロビジョニングした VM を使用して、Linux で [VM カスタム イメージを作成する](#create-a-custom-vm-image)ことができるようになります。

---

## <a name="download-os-disk-to-storage-account"></a>OS ディスクをストレージ アカウントにダウンロードする

カスタム VM イメージを使用してデバイスに VM をデプロイするには、OS ディスクを Azure ストレージ アカウントにダウンロードする必要があります。 デバイスに使用したのと同じストレージ アカウントを使用することをお勧めします。

VM 用の OS ディスクを Azure ストレージ アカウントにダウンロードするには、次の手順のようにします。

1. [ポータルで VM を停止します](../virtual-machines/windows/download-vhd.md#stop-the-vm)。 `sysprep` を実行して Windows VM を一般化した後でそれがシャットダウンされた場合でも、OS ディスクの割り当てを解除するためにこれを行う必要があります。

1. [OS ディスクのダウンロード URL を生成](../virtual-machines/windows/download-vhd.md#generate-download-url)し、その URL を記録しておきます。 既定では、その URL は 3600 秒 (1 時間) 後に期限切れになります。 必要に応じて、その時間を延ばすことができます。
      
1. 次のいずれかの方法を使用して、Azure ストレージ アカウントに VHD をダウンロードします。
   
   - 方法 1: 高速で転送するには、AzCopy を使用して VHD を Azure ストレージ アカウントにコピーします。 手順については、後の [AzCopy を使用して VM イメージをストレージ アカウントにコピーする方法](#copy-vhd-to-storage-account-using-azcopy)に関する説明を参照してください。 

   - 方法 2: 1 回クリックするだけの簡単な方法としては、ダウンロード URL (ステップ 3b) を生成するときに **[VHD ファイルのダウンロード]** を選択して、ポータルからディスクをダウンロードできます。 **この方法を使用すると、ディスクのコピーに長い時間がかかる場合があり、ポータルを使用して VM を作成するには、[VHD を Azure ストレージ アカウントにアップロードする](azure-stack-edge-gpu-deploy-virtual-machine-templates.md#use-storage-explorer-for-upload)必要があります。**

これで、この VHD を使用して VM を作成し、Azure Stack Edge Pro GPU デバイスにデプロイできます。

## <a name="copy-vhd-to-storage-account-using-azcopy"></a>AzCopy を使用してストレージ アカウントに VHD をコピーする

次の手順では、イメージを使用して Azure Stack Edge Pro GPU デバイスに VM をデプロイできるように、AzCopy を使用してカスタム VM イメージを Azure ストレージ アカウントにコピーする方法について説明します。 カスタム VM イメージは、Azure Stack Edge と同じリージョンまたはサブスクリプションにある、使用中の既存のストレージ アカウントに格納することをお勧めします。


### <a name="create-target-uri-for-a-container"></a>コンテナー用のターゲット URI を作成する

AzCopy を使用するには、新しいイメージのコピー先であるストレージ アカウント内の場所を示す "*ターゲット URI*" が必要です。 AzCopy を実行する前に、ファイルのコピー先の BLOB コンテナーの Shared Access Signature (SAS) URL を生成します。 ターゲット URI を作成するには、SAS URL にファイル名を追加します。

準備した VHD 用のターゲット URI を作成するには、次の手順のようにします。

1. Azure ストレージ アカウント内のコンテナー用の SAS URL を生成するには、次の手順のようにします。

   1. Azure portal でストレージ アカウントを開き、 **[コンテナー]** を選択します。 使用する BLOB コンテナーを選択して右クリックし、 **[SAS の生成]** を選択します。

      ![Azure portal での BLOB コンテナーの [SAS の生成] オプションのスクリーンショット](./media/azure-stack-edge-gpu-create-virtual-machine-image/storage-container-generate-sas-option.png)

   1. **[SAS の生成]** 画面で、 **[アクセス許可]** の **[読み取り]** と **[書き込み]** を選択します。  

      ![[読み取り] と [書き込み] アクセス許可が選択されている [SAS の生成] 画面のスクリーンショット](./media/azure-stack-edge-gpu-create-virtual-machine-image/generate-sas-screen-read-and-write-permissions.png)

   1. **[SAS トークンおよび URL を生成]** を選択した後、 **[コピー]** を選択して **BLOB SAS URL** をコピーします。

      ![BLOB SAS URL を生成してコピーするためのオプションが表示されている [SAS の生成] 画面のスクリーンショット](./media/azure-stack-edge-gpu-create-virtual-machine-image/generate-sas-screen-copy-blob-sas-url.png)

1. `azcopy` コマンドのターゲット URI を作成するには、目的のファイル名を SAS URL に追加します。

   BLOB SAS URL の形式は次のとおりです。 

   ![新しいラベル付きファイル名を挿入するコンテナー パスと場所が表示されている BLOB SAS URL の画像](./media/azure-stack-edge-gpu-create-virtual-machine-image/blob-sas-url-format.png)

   クエリ文字列を開始する疑問符の前に、`/<filename>.vhd` の形式でファイル名を挿入します。 ファイル名の拡張子は vhd にする必要があります。 

   たとえば、次の BLOB SAS URL を使用すると、**osdisk.vhd** ファイルが **mystorageaccount** 内の **virtualmachines** BLOB コンテナーにコピーされます。

   ![osdisk という名前の VHD の BLOB SAS URL の例の画像](./media/azure-stack-edge-gpu-create-virtual-machine-image/target-uri-example-based-on-blob-sas-url.png)


### <a name="copy-vhd-to-blob-container"></a>VHD を BLOB コンテナーにコピーする

AzCopy を使用して VHD を BLOB コンテナーにコピーするには、次の手順のようにします。

 1. まだ行っていない場合は、[AZCopy](../storage/common/storage-use-azcopy-v10.md#download-azcopy) をダウンロードします。
 
 1. PowerShell で azcopy.exe を保存したディレクトリに移動し、次のコマンドを実行します。

    `.\azcopy copy <source URI> <target URI> --recursive`

    各値の説明:
    * `<source URI>` は、前に生成したダウンロード URL です。
    * `<target URI>` は、新しいイメージをコピーする Azureストレージ アカウント内の BLOB コンテナーを示します。 手順については、[AzCopy を使用して VM イメージをストレージ アカウントにコピーする方法](#copy-vhd-to-storage-account-using-azcopy)に関する説明を参照してください。

    たとえば、次の URI を使用すると、**windowsosdisk.vhd** という名前のファイルが、**mystorageaccount** ストレージ アカウントの **仮想マシン** BLOB コンテナーにコピーされます。

    ```azcopy 
    .\azcopy copy "https://md-h1rvdq3wwtdp.z24.blob.storage.azure.net/gxs3kpbgjhkr/abcd?sv=2018-03-28&sr=b&si=f86003fc-a231-43b0-baf2-61dd51e3a05a&sig=o5Rj%2BNZSook%2FVNMcuCcwEwsr0i7sy%2F7gIDzak6JhlKg%3D" "https://mystorageaccount.blob.core.windows.net/virtualmachines/osdisk.vhd?sp=rw&st=2021-05-21T16:52:24Z&se=2021-05-22T00:52:24Z&spr=https&sv=2020-02-10&sr=c&sig=PV3Q3zpaQ%2FOLidbQJDKlW9nK%2BJ7PkzYv2Eczxko5k%2Bg%3D" --recursive
    ```

#### <a name="sample-output"></a>サンプル出力

次に示すのは、上記の AzCopy コマンドの例を使用してコピーが正常に完了した場合の出力です。

   ```output
   PS C:\azcopy\azcopy_windows_amd64_10.10.0> .\azcopy copy "https://md-h1rvdq3wwtdp.z24.blob.storage.azure.net/gxs3kpbgjhkr/abcd?sv=2018-03-28&sr=b&si=f86003fc-a231-43b0-baf2-61dd51e3a05a&sig=o5Rj%2BNZSook%2FVNMcuCcwEwsr0i7sy%2F7gIDzak6JhlKg%3D" "https://mystorageaccount.blob.core.windows.net/virtualmachines/osdisk.vhd?sp=rw&st=2021-05-21T16:52:24Z&se=2021-05-22T00:52:24Z&spr=https&sv=2020-02-10&sr=c&sig=PV3Q3zpaQ%2FOLidbQJDKlW9nK%2BJ7PkzYv2Eczxko5k%2Bg%3D" --recursive
   INFO: Scanning...
   INFO: Failed to create one or more destination container(s). Your transfers may still succeed if the container already exists.
   INFO: Any empty folders will not be processed, because source and/or destination doesn't have full folder support

   Job 783f2177-8317-3e4b-7d2f-697a8f1ab63c has started
   Log file is located at: C:\Users\aseuser\.azcopy\783f2177-8317-3e4b-7d2f-697a8f1ab63c.log

   INFO: Destination could not accommodate the tier P10. Going ahead with the default tier. In case of service to service transfer, consider setting the flag --s2s-preserve-access-tier=false.
   100.0 %, 0 Done, 0 Failed, 1 Pending, 0 Skipped, 1 Total,

   Job 783f2177-8317-3e4b-7d2f-697a8f1ab63c summary
   Elapsed Time (Minutes): 1.4671
   Number of File Transfers: 1
   Number of Folder Property Transfers: 0
   Total Number of Transfers: 1
   Number of Transfers Completed: 1
   Number of Transfers Failed: 0
   Number of Transfers Skipped: 0
   TotalBytesTransferred: 136367309312
   Final Job Status: Completed

   PS C:\azcopy\azcopy_windows_amd64_10.10.0>
   ```

## <a name="next-steps"></a>次のステップ

- [Azure portal を使用してデバイスに VM をデプロイする](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)
- [PowerShell を使用してデバイスに VM をデプロイする](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)