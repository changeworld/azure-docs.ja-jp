---
title: Azure portal を使用して Azure Stack Edge Pro GPU に VM をデプロイする
description: Azure portal を使用して Azure Stack Edge Pro GPU に VM を作成して管理する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/03/2021
ms.author: alkohli
ms.openlocfilehash: 8089666388134e8443c515d86e91ae3ba5c32b58
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121749772"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-via-the-azure-portal"></a>Azure portal を使用して Azure Stack Edge Pro GPU デバイスに VM をデプロイする

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure portal、テンプレート、Azure PowerShell コマンドレット、および Azure CLI または Python のスクリプトを使用して、Azure Stack Edge Pro GPU デバイス上に仮想マシン (VM) を作成および管理できます。 この記事では、Azure portal を使用して、Azure Stack Edge Pro GPU デバイスに VM を作成し、それを管理する方法について説明します。

> [!IMPORTANT] 
> クラウドからデバイスにデプロイされた VM を管理するユーザーに対して、多要素認証を有効にすることをお勧めします。
        
## <a name="vm-deployment-workflow"></a>VM デプロイのワークフロー

デプロイの大まかなワークフローは次のとおりです。

1. お使いの Azure Stack Edge デバイスでコンピューティング用のネットワーク インターフェイスを有効にします。 この手順により、指定されたネットワーク インターフェイスに仮想スイッチが作成されます。
1. Azure portal からの VM のクラウド管理を有効にします。
1. Azure Storage Explorer を使用して Azure Storage アカウントに VHD をアップロードします。 
1. アップロードした VHD を使用して、VHD をデバイスにダウンロードし、VHD から VM イメージを作成します。 
1. 前の手順で作成したリソースを使用します。
    1. 作成した VM イメージ。
    1. コンピューティングを有効にしたネットワーク インターフェイスに関連付けられている仮想スイッチ。
    1. 仮想スイッチに関連付けられているサブネット。

    次のリソースをインラインで作成または指定します。
    1. VM 名、サポートされている VM サイズ、VM のサインイン資格情報を選択します。 
    1. 新しいデータ ディスクを作成するか、既存のデータ ディスクを接続します。
    1. VM の静的 IP または動的 IP を構成します。 静的 IP を指定する場合は、コンピューティング用に有効化されているネットワーク インターフェイスのサブネット範囲内の空き IP アドレスから選択します。

    前のリソースを使用して VM を作成します。

## <a name="prerequisites"></a>[前提条件]

Azure portal を使用してデバイスで VM の作成と管理を開始する前に、次のことを確認してください。

1. 「[手順 1: Azure Stack Edge Pro GPU デバイスを構成する](./azure-stack-edge-gpu-connect-resource-manager.md#step-1-configure-azure-stack-edge-device)」の説明に従って Azure Stack Edge Pro GPU デバイスでネットワーク設定を完了していること。

    1. コンピューティング用のネットワーク インターフェイスを有効にしていること。 このネットワーク インターフェイスの IP を使用して、VM デプロイ用の仮想スイッチを作成します。 デバイスのローカル UI で、 **[Compute]\(コンピューティング\)** に移動します。 仮想スイッチの作成に使用するネットワーク インターフェイスを選択します。

        > [!IMPORTANT] 
        > コンピューティング用に構成できるポートは 1 つだけです。

    1. そのネットワーク インターフェイスでコンピューティングを有効にします。 そのネットワーク インターフェイスに対応する仮想スイッチが Azure Stack Edge Pro GPU によって作成、管理されます。

1. 作成する仮想マシンの VM イメージを作成するために使用する Windows または Linux VHD にアクセスできること。

## <a name="deploy-a-vm"></a>VM をデプロイする

Azure Stack Edge Pro GPU デバイスに仮想マシンを作成するには、次の手順に従います。

### <a name="add-a-vm-image"></a>VM イメージを追加する

1. VHD を Azure Storage アカウントにアップロードします。 「[アップロードに Storage Explorer を使用する](azure-stack-edge-gpu-deploy-virtual-machine-templates.md#use-storage-explorer-for-upload)」の手順に従います。

   VHD の準備の詳細については、[Windows VHD からの一般化されたイメージの準備](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md)に関するページを参照してください。

   [VM イメージのアップロードのトラブルシューティング](azure-stack-edge-gpu-troubleshoot-virtual-machine-image-upload.md)を行います。

1. Azure portal で、デバイスの Azure Stack Edge リソースに移動します。 次に、 **[Edge サービス]**  >  **[仮想マシン]** の順に移動します。

    ![Azure Stack Edge デバイスの [概要] ペインで、[エッジ サービス] の下の [仮想マシン] オプションが強調表示されているスクリーンショット。](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-1.png)

1. **[概要]** ページ上。 **[有効]** を選択して、仮想マシンのクラウド管理を有効にします。 

    ![Azure Stack Edge デバイスの [仮想マシン] ビューの [概要] ウィンドウのスクリーンショット。 VM クラウド管理を有効にする [有効にする] ボタンが強調表示されている。](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-2.png)

1. 最初の手順では、VM イメージを追加します。 前の手順で、既にストレージ アカウントに VHD をアップロードしています。 この VHD を使用して、VM イメージを作成します。

    **[+ イメージの追加]** を選択して、ストレージ アカウントから VHD をダウンロードしてデバイスに追加します。 VHD のサイズとダウンロードに使用できるインターネット帯域幅によっては、ダウンロード プロセスに数分かかります。 

    ![仮想マシンの [概要] ウィンドウで [+ イメージの追加] ボタンが強調表示されているスクリーンショット。](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-3.png)

1. **[イメージの追加]** ペインで、次のフィールドに入力します。 その後、 **[追加]** を選択します。

    |フィールド  |説明  |
    |---------|---------|
    |ストレージ BLOB からダウンロード    |VHD をアップロードしたストレージ アカウントのストレージ BLOB の場所を参照します。         |
    |ダウンロード先    | 仮想マシンをデプロイしている現在のデバイスに自動的に設定されます。        |
    |エッジ リソース グループ  |イメージを追加するリソース グループを選択します。 |
    |イメージに名前を付けて保存      | ストレージ アカウントにアップロードした VHD から作成する VM イメージの名前。        |
    |OS の種類     |VM イメージの作成に使用する VHD のオペレーティング システムとして、Windows または Linux から選択します。         |
   

    ![仮想マシンの [イメージの追加] ページで、[追加] ボタンが強調表示されているスクリーンショット。](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-6.png)

1. VHD がダウンロードされ、VM イメージが作成されます。 イメージの作成には、完了するまでに数分かかります。 VM イメージが正常に完了したことを知らせる通知が表示されます。<!--There's a fleeting notification that image creation is in progress, but I didn't see any notification that image creation completed successfully.-->

    ![Azure Stack Edge デバイスの [仮想マシン] の [概要] のスクリーンショット。 [イメージ] ペインで、新しく作成された VM が強調表示されている。](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-8.png)


1. VM イメージが正常に作成されると、 **[イメージ]** ウィンドウドのイメージの一覧に追加されます。

    ![Azure Stack Edge デバイスの [仮想マシン] ビューの [イメージ] ペインを示すスクリーンショット。 VM イメージのエントリが強調表示されている。](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-9.png)

    **[Deployments]\(デプロイ\)** ウィンドウが更新され、デプロイの状態が示されます。

    ![Azure Stack Edge デバイスの [仮想マシン] ビューの [デプロイ] ペインを示すスクリーンショット。 デプロイされた VM のエントリが強調表示されている。](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-10.png)

    新しく追加したイメージは、 **[概要]** ページにも表示されます。

    ![Azure Stack Edge デバイスの [仮想マシン] の [概要] ペインを示すスクリーンショット。 新しく追加された VM イメージが強調表示されている。](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-11.png)


### <a name="add-a-vm"></a>VM を追加する

VM イメージを作成した後に VM を作成するには、次の手順に従います。

1. **[仮想マシン]** の **[概要]** ページで、 **[+ 仮想マシンの追加]** を選択します。

    ![Azure Stack Edge デバイスの [仮想マシン] の [概要] ペインを示すスクリーンショット。 [+ 仮想マシンの追加] ボタンが強調表示されている。](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-1.png)

1. **[基本]** タブで、次のパラメーターを入力します。

    |パラメーター |説明  |
    |---------|---------|
    |仮想マシン名     | 新しい仮想マシンの名前を入力します。        |
    |エッジ リソース グループ     | VM に関連付けられているすべてのリソースの新しいリソース グループを作成します。        |
    |Image     | デバイスで使用可能な VM イメージから選択します。        |
    |サイズ     | [サポートされる VM のサイズ](azure-stack-edge-gpu-virtual-machine-sizes.md)から選択します。<br>GPU VM の場合は、[NCasT4-v3 シリーズの VM サイズ](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview)を選択します。 |
    |ユーザー名     | 管理者が VM にサインインするには、既定のユーザー名 **azureuser** を使用します。        |
    |認証の種類    | SSH 公開キーまたはユーザー定義のパスワードから選択します。       |
    |SSH 公開キー | **SSH 公開キー** 認証の種類を選択すると表示されます。 SSH 公開キーを貼り付けます。 |
    |パスワード     | **パスワード** 認証の種類を選択すると表示されます。 VM にサインインするためのパスワードを入力します。 パスワードは 12 文字以上で、定義された[複雑さの要件](../virtual-machines/windows/faq.yml#what-are-the-password-requirements-when-creating-a-vm-)を満たす必要があります。 |
    |[パスワードの確認入力]    | パスワードをもう一度入力します。        |

    ![Azure Stack Edge の "仮想マシンの追加" ウィザードの [基本] タブを示すスクリーンショット。 [基本] タブと [次へ: ディスク] ボタンが強調表示されている。](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-basics-1.png)

    **ディスク** を選択します。

1. **[ディスク]** タブで、VM にディスクを接続します。 
    
    1. **[新しいディスクを作成し接続する]** または **[既存のディスクの接続]** を選択できます。

        ![Azure Stack Edge の "仮想マシンの追加" ウィザードの [ディスク] タブを示すスクリーンショット。 [新しいディスクを作成しアタッチする] オプションが強調表示されている。](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-disks-1.png)

    1. **[新しいディスクを作成し接続する]** を選択します。 **[新しいディスクの作成]** ウィンドウで、ディスクの名前と GiB 単位のサイズを指定します。

        ![Azure Stack Edge の [仮想マシンの追加] の [新しいディスクの作成] 画面を示すスクリーンショット。 [OK] ボタンが強調表示されています。](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-disks-2.png)

    1. 前述の手順を繰り返して、ディスクを追加します。 ディスクが作成されると、 **[ディスク]** タブに表示されます。 **[次へ: ネットワーク]** を選択します。

1. **[ネットワーク]** タブで、VM のネットワーク接続を構成します。

    |パラメーター  |説明 |
    |---------|---------|
    |仮想ネットワーク    | ドロップダウン リストから、ネットワーク インターフェイス上でコンピューティングを有効にしたときに Azure Stack Edge デバイスに作成された仮想スイッチを選択します。    |
    |サブネット     | このフィールドには、コンピューティングを有効にしたネットワーク インターフェイスに関連付けられているサブネットが自動的に入力されます。         |
    |IP アドレス     | VM の静的または動的な IP を指定します。 静的 IP は、指定されたサブネットの範囲内の使用可能な空き IP アドレスである必要があります。        |

    ![Azure Stack Edge の [仮想マシンの追加] の [ネットワーク] タブのスクリーンショット。 [ネットワーク] タブと [次へ: 詳細] ボタンが強調表示されている。](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-networking-1.png)

    **[次: 詳細]** を選択します。 **[詳細]** タブで、VM のデプロイ時にインストールする拡張機能を選択できます。また、`cloud-init` スクリプトを指定して、VM をカスタマイズすることもできます。

1. VM を作成するときに VM に拡張機能をインストールする場合は、 **[インストールする拡張機能の選択]** を選択します。 次に、 **[拡張機能の追加]** 画面で拡張機能を選択します。

    VM のデプロイ時に GPU 拡張機能をインストールするための詳細な手順については、[GPU VM のデプロイ](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#create-gpu-vms)に関するセクションを参照してください。

    ![[仮想マシンの追加] の [詳細] タブに GPU 拡張機能を追加するための 2 つの手順の図。 拡張機能を選択して追加するためのオプションが強調表示されている。](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-extension-01.png)

1. `cloud-init` ユーティリティを使用して最初の起動時に新しい VM をカスタマイズする場合は、 **[詳細]** タブで、`cloud-init` スクリプトを **[カスタム データと cloud init]** の下の **[カスタム データ]** ボックスに貼り付けます。 

    `cloud-init` の使用方法の詳細については、「[cloud-init の概要](../virtual-machines/linux/tutorial-automate-vm-deployment.md#cloud-init-overview)」を参照してください。

    !["新しい仮想マシンの作成" ウィザードの [詳細設定] タブで、[カスタム データ] ボックスの cloud init スクリプトが強調表示されているスクリーンショット。](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-advanced-tab-with-cloud-init-script.png)

    **確認と作成** を選択します。

1. **[確認と作成]** タブで、VM の仕様を確認します。 **[作成]** を選択します。

    ![Azure Stack Edge の "仮想マシンの追加" ウィザードの [確認と作成] タブを示すスクリーンショット。 [確認と作成] タブが強調表示されている。](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-review-create-1.png)

1. VM の作成が開始され、最大 20 分かかることがあります。 **[Deployments]\(デプロイ\)** にアクセスして、VM の作成を監視することができます。

    ![Azure Stack Edge デバイスの [仮想マシン] ビューの [デプロイ] ペインを示すスクリーンショット。 デプロイされた VM のエントリが強調表示されている。](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-deployments-page-1.png)

1. VM が正常に作成されると、 **[概要]** ペインに新しい VM が表示されます。

    ![Azure Stack Edge デバイスの [仮想マシン] の [概要] ペインを示すスクリーンショット。 新しい仮想マシンがリストで強調表示されている。](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-overview-page-1.png)

1. 新しく作成された VM を選択し、 **[仮想マシン]** にアクセスします。

    ![Azure Stack Edge デバイスの [仮想マシン] ペインを示すスクリーンショット。 [仮想マシン] ラベルと [仮想マシン] エントリが強調表示されている。](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-page-1.png)

    VM を選択すると、詳細が表示されます。

    ![Azure Stack Edge の仮想マシンの [概要] ペインの [詳細] タブを示すスクリーンショット。 [概要] ラベルと [ネットワーク] の中の [IP アドレス] が強調表示されている。](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-details-1.png)

    VM に接続するネットワーク インターフェイスの IP アドレスを使用します。

## <a name="connect-to-a-vm"></a>VM への接続

Windows と Linux のどちらの VM を作成したかによって、接続する手順が異なる場合があります。 Azure portal を経由して、デバイスにデプロイされている VM に接続することはできません。 ご自分の Linux または Windows の VM に接続する手順に従ってください。

### <a name="connect-to-a-linux-vm"></a>Linux VM に接続する

Linux VM に接続するには、これらの手順に従います。

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]

### <a name="connect-to-a-windows-vm"></a>Windows VM に接続する

Windows VM に接続するには、これらの手順に従います。

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]

## <a name="next-steps"></a>次の手順

- [GPU VM のデプロイ](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md)
- [VM のデプロイのトラブルシューティング](azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning.md)
- [デバイスの VM アクティビティの監視](azure-stack-edge-gpu-monitor-virtual-machine-activity.md)
- [VM の CPU とメモリの監視](azure-stack-edge-gpu-monitor-virtual-machine-metrics.md)

