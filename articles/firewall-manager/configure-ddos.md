---
title: Azure Firewall Manager を使用して Azure DDoS Protection プランを構成する
description: Azure Firewall Manager を使用して Azure DDoS Protection Standard プランを構成する方法について説明します
author: vhorne
ms.author: victorh
ms.service: firewall-manager
ms.topic: how-to
ms.date: 09/30/2021
ms.custom: template-how-to
ms.openlocfilehash: 4ec6c08fab14c2c08ed719d616b84f1a4d744eb9
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367475"
---
# <a name="configure-an-azure-ddos-protection-plan-using-azure-firewall-manager-preview"></a>Azure Firewall Manager を使用して Azure DDoS Protection プランを構成する (プレビュー)

Azure Firewall Manager は、大規模なネットワーク リソースを管理および保護するためのプラットフォームです。 Azure Firewall Manager では、仮想ネットワークを DDoS 保護プランに関連付けることができます。

> [!IMPORTANT]
> Azure Firewall Manager を使用した Azure DDoS Protection プランの構成は、現在プレビュー段階です。
> ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用される法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。 

> [!TIP]
> 現在、DDoS Protection Standard では仮想 WAN はサポートされていません。 ただし、DDoS Protection プランが関連付けられている仮想ネットワーク内の Azure Firewall に対してインターネット トラフィックを強制的にトンネリングすることで、この制限を回避することができます。

シングル テナントでは、複数のサブスクリプションにわたって DDoS Protection プランを仮想ネットワークに適用できます。 DDoS Protection プランの詳細については、「[Azure DDoS Protection Standard の概要](../ddos-protection/ddos-protection-overview.md)」を参照してください。

このしくみを確認するため、ファイアウォール ポリシーを作成し、Azure Firewall で保護された仮想ネットワークを作成します。 その後、DDoS Protection プランを作成し、仮想ネットワークに関連付けます。

## <a name="create-a-firewall-policy"></a>ファイアウォール ポリシーを作成する

Firewall Manager を使用して、ファイアウォール ポリシーを作成します。

1. [Azure portal](https://portal.azure.com) から Firewall Manager を開きます。
1. **[Azure Firewall ポリシー]** を選択します。
1. **[Azure ファイアウォール ポリシーの作成]** を選択します。
1. **[リソース グループ]** で、 **[DDoS-Test-rg]** を選択します。
1. **[ポリシーの詳細]** 、 **[名前]** で、「**fw-pol-01**」と入力します。
1. **[リージョン]** では **[米国西部 2]** を選択します。
1. **[Review + create]\(レビュー + 作成\)** を選択します。
1. **［作成］** を選択します


## <a name="create-a-secured-virtual-network"></a>セキュリティで保護された仮想ネットワークの作成

Firewall Manager を使用して、セキュリティで保護された仮想ネットワークを作成します。

1. Firewall Manager を開きます。
1. **[仮想ネットワーク]** を選択します。
1. **[セキュリティで保護された仮想ネットワークの新規作成]** を選択します。
1. **[リソース グループ]** で、 **[DDoS-Test-rg]** を選択します。
1. **[リージョン]** では **[米国西部 2]** を選択します。
1. **[ハブの仮想ネットワークの名前]** で、「**Hub-vnet-01**」と入力します。
1. **[アドレス範囲]** に「**10.0.0.0/16**」と入力します。
1. **[次へ: Azure Firewall]** を選択します。
1. **[パブリック IP アドレス]** で **[新規追加]** を選択し、名前として「**fw-pip**」と入力し、 **[OK]** を選択します。
1. **[ファイアウォール サブネットのアドレス空間]** に「**10.0.0.0/24**」と入力します。
1. **[ファイアウォール ポリシー]** で **[fw-pol-01]** を選択します。
1. **[次へ :確認と作成]** をクリックします。
1. **［作成］** を選択します

## <a name="create-a-ddos-protection-plan"></a>DDoS Protection プランを作成する

Firewall Manager を使用して DDoS Protection プランを作成します。 **[DDoS Protection プラン]** ページを使用して、Azure DDoS Protection プランを作成および管理できます。

:::image type="content" source="media/configure-ddos/firewall-ddos.png" alt-text="Firewall Manager の [DDoS Protection プラン] ページのスクリーンショット":::

1. Firewall Manager を開きます。
1. **[DDoS Protection プラン]** を選択します。
1. **［作成］** を選択します
1. **[リソース グループ]** には **[新規作成]** を選択します。
1. リソース グループ名として「**DDos-Test-rg**」と入力します。
1. **[インスタンスの詳細]** 、 **[名前]** で、「**DDoS-plan-01**」と入力します。
1. **[リージョン]** で、 **[(米国) 米国西部 2]** を選択します。
1. **[Review + create]\(レビュー + 作成\)** を選択します。
1. **［作成］** を選択します

## <a name="associate-a-ddos-protection-plan"></a>DDoS Protection プランを関連付ける

これで、DDoS Protection プランをセキュリティで保護された仮想ネットワークに関連付けることができます。

1. Firewall Manager を開きます。
1. **[仮想ネットワーク]** を選択します。
1. **[Hub-vnet-01]** のチェック ボックスをオンにします。
1. **[セキュリティの管理]** 、 **[DDoS Protection プランの追加]** を選択します。
1. **[DDoS Protection Standard]** で、 **[有効にする]** を選択します。
1. **[DDoS 保護プラン]** で、 **[DDoS-plan-01]** を選択します。
1. **[追加]** を選択します。
1. デプロイが完了したら、 **[更新]** を選択します。

これで、仮想ネットワークに DDoS Protection プランが関連付けられていることがわかります。

:::image type="content" source="media/configure-ddos/ddos-protection.png" alt-text="DDoS Protection プランの仮想ネットワークを示すスクリーンショット":::

## <a name="next-steps"></a>次のステップ

DDoS Protection プランの詳細については、以下を参照してください。

- [Azure DDoS Protection Standard の概要](../ddos-protection/ddos-protection-overview.md)