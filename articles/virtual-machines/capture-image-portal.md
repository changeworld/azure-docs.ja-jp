---
title: ポータルを使用して VM のイメージをキャプチャする
description: Azure portal を使用して VM のイメージを作成します。
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/21/2021
ms.author: cynthn
ms.custom: portal
ms.openlocfilehash: bfab2d65de8b15ac72fe3c3b536b76ef14f046b4
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131437298"
---
# <a name="create-an-image-of-a-vm-in-the-portal"></a>ポータルで VM のイメージを作成する

VM からイメージを作成し、それを使用して複数の VM を作成できます。

Azure Compute Gallery (旧称 Shared Image Gallery) に格納されているイメージの場合、アカウントが既に作成されている VM を使用することも (特殊化)、イメージを作成する前に VM を一般化して、マシン アカウントや他のマシン固有の情報を削除することもできます。 VM を一般化するには、[Windows VM の一般化](generalize.md)に関するページ参照してください。 詳細については、「[一般化されたイメージと特殊化されたイメージ](shared-image-galleries.md#generalized-and-specialized-images)」を参照してください。


## <a name="capture-a-vm-in-the-portal"></a>ポータルで VM をキャプチャする 

1. [Azure portal](https://portal.azure.com) に移動し、**仮想マシン** を検索して選択します。

2. 一覧で VM を選択します。

3. VM の **[仮想マシン]** ページの上部のメニューで、 **[キャプチャ]** を選択します。

   **[イメージの作成]** ページが表示されます。

5. **[リソース グループ]** で、 **[新規作成]** を選択して名前を入力するか、使用するリソースグループをドロップダウンリストから選択します。 既存のギャラリーを使用する場合は、使用するギャラリーのリソース グループを選択します。

1. ギャラリーにイメージを作成するには、 **[はい、ギャラリーにイメージ バージョンとして共有します]** を選択します。
    
   マネージド イメージのみを作成するには、 **[いいえ、マネージド イメージのみをキャプチャします]** を選択します。 マネージド イメージを作成するには、VM が一般化されている必要があります。 他に必要な情報は、イメージの名前のみです。

6. イメージが作成された後にソース VM を削除する場合、 **[イメージの作成後、この仮想マシンを自動的に削除します]** を選択します。 これは推奨されません。

1. **[ギャラリーの詳細]** で、ギャラリーを選択するか、 **[新規作成]** を選択して新しいギャラリーを作成します。

1. **[オペレーティング システムの状態]** で、一般化または特殊化を選択します。 詳細については、「[一般化されたイメージと特殊化されたイメージ](shared-image-galleries.md#generalized-and-specialized-images)」を参照してください。
 
1. イメージの定義を選択するか、 **[新規作成]** を選択して、新しい [イメージの定義](shared-image-galleries.md#image-definitions)の名前と情報を指定します。

1. [イメージのバージョン](shared-image-galleries.md#image-versions)番号を入力します。 これがこのイメージの最初のバージョンである場合は、「*1.0.0*」と入力します。

1. イメージ バージョンに対して "*最新*" を指定するときにこのバージョンを含めたい場合は、 **[最新から除外]** をオフのままにします。

1. **[有効期間の終了]** の日付を選択します。 この日付を使用して、古いイメージをいつ廃止する必要があるかを追跡できます。

1. [[複製]](shared-image-galleries.md#replication) で、既定のレプリカ数を選択した後、イメージをレプリケートする追加のリージョンを選択します。

8. 完了したら、 **[確認および作成]** を選択します。

1. 検証に合格したら、 **[作成]** を選択してイメージを作成します。



## <a name="next-steps"></a>次のステップ

- [Azure Compute Gallery の概要](shared-image-galleries.md) 
