---
title: 物理ラボ環境から Linux カスタム イメージを取り込む方法
description: 物理ラボ環境から Linux カスタム イメージを取り込む方法を説明します。
ms.date: 07/27/2021
ms.topic: how-to
ms.openlocfilehash: 6f044c062b770c6653a1e239ca7c1074ed969b9c
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2021
ms.locfileid: "130180896"
---
# <a name="bring-a-linux-custom-image-from-your-physical-lab-environment"></a>物理ラボ環境から Linux カスタム イメージを取り込む

この記事の手順では、物理ラボ環境から開始する Linux カスタム イメージをインポートする方法について説明します。 この方法では、物理環境から VHD を作成し、VHD を共有イメージ ギャラリーにインポートして、Azure Lab Services 内で使用できるようにします。 この方法でカスタム イメージを作成する前に、「[カスタム イメージの推奨される作成方法](approaches-for-custom-image-creation.md)」を読んで、自分のシナリオに最適な方法を判断してください。

Azure では、さまざまな[ディストリビューションとバージョン](../virtual-machines/linux/endorsed-distros.md#supported-distributions-and-versions)がサポートされています。 VHD からカスタムの Linux イメージを取り込む手順は、ディストリビューションごとに異なります。 すべてのディストリビューションは、Azure で実行するために設定する必要がある固有の前提条件を持つため、それぞれ異なります。

この記事では、カスタム Ubuntu 16.04\18.04\20.04 イメージを VHD から取り込む手順について説明します。 VHD を使用して他のディストリビューション用のカスタムイメージを作成する方法については、[Linux ディストリビューションの一般的な手順](../virtual-machines/linux/create-upload-generic.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、学校の Azure サブスクリプションで [Azure マネージド ディスク](../virtual-machines/managed-disks-overview.md)を作成するためのアクセス許可が必要です。

イメージを物理ラボ環境から Lab Services に移動するときは、各イメージを再構築してラボのクラスに必要なソフトウェアだけが含まれるようにします。 詳細については、ブログ記事「[物理ラボから Azure Lab Services への移動](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)」を参照してください。

## <a name="prepare-a-custom-image-by-using-hyper-v-manager"></a>Hyper-V マネージャーを使用してカスタム イメージを準備する

次の手順では Windows Hyper-V マネージャーを使用して、Hyper-V 仮想マシン (VM) から Ubuntu 18.04\20.04 イメージを作成する方法を示します。

1. Hyper-V VM でカスタムイメージを設定するために使用する Windows ホストコンピューターに、公式の [Linux Ubuntu サーバー](https://ubuntu.com/server/docs)イメージをダウンロードします。

   Ubuntu 18.04 LTS を使用している場合は、[GNOME](https://www.gnome.org/) または [MATE](https://mate-desktop.org/) グラフィカル デスクトップがインストールされて *いない* イメージを使用することをお勧めします。 現在、GNOME と MATE は、Azure Lab Services でイメージが正常に機能するために必要な Azure Linux エージェントとネットワーク競合が発生します。 代わりに、Ubuntu Server イメージを使用して、[XFCE](https://www.xfce.org/) などの別のグラフィカル デスクトップをインストールします。  もう 1 つのオプションは、ラボのテンプレート VM を使用して [GNOME\MATE](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/LinuxGraphicalDesktopSetup/GNOME_MATE/ReadMe.md) をインストールする方法です。

   Ubuntu では、[ダウンロード用に構築済みの Azure VHD](https://cloud-images.ubuntu.com/) の発行も行います。 これらの VHD は、Linux ホスト コンピューターおよびハイパーバイザー (KVM など) からカスタム イメージを作成することを目的としています。 これらの VHD では、最初に既定のユーザー パスワードを設定する必要があります。これは、Windows では使用できない、qemu などの Linux ツールを使用してのみ実行できます。 このため、Windows Hyper-V を使用してカスタム イメージを作成する場合、これらの VHD に接続してイメージをカスタマイズすることはできません。 あらかじめ構築された Azure VHD の詳細については、[Ubuntu のドキュメント](https://help.ubuntu.com/community/UEC/Images?_ga=2.114783623.1858181609.1624392241-1226151842.1623682781#QEMU_invocation)を参照してください。

1. ご自分のイメージから作成されたご自分の物理ラボ環境の Hyper-V VM から始めます。 詳細については、[Hyper-V で仮想マシンを作成する方法](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v)に関する記事を参照してください。 次に示すように設定を行います。
    - VM は **第 1 世代** の VM として作成されている必要があります。
    - ネットワーク 構成の **[既定のスイッチ]** オプションを使用すると、VM がインターネットに接続できます。
    - **[仮想ハード ディスクの接続]** 設定では、次の図に示すように、ディスクの **サイズ** を 128 GB より大きくすることは "*できません*"。

        :::image type="content" source="./media/upload-custom-image-shared-image-gallery/connect-virtual-hard-disk.png" alt-text="[仮想ハード ディスクの接続] 画面を示すスクリーンショット。":::

    - **[インストール オプション]** 設定で、以前に Ubuntu からダウンロードした **.iso** ファイルを選択します。

    ディスク サイズが 128 GB より大きいイメージは、Lab Services ではサポート "*されていません*"。

1. [Ubuntu VHD を作成およびアップロードする手動の手順](../virtual-machines/linux/create-upload-ubuntu.md#manual-steps)に従って、Hyper-V VM に接続し、Azure 用に準備します。

    Azure の Linux イメージを準備する手順は、ディストリビューションによって異なります。 各ディストリビューションの詳細および具体的な手順については、[ディストリビューションとバージョン](../virtual-machines/linux/endorsed-distros.md#supported-distributions-and-versions)に関するページを参照してください。

    上記の手順に従うときに、強調すべきいくつかの重要な点があります。
    - **deprovision+user** コマンドを実行すると、[一般化](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images)されたイメージが作成されます。 ただし、イメージからすべての機密情報が削除されることや、イメージが再配布に適した状態になることが保証されるわけではありません。
    - 最後の手順は、**VHDX** ファイルを **VHD** ファイルに変換することです。 **Hyper-V マネージャー** を使用してこれを行う同等の手順を次に示します。

        1. **[Hyper-V マネージャー]**  >  **[操作]**  >  **[ディスクの編集]** に移動します。
        1. 変換する VHDX ディスクを見つけます。
        1. 次に、ディスクの **[変換]** を選択します。
        1. 選択するオプションは、**VHD ディスク形式** です。
        1. **[ディスクの種類]** で、 **[固定サイズ]** を選択します。
            - また、ここでディスク サイズを拡大も行う場合は、128 GB を "*超えない*" ようにしてください。
            :::image type="content" source="./media/upload-custom-image-shared-image-gallery/choose-action.png" alt-text="[操作の選択] 画面を示すスクリーンショット。":::

VHD のサイズ変更と VHDX への変換を支援するために、次の PowerShell コマンドレットを使用することもできます。

- [Resize-VHD](/powershell/module/hyper-v/resize-vhd)
- [Convert-VHD](/powershell/module/hyper-v/convert-vhd)

## <a name="upload-the-custom-image-to-a-shared-image-gallery"></a>カスタム イメージを共有イメージ ギャラリーにアップロードする

1. VHD を Azure にアップロードして、マネージド ディスクを作成します。
    1. Azure Storage Explorer またはコマンド ラインから AzCopy を使用することができます。「[VHD を Azure にアップロードするか、他のリージョンにマネージド ディスクをコピーする](../virtual-machines/windows/disks-upload-vhd-to-managed-disk-powershell.md)」を参照してください。

        > [!WARNING]
        > コンピューターがスリープ状態またはロック状態になった場合、アップロード プロセスが中断され、失敗するおそれがあります。 また、AzCopy が完了したら、ディスクへの SAS アクセスを取り消していることを確認してください。 そうせずに、ディスクからイメージを作成しようとすると、次のエラーが表示されます。"操作 'イメージの作成' は、ディスク 'ディスク名' が 'アクティブ アップロード' 状態ではサポートされません。 エラー コード: OperationNotAllowed*。"

    1. VHD をアップロードすると、マネージド ディスクが作成されて Azure portal に表示されます。

        Azure portal のマネージド ディスクの **[サイズおよびパフォーマンス]** タブを使用して、ディスク サイズを変更できます。 前に説明したように、サイズは 128 GB を "*超えない*" ようにしてください。

1. 共有イメージ ギャラリーで、イメージの定義とバージョンを作成します。
    1. [イメージの定義を作成します](../virtual-machines/image-version.md)。
        - **VM の世代** として **[Gen 1]** を選択します。
        - **[オペレーティング システム]** として **[Linux]** を選択します。
        - **[オペレーティング システムの状態]** として **[一般化]** を選択します。

        イメージ定義に指定できる値の詳細については、[イメージ定義](../virtual-machines/shared-image-galleries.md#image-definitions)に関するページを参照してください。

        既存のイメージ定義を使用して、カスタム イメージの新しいバージョンを作成することも選択できます。

    1. [イメージ バージョンを作成します](../virtual-machines/image-version.md)。
        - **[バージョン番号]** プロパティで使用する形式は *MajorVersion.MinorVersion.Patch* です。 Lab Services を使用してラボを作成してカスタム イメージを選択すると、最新バージョンのイメージが自動的に使用されます。 最新バージョンは、MajorVersion、MinorVersion、Patch の順で最高値に基づいて選択されます。
        - **[ソース]** で、 **[ディスクやスナップショット]** をドロップダウン リストから選択します。
        - **[OS ディスク]** プロパティで、前の手順で作成したディスクを選択します。

        イメージ バージョンに指定できる値の詳細については、[イメージ バージョン](../virtual-machines/shared-image-galleries.md#image-versions)に関するページを参照してください。

## <a name="create-a-lab"></a>ラボを作成する

Lab Services で[ラボを作成](tutorial-setup-classroom-lab.md)し、共有イメージ ギャラリーからカスタム イメージを選択します。

元の Hyper-V VM に OS をインストールした "*後*" でディスクを拡張した場合は、未割り当てのディスク領域を使用するように Linux のファイル システムのパーティションの拡張も必要になる可能性があります。  ラボのテンプレート VM にログインし、[ディスク パーティションとファイル システムの拡張](../virtual-machines/linux/expand-disks.md#expand-a-disk-partition-and-filesystem)に関する記事に示されているような手順に従います。

OS ディスクは、通常、 **/dev/sad2** パーティションに存在します。 OS ディスクのパーティションの現在のサイズを表示するには、**df -h** コマンドを使用します。

## <a name="next-steps"></a>次のステップ

- [共有イメージ ギャラリーの概要](../virtual-machines/shared-image-galleries.md)
- [共有イメージ ギャラリーをアタッチまたはデタッチする](how-to-attach-detach-shared-image-gallery.md)
- [共有イメージ ギャラリーを使用する](how-to-use-shared-image-gallery.md)