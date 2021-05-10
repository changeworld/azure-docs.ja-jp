---
title: ポータルを使用して Azure Application Gateway でリスナー固有の SSL ポリシーを構成する
description: ポータルを使用して Application Gateway でリスナー固有の SSL ポリシーを構成する方法を説明します
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: how-to
ms.date: 03/30/2021
ms.author: caya
ms.openlocfilehash: b0bda1921c63b4e353365c3b391e17f620740396
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231004"
---
# <a name="configure-listener-specific-ssl-policies-on-application-gateway-through-portal-preview"></a>ポータルを使用して Application Gateway でリスナー固有の SSL ポリシーを構成する (プレビュー)

この記事では、Azure portal を使用して、アプリケーション ゲートウェイにリスナー固有の SSL ポリシーを構成する方法について説明します。 リスナー固有の SSL ポリシーを使用すると、個々のリスナーにそれぞれ異なる SSL ポリシーが使用されるように構成できます。 リスナー固有の SSL ポリシーによって上書きされない限り、すべてのリスナーによって使用される、既定の SSL ポリシーを設定することもできます。 

> [!NOTE]
> リスナー固有のポリシーは、Standard_v2 と WAF_v2 の SKU のみでサポートされています。リスナー固有のポリシーは SSL プロファイルの一部であり、SSL プロファイルは v2 ゲートウェイのみでサポートされているためです。 



Azure サブスクリプションをお持ちでない場合は、始める前に[無料アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。

## <a name="create-a-new-application-gateway"></a>新しい Application Gateway の作成

最初に、通常のポータルでの操作と同じようにして、新しいアプリケーション ゲートウェイを作成します。リスナー固有の SSL ポリシーを構成するために、作成時に必要な追加の手順はありません。 ポータルでアプリケーション ゲートウェイを作成する方法の詳細については、[ポータルのクイックスタート チュートリアル](./quick-create-portal.md)を参照してください。

## <a name="set-up-a-listener-specific-ssl-policy"></a>リスナー固有の SSL ポリシーを設定する

リスナー固有の SSL ポリシーを設定するには、まず、ポータルの **[SSL settings (Preview)]\(SSL 設定 (プレビュー)\)** タブに移動し、新しい SSL プロファイルを作成する必要があります。 SSL プロファイルを作成すると、 **[クライアント認証]** と **[SSL ポリシー]** の 2 つのタブが表示されます。 **[SSL ポリシー]** タブでは、リスナー固有の SSL ポリシーを構成します。 **[クライアント認証]** タブでは、相互認証用のクライアント証明書をアップロードします。詳細については、[相互認証の構成](./mutual-authentication-portal.md)に関するページを参照してください。

> [!NOTE]
> 今後、TLS 1.2 が必須となるため、TLS 1.2 を使用することをお勧めします。 

1. ポータルで「**アプリケーション ゲートウェイ**」を検索し、 **[アプリケーション ゲートウェイ]** を選択して、既存のアプリケーション ゲートウェイをクリックします。

2. 左側のメニューで **[SSL settings (Preview)]\(SSL 設定 (プレビュー)\)** を選択します。

3. 上部にある **[SSL Profiles]\(SSL プロファイル\)** の横にあるプラス記号をクリックして、新しい SSL プロファイルを作成します。

4. **[SSL Profile Name]\(SSL プロファイル名\)** に名前を入力します。 この例では、SSL プロファイルに *applicationGatewaySSLProfile* という名前を付けます。 

5. **[SSL ポリシー]** タブに移動し、 **[Enable listener-specific SSL Policy]\(リスナー固有の SSL ポリシーを有効にする\)** チェック ボックスをオンにします。 

6. 要件に応じて、リスナー固有の SSL ポリシーを設定します。 定義済みの SSL ポリシーの中から選択し、独自の SSL ポリシーにカスタマイズすることもできます。 SSL ポリシーの詳細については、[SSL ポリシーの概要](./application-gateway-ssl-policy-overview.md)に関するページを参照してください。 TLS 1.2 を使用することをお勧めします

7. **[追加]** を選択して保存します。

    > [!NOTE]
    > SSL プロファイルをリスナーに関連付けるために、SSL プロファイルでクライアント認証を構成する必要はありません。 クライアント認証だけを構成するか、リスナー固有の SSL ポリシーだけを構成するか、両方を SSL プロファイルで構成することができます。  

    ![SSL プロファイルにリスナー固有の SSL ポリシーを追加する](./media/application-gateway-configure-listener-specific-ssl-policy/listener-specific-ssl-policy-ssl-profile.png)
    
## <a name="associate-the-ssl-profile-with-a-listener"></a>SSL プロファイルをリスナーに関連付ける

これで、リスナー固有の SSL ポリシーがある SSL プロファイルを作成できたので、リスナー固有のポリシーが動作するように、SSL プロファイルをリスナーに関連付ける必要があります。 

1. 既存のアプリケーション ゲートウェイに移動します。 上の手順を完了したばかりの場合、ここでは何もする必要がありません。 

2. 左側のメニューで **[リスナー]** を選択します。 

3. まだ HTTPS リスナーを設定していない場合は、 **[リスナーの追加]** をクリックします。 既に HTTPS リスナーがある場合は、一覧でそれをクリックします。 

4. 要件に合わせて、 **[リスナー名]** 、 **[フロントエンド IP]** 、 **[ポート]** 、 **[プロトコル]** 、およびその他の **[HTTPS 設定]** を入力します。

5. リスナーに関連付ける SSL プロファイルを選択できるように、 **[Enable SSL Profile]\(SSL プロファイルを有効にする\)** チェック ボックスをオンにします。 

6. ドロップダウン リストで、作成した SSL プロファイルを選択します。 この例では、前の手順で作成した SSL プロファイル (*applicationGatewaySSLProfile*) を選択します。 

7. 要件に合わせて、リスナーの残りの部分を構成します。 

8. **[追加]** をクリックして、新しいリスナーと、それに関連付けられている SSL プロファイルを保存します。 

    ![SSL プロファイルを新しいリスナーに関連付ける](./media/mutual-authentication-portal/mutual-authentication-listener-portal.png)        

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure CLI を使用してアプリケーション ゲートウェイで Web トラフィックを管理する](./tutorial-manage-web-traffic-cli.md)