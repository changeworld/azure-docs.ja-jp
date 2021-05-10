---
title: クライアント認証用の信頼されたクライアント CA 証明書チェーンをエクスポートする
titleSuffix: Azure Application Gateway
description: Azure Application Gateway でクライアント認証を行うために、信頼されたクライアント CA 証明書チェーンをエクスポートする方法について説明します。
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: how-to
ms.date: 03/31/2021
ms.author: caya
ms.openlocfilehash: 2329dc7426b223ef2c81dd0e2e607bccf73192e6
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2021
ms.locfileid: "106230984"
---
# <a name="export-a-trusted-client-ca-certificate-chain-to-use-with-client-authentication"></a>クライアント認証で使用する信頼されたクライアント CA 証明書チェーンをエクスポートする
クライアントまたはクライアント認証との相互認証を構成するには、Application Gateway は、信頼されたクライアント CA 証明書チェーンをゲートウェイにアップロードする必要があります。 複数の証明書チェーンがある場合は、チェーンを個別に作成し、それらを別のファイルとして Application Gateway にアップロードする必要があります。 この記事では、ゲートウェイのクライアント認証構成で使用できる信頼されたクライアント CA 証明書チェーンをエクスポートする方法について説明します。  

## <a name="prerequisites"></a>前提条件

信頼されたクライアント CA 証明書チェーンを生成するには、既存のクライアント証明書が必要です。 

## <a name="export-trusted-client-ca-certificate"></a>信頼されたクライアント CA 証明書をエクスポートする

Application Gateway でクライアント認証を許可するには、信頼されたクライアント CA 証明書が必要です。 この例では、クライアント証明書に TLS/SSL 証明書を使用し、その公開キーをエクスポートし、公開キーから信頼された CA 証明書をエクスポートして、信頼されたクライアント CA 証明書を取得します。 次に、すべてのクライアント CA 証明書を 1 つの信頼されたクライアント CA 証明書チェーンに連結します。 

次の手順で、証明書の .pem または .cer ファイルをエクスポートします。

### <a name="export-public-certificate"></a>公開証明書をエクスポートする 

1. 証明書から .cer ファイルを取得するには、 **[ユーザー証明書の管理]** を開きます。 証明書を探して右クリックします (通常は Current User\Personal\Certificates にあります)。 **[すべてのタスク]** 、 **[エクスポート]** の順にクリックします。 **証明書のエクスポート ウィザード** が開きます。 Current User\Personal\Certificates に証明書が見つからない場合は、誤って "Certificates - Current User" ではなく "Certificates - Local Computer" を開いている可能性があります。 PowerShell を使用して現在のユーザー スコープで証明書マネージャーを開きたい場合は、コンソール ウィンドウで「*certmgr*」と入力します。

    > [!div class="mx-imgBorder"]
    > ![証明書が選択されている証明書管理者と、[すべてのタスク]、[エクスポート] が選択されているコンテキスト メニューを示すスクリーンショット。](./media/certificates-for-backend-authentication/export.png)

2. ウィザードで **[次へ]** をクリックします。
    > [!div class="mx-imgBorder"]
    > ![証明書のエクスポート](./media/certificates-for-backend-authentication/exportwizard.png)

3. **[いいえ、秘密キーをエクスポートしません]** を選択して、 **[次へ]** をクリックします。
    > [!div class="mx-imgBorder"]
    > ![秘密キーをエクスポートしません](./media/certificates-for-backend-authentication/notprivatekey.png)

4. **[エクスポート ファイルの形式]** ページで **[Base-64 encoded X.509 (.CER)]** を選択し、 **[次へ]** をクリックします。
    > [!div class="mx-imgBorder"]
    > ![Base-64 エンコード](./media/certificates-for-backend-authentication/base64.png)

5. **[エクスポートするファイル]** で、 **[参照]** をクリックして証明書をエクスポートする場所を選択します。 **[ファイル名]** に証明書ファイルの名前を指定します。 次に、 **[次へ]** をクリックします。

    > [!div class="mx-imgBorder"]
   > ![エクスポートするファイルを指定する証明書のエクスポート ウィザードが表示されているスクリーンショット。](./media/certificates-for-backend-authentication/browse.png)

6. **[完了]** をクリックして、証明書をエクスポートします。

    > [!div class="mx-imgBorder"]
    > ![ファイル エクスポートの完了後の証明書のエクスポート ウィザードが表示されているスクリーンショット。](./media/certificates-for-backend-authentication/finish.png)

7. 証明書が正常にエクスポートされました。

    > [!div class="mx-imgBorder"]
    > ![成功を示すメッセージが表示された証明書のエクスポート ウィザードを示すスクリーンショット。](./media/certificates-for-backend-authentication/success.png)

   エクスポートされた証明書は次のようになります。

    > [!div class="mx-imgBorder"]
    > ![証明書のシンボルを示すスクリーンショット。](./media/certificates-for-backend-authentication/exported.png)

### <a name="export-ca-certificates-from-the-public-certificate"></a>公開証明書から CA 証明書をエクスポートする

これで、公開証明書がエクスポートされたので、今度は公開証明書から CA 証明書をエクスポートします。 ルート CA しかない場合は、その証明書のみをエクスポートする必要があります。 ただし、中間 CA が 1 つ以上ある場合は、それらの各 CA もエクスポートする必要があります。 

1. 公開キーがエクスポートされたらファイルを開きます。

    > [!div class="mx-imgBorder"]
    > ![認証証明書を開く](./media/certificates-for-backend-authentication/openAuthcert.png)

    > [!div class="mx-imgBorder"]
    > ![証明書について](./media/mutual-authentication-certificate-management/general.png)

1. [証明のパス] タブを選択して証明機関を表示します。

    > [!div class="mx-imgBorder"]
    > ![証明書の詳細](./media/mutual-authentication-certificate-management/cert-details.png) 

1. ルート証明書を選択して、 **[証明書の表示]** をクリックします。

    > [!div class="mx-imgBorder"]
    > ![証明書のパス](./media/mutual-authentication-certificate-management/root-cert.png) 

   ルート証明書の詳細が表示されます。

    > [!div class="mx-imgBorder"]
    > ![証明書の情報](./media/mutual-authentication-certificate-management/root-cert-details.png)

1. **[詳細]** タブを選択し、 **[ファイルにコピー]** をクリックします。

    > [!div class="mx-imgBorder"]
    > ![ルート証明書のコピー](./media/mutual-authentication-certificate-management/root-cert-copy-to-file.png)

1. この時点で、公開証明書からルート CA 証明書の詳細を抽出しました。 **証明書のエクスポート ウィザード** が表示されます。 前のセクション ([公開証明書をエクスポートする](./mutual-authentication-certificate-management.md#export-public-certificate)) の手順 2 ～ 7 に従って、証明書のエクスポート ウィザードを完了します。 

1. ここで、すべての中間 CA が、すべての中間 CA 証明書を Base-64 encoded X.509(.CER) 形式でエクスポートするには、現在のセクション ([公開証明書から CA 証明書をエクスポートする](./mutual-authentication-certificate-management.md#export-ca-certificates-from-the-public-certificate)) の手順 2 ～ 6 を繰り返します。

    > [!div class="mx-imgBorder"]
    > ![中間証明書](./media/mutual-authentication-certificate-management/intermediate-cert.png)

    たとえば、*MSIT CAZ2* 中間 CA のこのセクションの手順 2 ～ 6 を繰り返して、独自の証明書として抽出します。 

### <a name="concatenate-all-your-ca-certificates-into-one-file"></a>すべての CA 証明書を 1 つのファイルに連結する

1. 前に抽出したすべての CA 証明書を使用して、次のコマンドを実行します。 

    Windows:
    ```console
    type intermediateCA.cer rootCA.cer > combined.cer
    ```
    
    Linux:
    ```console
    cat intermediateCA.cer rootCA.cer >> combined.cer
    ```

    結果の結合証明書は、次のようになります。
    
    > [!div class="mx-imgBorder"]
    > ![結合証明書](./media/mutual-authentication-certificate-management/combined-cert.png)

## <a name="next-steps"></a>次の手順

これで、信頼されたクライアント CA 証明書チェーンが作成されました。 これを Application Gateway のクライアント認証構成に追加して、ゲートウェイとの相互認証を可能にすることができます。 「[ポータルで Application Gateway を使用して相互認証を構成する](./mutual-authentication-portal.md)」または「[PowerShell で Application Gateway を使用して相互認証を構成する](./mutual-authentication-powershell.md)」をご覧ください。

