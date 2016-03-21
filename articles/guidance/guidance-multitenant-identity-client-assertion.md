<properties
   pageTitle="クライアント アサーションを使用して Azure AD からのアクセス トークンを取得する | Microsoft Azure"
   description="クライアント アサーションを使用して Azure AD からのアクセス トークンを取得する方法について説明します。"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="mwasson"/>

# クライアント アサーションを使用して Azure AD からのアクセス トークンを取得する

この記事は[シリーズの一部]です。このシリーズに付属する完成した[サンプル アプリケーション]もあります。

## 背景

OpenID Connect で承認コード フローまたはハイブリッド フローを使用すると、クライアントはアクセス トークンの承認コードを交換します。クライアントは、この手順中にサーバーに対して自身を認証する必要があります。

![クライアント シークレット](media/guidance-multitenant-identity/client-secret.png)

クライアントを認証する方法の 1 つは、クライアント シークレットの使用です。これは [Tailspin Surveys][Surveys] アプリケーションの既定の構成です。

次に、クライアントから IDP に対して、アクセス トークンを要求する要求の例を示します。`client_secret` パラメーターに注目してください。

```
POST https://login.microsoftonline.com/b9bd2162xxx/oauth2/token HTTP/1.1
Content-Type: application/x-www-form-urlencoded

resource=https://tailspin.onmicrosoft.com/surveys.webapi
  &client_id=87df91dc-63de-4765-8701-b59cc8bd9e11
  &client_secret=i3Bf12Dn...
  &grant_type=authorization_code
  &code=PG8wJG6Y...
```

シークレットは単なる文字列なので、値が漏えいしないように注意する必要があります。ベスト プラクティスは、クライアント シークレットをソース制御とは別に保存することです。Azure にデプロイするときに、シークレットを[アプリ設定][configure-web-app]に保存します。

ただし、Azure サブスクリプションにアクセス権を持つ全員がアプリ設定を表示できます。さらに、シークレットをソース制御 (たとえばデプロイ スクリプトなど) に組み入れたり、電子メールで共有したりするなどの誘惑は常にあります。

セキュリティを強化するには、クライアント シークレットではなく、_クライアント アサーション_を使用できます。クライアント アサーションの場合、クライアントは X.509 証明書を使用して、クライアントから送信されたトークン要求を証明します。クライアント証明書は Web サーバーにインストールされています。一般的に、クライアント シークレットが誤って暴露されないようにするよりも、証明書に対するアクセス権を制限する方が簡単です。

クライアント アサーションを使用したトークン要求を次に示します。

```
POST https://login.microsoftonline.com/b9bd2162xxx/oauth2/token HTTP/1.1
Content-Type: application/x-www-form-urlencoded

resource=https://tailspin.onmicrosoft.com/surveys.webapi
  &client_id=87df91dc-63de-4765-8701-b59cc8bd9e11
  &client_assertion_type=urn:ietf:params:oauth:client-assertion-type:jwt-bearer
  &client_assertion=eyJhbGci...
  &grant_type=authorization_code
  &code= PG8wJG6Y...
```

今度は `client_secret` パラメーターが使用されていません。代わりに、`client_assertion` パラメーターには、クライアント証明書を使用して署名された JWT トークンが含まれています。`client_assertion_type` パラメーターは、アサーションの種類を指定します。この例では、JWT トークンです。サーバーは JWT トークンを検証します。JWT トークンが有効でない場合、トークン要求からエラーが返されます。

> [AZURE.NOTE] X.509 証明書は、クライアント アサーションの唯一の形式ではありませんが、Azure AD でサポートされているため、この記事では取り上げています。

## Surveys アプリケーションでクライアント アサーションを使用する

ここでは、クライアント アサーションを使用するように Tailspin Surveys アプリケーションを構成する方法について説明します。この手順で、運用目的ではなく、開発に適した自己署名証明書を生成します。

1. 次のように PowerShell スクリプト [/Scripts/Setup-KeyVault.ps1][Setup-KeyVault] を実行します。

    ```
    .\Setup-KeyVault.ps -Subject [subject]
    ```

    `Subject` パラメーターに任意の名前を入力します ("surveysapp" など)。このスクリプトによって、自己署名証明書が生成され、"Current User/Personal" 証明書ストアに保存されます。

2. スクリプトの出力は JSON フラグメントです。このフラグメントを、次のように Web アプリのアプリケーション マニフェストに追加します。

    1. [Microsoft Azure 管理ポータル][azure-management-portal]にログインし、Azure AD ディレクトリに移動します。

    2. **[アプリケーション]** をクリックします。

    3. Surveys アプリケーションを選択します。

    4.	**[マニフェストの管理]**、**[マニフェストのダウンロード]** の順にクリックします。

    5.	テキスト エディターでマニフェスト JSON ファイルを開きます。スクリプトの出力を `keyCredentials` プロパティに貼り付けます。結果は次のようになります。

        ```    
        "keyCredentials": [
            {
              "type": "AsymmetricX509Cert",
              "usage": "Verify",
              "keyId": "29d4f7db-0539-455e-b708-....",
              "customKeyIdentifier": "ZEPpP/+KJe2fVDBNaPNOTDoJMac=",
              "value": "MIIDAjCCAeqgAwIBAgIQFxeRiU59eL.....
            }
          ],
         ```

    6.	JSON ファイルに変更内容を保存します。

    7.	ポータルに戻ります。**[マニフェストの管理]**、**[マニフェストのアップロード]** の順にクリックし、JSON ファイルをアップロードします。

3. 次のコマンドを実行して、証明書の拇印を取得します。

    ```
    certutil -store -user my [subject]
    ```

    この `[subject]` は、PowerShell スクリプトの Subject に指定した値です。拇印は "Cert Hash(sha1)" の下に表示されます。16 進数の間のスペースは削除します。

4. アプリ シークレットを更新します。ソリューション エクスプローラーで、Tailspin.Surveys.Web プロジェクトを右クリックし、**[ユーザー シークレットの管理]** を選択します。次のように、"AzureAd" の下に "Asymmetric" のエントリを追加します。

    ```
    {
      "AzureAd": {
        "ClientId": "[Surveys application client ID]",
        // "ClientSecret": "[client secret]",  << Delete this entry
        "PostLogoutRedirectUri": "https://localhost:44300/",
        "WebApiResourceId": "[App ID URI of your Survey.WebAPI application]",
        // new:
        "Asymmetric": {
          "CertificateThumbprint": "[certificate thumbprint]",  // Example: "105b2ff3bc842c53582661716db1b7cdc6b43ec9"
          "StoreName": "My",
          "StoreLocation": "CurrentUser",
          "ValidationRequired": "false"
        }
      },
      "Redis": {
        "Configuration": "[Redis connection string]"
      }
    }
    ```

    証明書はルート CA 機関によって署名されていなかったため、`ValidationRequired` を false に設定する必要があります。運用時には、CA 機関によって署名された証明書を使用し、`ValidationRequired` を true に設定します。

    また、クライアント アサーションには必要ないため、`ClientSecret` のエントリを削除します。

5. Startup.cs で、`ICredentialService` を登録するコードを探します。`CertificateCredentialService` を使用する行のコメントを解除し、`ClientCredentialService` を使用する行をコメントアウトします。

    ```csharp
    // Uncomment this:
    services.AddSingleton<ICredentialService, CertificateCredentialService>();
    // Comment out this:
    //services.AddSingleton<ICredentialService, ClientCredentialService>();
    ```

実行時に、Web アプリケーションは証明書ストアから証明書を読み取ります。証明書は Web アプリと同じコンピューターにインストールされている必要があります。

## その他のリソース

- [Azure Web サイト アプリケーションでの証明書の使用][using-certs-in-websites]
- [RFC 7521][RFC7521]。クライアント アサーションを送信する一般的なメカニズムを定義しています。
- [RFC 7523][RFC7523]。クライアント アサーションに JWT トークンを使用する方法を定義しています。


<!-- Links -->
[configure-web-app]: ../app-service-web/web-sites-configure.md
[azure-management-portal]: https://manage.windowsazure.com
[RFC7521]: https://tools.ietf.org/html/rfc7521
[RFC7523]: https://tools.ietf.org/html/rfc7523
[Setup-KeyVault]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/scripts/Setup-KeyVault.ps1
[Surveys]: guidance-multitenant-identity-tailspin.md
[using-certs-in-websites]: https://azure.microsoft.com/blog/using-certificates-in-azure-websites-applications/
[シリーズの一部]: guidance-multitenant-identity.md
[サンプル アプリケーション]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->