---
title: 物理ラボ環境から Windows カスタムイメージを取り込む方法
description: 物理ラボ環境から Windows カスタム イメージを取り込む方法について説明します。
ms.date: 07/27/2021
ms.topic: how-to
ms.openlocfilehash: 36a6c8f920ebada3d5cd4110e48fe8544e4c7ae3
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2021
ms.locfileid: "130179015"
---
# <a name="bring-a-windows-custom-image-from-a-physical-lab-environment"></a>物理ラボ環境からの Windows カスタム イメージの取り込み

この記事の手順では、物理ラボ環境から開始するカスタム イメージをインポートする方法について説明します。  この方法では、物理環境から VHD を作成し、VHD を共有イメージ ギャラリーにインポートして、Lab Services 内で使用できるようにします。  この方法でカスタム イメージを作成する前に、「[カスタム イメージの推奨される作成方法](approaches-for-custom-image-creation.md)」の記事を読んで、自分のシナリオに最適な方法を判断してください。

## <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、学校の Azure サブスクリプションで [Azure マネージド ディスク](../virtual-machines/managed-disks-overview.md)を作成するためのアクセス許可が必要です。

イメージを物理ラボ環境から Lab Services に移動するときは、各イメージを再構築してラボのクラスに必要なソフトウェアだけが含まれるようにする必要があります。  詳細については、ブログ記事「[物理ラボから Azure Lab Services への移動](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)」を参照してください。

## <a name="prepare-a-custom-image-using-hyper-v-manager"></a>Hyper-V マネージャーを使用してカスタム イメージを準備する

次の手順では、Hyper-V マネージャーを使用して Windows Hyper-V 仮想マシン (VM) から Windows イメージを作成する方法を示します。

1. ご自分のイメージから作成されたご自分の物理ラボ環境の Hyper-V VM から始めます。  詳細については、[Hyper-V で仮想マシンを作成する方法](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v)に関する記事をお読みください。
    - VM は **第 1 世代** の VM として作成されている必要があります。
    - ネットワーク構成の **[既定のスイッチ]** オプションを使用すると、VM がインターネットに接続できます。
    - VM の仮想ディスクは、固定サイズの VHD でなければなりません。  ディスクのサイズは 128 GB "*以下*" である必要があります。 VM を作成するとき、ディスクのサイズを下の図で示すように入力します。

        :::image type="content" source="./media/upload-custom-image-shared-image-gallery/connect-virtual-hard-disk.png" alt-text="仮想ハード ディスクを接続する":::

    ディスク サイズが 128 GB より大きいイメージは、Lab Services ではサポート "*されていません*"。

1. 次の手順に従って、Hyper-V VM に接続し、[Azure 用に準備](../virtual-machines/windows/prepare-for-upload-vhd-image.md)します。
    1. [Azure 用の Windows 構成を設定します](../virtual-machines/windows/prepare-for-upload-vhd-image.md#set-windows-configurations-for-azure)。
    1. [VM の接続を確保するために必要な Windows サービスを確認します](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services)
    1. [リモート デスクトップのレジストリ設定を更新します](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings)。
    1. [Windows ファイアウォール規則を構成します](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules)。
    1. [Windows Update をインストールします](../virtual-machines/windows/prepare-for-upload-vhd-image.md)。
    1. [ここで示されているように、Azure VM エージェントと追加の構成をインストールします](../virtual-machines/windows/prepare-for-upload-vhd-image.md#complete-the-recommended-configurations)

    特殊化または一般化されたイメージのいずれかを共有イメージ ギャラリーにアップロードし、それらを使用してラボを作成できます。  上の手順を実行すると、特殊化されたイメージが作成されます。 代わりに一般化されたイメージを作成する場合は、[SysPrep を実行](../virtual-machines/windows/prepare-for-upload-vhd-image.md#determine-when-to-use-sysprep)する必要もあります。  

    マシン固有の情報とユーザー プロファイルを維持する場合は、特殊化されたイメージを作成する必要があります。  一般化と特殊化されたイメージの違いの詳細については、「[一般化されたイメージと特殊化されたイメージ](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images)」を参照してください。

1. **Hyper-V** の場合は既定で **VHDX** ファイルが作成されるため、これを VHD ファイルに変換する必要があります。
    1. **[Hyper-V マネージャー]**  ->  **[操作]**  ->  **[ディスクの編集]** に移動します。
    1. 次に、ディスクを VHDX から VHD に **変換** します。  
     - ディスク サイズを拡大する場合は、128 GB を "*超えない*" ようにしてください。
        :::image type="content" source="./media/upload-custom-image-shared-image-gallery/choose-action.png" alt-text="アクションの選択":::

    詳細については、[仮想ディスクを固定サイズの VHD に変換する](../virtual-machines/windows/prepare-for-upload-vhd-image.md#convert-the-virtual-disk-to-a-fixed-size-vhd)方法を示す記事を参照してください。

VHD のサイズ変更と VHDX への変換を支援するために、次の PowerShell コマンドレットを使用することもできます。

- [Resize-VHD](/powershell/module/hyper-v/resize-vhd)
- [Convert-VHD](/powershell/module/hyper-v/convert-vhd)

## <a name="upload-the-custom-image-to-a-shared-image-gallery"></a>カスタム イメージを共有イメージ ギャラリーにアップロードする

1. VHD を Azure にアップロードして、マネージド ディスクを作成します。
    1. Storage Explorer またはコマンド ラインから AzCopy を使用することができます。「[VHD を Azure にアップロードするか、他のリージョンにマネージド ディスクをコピーする](../virtual-machines/windows/disks-upload-vhd-to-managed-disk-powershell.md)」を参照してください。

    1. VHD をアップロードすると、マネージド ディスクが作成されて Azure portal に表示されます。
    コンピューターがスリープ状態またはロック状態になった場合、アップロード プロセスが中断され、失敗する可能性があります。  また、AzCopy が完了したら、ディスクへの SAS アクセスを取り消していることを確認してください。  そうせずに、ディスクからイメージを作成しようとすると、次のエラーが表示されます。**操作 'イメージの作成' は、ディスク 'ディスク名' が 'アクティブ アップロード' 状態ではサポートされません。エラー コード: OperationNotAllowed**

    Azure portal のマネージド ディスクの **[サイズおよびパフォーマンス]** タブでは、ディスク サイズを変更できます。 前に説明したように、サイズは 128 GB を "*超えない*" ようにしてください。

1. 共有イメージ ギャラリーで、イメージの定義とバージョンを作成します。
    1. [イメージの定義を作成します](../virtual-machines/image-version.md)。  
     - **VM の世代** として **[Gen 1]** を選択します。
     - **オペレーティング システムの状態** に対して **特殊化されたイメージ** を作成するのか **一般化されたイメージ** を作成するのかを選択します。

    イメージ定義に指定できる値の詳細については、[イメージ定義](../virtual-machines/shared-image-galleries.md#image-definitions)に関するページを参照してください。

    既存のイメージ定義を使用して、カスタム イメージの新しいバージョンを作成することも選択できます。

1. [イメージ バージョンを作成します](../virtual-machines/image-version.md)。
    - **[バージョン番号]** プロパティで使用する形式は *MajorVersion.MinorVersion.Patch* です。   Lab Services を使用してラボを作成してカスタム イメージを選択すると、最新バージョンのイメージが自動的に使用されます。  最新バージョンは、MajorVersion、MinorVersion、Patch の順で最高値に基づいて選択されます。
    - **[ソース]** で、 **[ディスクやスナップショット]** をドロップダウン リストから選択します。
    - **[OS ディスク]** プロパティで、前の手順で作成したディスクを選択します。

    イメージ定義に指定できる値の詳細については、[イメージ バージョン](../virtual-machines/shared-image-galleries.md#image-versions)に関するページを参照してください。

## <a name="create-a-lab"></a>ラボを作成する

1. Lab Services で[ラボを作成](tutorial-setup-classroom-lab.md)し、共有イメージ ギャラリーからカスタム イメージを選択します。

    元の Hyper-V VM に OS をインストールした "*後*" でディスクを拡張した場合は、未割り当てのディスク領域を使用するように Windows の C ドライブを拡張する必要もあります。
    - ラボのテンプレート VM にログインし、「[ベーシック ボリュームを拡張する](/windows-server/storage/disk-management/extend-a-basic-volume)」に示されているのと同じような手順に従います。

## <a name="next-steps"></a>次のステップ

- [共有イメージ ギャラリーの概要](../virtual-machines/shared-image-galleries.md)
- [共有イメージ ギャラリーをアタッチまたはデタッチする](how-to-attach-detach-shared-image-gallery.md)
- [共有イメージ ギャラリーを使用する方法](how-to-use-shared-image-gallery.md)
