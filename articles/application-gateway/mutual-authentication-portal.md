---
title: ポータルを使用して Azure Application Gateway での相互認証を構成する
description: ポータルを使用して、相互認証を行うようにアプリケーション ゲートウェイを構成する方法について説明します
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: how-to
ms.date: 04/02/2021
ms.author: caya
ms.openlocfilehash: b2118a257f029a63445b09dfa618d6e3ceacddda
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2021
ms.locfileid: "106230941"
---
# <a name="configure-mutual-authentication-with-application-gateway-through-portal-preview"></a>ポータルを使用して Application Gateway での相互認証を構成する (プレビュー)

この記事では、Azure portal を使用して、アプリケーション ゲートウェイで相互認証を構成する方法について説明します。 相互認証とは、Application Gateway にアップロードされるクライアント証明書を使用して、要求を送信するクライアントを Application Gateway が認証することを意味します。 

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="before-you-begin"></a>開始する前に

アプリケーション ゲートウェイでの相互認証を構成するには、ゲートウェイにアップロードするクライアント証明書が必要です。 クライアント証明書は、クライアントが Application Gateway に提示する証明書の検証に使用されます。 テスト目的で、自己署名証明書を使用してもかまいません。 ただし、これらは管理が難しく、完全にセキュリティで保護されていないため、運用環境のワークロードには推奨されません。 

アップロードできるクライアント証明書の種類などの詳細については、[Application Gateway での相互認証の概要](./mutual-authentication-overview.md#certificates-supported-for-mutual-authentication)に関するセクションを参照してください。

## <a name="create-a-new-application-gateway"></a>新しい Application Gateway の作成

最初に、通常のポータルでの操作と同じようにして、新しいアプリケーション ゲートウェイを作成します。相互認証を有効にするために、作成時に必要な追加の手順はありません。 ポータルでアプリケーション ゲートウェイを作成する方法の詳細については、[ポータルのクイックスタート チュートリアル](./quick-create-portal.md)を参照してください。

## <a name="configure-mutual-authentication"></a>相互認証を構成する 

既存のアプリケーション ゲートウェイで相互認証を構成するには、まず、ポータルの **[SSL settings (Preview)]\(SSL 設定 (プレビュー)\)** タブにアクセスし、新しい SSL プロファイルを作成する必要があります。 SSL プロファイルを作成すると、 **[クライアント認証]** と **[SSL ポリシー]** の 2 つのタブが表示されます。 **[クライアント認証]** タブでは、クライアント証明書をアップロードします。 **[SSL ポリシー]** タブでは、リスナー固有の SSL ポリシーを構成します。詳細については、[リスナー固有の SSL ポリシーの構成](./application-gateway-configure-listener-specific-ssl-policy.md)に関するページを参照してください。

> [!IMPORTANT]
> クライアント CA 証明書チェーン全体を 1 つのファイルでアップロードし、ファイルにはチェーンが 1 つだけ含まれるようにしてください。

1. ポータルで「**アプリケーション ゲートウェイ**」を検索し、 **[アプリケーション ゲートウェイ]** を選択して、既存のアプリケーション ゲートウェイをクリックします。

2. 左側のメニューで **[SSL settings (Preview)]\(SSL 設定 (プレビュー)\)** を選択します。

3. 上部にある **[SSL Profiles]\(SSL プロファイル\)** の横にあるプラス記号をクリックして、新しい SSL プロファイルを作成します。

4. **[SSL Profile Name]\(SSL プロファイル名\)** に名前を入力します。 この例では、SSL プロファイルに *applicationGatewaySSLProfile* という名前を付けます。 

5. **[クライアント認証]** タブで、 **[新しい証明書のアップロード]** ボタンを使用して、クライアントと Application Gateway の間の相互認証に使用する PEM 証明書をアップロードします。 

    ここでアップロードする信頼されたクライアント CA 証明書チェーンを抽出する方法の詳細については、[信頼されたクライアント CA 証明書チェーンを抽出する方法](./mutual-authentication-certificate-management.md)に関するページを参照してください。

   > [!NOTE]
   > これが最初の SSL プロファイルではなく、アプリケーション ゲートウェイに他のクライアント証明書をアップロードしたことがある場合は、ドロップダウン メニューを使用して、ゲートウェイ上の既存の証明書を再利用できます。 

6. クライアント証明書の直接の発行者の識別名が Application Gateway によって確認されるようにする場合にのみ、 **[Verify client certificate issuer's DN]\(クライアント証明書の発行者の DN を確認する\)** チェック ボックスをオンにします。 

7. リスナー固有のポリシーを追加することを検討してください。 [リスナー固有の SSL ポリシーの設定](./application-gateway-configure-listener-specific-ssl-policy.md)に関するページの手順を参照してください。

8. **[追加]** を選択して保存します。
    > [!div class="mx-imgBorder"]
    > ![SSL プロファイルにクライアント認証を追加する](./media/mutual-authentication-portal/mutual-authentication-portal.png)

## <a name="associate-the-ssl-profile-with-a-listener"></a>SSL プロファイルをリスナーに関連付ける

これで、相互認証が構成された SSL プロファイルが作成できました。相互認証の設定を完了するには、SSL プロファイルをリスナーに関連付ける必要があります。 

1. 既存のアプリケーション ゲートウェイに移動します。 上の手順を完了したばかりの場合、ここでは何もする必要がありません。 

2. 左側のメニューで **[リスナー]** を選択します。 

3. まだ HTTPS リスナーを設定していない場合は、 **[リスナーの追加]** をクリックします。 既に HTTPS リスナーがある場合は、一覧でそれをクリックします。 

4. 要件に合わせて、 **[リスナー名]** 、 **[フロントエンド IP]** 、 **[ポート]** 、 **[プロトコル]** 、およびその他の **[HTTPS 設定]** を入力します。

5. リスナーに関連付ける SSL プロファイルを選択できるように、 **[Enable SSL Profile]\(SSL プロファイルを有効にする\)** チェック ボックスをオンにします。 

6. ドロップダウン リストで、先ほど作成した SSL プロファイルを選択します。 この例では、前の手順で作成した SSL プロファイル (*applicationGatewaySSLProfile*) を選択します。 

7. 要件に合わせて、リスナーの残りの部分を構成します。 

8. **[追加]** をクリックして、新しいリスナーと、それに関連付けられている SSL プロファイルを保存します。 

    > [!div class="mx-imgBorder"]
    > ![SSL プロファイルを新しいリスナーに関連付ける](./media/mutual-authentication-portal/mutual-authentication-listener-portal.png)

## <a name="renew-expired-client-ca-certificates"></a>期限切れのクライアント CA 証明書を更新する

クライアント CA 証明書の有効期限が切れている場合は、次の手順に従って、ゲートウェイの証明書を更新できます。 

1. 自分のアプリケーション ゲートウェイに移動し、左側のメニューの **[SSL settings (Preview)]\(SSL 設定 (プレビュー)\)** タブに移動します。 
 
1. 有効期限が切れたクライアント証明書がある既存の SSL プロファイルを選択します。 
 
1. **[クライアント認証]** タブで **[新しい証明書のアップロード]** を選択し、新しいクライアント証明書をアップロードします。 
 
1. 有効期限が切れた証明書の横にあるごみ箱アイコンを選択します。 これにより、SSL プロファイルからその証明書との関連付けが削除されます。 

1. 同じ有効期限切れのクライアント証明書を使用していた他の SSL プロファイルに対して、上の手順 2. から 4. を繰り返します。 他の SSL プロファイルのドロップダウン メニューから、手順 3. でアップロードした新しい証明書を選択できるようになります。

## <a name="next-steps"></a>次のステップ

- [Azure CLI を使用してアプリケーション ゲートウェイで Web トラフィックを管理する](./tutorial-manage-web-traffic-cli.md)