---
title: 'P2S OpenVPN プロトコル接続用に VPN クライアントを構成する: Azure AD 認証: macOS: プレビュー'
description: 'プレビュー: VPN Gateway ポイント対サイトと Azure Active Directory 認証を使用して仮想ネットワークに接続するように macOS VPN クライアントを構成する方法について説明します。'
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 05/13/2021
ms.author: cherylmc
ms.openlocfilehash: defcc3c35cab51e9606151f11c37397dc8f4a06e
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2021
ms.locfileid: "110104784"
---
# <a name="azure-active-directory-authentication-configure-a-vpn-client-for-p2s-openvpn-protocol-connections---macos---preview"></a>Azure Active Directory 認証: P2S OpenVPN プロトコル接続用に VPN クライアントを構成する - macOS - プレビュー

この記事は、ポイント対サイト VPN と Azure Active Directory 認証を使用して仮想ネットワークに接続するように、macOS 10.15 以降を実行しているコンピューター用の VPN クライアントを構成する際に役立ちます。 Azure AD を使用して接続と認証を行うには、まず Azure AD テナントを構成する必要があります。 詳細については、「[Azure AD テナントの構成](openvpn-azure-ad-tenant.md)」を参照してください。 ポイント対サイト接続について詳しくは、[ポイント対サイト接続](point-to-site-about.md)に関するページを参照してください。

> [!NOTE]
> * このプレビューは現在、地元の規制要件により、ほとんどの (すべてではない) 国でご利用いただけます。
> * Azure AD 認証は、OpenVPN® プロトコル接続でのみサポートされ、Azure VPN クライアントを必要とします。
>

ポイント対サイト VPN 接続を使用して VNet に接続するすべてのコンピューターについて、次の手順を実行する必要があります。
 
* Azure VPN クライアントをコンピューターにダウンロードします。
* VPN 設定を含むクライアント プロファイルを構成します。 

複数のコンピューターを構成する場合は、1 台のコンピューターでクライアント プロファイルを作成し、これをエクスポートして、他のコンピューターにインポートすることができます。

## <a name="prerequisites"></a>前提条件

Azure AD を使用して接続と認証を行うには、まず Azure AD テナントを構成する必要があります。 詳細については、「[Azure AD テナントの構成](openvpn-azure-ad-tenant.md)」を参照してください。

## <a name="to-download-the-azure-vpn-client"></a><a name="download"></a>Azure VPN Client をダウンロードするには

1. Apple Store から [Azure VPN Client](https://apps.apple.com/us/app/azure-vpn-client/id1553936137) をダウンロードします。
1. クライアントをコンピューターにインストールします。

## <a name="to-import-a-connection-profile"></a><a name="import"></a>接続プロファイルをインポートするには

1. プロファイル ファイルをダウンロードして抽出します。 手順については、[VPN クライアント プロファイル ファイルの操作](about-vpn-profile-download.md)に関するページを参照してください。
1. [Azure VPN クライアント] ページで、 **[インポート]** を選択します。

   :::image type="content" source="media/openvpn-azure-ad-client-mac/import-1.png" alt-text="Azure VPN クライアントで [インポート] を選択しているスクリーンショット。":::
1. インポートするプロファイル ファイルに移動し、それを選択して、 **[開く]** をクリックします。

   :::image type="content" source="media/openvpn-azure-ad-client-mac/import-2.png" alt-text="Azure VPN クライアントのインポートで [開く] をクリックしているスクリーンショット。":::
1. 接続プロファイル情報を表示し、 **[保存]** をクリックします。

   :::image type="content" source="media/openvpn-azure-ad-client-mac/import-3.png" alt-text="Azure VPN クライアントでインポートしたプロファイル設定を保存しているスクリーンショット。":::
1. [VPN 接続] ペインで、保存した接続プロファイルを選択します。 次に、 **[接続]** をクリックします。

   :::image type="content" source="media/openvpn-azure-ad-client-mac/import-4.png" alt-text="Azure VPN クライアントで [接続] をクリックしているスクリーンショット。":::
1. 接続されると、状態が **[接続済み]** に変わります。 セッションから切断するには、 **[切断]** をクリックします。

   :::image type="content" source="media/openvpn-azure-ad-client-mac/import-5.png" alt-text="Azure VPN クライアントで [接続済み] 状態と [切断] ボタンが表示されているスクリーンショット。":::

## <a name="to-create-a-connection-manually"></a><a name="manual"></a>手動で接続を作成するには

1. Azure VPN クライアントを開きます。 **[追加]** を選択して、新しい接続を作成します。

   :::image type="content" source="media/openvpn-azure-ad-client-mac/add-1.png" alt-text="Azure VPN クライアントで [追加] を選択しているスクリーンショット。":::

1. **[Azure VPN クライアント]** ページで、プロファイル設定を構成できます。

   :::image type="content" source="media/openvpn-azure-ad-client-mac/add-2.png" alt-text="Azure VPN クライアントのプロファイル設定のスクリーンショット。":::

   次の設定を構成します。

   * **接続名:** 接続プロファイルを参照するときに使用する名前。
   * **VPN サーバー:** この名前は、サーバーを参照するために使用する名前です。 ここで選択する名前は、サーバーの正式な名前である必要はありません。
   * **サーバー検証**
     * **証明書情報:** 証明書の CA。
     * **サーバー シークレット:** サーバー シークレット。
   * **クライアント認証**
     * **認証の種類:** Azure Active Directory
     * **テナント:** テナントの名前。
     * **発行者:** 発行者の名前。
1. フィールドに入力した後、 **[保存]** をクリックします。
1. [VPN 接続] ペインで、構成した接続プロファイルを選択します。 次に、 **[接続]** をクリックします。

   :::image type="content" source="media/openvpn-azure-ad-client-mac/add-3.png" alt-text="Azure VPN クライアントで接続しているスクリーンショット。":::
1. 資格情報を使用して、サインインして接続します。

   :::image type="content" source="media/openvpn-azure-ad-client-mac/add-4.png" alt-text="Azure VPN クライアントでサインインして接続しているスクリーンショット。":::
1. 接続されると、 **[接続済み]** 状態が表示されます。 切断する場合は、 **[切断]** をクリックして接続を切断します。

   :::image type="content" source="media/openvpn-azure-ad-client-mac/add-5.png" alt-text="Azure VPN クライアントの [接続済み] と [切断] ボタンのスクリーンショット。":::

## <a name="to-remove-a-connection-profile"></a><a name="remove"></a>接続プロファイルを削除するには

お使いのコンピューターから VPN 接続プロファイルを削除することができます。 

1. Azure VPN クライアントに移動します。
1. 削除する VPN 接続を選択し、ドロップダウンをクリックして、 **[削除]** を選択します。

   :::image type="content" source="media/openvpn-azure-ad-client-mac/remove-1.png" alt-text="削除のスクリーンショット。":::
1. **[VPN 接続を削除しますか?]** ボックスで、 **[削除]** をクリックします。
   :::image type="content" source="media/openvpn-azure-ad-client-mac/remove-2.png" alt-text="削除中のスクリーンショット。":::

## <a name="next-steps"></a>次のステップ

詳細については、[Azure AD 認証を使用する P2S Open VPN 接続用の Azure Active Directory テナントの作成](openvpn-azure-ad-tenant.md)に関するページを参照してください。