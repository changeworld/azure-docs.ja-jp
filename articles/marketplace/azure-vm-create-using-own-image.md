---
title: 独自のイメージを使用して Azure Marketplace に Azure 仮想マシン オファーを作成する
description: 独自のイメージを使用して Azure Marketplace に Azure 仮想マシン オファーを公開する方法について説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: krsh
ms.date: 10/20/2020
ms.openlocfilehash: 42022d1204c3b524ee2e9ef2770f616fba89dc8c
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92283380"
---
# <a name="how-to-create-a-virtual-machine-using-your-own-image"></a>独自のイメージを使用して仮想マシンを作成する方法

この記事では、ユーザー指定の仮想マシン (VM) イメージを作成してデプロイする方法について説明します。

> [!NOTE]
> この手順を開始する前に、Azure VM オファーの仮想ハード ディスク (VHD) などに関する[技術要件](marketplace-virtual-machines.md#technical-requirements)を確認してください。

代わりに、承認済みのベース イメージを使用する場合は、[承認済みのベースからの VM イメージの作成](azure-vm-create-using-approved-base.md)に関する手順に従います。

## <a name="configure-the-vm"></a>VM を構成する

このセクションでは、Azure VM のサイズ変更、更新、一般化を行う方法について説明します。 これらの手順は、Azure Marketplace にデプロイする VM を準備するために必要です。

### <a name="size-the-vhds"></a>VHD のサイズを調整する

[!INCLUDE [Discussion of VHD sizing](includes/vhd-size.md)]

### <a name="install-the-most-current-updates"></a>最新の更新プログラムをインストールする

[!INCLUDE [Discussion of most current updates](includes/most-current-updates.md)]

### <a name="perform-additional-security-checks"></a>追加のセキュリティ チェックを実行する

[!INCLUDE [Discussion of addition security checks](includes/additional-security-checks.md)]

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>カスタム構成とスケジュールされたタスクを実行する

[!INCLUDE [Discussion of custom configuration and scheduled tasks](includes/custom-config.md)]

## <a name="upload-the-vhd-to-azure"></a>VHD を Azure にアップロードする

「[Azure にアップロードする Windows VHD または VHDX を準備する](../virtual-machines/windows/prepare-for-upload-vhd-image.md)」または [Linux VHD の作成とアップロード](../virtual-machines/linux/create-upload-generic.md)に関するページで説明されている方法で、VM をアップロードするための構成と準備を行います。

## <a name="extract-the-vhd-from-image-if-using-image-building-services"></a>イメージから VHD を抽出する (イメージ作成サービスを使用している場合)

[Packer](https://www.packer.io/) などのイメージ作成サービスを使用している場合は、イメージから VHD を抽出する必要があります。 これは直接的には実行できません。 VM を作成し、VM ディスクから VHD を抽出する必要があります。

### <a name="create-the-vm-on-the-azure-portal"></a>Azure portal で VM を作成する

[Azure portal](https://ms.portal.azure.com/) でベース VM イメージを作成するには、次の手順に従います。

1. [Azure portal](https://ms.portal.azure.com/) にサインインします。
2. **[Virtual Machines]** を選択します。
3. **[+ 追加]** を選択して、 **[仮想マシンの作成]** 画面を開きます。
4. ドロップダウン リストからイメージを選択するか、または **[すべてのパブリックおよびプライベート イメージを参照する]** を選択して、使用可能なすべての仮想マシン イメージを検索または参照します。
5. **Gen 2** VM を作成するには、 **[詳細設定]** タブに移動し、 **[Gen 2]** オプションを選択します。

    :::image type="content" source="media/create-vm/vm-gen-option.png" alt-text="Gen 1 または Gen 2 を選択します。":::

6. デプロイする VM のサイズを選択します。

    :::image type="content" source="media/create-vm/create-virtual-machine-sizes.png" alt-text="Gen 1 または Gen 2 を選択します。":::

7. VM の作成に必要なその他の詳細を指定します。
8. **[確認と作成]** を選択して、選択内容を確認します。 **検証に成功** のメッセージが表示されたら、 **[作成]** を選択します。

Azure で、指定した仮想マシンのプロビジョニングが開始されます。 左側のメニューで **[Virtual Machines]** タブを選択して、進捗状況を追跡します。 仮想マシンが作成されると、状態が **[実行中]** に変更されます。

### <a name="connect-to-your-vm"></a>VM に接続する

お使いの [Windows](../virtual-machines/windows/connect-logon.md) または [Linux](../virtual-machines/linux/ssh-from-windows.md#connect-to-your-vm) VM に接続する方法については、次のドキュメントを参照してください。

[!INCLUDE [Discussion of addition security checks](includes/size-connect-generalize.md)]

## <a name="next-steps"></a>次の手順

- 推奨される次の手順:[お使いの VM イメージをテストし](azure-vm-image-test.md)、それが Azure Marketplace の公開要件を満たしていることを確認します。 これは省略可能です。
- お使いの VM イメージをテストしない場合は、[SAS URI の生成](azure-vm-get-sas-uri.md)に進んでください。
- お使いの Azure ベースの新しい VHD の作成で問題が発生した場合は、[Azure Marketplace の VM に関する FAQ](azure-vm-create-faq.md) を参照してください。
