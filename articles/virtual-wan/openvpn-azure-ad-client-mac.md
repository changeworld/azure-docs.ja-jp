---
title: 'P2S OpenVPN プロトコル接続用に VPN クライアントを構成する: Azure AD 認証: macOS: プレビュー'
description: 'プレビュー: ポイント対サイト VPN と Azure Active Directory 認証を使用して仮想ネットワークに接続するように macOS VPN クライアントを構成する方法について説明します。'
titleSuffix: Azure Virtual WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 05/14/2021
ms.author: cherylmc
ms.openlocfilehash: 0128590162f55dc5c6484d1f4f6bd9b1e957112e
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2021
ms.locfileid: "122822096"
---
# <a name="azure-active-directory-authentication-configure-vpn-clients-for-p2s-openvpn-protocol-connections---macos"></a>Azure Active Directory 認証: P2S OpenVPN プロトコル接続用に VPN クライアントを構成する - macOS

この記事では、macOS 10.15 以降を実行しているコンピューターで、ポイント対サイト VPN と Azure Active Directory 認証を使用して仮想ネットワークに接続するように VPN クライアントを構成する方法をわかりやすく説明しています。 Azure AD を使用して接続と認証を行うには、まず Azure AD テナントを構成する必要があります。 詳細については、「[Azure AD テナントの構成](openvpn-azure-ad-tenant.md)」を参照してください。

> [!NOTE]
> * Azure VPN クライアントは、ローカルの規制により、一部のリージョンでは利用できない場合があります。
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

   :::image type="content" source="media/openvpn-azure-ad-client-mac/import-1.png" alt-text="Azure VPN クライアントで [インポート] を選択する画面のスクリーンショット。":::
1. インポートするプロファイル ファイルに移動してそれを選択し、 **[開く]** をクリックします。

   :::image type="content" source="media/openvpn-azure-ad-client-mac/import-2.png" alt-text="Azure VPN クライアントのインポートで [開く] をクリックする画面のスクリーンショット。":::
1. 接続プロファイル情報を表示し、 **[保存]** をクリックします。

   :::image type="content" source="media/openvpn-azure-ad-client-mac/import-3.png" alt-text="Azure VPN クライアントでインポートしたプロファイル設定を保存する画面のスクリーンショット。":::
1. [VPN 接続] ペインで、保存した接続プロファイルを選択します。 次に、 **[接続]** をクリックします。

   :::image type="content" source="media/openvpn-azure-ad-client-mac/import-4.png" alt-text="Azure VPN クライアントで [接続] をクリックする画面のスクリーンショット。":::
1. 接続後、状態が **[接続済み]** に変わります。 セッションから切断するには、 **[切断]** をクリックします。

   :::image type="content" source="media/openvpn-azure-ad-client-mac/import-5.png" alt-text="Azure VPN クライアントで [接続済み] 状態と [切断] ボタンを表示する画面のスクリーンショット。":::

## <a name="to-create-a-connection-manually"></a><a name="manual"></a>手動で接続を作成するには

1. Azure VPN クライアントを開きます。 **[追加]** を選択して、新しい接続を作成します。

   :::image type="content" source="media/openvpn-azure-ad-client-mac/add-1.png" alt-text="Azure VPN クライアントで [追加] を選択する画面のスクリーンショット。":::

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

   :::image type="content" source="media/openvpn-azure-ad-client-mac/add-3.png" alt-text="Azure VPN クライアントで接続する画面のスクリーンショット。":::
1. 資格情報を使用して、サインインして接続します。

   :::image type="content" source="media/openvpn-azure-ad-client-mac/add-4.png" alt-text="Azure VPN クライアントでサインインして接続する画面のスクリーンショット。":::
1. 接続後、 **[接続済み]** 状態が表示されます。 切断する場合は、 **[切断]** をクリックして接続を切断します。

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
