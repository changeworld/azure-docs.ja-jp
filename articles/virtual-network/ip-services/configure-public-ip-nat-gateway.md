---
title: NAT ゲートウェイを使用してパブリック IP アドレスを管理する
titleSuffix: Azure Virtual Network
description: Azure Virtual Network NAT ゲートウェイでパブリック IP アドレスを使用する方法と、構成を変更する方法について説明します。
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 06/28/2021
ms.custom: template-how-to
ms.openlocfilehash: b87947aa265e5a92abf033fffd28ab3424785bb5
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130217394"
---
# <a name="manage-a-public-ip-address-with-a-nat-gateway"></a>NAT ゲートウェイを使用してパブリック IP アドレスを管理する

Azure NAT Gateway リソースにより、仮想ネットワーク内のサブネットからの送信インターネット接続が可能になります。 NAT ゲートウェイ仮想ネットワーク サブネットにデプロイされるリソースは、Standard SKU である必要があります。 Basic SKU リソースによる仮想ネットワーク サブネットへの NAT ゲートウェイのデプロイはサポートされていません。 

NAT ゲートウェイにより、NAT ゲートウェイを使用したリソースから送信元ネットワークアドレス変換 (SNAT) 接続が可能になります。 NAT ゲートウェイでは、Standard SKU のパブリック IP アドレスとパブリック IP プレフィックスがサポートされます。 任意の組み合わせがサポートされていますが、指定された IP の数が 16 を超えることはできません。 IP アドレスまたは IP プレフィックスを追加することで、NAT ゲートウェイを使用したリソースからの SNAT 接続をスケールします。 

この記事では、サブスクリプション内の既存のパブリック IP を使用して NAT ゲートウェイ を作成する方法について説明します。 

NAT ゲートウェイに関連付けられている現在のパブリック IP を変更する方法について説明します。 

最後に、パブリック IP アドレスから IP プレフィックスに IP 構成を変更します。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料で作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- サブスクリプション内の 2 つの Standard SKU のパブリック IP アドレス。 この IP アドレスは、どのリソースにも関連付けることができません。 Standard SKU のパブリック IP アドレスの作成の詳細については、[パブリック IP の作成 - Azure portal](./create-public-ip-portal.md) に関するページを参照してください。
    - この記事の例では、新しいパブリック IP アドレスに **myStandardPublicIP-1** および **myStandardPublicIP-2** という名前を付けます。
- サブスクリプション内のパブリック IP プレフィックス。 パブリック IP プレフィックスの作成の詳細については、「[Azure portal を使用してパブリック IP アドレス プレフィックスを作成する](./create-public-ip-prefix-portal.md)」を参照してください。
    - この記事の例の目的のため、新しいパブリック IP プレフィックスに **myPublicIPPrefixNAT** という名前を付けます。

## <a name="create-nat-gateway-existing-public-ip"></a>NAT ゲートウェイの既存のパブリック IP を作成する

このセクションでは、NAT ゲートウェイ リソースを作成します。 前提条件で作成した IP アドレスを NAT ゲートウェイのパブリック IP として選択します。

1. [Azure portal](https://portal.azure.com) にサインインします。

2. ポータルの上部にある検索ボックスに、「**NAT ゲートウェイ**」と入力します。

3. 検索結果から **[NAT ゲートウェイ]** を選択します。

4. **[+ 作成]** を選択します。

5. **[ネットワーク アドレス変換 (NAT) ゲートウェイを作成します]** で、次の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | **プロジェクトの詳細** |   |
    | サブスクリプション | サブスクリプションを選択します。 |
    | Resource group | **[新規作成]** を選択します。 </br> 「**myResourceGroupNAT**」と入力します。 </br> **[OK]** を選択します。 |
    | **インスタンスの詳細** |   |
    | 名前 | 「**myNATgateway**」と入力します。 |
    | リージョン | **[(米国) 米国西部 2]** を選択します。 |
    | 可用性ゾーン | 既定値の **[なし]** のままにします。 |
    | アイドル タイムアウト | 既定値の **[4]** のままにします。 |

6. **[送信 IP]** タブを選択するか、 **[次へ: 送信 IP]** を選択します。

7. **[送信 IP]** タブの **[パブリック IP アドレス]** で **[myStandardPublicIP-1]** を選択します。

6. **[確認と作成]** タブを選択するか、青い **[確認と作成]** ボタンを選択します。 

7. **［作成］** を選択します

> [!NOTE]
> これは、NAT ゲートウェイの簡単なデプロイです。 詳細な構成とセットアップについては、「[チュートリアル: Azure portal を使用した NAT ゲートウェイの作成](../nat-gateway/tutorial-create-nat-gateway-portal.md)」を参照してください
>
> Azure Virtual Network NAT の詳細については、「[Azure Virtual Network NAT とは](../nat-gateway/nat-overview.md)」を参照してください。

## <a name="change-or-remove-public-ip-address"></a>パブリック IP アドレスを変更または削除する

このセクションでは、Azure portal にサインインし、NAT ゲートウェイの IP アドレスを変更します。 

IP を変更するには、以前に作成した新しいパブリック IP アドレスを NAT ゲートウェイに関連付けます。 NAT ゲートウェイには、少なくとも 1 つの IP アドレスが割り当てられている必要があります。

1. [Azure portal](https://portal.azure.com) にサインインします。

2. ポータルの上部にある検索ボックスに、「**NAT ゲートウェイ**」と入力します。

3. 検索結果から **[NAT ゲートウェイ]** を選択します。

4. **[Nat ゲートウェイ]** で、 **[myNATgateway]** または変更する NAT ゲートウェイを選択します。

5. **[myNATgateway]** の設定で **[送信 IP]** を選択します。

6. **[送信 IP]** で、 **[パブリック IP アドレス]** の横にある **[変更]** を選択します。

7. 現在の IP アドレスを置き換えるか、既存のアドレスを追加するかを選択できます。 **[パブリック IP アドレス]** の横にある **[パブリック IP のアドレスとプレフィックスの管理]** で、 **[myStandardPublicIP-2]** を選択します。

8. **[OK]** を選択します。

9. **Mystandardpublicip-2** がパブリック IP アドレスに追加されたことを確認します。 必要に応じてごみ箱を選択して、既に割り当てられている IP アドレスを削除できます。

10. **[保存]** を選択します。

## <a name="add-public-ip-prefix"></a>パブリック IP プレフィックスの追加

パブリック IP プレフィックスにより、NAT ゲートウェイからの送信接続の SNAT の拡張性が向上します。 パブリック IP プレフィックスにより、SNAT ポートの枯渇が回避されます。 各 IP には、64,000 個のエフェメラル ポートが用意され、使用できます。

> [!NOTE] 
> NAT ゲートウェイにパブリック IP プレフィックスを割り当てると、範囲全体が使用されます。 

このセクションでは、前に作成したパブリック IP プレフィックスを使用するように送信 IP 構成を変更します。

> [!NOTE]
> NAT ゲートウェイに関連付けられている単一の IP アドレスを削除して再利用するか、または NAT ゲートウェイに関連付けたままにして、送信 SNAT ポートを増やすことができます。 NAT ゲートウェイでは、送信 IP 構成でパブリック IP とプレフィックスの組み合わせがサポートされています。 16 個のアドレスでパブリック IP プレフィックスを作成した場合は、プレフィックスを追加する前に、1 つのパブリック IP を削除し、構成を保存します。 割り当てられる IP の数が 16 を超えることはできません。

1. [Azure portal](https://portal.azure.com) にサインインします。

2. ポータルの上部にある検索ボックスに、「**NAT ゲートウェイ**」と入力します。

3. 検索結果から **[NAT ゲートウェイ]** を選択します。

4. **[Nat ゲートウェイ]** で、 **[myNATgateway]** または変更する NAT ゲートウェイを選択します。

5. **[myNATgateway]** の設定で **[送信 IP]** を選択します。

6. **[送信 IP]** で、 **[パブリック IP プレフィックス]** の横にある **[変更]** を選択します。

7. **[myPublicIPPrefixNAT]** またはプレフィックスを選択します。

8. **[OK]** を選択します。

9. **myPublicIPPrefixNAT-2** がパブリック IP プレフィックスに追加されたことを確認します。

10. **[保存]** を選択します。

## <a name="more-information"></a>詳細情報

* NAT ゲートウェイを使用して仮想ネットワークに仮想マシンをデプロイする場合、NAT ゲートウェイを宛先にするすべてのイングレス トラフィックは NAT ゲートウェイ経由でエグレスされます。 標準のパブリック ロード バランサーで NAT ゲートウェイを使用する場合は、NAT ゲートウェイのパブリック IP アドレスを宛先にするすべてのイングレス トラフィックは、NAT ゲートウェイ経由でエグレスされます。 

    > [!NOTE] 
    > 負荷分散規則またはアウトバウンド規則に含まれるアウトバウンド構成は、NAT ゲートウェイに取って代わられます。 ロードバランサー バックエンド プールのメンバーも、送信接続に NAT ゲートウェイを使用します。 詳細については、「[NAT ゲートウェイ リソースを使用した仮想ネットワークの設計](../nat-gateway/nat-gateway-resource.md)」を参照してください

* NAT ゲートウェイとパブリック IP アドレスには、キープアライブを聞き取る前に接続を開いた状態に維持する時間について、TCP タイムアウト値を割り当てることができます。  パブリック IP が NAT ゲートウェイに割り当てられている場合は、IP のタイムアウト値が優先されます。  詳細については、「[NAT ゲートウェイ リソースを使用した仮想ネットワークの設計](../nat-gateway/nat-gateway-resource.md#timers)」を参照してください

## <a name="caveats"></a>注意事項

* 現時点では、パブリック IPv6 アドレスとパブリック IPv6 プレフィックスは NAT ゲートウェイでサポートされていません。 IPv6 プレフィックスを使用した仮想ネットワーク サブネットに NAT ゲートウェイをデプロイすることはできません。 詳細については、「[Azure Virtual Network NAT 接続のトラブルシューティング](../nat-gateway/troubleshoot-nat.md)」を参照してください
## <a name="next-steps"></a>次のステップ

この記事では、NAT ゲートウェイを作成し、既存のパブリック IP を使用する方法について説明しました。 NAT ゲートウェイの送信 IP 構成の IP アドレスを置き換えました。 最後に、パブリック IP プレフィックスを使用するように送信 IP 構成を変更しました。

- Azure Virtual Network NAT の詳細については、「[Azure Virtual Network NAT とは](../nat-gateway/nat-overview.md)」を参照してください。
- Azure のパブリック IP アドレスの詳細については、「[パブリック IP アドレス](./public-ip-addresses.md)」を参照してください。