---
title: Azure Lab Services - ラボ用のカスタム イメージを作成するための推奨される方法
description: ラボ用のカスタム イメージを作成する方法について説明します。
ms.date: 07/27/2021
ms.topic: article
ms.openlocfilehash: aa318ad47187fd93d331c7a6f33d4699dfe42af6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779613"
---
# <a name="recommended-approaches-for-creating-custom-images"></a>カスタム イメージを作成するための推奨される方法
この記事では、カスタム イメージを作成するための次の推奨される方法について説明します。

-   [ラボのテンプレート仮想マシン (VM)](how-to-create-manage-template.md) からカスタム イメージを作成して保存します。
-   次のものを使用して、ラボのコンテキストの外部からカスタム イメージを取り込みます。
    - [Azure VM](https://azure.microsoft.com/services/virtual-machines/)。
    - 物理ラボ環境の VHD。

## <a name="save-a-custom-image-from-a-labs-template-vm"></a>ラボのテンプレート VM からカスタム イメージを保存する 

ラボのテンプレート VM を使用したカスタム イメージの作成と保存は、ラボ サービスのポータルを使用してサポートされているため、イメージを作成する最も簡単な方法です。  結果として、IT 部門と教師の両方は、ラボのテンプレート VM を使用してカスタム イメージを作成できます。

たとえば、Marketplace イメージの 1 つから始めて、クラスに必要な追加のソフトウェア アプリケーションやツールなどをインストールすることができます。  イメージの設定が完了したら、[接続された共有イメージ ギャラリー](how-to-attach-detach-shared-image-gallery.md)に保存して、自分や他の教師がイメージを使用して新しいラボを作成できるようにすることができます。

この方法で注意する必要がある重要な点がいくつかあります。
-   Azure Lab Services は、テンプレート VM からイメージをエクスポートするときに、*特殊化された* イメージを自動的に保存します。  多くの場合、イメージはコンピューター固有の情報とユーザー プロファイルを保持するので、特殊化されたイメージは、新しいラボの作成に適しています。  特殊化されたイメージを使用すると、イメージを使用して新しいラボを作成するときに、インストールされているソフトウェアを確実に実行できます。  *一般化された* イメージを作成する必要がある場合は、この記事の他の推奨される方法のいずれかを使用して、カスタム イメージを作成する必要があります。

    Azure Lab Services の一般化されたイメージと特殊化されたイメージの両方に基づいて、テンプレート VM を作成できます。  相違点の詳細については、[一般化および特殊化されたイメージ](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images)に関するページを参照してください。

-   イメージを設定するより高度なシナリオでは、代わりに、Azure VM または物理ラボ環境の VHD を使用して、ラボの外部にイメージを作成すると便利です。  詳細については、以降のセクションを参照してください。

### <a name="how-to-save-a-custom-image-from-a-labs-template-vm"></a>ラボのテンプレート VM からカスタム イメージを保存する方法

ラボのテンプレート VM を使用して、Windows または Linux のカスタム イメージを作成できます。  詳細については、[共有イメージ ギャラリーにイメージを保存する方法](how-to-use-shared-image-gallery.md#save-an-image-to-the-shared-image-gallery)に関する記事を参照してください。

## <a name="bring-a-custom-image-from-an-azure-vm"></a>Azure VM からカスタム イメージを取り込む

もう 1 つの方法は、Azure VM を使用してカスタム イメージをセットアップすることです。  イメージの設定が完了したら、共有イメージ ギャラリーに保存して、自分や同僚がイメージを使用して新しいラボを作成できるようにすることができます。

Azure VM を使用すると、次のように柔軟性が向上します。
-   [一般化または特殊化された](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images)イメージを作成できます。  あるいは、ラボのテンプレート VM を使用して[イメージをエクスポート](how-to-use-shared-image-gallery.md)すると、イメージは常に特殊化されます。
-   Azure VM のより高度な機能にアクセスできます。この機能は、イメージの設定に役立ちます。  たとえば、[拡張機能](../virtual-machines/extensions/overview.md)を使用して展開後構成とオートメーションを実行できます。  また、VM の[ブート診断](../virtual-machines/boot-diagnostics.md)と[シリアル コンソール](/troubleshoot/azure/virtual-machines/serial-console-overview)にアクセスすることもできます。

ただし、Azure VM を使用したイメージの設定はより複雑です。  そのため、通常、Azure VM でカスタム イメージを作成する責任は IT 部門が担います。

### <a name="how-to-bring-a-custom-image-from-an-azure-vm"></a>Azure VM からカスタム イメージを取り込む方法

Azure VM からカスタム イメージを取り込む手順の概要を次に示します。

1.  Windows または Linux Marketplace イメージを使用して [Azure VM](https://azure.microsoft.com/services/virtual-machines/) を作成します。  
1.  Azure VM に接続し、追加のソフトウェアをインストールします。  また、ラボに必要なその他のカスタマイズを行うこともできます。  
1.  イメージのセットアップが完了したら、[VM のイメージを共有イメージ ギャラリーに保存](../virtual-machines/image-version-vm-powershell.md)します。  この一環として、イメージの定義とバージョンも作成する必要があります。
1.  カスタム イメージをギャラリーに保存したら、イメージを使用して新しいラボを作成できます。   

手順は、カスタム Windows または Linux イメージを作成するかどうかによって異なります。  詳細な手順については、次の記事を参照してください。

-   [Azure VM からカスタム Windows イメージを取り込む方法](how-to-bring-custom-windows-image-azure-vm.md)
-   [Azure VM からカスタム Linux イメージを取り込む方法](how-to-bring-custom-linux-image-azure-vm.md)

## <a name="bring-a-custom-image-from-a-vhd-in-your-physical-lab-environment"></a>物理ラボ環境の VHD からカスタム イメージを取り込む

検討すべき 3 番目の方法は、物理ラボ環境の VHD から共有イメージ ギャラリーにカスタム イメージを取り込むことです。  イメージが共有イメージ ギャラリーに追加されると、そのイメージを使用して新しいラボを作成できます。

このアプローチを使用する理由はいくつかあります。

-   ラボで使用する[一般化されたまたは特殊化された](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images)イメージのどちらも作成できます。  あるいは、[ラボのテンプレート VM](how-to-use-shared-image-gallery.md) を使用してイメージをエクスポートすると、イメージは常に特殊化されます。
-   オンプレミス環境内に存在するリソースにアクセスできます。  たとえば、オンプレミス環境に大規模なインストール ファイルがあり、ラボのテンプレート VM へのコピーに時間がかかることがあります。
-   [Microsoft Endpoint Configuration Manager](/mem/configmgr/core/understand/introduction) などの他のツールを使用して作成したイメージをアップロードできます。そうすると、ラボのテンプレート VM を使用してイメージを手動で設定する必要がありません。

VHD からカスタム イメージを取り込むことは、Azure 内で動作するようにイメージが適切に設定されていることを確認する必要があるため、最も高度な方法です。  そのため、通常、VHD からカスタム イメージを作成する責任は IT 部門が担います。

### <a name="how-to-bring-a-custom-image-from-a-vhd"></a>VHD からカスタム イメージを取り込む方法

VHD からカスタム イメージを取り込む手順の概要を次に示します。

1.  オンプレミスのマシンで [Windows Hyper-V](/virtualization/hyper-v-on-windows/about/) を使用して、Windows または Linux VHD を作成します。
1.  Hyper-V VM に接続し、追加のソフトウェアをインストールします。  また、ラボに必要なその他のカスタマイズを行うこともできます。  
1.  イメージのセットアップが完了したら、VHD をアップロードして、Azure に[マネージド ディスク](../virtual-machines/managed-disks-overview.md)を作成します。
1.  マネージド ディスクから、共有イメージギャラリーに[イメージの定義](../virtual-machines/shared-image-galleries.md#image-definitions)とバージョンを作成します。
1.  カスタム イメージをギャラリーに保存したら、イメージを使用して新しいラボを作成できます。   

手順は、カスタム Windows または Linux イメージを作成するかどうかによって異なります。  詳細な手順については、次の記事を参照してください。

-   [VHD からカスタム Windows イメージを取り込む方法](upload-custom-image-shared-image-gallery.md)
-   [VHD からカスタム Linux イメージを取り込む方法](how-to-bring-custom-linux-image-vhd.md)

## <a name="next-steps"></a>次のステップ

* [共有イメージ ギャラリーの概要](../virtual-machines/shared-image-galleries.md)
* [共有イメージ ギャラリーをアタッチまたはデタッチする](how-to-attach-detach-shared-image-gallery.md)
* [Shared Image Gallery を使用する方法](how-to-use-shared-image-gallery.md)