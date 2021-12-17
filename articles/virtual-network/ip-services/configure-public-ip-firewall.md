---
title: Azure Firewall でパブリック IP アドレスを管理する
titleSuffix: Azure Virtual Network
description: Azure Firewall でパブリック IP アドレスを使用する方法と、構成を変更する方法について説明します。
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 06/28/2021
ms.custom: template-how-to
ms.openlocfilehash: a6f86fcf24316a24c36d0d42dfff4cef0c3ccaf3
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130217432"
---
# <a name="manage-a-public-ip-address-with-azure-firewall"></a>Azure Firewall でパブリック IP アドレスを管理する

Azure Firewall は、Azure Virtual Network リソースを保護するクラウドベースのネットワーク セキュリティ サービスです。 Azure Firewall には、少なくとも 1 つのパブリック静的 IP アドレスが構成されている必要があります。 その IP または一連の IP が、ファイアウォールに対する外部の接続ポイントとして使用されます。 Azure Firewall では、Standard SKU のパブリック IP アドレスがサポートされます。 Basic SKU のパブリック IP アドレスとパブリック IP プレフィックスはサポートされません。 

この記事では、サブスクリプションで既存のパブリック IP を使用して Azure ファイアウォールを作成する方法について説明します。 ファイアウォールの IP 構成を変更します。 最後に、IP 構成をファイアウォールに追加します。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料で作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- サブスクリプション内の 3 つの Standard SKU パブリック IP アドレス。 IP アドレスはどのリソースにも関連付けることはできません。 Standard SKU のパブリック IP アドレスの作成について詳しくは、[Azure portal を使用してパブリック IP アドレスを作成する](./create-public-ip-portal.md)方法に関するページを参照してください。
    - この記事の例では、新しいパブリック IP アドレスに **myStandardPublicIP-1**、**myStandardPublicIP-2**、**myStandardPublicIP-3** という名前を付けます。

## <a name="create-azure-firewall-existing-public-ip"></a>Azure Firewall の既存のパブリック IP を作成する

このセクションでは、Azure ファイアウォールを作成します。 前提条件で作成した IP アドレスをファイアウォールのパブリック IP として選択します。

1. [Azure portal](https://portal.azure.com) にサインインします。

2. ポータルの上部にある検索ボックスに、「**Firewall**」と入力します。

3. 検索結果から **[ファイアウォール]** を選択します。

4. **[+ 作成]** を選択します。

5. **[Create firewall]\(ファイアウォールの作成\)** で、次の情報を入力するか選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | **プロジェクトの詳細** |   |
    | サブスクリプション | サブスクリプションを選択します。 |
    | Resource group | **[新規作成]** を選択します。 </br> 「**myResourceGroupFW**」と入力します。 </br> **[OK]** を選択します。 |
    | **インスタンスの詳細** |   |
    | 名前 | 「**myFirewall**」と入力します。 |
    | リージョン | **[米国西部 2]** を選択します。 |
    | 可用性ゾーン | 既定値の **[なし]** のままにします。 |
    | ファイアウォール層  | 規定値である **[標準]** のままにします。 |
    | ファイアウォール管理 | **[ファイアウォール ポリシーを使用してこのファイアウォールを管理する]** は、既定値のままにします。|
    | ファイアウォール ポリシー | **[新規追加]** を選択します。 </br> **[ポリシー名]** に「**myFirewallPolicy**」と入力します。 </br> **[リージョン]** では **[米国西部 2]** を選択します。 </br> **[はい]** を選択します。 |
    | 仮想ネットワーク名 | 「**myVNet**」と入力します。 |
    | アドレス空間 | 「**10.0.0.0/16**」と入力します。 |
    | サブネットのアドレス空間 | 「**10.0.0.0/26**」と入力します。 |
    | パブリック IP アドレス | **myStandardPublicIP-1** または実際のパブリック IP を選択します。 |
    | 強制トンネリング | 既定値の **[無効]** のままにします。 |
    
 
6. **[確認と作成]** タブを選択するか、青い **[確認と作成]** ボタンを選択します。

7. **［作成］** を選択します

> [!NOTE]
> これは Azure Firewall の単純なデプロイです。 高度な構成と設定については、「[チュートリアル: Azure portal を使用して Azure Firewall とポリシーをデプロイして構成する](../../firewall/tutorial-firewall-deploy-portal-policy.md)」を参照してください。
>
> Azure Firewall の詳細については、「[Azure Firewall とは](../../firewall/overview.md)」を参照してください。

## <a name="change-public-ip-address"></a>パブリック IP アドレスを変更する

このセクションでは、ファイアウォールに関連付けられているパブリック IP アドレスに変更を加えます。 ファイアウォールの構成には、パブリック IP アドレスが少なくとも 1 つ関連付けられている必要があります。 

1. ポータルの上部にある検索ボックスに、「**Firewall**」と入力します。

2. 検索結果から **[ファイアウォール]** を選択します。

3. **[ファイアウォール]** から **[myFirewall]** を選択します。

4. **[myFirewall]** の **[設定]** で **[パブリック IP 構成]** を選択します。

5. **[パブリック IP 構成]** で、 **[myStandardPublicIP-1]** または実際の IP アドレスを選択します。

6. **[パブリック IP 構成の編集]** の **[パブリック IP アドレス]** で **[myStandardPublicIP-2]** を選択します。

7. **[保存]** を選択します。

## <a name="add-public-ip-configuration"></a>パブリック IP 構成を追加する

このセクションでは、Azure ファイアウォールにパブリック IP 構成を追加します。 複数の IP の詳細については、「[複数のパブリック IP アドレス](../../firewall/features.md#multiple-public-ip-addresses)」を参照してください。  

1. ポータルの上部にある検索ボックスに、「**Firewall**」と入力します。

2. 検索結果から **[ファイアウォール]** を選択します。

3. **[ファイアウォール]** から **[myFirewall]** を選択します。

4. **[myFirewall]** の **[設定]** で **[パブリック IP 構成]** を選択します。

5. **[+ パブリック IP 構成の追加]** を選択します。

6. **[名前]** に「**myNewPublicIPconfig**」と入力します。

7. **[パブリック IP アドレス]** で **[myStandardPublicIP-3]** を選択します。

8. **[追加]** を選択します。

## <a name="more-information"></a>詳細情報

* Azure ファイアウォールを Standard SKU のロード バランサーと統合することでバックエンド プール リソースを保護できます。  ファイアウォールをパブリック ロード バランサーと関連付ける場合、イグレス トラフィックをファイアウォールのパブリック IP アドレスに誘導するように構成します。 エグレスは、ファイアウォールのパブリック IP アドレスへのユーザー定義ルートを使用して構成します。  詳細および設定手順については、「[Azure Firewall と Azure Standard Load Balancer を統合する](../../firewall/integrate-lb.md)」を参照してください。 

* Source Network Address Translation (SNAT) の拡張性を拡大するために、Azure ファイアウォールを NAT ゲートウェイに関連付けることもできます。 NAT ゲートウェイがあれば、ファイアウォールに関連付けられた多数のパブリック IP からのトラフィックを構成で許可する必要はありません。 この構成では、すべてのインバウンド トラフィックで NAT ゲートウェイのパブリック IP アドレスが使用されます。 トラフィックは、Azure Firewall のパブリック IP アドレスを通って外へ出ていきます。  詳細については、「[Azure NAT ゲートウェイを使用した SNAT ポートのスケーリング](../../firewall/integrate-with-nat-gateway.md)」を参照してください。

## <a name="caveats"></a>注意事項

* Azure Firewall では、Standard SKU のロード バランサーが使用されます。 ネットワーク フィルター規則では、ファイアウォールのパブリック IP への SNAT に TCP と UDP 以外のプロトコルはサポートされません。 
## <a name="next-steps"></a>次のステップ

この記事では、Azure ファイアウォールを作成し、既存のパブリック IP を使用する方法について説明しました。 既定の IP 構成のパブリック IP を変更しました。 最後に、パブリック IP 構成をファイアウォールに追加しました。

- Azure のパブリック IP アドレスの詳細については、「[パブリック IP アドレス](./public-ip-addresses.md)」を参照してください。
- Azure Firewall の詳細については、「[Azure Firewall とは](../../firewall/overview.md)」を参照してください。