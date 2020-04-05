---
title: Azure Lab Services で共有イメージ ギャラリーを使用する | Microsoft Docs
description: 共有イメージ ギャラリーを使用するようにラボ アカウントを構成して、ユーザーが他のユーザーとイメージを共有したり、他のユーザーがイメージを使用してラボでテンプレート VM を作成したりできるようにする方法について説明します。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2020
ms.author: spelluru
ms.openlocfilehash: c611ecdb5a2534f7368e533e3e19e6e3f96de57f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190451"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Azure Lab Services で共有イメージ ギャラリーを使用する
この記事では、教師およびラボ管理者が、他のユーザーが再利用できるようテンプレート仮想マシン イメージを保存する方法を示します。 これらのイメージは、Azure [共有イメージ ギャラリー](../../virtual-machines/windows/shared-image-galleries.md)に保存されます。 最初の手順として、ラボ管理者は、ラボ アカウントに既存の共有イメージ ギャラリーをアタッチします。 共有イメージ ギャラリーがアタッチされたら、ラボ アカウントで作成されたラボは、共有イメージ ギャラリーにイメージを保存できます。 その他の教師は、共有イメージ ギャラリーからこのイメージを選択して、そのクラスのテンプレートを作成できます。 

> [!NOTE]
> 現在、Azure Lab Services は、共有イメージギャラリーにある**汎用の** VM イメージ (特化されていないイメージ) のみに基づいてテンプレート VM の作成をサポートしています。 

## <a name="prerequisites"></a>前提条件
- [Azure PowerShell](../../virtual-machines/windows/shared-images.md) または [Azure CLI](../../virtual-machines/linux/shared-images.md) のいずれかを使用して、共有イメージ ギャラリーを作成します。
- あらかじめ共有イメージ ギャラリーをラボ アカウントにアタッチしておきます。 詳細な手順については、[共有イメージ ギャラリーをアタッチまたはデタッチする方法](how-to-attach-detach-shared-image-gallery.md)に関するページを参照してください。


## <a name="save-an-image-to-the-shared-image-gallery"></a>共有イメージ ギャラリーに既存のイメージを保存する
共有イメージ ギャラリーがアタッチされたら、ラボ アカウントの管理者または教師は、他の教師が再利用できるよう共有イメージ ギャラリーにイメージを保存できます。 

1. ラボの **[テンプレート]** ページで、ツール バーにある **[Export to Shared Image Gallery]\(Shared Image Gallery にエクスポートする\)** を選択します。

    ![[Save image]\(イメージの保存\) ボタン](../media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-button.png)
2. **[Export to Shared Image Gallery]\(Shared Image Gallery にエクスポートする\)** ダイアログで**イメージの名前を入力**し、 **[エクスポート]** を選択します。 

    ![[Export to Shared Image Gallery]\(Shared Image Gallery にエクスポートする\) ダイアログ](../media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-dialog.png)
3. この操作の進捗状況は **[テンプレート]** ページで確認できます。 この操作には時間がかかることがあります。 

    ![エクスポートが進行中](../media/how-to-use-shared-image-gallery/exporting-image-in-progress.png)
4. エクスポート操作に成功すると、次のメッセージが表示されます。

    ![エクスポート完了](../media/how-to-use-shared-image-gallery/exporting-image-completed.png)

ラボのコンテキスト外で共有イメージ ギャラリーにイメージをアップロードすることもできます。 詳細については、[共有イメージ ギャラリーの概要](../../virtual-machines/windows/shared-images.md)に関するページを参照してください。 

## <a name="use-an-image-from-the-shared-image-gallery"></a>共有イメージ ギャラリーのイメージを使用する
教師や教授は、新しいラボの作成時に、テンプレートの共有イメージ ギャラリーにあるカスタム イメージを選択できます。

![ギャラリーの仮想マシン イメージを使用する](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>次のステップ
共有イメージ ギャラリーの詳細については、[共有イメージ ギャラリー](../../virtual-machines/windows/shared-image-galleries.md)に関するページを参照してください。
