---
title: Azure Lab Services でラボ用の Marketplace イメージを指定する
description: この記事では、ラボ作成者が Azure Lab Services でラボ アカウントにラボを作成するために使用できる Marketplace イメージを指定する方法について説明します。
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
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: ad56041f853d030e3a286610fe4872bffecaee12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444896"
---
# <a name="specify-marketplace-images-available-to-lab-creators"></a>ラボ作成者が利用できる Marketplace イメージを指定する
ラボ アカウント所有者は、ラボ作成者がラボ アカウントにラボを作成するために使用できる Marketplace イメージを指定できます。 

## <a name="select-images-available-for-labs"></a>ラボで使用可能なイメージを選択する
左側のメニューで、 **[Marketplace イメージ]** を選択します。 既定では、イメージの完全な一覧 (有効なものと無効なものの両方) が表示されます。 上部のドロップダウン リストから **[有効のみ]** / **[無効のみ]** オプションを選択することにより、一覧をフィルター処理して、有効/無効のイメージのみを表示することができます。 
    
![Marketplace イメージのページ](../media/tutorial-setup-lab-account/marketplace-images-page.png)

一覧に表示される Marketplace イメージは、次の条件を満たすものだけです。
    
- 単一の VM を作成する。
- Azure Resource Manager を使用して VM をプロビジョニングする
- 追加のライセンス プランの購入を必要としない

## <a name="disable-images-for-a-lab"></a>ラボに対してイメージを無効にする 
1 つのイメージをラボに対して無効にするには、最後の列の **[...]** (省略記号) を選択し、 **[イメージを無効にする]** を選択します。 

![1 つのイメージを無効にする](../media/tutorial-setup-lab-account/disable-one-image.png) 

または、イメージ名の前にあるチェック ボックスをオンにして、ツール バーの **[選択したイメージを無効にする]** を選択します。 

複数のイメージを同時に無効にするには、イメージ名の前にあるチェック ボックスをオンにして、ツール バーの **[選択したイメージを無効にする]** を選択します。 

![複数のイメージを無効にする](../media/tutorial-setup-lab-account/disable-multiple-images.png) 


## <a name="enable-images-for-a-lab"></a>ラボに対してイメージを有効にする
無効になっているイメージを有効にするには、最後の列の **[...]** (省略記号) を選択し、 **[イメージを有効にする]** を選択します。 または、イメージ名の前にあるチェック ボックスをオンにして、ツール バーの **[選択したイメージを有効にする]** を選択します。 

複数のイメージを同時に無効にするには、イメージ名の前にあるチェック ボックスをオンにして、ツール バーの **[選択したイメージを有効にする]** を選択します。 

## <a name="next-steps"></a>次のステップ
次の記事をご覧ください。

- [ラボ所有者としてラボを作成および管理する](how-to-manage-classroom-labs.md)
- [ラボ所有者としてテンプレートを設定および発行する](how-to-create-manage-template.md)
- [ラボ所有者としてラボの使用を構成および制御する](how-to-configure-student-usage.md)
- [ラボ ユーザーとしてクラスルーム ラボにアクセスする](how-to-use-classroom-lab.md)
