---
title: ラボのカスタム イメージを作成するための推奨される方法
description: ラボ用のカスタム イメージを作成する方法について説明します。
ms.date: 07/27/2021
ms.topic: how-to
ms.openlocfilehash: 47a97633b74129e8d44cba9844a65c6e95bd2c61
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2021
ms.locfileid: "130177177"
---
# <a name="recommended-approaches-for-creating-custom-images"></a>カスタム イメージを作成するための推奨される方法
この記事では、カスタム イメージを作成するための次の推奨される方法について説明します。

-   [ラボのテンプレート仮想マシン (VM)](how-to-create-manage-template.md) からカスタム イメージを作成して保存します。
-   次のものを使用して、ラボのコンテキストの外部からカスタム イメージを取り込みます。
    - [Azure VM](https://azure.microsoft.com/services/virtual-machines/)。
    - 物理ラボ環境の VHD。

## <a name="save-a-custom-image-from-a-labs-template-vm"></a>ラボのテンプレート VM からカスタム イメージを保存する

ラボのテンプレート VM を使用したカスタム イメージの作成と保存は、Azure Lab Services のポータルを使用してサポートされているため、イメージを作成する最も簡単な方法です。 結果として、IT 部門と教師の両方は、ラボのテンプレート VM を使用してカスタム イメージを作成できます。

たとえば、Azure Marketplace イメージの 1 つから始めて、その後、クラスに必要なソフトウェア アプリケーションやツールなどをインストールすることができます。 イメージの設定が完了したら、[接続された共有イメージ ギャラリー](how-to-attach-detach-shared-image-gallery.md)に保存して、自分や他の教師がイメージを使用して新しいラボを作成できるようにすることができます。

この方法で注意する必要がある重要な点がいくつかあります。

- ラボ サービスは、テンプレート VM からイメージをエクスポートするときに、*特殊化された* イメージを自動的に保存します。 多くの場合、イメージはコンピューター固有の情報とユーザー プロファイルを保持するので、特殊化されたイメージは、新しいラボの作成に適しています。 特殊化されたイメージを使用すると、イメージを使用して新しいラボを作成するときに、インストールされているソフトウェアを確実に実行できます。 *一般化された* イメージを作成する必要がある場合は、この記事の他の推奨される方法のいずれかを使用して、カスタム イメージを作成する必要があります。

    Azure Lab Services の一般化されたイメージと特殊化されたイメージの両方に基づいて、テンプレート VM を作成できます。 相違点の詳細については、「[一般化されたイメージと特殊化されたイメージ](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images)」を参照してください。

- イメージを設定するより高度なシナリオでは、代わりに、Azure VM または物理ラボ環境の VHD を使用して、ラボの外部にイメージを作成すると便利です。 詳細については、以降のセクションを参照してください。

### <a name="use-a-labs-template-vm-to-save-a-custom-image"></a>ラボのテンプレート VM を使用してカスタム イメージを保存する 

ラボのテンプレート VM を使用して、Windows または Linux のカスタム イメージを作成できます。 詳細については、「[共有イメージ ギャラリーに既存のイメージを保存する](how-to-use-shared-image-gallery.md#save-an-image-to-the-shared-image-gallery)」を参照してください。

## <a name="bring-a-custom-image-from-an-azure-vm"></a>Azure VM からカスタム イメージを取り込む

もう 1 つの方法は、Azure VM を使用してカスタム イメージをセットアップすることです。 イメージの設定が完了したら、共有イメージ ギャラリーに保存して、自分や同僚がイメージを使用して新しいラボを作成できるようにすることができます。

Azure VM を使用すると、次のように柔軟性が向上します。
- [一般化または特殊化された](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images)イメージを作成できます。 あるいは、ラボのテンプレート VM を使用して[イメージをエクスポート](how-to-use-shared-image-gallery.md)すると、イメージは常に特殊化されます。
- Azure VM のより高度な機能にアクセスできます。この機能は、イメージの設定に役立ちます。 たとえば、[拡張機能](../virtual-machines/extensions/overview.md)を使用して展開後構成とオートメーションを実行できます。 また、VM の[ブート診断](../virtual-machines/boot-diagnostics.md)と[シリアル コンソール](/troubleshoot/azure/virtual-machines/serial-console-overview)にアクセスすることもできます。

Azure VM を使用したイメージの設定は、より複雑です。 そのため、通常、Azure VM でカスタム イメージを作成する責任は IT 部門が担います。

### <a name="use-an-azure-vm-to-set-up-a-custom-image"></a>Azure VM を使用してカスタム イメージを設定する

Azure VM からカスタム イメージを取り込む手順の概要を次に示します。

1. Windows または Linux Marketplace イメージを使用して [Azure VM](https://azure.microsoft.com/services/virtual-machines/) を作成します。
1. Azure VM に接続し、追加のソフトウェアをインストールします。 また、ラボに必要なその他のカスタマイズを行うこともできます。
1. イメージのセットアップが完了したら、[VM のイメージを共有イメージ ギャラリーに保存](../virtual-machines/image-version.md)します。 この手順の一環として、イメージの定義とバージョンも作成する必要があります。
1. カスタム イメージをギャラリーに保存した後は、イメージを使用して新しいラボを作成できます。 


手順は、カスタム Windows または Linux イメージを作成するかどうかによって異なります。 詳細な手順については、次の記事を参照してください。

-   [Azure VM からカスタム Windows イメージを取り込む](how-to-bring-custom-windows-image-azure-vm.md)
-   [Azure VM からカスタム Linux イメージを取り込む](how-to-bring-custom-linux-image-azure-vm.md)

## <a name="bring-a-custom-image-from-a-vhd-in-your-physical-lab-environment"></a>物理ラボ環境の VHD からカスタム イメージを取り込む

検討すべき 3 番目の方法は、物理ラボ環境の VHD から共有イメージ ギャラリーにカスタム イメージを取り込むことです。 イメージが共有イメージ ギャラリーに追加されたら、ユーザーや他の教師は、そのイメージを使用して新しいラボを作成できます。

このアプローチを使用する理由はいくつかあります。

- ラボで使用する[一般化されたまたは特殊化された](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images)イメージのどちらも作成できます。 あるいは、[ラボのテンプレート VM](how-to-use-shared-image-gallery.md) を使用してイメージをエクスポートすると、イメージは常に特殊化されます。
- オンプレミス環境内に存在するリソースにアクセスできます。 たとえば、オンプレミス環境に大規模なインストール ファイルがあり、ラボのテンプレート VM へのコピーに時間がかかる場合などです。
- [Microsoft Endpoint Configuration Manager](/mem/configmgr/core/understand/introduction) などの他のツールを使用して作成したイメージをアップロードできます。そうすると、ラボのテンプレート VM を使用してイメージを手動で設定する必要がありません。

VHD からカスタム イメージを取り込むことは、Azure 内で動作するようにイメージが適切に設定されていることを確認する必要があるため、最も高度な方法です。 そのため、通常、VHD からカスタム イメージを作成する責任は IT 部門が担います。

### <a name="bring-a-custom-image-from-a-vhd"></a>VHD からカスタム イメージを取り込む

VHD からカスタム イメージを取り込む手順の概要を次に示します。

1. オンプレミスのマシンで [Windows Hyper-V](/virtualization/hyper-v-on-windows/about/) を使用して、Windows または Linux VHD を作成します。
1. Hyper-V VM に接続し、追加のソフトウェアをインストールします。 また、ラボに必要なその他のカスタマイズを行うこともできます。
1. イメージのセットアップが完了したら、VHD をアップロードして、Azure に[マネージド ディスク](../virtual-machines/managed-disks-overview.md)を作成します。
1. マネージド ディスクから、共有イメージギャラリーに[イメージの定義](../virtual-machines/shared-image-galleries.md#image-definitions)とバージョンを作成します。
1. カスタム イメージをギャラリーに保存した後は、イメージを使用して新しいラボを作成できます。 

手順は、カスタム Windows または Linux イメージを作成するかどうかによって異なります。 詳細な手順については、次の記事を参照してください。

-   [VHD からカスタム Windows イメージを取り込む](upload-custom-image-shared-image-gallery.md)
-   [VHD からカスタム Linux イメージを取り込む](how-to-bring-custom-linux-image-vhd.md)

## <a name="next-steps"></a>次のステップ

* [共有イメージ ギャラリーの概要](../virtual-machines/shared-image-galleries.md)
* [共有イメージ ギャラリーをアタッチまたはデタッチする](how-to-attach-detach-shared-image-gallery.md)
* [共有イメージ ギャラリーを使用する](how-to-use-shared-image-gallery.md)
