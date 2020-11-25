---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/19/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 018cf621c65e86877a76c9861c999caf67f3b8cf
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2020
ms.locfileid: "94553080"
---
クライアント証明書を生成すると、このクライアント証明書は、生成に使用したコンピューターに自動的にインストールされます。 別のクライアント コンピューターにクライアント証明書をインストールする場合は、生成したクライアント証明書をエクスポートする必要があります。

1. クライアント証明書をエクスポートするには、 **[ユーザー証明書の管理]** を開きます。 生成したクライアント証明書は、既定では "Certificates - Current User\Personal\Certificates" にあります。 エクスポートするクライアント証明書を右クリックして、 **[すべてのタスク]** 、 **[エクスポート]** の順にクリックし、**証明書のエクスポート ウィザード** を開きます。

   ![証明書が選択されている現在のユーザーの [証明書] ウィンドウと、[すべてのタスク] で [エクスポート] が選択されている状態を示すスクリーンショット。](./media/vpn-gateway-certificates-export-client-cert-include/export.png)
2. 証明書のエクスポート ウィザードで **[次へ]** をクリックして続行します。

   ![証明書のエクスポート ウィザードのウェルカム メッセージを示すスクリーンショット。](./media/vpn-gateway-certificates-export-client-cert-include/next.png)
3. **[はい、秘密キーをエクスポートします]** を選び、 **[次へ]** をクリックします。

   ![秘密キーをエクスポートする](./media/vpn-gateway-certificates-export-client-cert-include/privatekeyexport.png)
4. **[エクスポート ファイルの形式]** ページでは、既定値をそのまま使用します。 **[証明のパスにある証明書を可能であればすべて含む]** がオンになっていることを確認します。 この設定はさらに、クライアント認証が成功するために必要なルート証明書情報もエクスポートされます。 これがないと、クライアントは信頼されたルート証明書を持っていないので、クライアント認証は失敗します。 次に、 **[次へ]** をクリックします。

   ![ファイルの形式をエクスポートする](./media/vpn-gateway-certificates-export-client-cert-include/includeallcerts.png)
5. **[セキュリティ]** ページでは、秘密キーを保護する必要があります。 パスワードを使用する場合は、この証明書に設定したパスワードを必ず記録しておくか、覚えておいてください。 次に、 **[次へ]** をクリックします。

   ![パスワードが入力および確定され、[次へ] が強調表示されている証明書のエクスポート ウィザードのセキュリティ ページを示すスクリーンショット。](./media/vpn-gateway-certificates-export-client-cert-include/security.png)
6. **[エクスポートするファイル]** で、 **[参照]** をクリックして証明書をエクスポートする場所を選択します。 **[ファイル名]** に証明書ファイルの名前を指定します。 次に、 **[次へ]** をクリックします。

   ![エクスポートするファイル](./media/vpn-gateway-certificates-export-client-cert-include/filetoexport.png)
7. **[完了]** をクリックして、証明書をエクスポートします。

   ![入力された設定が表示されている証明書のエクスポート ウィザードを示すスクリーンショット。](./media/vpn-gateway-certificates-export-client-cert-include/finish.png)