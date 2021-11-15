---
title: ラボ VM に既存のデータ ディスクを接続する
description: Azure DevTest Labs でラボ データ ディスクをラボ仮想マシンにアタッチまたはデタッチする方法について説明します
ms.topic: how-to
ms.date: 10/26/2021
ms.openlocfilehash: 8afd2fff50a893fb293d7b419c4839c34487c32d
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131453993"
---
# <a name="attach-or-detach-a-lab-data-disk-to-a-lab-virtual-machine-in-azure-devtest-labs"></a>Azure DevTest Labs でラボ データ ディスクをラボ仮想マシンにアタッチまたはデタッチします

ラボの Azure 仮想マシン (VM) 用の新しいラボ [データ ディスク](../virtual-machines/managed-disks-overview.md)を作成して接続することができます。 その後、データ ディスクをデタッチし、削除したり、再アタッチしたり、所有している別のラボ VM にアタッチできます。 この機能は、個々の仮想マシンの外部でストレージまたはソフトウェアを管理する場合に便利です。

この記事では、ラボ仮想マシンへのデータ ディスクのアタッチおよびデタッチ方法について説明します。

## <a name="prerequisites"></a>前提条件

ラボ仮想マシンが実行されている必要があります。 仮想マシンのサイズによって、アタッチできるデータ ディスク数は変わります。 詳細については、「 [仮想マシンのサイズ](../virtual-machines/sizes.md)」を参照してください。

## <a name="attach-a-new-data-disk"></a>新しいデータ ディスクをアタッチする

Azure DevTest Labs で新しいマネージド データ ディスクを作成して VM にアタッチするには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) にサインインする

1. **DevTest Labs** のラボに移動します。

1. 実行中の仮想マシンを選択します。

1. **[仮想マシン]** ページの **[設定]** で、 **[ディスク]** を選択します。
 
1. **[新しいディスクのアタッチ]** を選択します。

    :::image type="content" source="./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new.png" alt-text="新しいデータ ディスクを仮想マシンにアタッチする場合のスクリーンショット。":::

1. **[新しいディスクのアタッチ]** ページから次の情報を指定します。 

    |プロパティ | 説明 |
    |---|---|
    |名前|一意の名前を入力します。|
    |ディスクの種類| ドロップダウン リストで [[ディスクの種類]](../virtual-machines/disks-types.md) を選択します。|
    |サイズ (GiB)|ギガバイト単位でサイズを入力します。|

    :::image type="content" source="./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new-form.png" alt-text="完了した [新しいディスクのアタッチ] フォームのスクリーンショット。":::

1. **[OK]** を選択します。

1. **[仮想マシン]** ページに戻ります。 **[データ ディスク]** の下で、アタッチされているディスクが表示されます。

    :::image type="content" source="./media/devtest-lab-attach-detach-data-disk/devtest-lab-attached-data-disk.png" alt-text="アタッチされたディスクが [データ ディスク] の下に表示される場合のスクリーンショット。":::

## <a name="detach-a-data-disk"></a>データ ディスクの切断

デタッチすると、ラボ ディスクはラボ VM から削除されますが、後で使用できるようにストレージ内に保持されます。

### <a name="detach-from-the-vms-management-page"></a>VM の管理ページからデタッチする

1. **DevTest Labs** のラボに移動します。

1. アタッチされたデータ ディスクを持つ実行中の仮想マシンを選択します。

1. **[仮想マシン]** ページの **[設定]** で、 **[ディスク]** を選択します。

1. **[データ ディスク]** で、デタッチするデータ ディスクを選択します。

    :::image type="content" source="./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-button.png" alt-text="仮想マシンのデータ ディスクを選択する場合のスクリーンショット。":::

1. **[データ ディスク]** ページから **[デタッチ]** を選択します。

    :::image type="content" source="./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk-2.png" alt-text="[デタッチ] 操作が強調表示されているディスクの詳細ペインを示すスクリーンショット。":::

1. **[OK]** を選択して、データ ディスクをアタッチすることを確認します。 ディスクがデタッチされ、別の VM にアタッチできるようになります。 

### <a name="detach-from-the-labs-management-page"></a>ラボの管理ページからデタッチする

1. **DevTest Labs** のラボに移動します。

1. **[個人用ラボ]** で、 **[個人用データ ディスク]** を選択します。

1. デタッチするディスクについて、省略記号ボタン ( **[...]** ) を選択し、 **[デタッチ]** を選択します。

    :::image type="content" source="./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk.png" alt-text="データ ディスクのデタッチのスクリーンショット。":::

1. **[はい]** を選択して、デタッチすることを確認します。

   > [!NOTE]
   > データ ディスクが既にデタッチされている場合は、 **[削除]** を選択して、使用可能なデータ ディスクの一覧から削除することができます。

## <a name="attach-an-existing-disk"></a>既存のディスクの接続

使用可能な既存のデータ ディスクを実行中の VM にアタッチするには、次の手順に従います。 

1. **DevTest Labs** のラボに移動します。

1. 実行中の仮想マシンを選択します。

1. **[仮想マシン]** ページの **[設定]** で、 **[ディスク]** を選択します。

1. **[既存のディスクのアタッチ]** を選択します。

    :::image type="content" source="./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing-button.png" alt-text="[ディスク] 設定が選択されて [既存のディスクのアタッチ] が選択されたことを示すスクリーンショット。":::

1. **[既存のディスクのアタッチ]** ページからディスクを選択して、 **[OK]** を選択します。 しばらくすると、データ ディスクが VM にアタッチされ、その VM に関する **[データ ディスク]** の一覧に表示されます。

    :::image type="content" source="./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing.png" alt-text="既存のデータ ディスクを仮想マシンにアタッチする場合のスクリーンショット。":::

## <a name="upgrade-an-unmanaged-data-disk"></a>非管理対象データ ディスクのアップグレード

非管理対象データ ディスクを持つ VM がある場合は、マネージド ディスクを使用するように VM を変換できます。 このプロセスでは、OS ディスクと接続されたすべてのデータ ディスクの両方を変換します。

最初に、非管理対象 VM から[データ ディスクをデタッチ](#detach-a-data-disk)します。 次に、データ ディスクが非管理対象から管理対象に自動的にアップグレードされるように、管理対象 VM に[ディスクを再アタッチ](#attach-an-existing-disk)します。

## <a name="next-steps"></a>次のステップ

[要求可能な仮想マシン](devtest-lab-add-claimable-vm.md#unclaim-a-vm)のためにデータ ディスクを管理する方法について学習します。
