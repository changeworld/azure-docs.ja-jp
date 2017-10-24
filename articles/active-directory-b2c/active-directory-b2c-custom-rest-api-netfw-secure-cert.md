---
title: "Azure Active Directory B2C: クライアント証明書を使用して RESTful サービスをセキュリティで保護する"
description: "サンプル: クライアント証明書を使用して Azure AD B2C でのカスタム REST API 要求交換をセキュリティで保護する方法"
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: joroja
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 09/25/2017
ms.author: yoelh
ms.openlocfilehash: a5cfe0e3f40b929e969e0a118939caa1ccb33cc2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-secure-your-restful-services-using-client-certificates"></a>Azure Active Directory B2C: クライアント証明書を使用して RESTful サービスをセキュリティで保護する
Azure AD B2C と対話するために [RESTful サービスを作成](active-directory-b2c-custom-rest-api-netfw.md)した後、クライアント証明書を使用して、Azure Web アプリ (RESTful API) へのアクセスを制限する方法について説明します。 このメカニズムは TLS 相互認証または**クライアント証明書認証**と呼ばれ、  適切な証明書を持つ Azure AD B2C などのサービスのみが、サービスにアクセスできます。

> [!NOTE]
>
>また、[HTTP 基本認証を使用して](active-directory-b2c-custom-rest-api-netfw-secure-basic.md)、RESTful サービスをセキュリティで保護することもできます。 ただし、HTTP 基本認証は、クライアント証明書より安全性の低いと見なされていることに注意してください。 この記事で説明するように、RESTful サービスは、クライアント証明書認証を使用してセキュリティで保護することをお勧めします。

この記事では、次の方法について詳しく説明します。
1. クライアント証明書認証を使用するように Web アプリを設定する
1. 証明書を Azure AD B2C ポリシー キーにアップロードする
1. クライアント証明書を使用するようにカスタム ポリシーを構成する

## <a name="prerequisites"></a>前提条件
* 前の記事で [REST API 要求交換を統合](active-directory-b2c-custom-rest-api-netfw.md)する手順を完了している
* 有効な証明書 (秘密キーを備えた .pfx ファイル) がある

## <a name="step-1-configure-web-app-for-client-certificate-authentication"></a>手順 1: Web アプリのクライアント証明書認証を構成する
クライアント証明書を要求するように **Azure App Service**を設定するには、Web アプリの `clientCertEnabled` サイト設定が true である必要があります。 現時点では、この設定は、Azure Portal での管理エクスペリエンスを通して実行することはできません。この変更を行うには、REST API を使用する必要があります。

> [!NOTE]
>
>Azure App Service プランが Standard 以上であることを確認してください。 詳細については、「[Azure App Service プランの詳細な概要](https://docs.microsoft.com/en-us/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview)」を参照してください


[Azure Resource Explorer (プレビュー)](https://resources.azure.com) を使用して、clientCertEnabled プロパティを true に切り替えます。 次のスクリーンショットは、Azure Resource Explorer で clientCertEnabled を設定する方法を示しています![Azure Resource Explorer での clientCertEnabled の設定](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-resource-explorer.png)

> [!NOTE]
>
>clientCertEnabled 属性を設定する方法の詳細については、「[Web アプリの TLS 相互認証を構成する方法](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-configure-tls-mutual-auth)」を参照してください


> [!TIP]
>
>また、[ARMClient](https://github.com/projectkudu/ARMClient) ツールを使用して、REST API 呼び出しを簡単に作成することもできます。

## <a name="step-2-upload-your-certificate-to-azure-ad-b2c-policy-keys"></a>手順 2: 証明書を Azure AD B2C ポリシー キーにアップロードする
`clientCertEnabled` を `true` に設定すると、RESTful API との通信にクライアント証明書が求められます。 このため、クライアント証明書を Azure AD B2C テナントにアップロードして、保存する必要があります。   
1.  Azure AD B2C テナントに移動し、**[B2C Settings]\(B2C 設定\)**  >  **[Identity Experience Framework]** の順に選択します。
2.  **[ポリシー キー]** を選択して、テナント内で利用できるキーを表示します。
3.  **[+ 追加]** をクリックします。
4.  **[オプション]** には **[アップロード]** を使用します。
5.  **[名前]** には `B2cRestClientCertificate` を使用します。  
    プレフィックス `B2C_1A_` が自動的に追加されます。
6.  **[ファイルのアップロード]** で、秘密キーを備えた証明書 .pfx ファイルを選択します。 また、証明書の**パスワード**を入力します。

    ![ポリシー キーのアップロード](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-upload.png)

7.  **[作成]**
8.  作成したキー `B2C_1A_B2cRestClientCertificate` を確認するには、**[ポリシー キー]** を選択して、テナント内で利用できるキーを表示します。

## <a name="step-3-change-the-technicalprofile-to-support-client-certificate-authentication-in-your-extension-policy"></a>手順 3: 拡張ポリシーでクライアント証明書認証をサポートするように `TechnicalProfile` を変更する
1.  作業ディレクトリから拡張ポリシー ファイル (TrustFrameworkExtensions.xml) を開きます。
2.  `Id="REST-API-SignUp"` を含む `<TechnicalProfile>` ノードを見つけます
3.  `<Metadata>` 要素を見つけます
4.  `AuthenticationType` を `ClientCertificate` に変更します

    ```xml
    <Item Key="AuthenticationType">ClientCertificate</Item>
    ```

5.  `<Metadata>` 要素の直後に次の XML スニペットを追加します。  

    ```xml
    <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
    </CryptographicKeys>
    ```

XML スニペットを追加すると、`TechnicalProfile` は次のようになります。

![ClientCertificate 認証 XML 要素の設定](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-tech-profile.png)

## <a name="step-4-upload-the-policy-to-your-tenant"></a>手順 4: ポリシーをテナントにアップロードする

1.  [Azure Portal](https://portal.azure.com) で、[Azure AD B2C テナントのコンテキストに切り替え](active-directory-b2c-navigate-to-b2c-context.md)、**[Azure AD B2C]** をクリックします。
2.  **[Identity Experience Framework]** を選択します。
3.  **[All Policies]\(すべてのポリシー\)** をクリックします。
4.  **[Upload Policy]**(ポリシーのアップロード) を選択します。
5.  **[ポリシーが存在する場合は上書きする]** チェック ボックスをオンにします。
6.  TrustFrameworkExtensions.xml を**アップロード**し、検証に失敗しないことを確認します。

## <a name="step-5-test-the-custom-policy-by-using-run-now"></a>手順 5: [今すぐ実行] を使用してカスタム ポリシーをテストする
1.  **[Azure AD B2C の設定]** を開き、**[Identity Experience Framework]** に移動します。

    >[!NOTE]
    >
    >    **[今すぐ実行]** を使用するには、テナントに少なくとも 1 つのアプリケーションが事前登録されている必要があります。 
    >    アプリケーションを登録する方法については、 Azure AD B2C の[概要](active-directory-b2c-get-started.md)に関する記事または[アプリケーションの登録](active-directory-b2c-app-registration.md)に関する記事を参照してください。

2.  アップロードした証明書利用者 (RP) カスタム ポリシーである **B2C_1A_signup_signin** を開きます。 **[今すぐ実行]** を選択します。

3.  **[名]** フィールドに「Test」と入力しようとすると、ページ上部にエラー メッセージが表示されます

    ![ID API のテスト](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-test.png)

4.  **[名]** フィールドに "test" 以外の名前を入力すると、 ユーザーがサインアップされ、アプリケーションに loyaltyNumber が送信されます。 この例ではこの JWT の番号に注意してください。

```
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1507125903,
  "nbf": 1507122303,
  "ver": "1.0",
  "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1507122303,
  "auth_time": 1507122303,
  "loyaltyNumber": "290",
  "given_name": "Emily",
  "emails": ["B2cdemo@outlook.com"]
}
```

   > [!NOTE]
   >上記のエラー メッセージは、クライアント証明書を提示しているときに、Azure AD B2C によって RESTful サービスが正常に呼び出されたことを意味します。 次は証明書を検証します。

## <a name="step-6-adding-certificate-validation"></a>手順 6: 証明書の検証を追加する
Azure AD B2C によって RESTful サービスに送信されたクライアント証明書は、Azure Web Apps プラットフォームによる検証が行われていません (証明書の有無の確認を除く)。 証明書の検証は、Web アプリが実行する必要があります。 認証するために証明書のプロパティを検証するサンプル ASP.NET コードを次に示します。

> [!NOTE]
>クライアント証明書認証に対して Azure サービス アプリを構成する方法の詳細については、「[Web アプリの TLS 相互認証を構成する方法](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-configure-tls-mutual-auth)」を参照してください

### <a name="61-add-application-settings-to-projects-webconfig-file"></a>6.1: アプリケーション設定をプロジェクトの web.config ファイルに追加する
以前に作成した Visual Studio プロジェクトを開き、次のアプリケーション設定を、`appSettings` 要素の下にある web.config ファイルに追加します

```XML
<add key="ClientCertificate:Subject" value="CN=Subject name" />
<add key="ClientCertificate:Issuer" value="CN=Issuer name" />
<add key="ClientCertificate:Thumbprint" value="Certificate thumbprint" />
```

証明書の次の値を置き換えます。
* 件名
* 発行者名
* 証明書のサムプリント

### <a name="62-add-isvalidclientcertificate-function"></a>6.2: IsValidClientCertificate 関数を追加する
Controllers\IdentityController.cs を開き、次の関数を `Identity` コントローラー クラスに追加します。 

```C#
private bool IsValidClientCertificate()
{
    string ClientCertificateSubject = ConfigurationManager.AppSettings["ClientCertificate:Subject"];
    string ClientCertificateIssuer = ConfigurationManager.AppSettings["ClientCertificate:Issuer"];
    string ClientCertificateThumbprint = ConfigurationManager.AppSettings["ClientCertificate:Thumbprint"];

    X509Certificate2 clientCertInRequest = RequestContext.ClientCertificate;
    if (clientCertInRequest == null)
    {
        Trace.WriteLine("Certificate is NULL");
        return false;
    }

    // Basic verification
    if (clientCertInRequest.Verify() == false)
    {
        Trace.TraceError("Basic verification failed");
        return false;
    }

    // 1. Check time validity of certificate
    if (DateTime.Compare(DateTime.Now, clientCertInRequest.NotBefore) < 0 ||
        DateTime.Compare(DateTime.Now, clientCertInRequest.NotAfter) > 0)
    {
        Trace.TraceError($"NotBefore '{clientCertInRequest.NotBefore}' or NotAfter '{clientCertInRequest.NotAfter}' not valid");
        return false;
    }

    // 2. Check subject name of certificate
    bool foundSubject = false;
    string[] certSubjectData = clientCertInRequest.Subject.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
    foreach (string s in certSubjectData)
    {
        if (String.Compare(s.Trim(), ClientCertificateSubject) == 0)
        {
            foundSubject = true;
            break;
        }
    }

    if (!foundSubject)
    {
        Trace.TraceError($"Subject name '{clientCertInRequest.Subject}' is not valid");
        return false;
    }
    
    // 3. Check issuer name of certificate
    bool foundIssuerCN = false;
    string[] certIssuerData = clientCertInRequest.Issuer.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
    foreach (string s in certIssuerData)
    {
        if (String.Compare(s.Trim(), ClientCertificateIssuer) == 0)
        {
            foundIssuerCN = true;
            break;
        }
    }

    if (!foundIssuerCN)
    {
        Trace.TraceError($"Issuer '{clientCertInRequest.Issuer}' is not valid");
        return false;
    }

    // 4. Check thumprint of certificate
    if (String.Compare(clientCertInRequest.Thumbprint.Trim().ToUpper(), ClientCertificateThumbprint) != 0)
    {
        Trace.TraceError($"Thumbprint '{clientCertInRequest.Thumbprint.Trim().ToUpper()}' is not valid");
        return false;
    }

    // 5. If you also want to test if the certificate chains to a Trusted Root Authority you can uncomment the code below
    //
    //X509Chain certChain = new X509Chain();
    //certChain.Build(certificate);
    //bool isValidCertChain = true;
    //foreach (X509ChainElement chElement in certChain.ChainElements)
    //{
    //    if (!chElement.Certificate.Verify())
    //    {
    //        isValidCertChain = false;
    //        break;
    //    }
    //}
    //if (!isValidCertChain) return false;
    return true;
}
```

上記の**例**では、すべての条件が満たされた場合にのみ、証明書が有効として承認されます。
1. 証明書が期限切れではなく、サーバー上の現在の時刻でアクティブである。
2. 証明書の `Subject` 名が、`ClientCertificate:Subject` アプリケーション設定値と共通である
3. 証明書の `Issuer` 名が、`ClientCertificate:Issuer` アプリケーション設定値と共通である
4. 証明書の `thumbprint` が、`ClientCertificate:Thumbprint` アプリケーション設定値と同じである

> [!IMPORTANT]
>
>サービスの機密性によっては、検証をさらに追加しなければならない場合があります。 たとえば、信頼されたルート証明機関に証明書がチェーンされているかどうかをテストしたり、発行者組織名を検証したりします。

### <a name="63-add-isvalidclientcertificate-function"></a>6.3: IsValidClientCertificate 関数を追加する
`SignUp()` 関数の先頭で Controllers\IdentityController.cs を開き、次のコード行を追加します。 

```C#
if (IsValidClientCertificate() == false)
{
    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Your client certificate is not valid", HttpStatusCode.Conflict));
}
```

コード スニペットを追加すると、`Identity` コントローラーは次のようになります。

![証明書の検証コードの追加](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-code.png)

## <a name="step-7-publish-to-azure-and-test"></a>手順 7: Azure に発行してテストする
1. **ソリューション エクスプローラー**で **Contoso.AADB2C.API** プロジェクトを右クリックし、**[発行]** を選択します。
2. 手順 6. を繰り返し、カスタム ポリシーを (もう一度) 証明書の検証でテストします。 ポリシーを実行し、検証を追加した後、すべてが正しく動作するかどうかを確認します。
3. 次に、web.config ファイルで、`ClientCertificate:Subject` の値を**無効**な値に変更します。 ポリシーを再実行すると、エラー メッセージが表示されます。
4. **有効**な値に戻して、ポリシーで REST API を呼び出すことができるかどうかを確認します
5. [Application Insights を使用したログの収集](active-directory-b2c-troubleshoot-custom.md)によるトラブルシューティング。

## <a name="optional-download-the-complete-policy-files-and-code"></a>完全なポリシー ファイルとコードをダウンロードする (省略可能)
* これらのサンプル ファイルを使う代わりに、カスタム ポリシーの概要チュートリアルの完了後に独自のカスタム ポリシーを使ってシナリオを構築することをお勧めします。  [参照用のサンプル ポリシー ファイル](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-cert)
* 完全なコードは、[参照用のVisual Studio ソリューションのサンプル](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API)からダウンロードできます