---
title: Azure Marketplace で IoT Edge モジュール オファーのプランを作成および編集する
description: Azure Marketplace で IoT Edge モジュール オファーのプランを作成および編集します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: aarathin
ms.author: aarathin
ms.date: 07/08/2021
ms.openlocfilehash: 238be0168be689b9bfa179a4e13e08c2c427b8a2
ms.sourcegitcommit: 2cff2a795ff39f7f0f427b5412869c65ca3d8515
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2021
ms.locfileid: "113598065"
---
# <a name="create-and-edit-plans-for-an-iot-edge-module-offer"></a>IoT Edge モジュール オファーのプランを作成および編集する

この概要ページでは、同じオファー内でさまざまなプラン オプションを作成できます。 プラン (旧称 SKU) は、提供される場所 (Azure Global または Azure Government) や、プランによって参照されるイメージの点で異なる場合があります。 オファーには少なくとも 1 つのプランが含まれている必要があります。

オファーごとに最大 100 のプランを作成できます。そのうち最大 45 をプライベートにすることができます。 プライベート プランの詳細については、「[Microsoft 商業マーケットプレースでのプライベート オファー](private-offers.md)」を参照してください。

プランを作成した後、 **[プランの概要]** ページには次のように表示されます。

- プラン名
- 価格モデル
- Azure リージョン (グローバルまたは Government)
- 現在の公開状態
- 利用できるアクション

プランに対してで使用可能なアクションは、プランの現在の状態によって異なります。 これには次のようなものがあります。

- **ドラフトを削除する**: プランの状態がドラフトの場合。
- **プランの配布を停止する**: プランの状態が一般公開の場合。

## <a name="edit-a-plan"></a>プランを編集する

プランの **[名前]** を選択して、その詳細を編集します。

## <a name="create-a-plan"></a>プランの作成

新しいプランをセットアップするには、 **[+ 新しいプランの作成]** を選択します。

各プランの一意な **[プラン ID]** を入力します。 この ID は、製品の Web アドレスで顧客に表示されます。 小文字、数字、ダッシュ、アンダースコアのみを使用し、最大文字数は 50 です。 **[作成]** を選択した後はプラン ID を変更できません。

**プラン名** を入力してください この名前は、顧客がオファー内のどのプランを選択するかを決めるときに表示されます。 このオファーの各プランには、一意の名前を付ける必要があります。 たとえば、**Windows Server 2016** プランと **Windows Server 2019** プランで **Windows Server** のオファー名を使用することがあります。

**[作成]** を選択して次に進みます。

## <a name="next-steps"></a>次のステップ

- [新しいプランを作成する](iot-edge-plan-setup.md)、または
- プランのセットアップを終了して [Microsoft との共同販売](./co-sell-overview.md) (オプション) に進む、または
- [プランをレビューして公開する](review-publish-offer.md)