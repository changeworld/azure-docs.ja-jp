---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d36d2be59010c47348a8e196b28d87e5b967868e
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/23/2019
ms.locfileid: "66157416"
---
### <a name="step-1-navigate-to-the-virtual-network-gateway"></a>手順 1:仮想ネットワーク ゲートウェイに移動する

1. [Azure Portal](https://portal.azure.com) で **[すべてのリソース]** に移動します。 
2. 仮想ネットワーク ゲートウェイのページを開くために、削除する仮想ネットワーク ゲートウェイに移動して、それをクリックします。

### <a name="step-2-delete-connections"></a>手順 2:接続を削除する

1. 仮想ネットワーク ゲートウェイのページで、 **[接続]** をクリックしてゲートウェイへのすべての接続を表示します。
2. 接続名の行で **[...]** をクリックして、ドロップダウンから **[削除]** を選択します。
3. 確認画面で **[はい]** をクリックして、接続を削除します。 複数の接続がある場合は、接続を一つずつ削除します。

### <a name="step-3-delete-the-virtual-network-gateway"></a>手順 3:仮想ネットワーク ゲートウェイの削除

S2S 構成に加えてこの VNet に対する P2S 構成がある場合は、仮想ネットワーク ゲートウェイを削除すると、警告なしにすべての P2S クライアントが自動的に切断されることに注意してください。

1. 仮想ネットワーク ゲートウェイのページで、 **[概要]** をクリックします。
2. **[概要]** ページで、 **[削除]** をクリックしてゲートウェイを削除します。
