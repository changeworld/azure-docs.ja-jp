---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/27/2020
ms.author: alkohli
ms.openlocfilehash: fa65a7354112a2b220686372459b348d45832dd9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96465618"
---
ご利用のデバイスのローカル Web UI で、次の手順を実行します。 この手順は、VPN 構成ファイル (またはサービス タグ ファイル) のアップロードを含め、約 15 分かかります。 

1. **[構成] > [VPN]** の順に移動します。 **[構成]** をクリックします。

    ![ローカル UI を構成する 1](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-1.png)

2. **[VPN の構成]** ブレードで、次の操作を行います。

    - **[VPN の設定]** を有効にします。
    - **[VPN 共有シークレット]** を指定します。 これは、Azure VPN 接続リソースの作成時に指定した共有キーです。
    - **[VPN ゲートウェイ IP]** アドレスを指定します。 これは、Azure ローカル ネットワーク ゲートウェイの IP アドレスです。
    - **PFS グループ** の場合は、 **[なし]** を選択します。 
    - **DH グループ** の場合は、 **[Group2]** を選択します。
    - **IPsec 整合性の方法** の場合は、 **[SHA256]** を選択します。
    - **IPsec 暗号化変換定数** の場合は、 **[GCMAES256]** を選択します。
    - **IPsec 認証変換定数** の場合は、 **[GCMAES256]** を選択します。
    - **IKE 暗号化の方法** の場合は、 **[AES256]** を選択します。
    - **[適用]** を選択します。

        ![ローカル UI を構成する 2](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-2.png)

    サポートされている暗号化アルゴリズムの詳細については、「[暗号化要件と Azure VPN ゲートウェイについて](../articles/vpn-gateway/vpn-gateway-about-compliance-crypto.md#ipsecike-policy-faq)」をご覧ください。 

3. VPN ルート構成ファイルをアップロードするには、 **[アップロード]** を選択します。 

    ![ローカル UI を構成する 3](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-3.png)

    - 前の手順でローカル システムにダウンロードしたサービス タグ *json* ファイルを参照します。
    - デバイス、仮想ネットワーク、ゲートウェイに関連付けられている Azure リージョンをリージョンに選択します。
    - **[適用]** を選択します。

        ![ローカル UI を構成する 4](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-4.png)
    
    デバイスでのアップロードには、おおよそ 7 分から 8 分かかります。

4. クライアント固有のルートを追加するには、VPN のみを使用してアクセスする IP アドレス範囲を構成します。 

    - **[IP address ranges to be accessed using VPN only]\(VPN のみを使用してアクセスする IP アドレス範囲\)** で、 **[構成]** を選択します。
    - IPv4 の有効な範囲を指定して、 **[追加]** を選択します。 他の範囲を追加するには、この手順を繰り返します。
    - **[適用]** を選択します。

        ![ローカル UI を構成する 5](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-5.png)

