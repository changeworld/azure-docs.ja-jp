---
title: Azure Lab Services - Windows カスタム イメージを共有イメージ ギャラリーに取り込む
description: Windows カスタム イメージを共有イメージ ギャラリーに取り込む方法について説明します。
ms.date: 09/30/2020
ms.topic: how-to
ms.openlocfilehash: 87ae45819a19390b1776cc81e45b85b4ba1e2aee
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111967782"
---
# <a name="bring-a-windows-custom-image-to-shared-image-gallery"></a>Windows カスタム イメージを共有イメージ ギャラリーに取り込む

共有イメージ ギャラリーを使用して自分専用の Windows カスタム イメージを取り込み、これらのイメージを使用して Azure Lab Services でラボを作成できます。  この記事では、カスタム イメージを以下から取り込む方法について説明します。

* 実際の[物理ラボ環境](upload-custom-image-shared-image-gallery.md#bring-a-windows-custom-image-from-a-physical-lab-environment)。
* [Azure 仮想マシン](upload-custom-image-shared-image-gallery.md#bring-a-windows-custom-image-from-an-azure-virtual-machine)。

このタスクは一般に学校の IT 部門によって行なわれます。

## <a name="bring-a-windows-custom-image-from-a-physical-lab-environment"></a>物理ラボ環境からの Windows カスタム イメージの取り込み

このセクションの手順では、物理ラボ環境から開始するカスタム イメージをインポートする方法について説明します。  この方法では、物理環境から VHD を作成し、VHD を共有イメージ ギャラリーにインポートして、Lab Services 内で使用できるようにします。  このアプローチの主な利点を次に示します。

* [一般化されたまたは特殊化された](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images)イメージを柔軟に作成してラボで使用できます。  あるいは、[ラボのテンプレート VM を使用してイメージをエクスポート](how-to-use-shared-image-gallery.md)すると、イメージは常に特殊化されます。
* [Microsoft Endpoint Configuration Manager](/mem/configmgr/core/understand/introduction) などの他のツールを使用して作成したイメージをアップロードできます。そうすると、ラボのテンプレート VM を使用してイメージを手動で設定する必要がありません。

このセクションの手順では、学校の Azure サブスクリプションで[マネージド ディスク](../virtual-machines/managed-disks-overview.md)を作成するアクセス許可が必要になります。

> [!IMPORTANT]
> イメージを物理ラボ環境から Lab Services に移動するときは、各イメージを再構築してラボのクラスに必要なソフトウェアだけが含まれるようにする必要があります。  詳細については、ブログ記事「[物理ラボから Azure Lab Services への移動](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)」を参照してください。

物理ラボ環境からの VHD の作成については、多くのオプションがあります。 下の手順では、Hyper-V マネージャーを使用して Windows Hyper-V 仮想マシン (VM) から VHD を作成する方法を示します。

1. ご自分のイメージから作成されたご自分の物理ラボ環境の Hyper-V VM から始めます。  詳細については、[Hyper-V で仮想マシンを作成する方法](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v)に関する記事をお読みください。
    1. VM は **第 1 世代** の VM として作成されている必要があります。
    1. VM の仮想ディスクは、固定サイズの VHD でなければなりません。  ディスクのサイズは 128 GB "*以下*" である必要があります。 VM を作成するとき、ディスクのサイズを下の図で示すように入力します。
       
        :::image type="content" source="./media/upload-custom-image-shared-image-gallery/connect-virtual-hard-disk.png" alt-text="仮想ハード ディスクを接続する":::

    > [!IMPORTANT]
    > ディスク サイズが 128 GB より大きいイメージは、Lab Services ではサポート "*されていません*"。 
   
1. 次の手順に従って、Hyper-V VM に接続し、[Azure 用に準備](../virtual-machines/windows/prepare-for-upload-vhd-image.md)します。
    1. [Azure 用の Windows 構成を設定します](../virtual-machines/windows/prepare-for-upload-vhd-image.md#set-windows-configurations-for-azure)。
    1. [VM の接続を確保するために必要な Windows サービスを確認します](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services)
    1. [リモート デスクトップのレジストリ設定を更新します](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings)。
    1. [Windows ファイアウォール規則を構成します](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules)。
    1. [Windows Update をインストールします](../virtual-machines/windows/prepare-for-upload-vhd-image.md)。
    1. [ここで示されているように、Azure VM エージェントと追加の構成をインストールします](../virtual-machines/windows/prepare-for-upload-vhd-image.md#complete-the-recommended-configurations) 
        
    特殊化または一般化されたイメージのいずれかを共有イメージ ギャラリーにアップロードし、それらを使用してラボを作成できます。  上の手順を実行すると、特殊化されたイメージが作成されます。 代わりに一般化されたイメージを作成する場合は、[SysPrep を実行](../virtual-machines/windows/prepare-for-upload-vhd-image.md#determine-when-to-use-sysprep)する必要もあります。  

    > [!IMPORTANT]
    > マシン固有の情報とユーザー プロファイルを維持する場合は、特殊化されたイメージを作成する必要があります。  一般化と特殊化されたイメージの違いの詳細については、「[一般化されたイメージと特殊化されたイメージ](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images)」を参照してください。

1. **Hyper-V** の場合は既定で **VHDX** ファイルが作成されるため、これを VHD ファイルに変換する必要があります。
    1. **[Hyper-V マネージャー]**  ->  **[操作]**  ->  **[ディスクの編集]** に移動します。
    1. 次に、ディスクを VHDX から VHD に **変換** します。  
     - ディスク サイズを拡大する場合は、128 GB を "*超えない*" ようにしてください。        
        :::image type="content" source="./media/upload-custom-image-shared-image-gallery/choose-action.png" alt-text="アクションの選択":::   

    詳細については、[仮想ディスクを固定サイズの VHD に変換する](../virtual-machines/windows/prepare-for-upload-vhd-image.md#convert-the-virtual-disk-to-a-fixed-size-vhd)方法を示す記事を参照してください。

1. VHD を Azure にアップロードして、マネージド ディスクを作成します。
    1. Storage Explorer またはコマンド ラインから AzCopy を使用することができます。「[VHD を Azure にアップロードするか、他のリージョンにマネージド ディスクをコピーする](../virtual-machines/windows/disks-upload-vhd-to-managed-disk-powershell.md)」を参照してください。        

    1. VHD をアップロードすると、マネージド ディスクが作成されて Azure portal に表示されます。 
    
    > [!WARNING]
    > コンピューターがスリープ状態またはロック状態になった場合、アップロード プロセスが中断され、失敗する可能性があります。 
    
    > [!IMPORTANT] 
    > Azure portal のマネージド ディスクの **[サイズおよびパフォーマンス]** タブでは、ディスク サイズを変更できます。 前に説明したように、サイズは 128 GB を "*超えない*" ようにしてください。

1. マネージド ディスクのスナップショットを作成します。
    これは、PowerShell、Azure portal、または Storage Explorer を使用して行うことができます。「[ポータルまたは PowerShell を使用してスナップショットを作成する](../virtual-machines/windows/snapshot-copy-managed-disk.md)」を参照してください。

1. 共有イメージ ギャラリーで、イメージの定義とバージョンを作成します。
    1. [イメージの定義を作成します](../virtual-machines/windows/shared-images-portal.md#create-an-image-definition)。  
     - **VM の世代** として **[Gen 1]** を選択します。
     - **オペレーティング システムの状態** に対して **特殊化されたイメージ** を作成するのか **一般化されたイメージ** を作成するのかを選択します。
     
    イメージ定義に指定できる値の詳細については、[イメージ定義](../virtual-machines/shared-image-galleries.md#image-definitions)に関するページを参照してください。 
    
    > [!NOTE] 
    > 既存のイメージ定義を使用して、カスタム イメージの新しいバージョンを作成することも選択できます。
    
1. [イメージ バージョンを作成します](../virtual-machines/windows/shared-images-portal.md#create-an-image-version)。
    - **[バージョン番号]** プロパティで使用する形式は *MajorVersion.MinorVersion.Patch* です。   Lab Services を使用してラボを作成してカスタム イメージを選択すると、最新バージョンのイメージが自動的に使用されます。  最新バージョンは、MajorVersion、MinorVersion、Patch の順で最高値に基づいて選択されます。
    - **[ソース]** で、 **[ディスクやスナップショット]** をドロップダウン リストから選択します。
    - **[OS ディスク]** プロパティで、前の手順で作成したスナップショットを選択します。
    
    イメージ定義に指定できる値の詳細については、[イメージ バージョン](../virtual-machines/shared-image-galleries.md#image-versions)に関するページを参照してください。 
   
1. Lab Services で[ラボを作成](tutorial-setup-classroom-lab.md)し、共有イメージ ギャラリーからカスタム イメージを選択します。

    元の Hyper-V VM に OS をインストールした "*後*" でディスクを拡張した場合は、未割り当てのディスク領域を使用するように Windows の C ドライブを拡張する必要もあります。      
    - ラボのテンプレート VM にログインし、「[ベーシック ボリュームを拡張する](/windows-server/storage/disk-management/extend-a-basic-volume)」に示されているのと同じような手順に従います。 

## <a name="bring-a-windows-custom-image-from-an-azure-virtual-machine"></a>Azure 仮想マシンから Windows カスタム イメージを取り込む
もう 1 つの方法では、[Azure 仮想マシン](../virtual-machines/windows/overview.md)を使用して Windows イメージを設定します。  Azure 仮想マシン (VM) を使用すると、特殊化または一般化されたイメージのいずれかを柔軟に作成してラボで使用できます。  Azure VM からイメージを準備するプロセスが [VHD からイメージを作成する場合](upload-custom-image-shared-image-gallery.md#bring-a-windows-custom-image-from-a-physical-lab-environment)と比べて簡単なのは、イメージは既に Azure で実行する準備ができているためです。

下の手順を完了するには、学校の Azure サブスクリプションで Azure VM を作成するためのアクセス許可が必要です。

1. [Azure portal](../virtual-machines/windows/quick-create-portal.md)、[PowerShell](../virtual-machines/windows/quick-create-powershell.md)、[Azure CLI](../virtual-machines/windows/quick-create-cli.md) を使用するか、または [ARM テンプレート](../virtual-machines/windows/quick-create-template.md)から、Azure VM を作成します。
    
    - ディスク設定を指定するときは、ディスクのサイズが 128 GB を "*超えない*" ようにしてください。
    
1. ソフトウェアをインストールして、Azure VM のイメージに対して必要な構成変更を行います。

1. 一般化されたイメージを作成する場合は、[SysPrep](../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep) を実行します。  詳細については、「[一般化されたイメージと特殊化されたイメージ](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images)」を参照してください。

1. 共有イメージ ギャラリーで、[イメージ定義を作成する](../virtual-machines/windows/shared-images-portal.md#create-an-image-definition)か、既存のイメージ定義を選択します。
     - **VM の世代** として **[Gen 1]** を選択します。
     - **オペレーティング システムの状態** に対して **特殊化されたイメージ** を作成するのか **一般化されたイメージ** を作成するのかを選択します。
    
1. [イメージ バージョンを作成します](../virtual-machines/windows/shared-images-portal.md#create-an-image-version)。
    - **[バージョン番号]** プロパティで使用する形式は *MajorVersion.MinorVersion.Patch* です。   
    - **[ソース]** で、 **[ディスクやスナップショット]** をドロップダウン リストから選択します。
    - **[OS ディスク]** プロパティで、前の手順で作成した Azure VM のディスクを選択します。

1. Lab Services で[ラボを作成](tutorial-setup-classroom-lab.md)し、共有イメージ ギャラリーからカスタム イメージを選択します。

PowerShell を使用してカスタム イメージを Azure VM から共有イメージ ギャラリーにインポートすることもできます。  詳細については、次のスクリプトと付属の ReadMe を参照してください。
    
- [イメージを共有イメージ ギャラリー スクリプトに取り込む](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/BringImageToSharedImageGallery/)

## <a name="next-steps"></a>次のステップ

* [共有イメージ ギャラリーの概要](../virtual-machines/shared-image-galleries.md)
* [共有イメージ ギャラリーをアタッチまたはデタッチする](how-to-attach-detach-shared-image-gallery.md)
* [Shared Image Gallery を使用する方法](how-to-use-shared-image-gallery.md)