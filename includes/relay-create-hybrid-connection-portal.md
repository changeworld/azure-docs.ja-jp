---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: ceb2626a43ed44338bb0faad475ae2333af2de9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "67181407"
---
既に [Relay 名前空間を作成][namespace-how-to]していることを確認してください。

1. [Azure portal](https://portal.azure.com) にサインインします。
2. 左側のメニューで、 **[すべてのリソース]** を選びます。
3. ハイブリッド接続を作成する名前空間を選択します。 この例では **mynewns** です。  
4. **リレー名前空間**の下で、 **[ハイブリッド接続]** を選択します。

    ![ハイブリッド接続の追加](./media/relay-create-hybrid-connection-portal/create-hc-1.png)

5. 名前空間の概要ウィンドウで **[+ ハイブリッド接続]** を選択します。
   
    ![ハイブリッド接続の選択](./media/relay-create-hybrid-connection-portal/create-hc-2.png)
6. **[ハイブリッド接続の作成]** の下で、ハイブリッド接続名の値を入力します。 その他は既定値のままにします。
   
    ![Select New](./media/relay-create-hybrid-connection-portal/create-hc-3.png)
7. **［作成］** を選択します

[namespace-how-to]: ../articles/service-bus-relay/relay-create-namespace-portal.md 