---
title: Azure 仮想マシンから Windows カスタム イメージを取り込む方法
description: Azure 仮想マシンから Windows カスタム イメージを取り込む方法について説明します。
ms.date: 07/27/2021
ms.topic: how-to
ms.openlocfilehash: b91e5dbf872c7d265871d1f2a435dde9246b5582
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2021
ms.locfileid: "130180858"
---
# <a name="bring-a-windows-custom-image-from-an-azure-virtual-machine"></a>Azure 仮想マシンから Windows カスタム イメージを取り込む

この記事の手順では、[Azure 仮想マシン (VM)](https://azure.microsoft.com/services/virtual-machines/) から起動するカスタム イメージをインポートする方法について説明します。 この方法では、Azure VM 上にイメージを設定し、そのイメージを Azure Lab Services 内で使用できるように共有イメージ ギャラリーにインポートします。 この方法でカスタム イメージを作成する前に、「[カスタム イメージの推奨される作成方法](approaches-for-custom-image-creation.md)」を読んで、自分のシナリオに最適な方法を判断してください。

## <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、学校の Azure サブスクリプションで Azure VM を作成するためのアクセス許可が必要です。

## <a name="prepare-a-custom-image-on-an-azure-vm"></a>Azure VM でカスタム イメージを準備する

1. [Azure portal](../virtual-machines/windows/quick-create-portal.md)、[PowerShell](../virtual-machines/windows/quick-create-powershell.md)、[Azure CLI](../virtual-machines/windows/quick-create-cli.md)、または [Azure Resource Manager テンプレート](../virtual-machines/windows/quick-create-template.md)を使用して、Azure VM を作成します。
    
    - ディスク設定を指定するときは、ディスクのサイズが 128 GB を "*超えない*" ようにしてください。
    
1. ソフトウェアをインストールして、Azure VM のイメージに対して必要な構成変更を行います。

1. 必要に応じて、イメージを汎用化できます。 一般化されたイメージを作成する場合は、[SysPrep](../virtual-machines/generalize.md#windows) を実行します。 そうでなく、特殊なイメージを作成する場合は、次の手順までスキップすることができます。

    マシン固有の情報とユーザー プロファイルを維持する場合は、特殊化されたイメージを作成します。 一般化と特殊化されたイメージの違いの詳細については、「[一般化されたイメージと特殊化されたイメージ](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images)」を参照してください。

## <a name="import-the-custom-image-into-a-shared-image-gallery"></a>カスタム イメージを共有イメージ ギャラリーにインポートする

1. 共有イメージ ギャラリーで、[イメージ定義を作成する](../virtual-machines/image-version.md)か、既存のイメージ定義を選択します。
     - **VM の世代** として **[Gen 1]** を選択します。
     - **オペレーティング システムの状態** に対して **特殊化されたイメージ** を作成するのか **一般化されたイメージ** を作成するのかを選択します。

    イメージ定義に指定できる値の詳細については、[イメージ定義](../virtual-machines/shared-image-galleries.md#image-definitions)に関するページを参照してください。 
    
    既存のイメージ定義を使用して、カスタム イメージの新しいバージョンを作成することも選択できます。
    
1. [イメージ バージョンを作成します](../virtual-machines/image-version.md)。
    - **[バージョン番号]** プロパティで使用する形式は *MajorVersion.MinorVersion.Patch* です。   
    - **[ソース]** で、 **[ディスクやスナップショット]** をドロップダウン リストから選択します。
    - **[OS ディスク]** プロパティで、前の手順で作成した Azure VM のディスクを選択します。

    PowerShell を使用してカスタム イメージを Azure VM から共有イメージ ギャラリーにインポートすることもできます。 詳細については、「[イメージを共有イメージ ギャラリー スクリプトに取り込む](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/BringImageToSharedImageGallery/)」のスクリプトと ReadMe を参照してください。

## <a name="create-a-lab"></a>ラボを作成する

Lab Services で[ラボを作成](tutorial-setup-classroom-lab.md)し、共有イメージ ギャラリーからカスタム イメージを選択します。

## <a name="next-steps"></a>次のステップ

* [共有イメージ ギャラリーの概要](../virtual-machines/shared-image-galleries.md)
* [共有イメージ ギャラリーをアタッチまたはデタッチする](how-to-attach-detach-shared-image-gallery.md)
* [共有イメージ ギャラリーを使用する](how-to-use-shared-image-gallery.md)