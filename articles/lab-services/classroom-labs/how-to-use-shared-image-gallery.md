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
ms.date: 05/07/2019
ms.author: spelluru
ms.openlocfilehash: 8d8b6fffe197d4180b091518dcd1615d0e0b9d19
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65412846"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Azure Lab Services で共有イメージ ギャラリーを使用する
この記事では、教師およびラボ管理者が、他のユーザーが再利用できるようテンプレート仮想マシン イメージを保存する方法を示します。 これらのイメージは、Azure [共有イメージ ギャラリー](../../virtual-machines/windows/shared-image-galleries.md)に保存されます。 最初の手順として、ラボ管理者は、ラボ アカウントに既存の共有イメージ ギャラリーをアタッチします。 共有イメージ ギャラリーがアタッチされたら、ラボ アカウントで作成されたラボは、共有イメージ ギャラリーにイメージを保存できます。 その他の教師は、共有イメージ ギャラリーからこのイメージを選択して、そのクラスのテンプレートを作成できます。 

## <a name="prerequisites"></a>前提条件
- [Azure PowerShell](../../virtual-machines/windows/shared-images.md) または [Azure CLI](../../virtual-machines/linux/shared-images.md) のいずれかを使用して、共有イメージ ギャラリーを作成します。
- あらかじめ共有イメージ ギャラリーをラボ アカウントにアタッチしておきます。 詳細な手順については、[共有イメージ ギャラリーをアタッチまたはデタッチする方法](how-to-attach-detach-shared-image-gallery.md)に関するページを参照してください。


## <a name="save-an-image-to-the-shared-image-gallery"></a>共有イメージ ギャラリーに既存のイメージを保存する
共有イメージ ギャラリーがアタッチされたら、教師は、他の教員が再利用できるよう共有イメージ ギャラリーにテンプレート イメージを保存またはアップロードできます。 共有イメージ ギャラリーにイメージをアップロードする手順については、[共有イメージ ギャラリーの概要](../../virtual-machines/windows/shared-images.md)に関するページを参照してください。 

> [!NOTE]
> 現在、クラスルーム ラボのユーザー インターフェイス (UI) には、ラボ イメージを共有イメージ ギャラリーに保存する機能がありません。 

## <a name="use-an-image-from-the-shared-image-gallery"></a>共有イメージ ギャラリーのイメージを使用する
教師や教授は、新しいラボの作成時に、テンプレートの共有イメージ ギャラリーにあるカスタム イメージを選択できます。

![ギャラリーの仮想マシン イメージを使用する](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>次の手順
共有イメージ ギャラリーの詳細については、[共有イメージ ギャラリー](../../virtual-machines/windows/shared-image-galleries.md)に関するページを参照してください。
