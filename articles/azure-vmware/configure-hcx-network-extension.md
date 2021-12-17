---
title: HCX ネットワーク拡張機能を作成する
description: オンプレミス環境から Azure VMware Solution までネットワークを拡張する方法について説明します。
ms.topic: how-to
ms.date: 09/07/2021
ms.openlocfilehash: 81e984975712e76033102c030093fc94abb2796f
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129456197"
---
# <a name="create-a-hcx-network-extension"></a>HCX ネットワーク拡張機能を作成する

これはオプションの手順であり、オンプレミス環境から Azure VMware Solution までネットワークを拡張します。

1. **[Services]\(サービス\)** で、 **[Network Extension]\(ネットワーク拡張機能\)**  >  **[Create a Network Extension]\(ネットワーク拡張機能の作成\)** の順に選択します。

   :::image type="content" source="media/tutorial-vmware-hcx/create-network-extension.png" alt-text="ネットワーク拡張機能の作成を開始するための選択を示すスクリーンショット。" lightbox="media/tutorial-vmware-hcx/create-network-extension.png":::

1. Azure VMware Solution へと拡張する各ネットワークを選択し、 **[Next]\(次へ\)** を選択します。

   :::image type="content" source="media/tutorial-vmware-hcx/select-extend-networks.png" alt-text="ネットワークの選択を示すスクリーンショット。":::

1. 拡張しようとしているネットワークごとに、オンプレミス ゲートウェイの IP を入力してから、 **[送信]** を選択します。

   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-gateway.png" alt-text="ゲートウェイ IP アドレスの入力を示すスクリーンショット。":::

   ネットワークの拡張が完了するまでに数分かかります。 完了すると、状態が **[Extension complete]\(拡張完了\)** に変わります。

   :::image type="content" source="media/tutorial-vmware-hcx/extension-complete.png" alt-text="拡張完了の状態を示すスクリーンショット。":::

## <a name="next-steps"></a>次のステップ

HCX ネットワーク拡張機能を構成したので、次について学習できます。

- [VMware HCX Mobility Optimized Networking (MON) のガイダンス](vmware-hcx-mon-guidance.md)
