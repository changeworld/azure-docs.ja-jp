---
title: Azure Lab Services - Azure 仮想マシンから Windows カスタム イメージを取り込む方法
description: Azure 仮想マシンから Windows カスタム イメージを取り込む方法について説明します。
ms.date: 07/27/2021
ms.topic: how-to
ms.openlocfilehash: ab79f4acb0b61a46e4a8f1cebe243e6df43702bf
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779121"
---
# <a name="bring-a-windows-custom-image-from-an-azure-virtual-machine"></a>Azure 仮想マシンから Windows カスタム イメージを取り込む

この記事の手順では、[Azure 仮想マシン (VM)](https://azure.microsoft.com/services/virtual-machines/) から起動するカスタム イメージをインポートする方法について説明します。  この方法では、Azure VM 上にイメージを設定し、そのイメージをラボ サービス内で使用できるように共有イメージ ギャラリーにインポートします。  この方法でカスタム イメージを作成する前に、「[カスタム イメージの推奨される作成方法](approaches-for-custom-image-creation.md)」の記事を読んで、自分のシナリオに最適な方法を判断してください。

## <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、学校の Azure サブスクリプションで Azure VM を作成するためのアクセス許可が必要です。

## <a name="prepare-a-custom-image-on-an-azure-vm"></a>Azure VM でカスタム イメージを準備する

1. [Azure portal](../virtual-machines/windows/quick-create-portal.md)、[PowerShell](../virtual-machines/windows/quick-create-powershell.md)、[Azure CLI](../virtual-machines/windows/quick-create-cli.md) を使用するか、または [ARM テンプレート](../virtual-machines/windows/quick-create-template.md)から、Azure VM を作成します。
    
    - ディスク設定を指定するときは、ディスクのサイズが 128 GB を "*超えない*" ようにしてください。
    
1. ソフトウェアをインストールして、Azure VM のイメージに対して必要な構成変更を行います。

1. 必要に応じて、イメージを汎用化できます。 一般化されたイメージを作成する場合は、[SysPrep](../virtual-machines/generalize.md#windows) を実行します。  そうでなく、特殊なイメージを作成する場合は、次の手順までスキップすることができます。

    マシン固有の情報とユーザー プロファイルを維持する場合は、特殊化されたイメージを作成する必要があります。  一般化と特殊化されたイメージの違いの詳細については、「[一般化されたイメージと特殊化されたイメージ](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images)」を参照してください。

## <a name="import-the-custom-image-into-a-shared-image-gallery"></a>カスタム イメージを共有イメージ ギャラリーにインポートする

1. 共有イメージ ギャラリーで、[イメージ定義を作成する](../virtual-machines/windows/shared-images-portal.md#create-an-image-definition)か、既存のイメージ定義を選択します。
     - **VM の世代** として **[Gen 1]** を選択します。
     - **オペレーティング システムの状態** に対して **特殊化されたイメージ** を作成するのか **一般化されたイメージ** を作成するのかを選択します。

    イメージ定義に指定できる値の詳細については、[イメージ定義](../virtual-machines/shared-image-galleries.md#image-definitions)に関するページを参照してください。 
    
    既存のイメージ定義を使用して、カスタム イメージの新しいバージョンを作成することも選択できます。
    
1. [イメージ バージョンを作成します](../virtual-machines/windows/shared-images-portal.md#create-an-image-version)。
    - **[バージョン番号]** プロパティで使用する形式は *MajorVersion.MinorVersion.Patch* です。   
    - **[ソース]** で、 **[ディスクやスナップショット]** をドロップダウン リストから選択します。
    - **[OS ディスク]** プロパティで、前の手順で作成した Azure VM のディスクを選択します。

    PowerShell を使用してカスタム イメージを Azure VM から共有イメージ ギャラリーにインポートすることもできます。  詳細については、次のスクリプトと付属の ReadMe を参照してください。
    - [イメージを共有イメージ ギャラリー スクリプトに取り込む](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/BringImageToSharedImageGallery/)

## <a name="create-a-lab"></a>ラボを作成する

1. Lab Services で[ラボを作成](tutorial-setup-classroom-lab.md)し、共有イメージ ギャラリーからカスタム イメージを選択します。

## <a name="next-steps"></a>次のステップ

* [共有イメージ ギャラリーの概要](../virtual-machines/shared-image-galleries.md)
* [共有イメージ ギャラリーをアタッチまたはデタッチする](how-to-attach-detach-shared-image-gallery.md)
* [共有イメージ ギャラリーを使用する方法](how-to-use-shared-image-gallery.md)