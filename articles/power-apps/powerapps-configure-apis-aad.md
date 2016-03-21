<properties
	pageTitle="PowerApps で Azure Active Directory ドメイン上のバックエンド システムに接続するために API を構成する | Microsoft Azure"
	description="PowerApps で AAD により保護されたバックエンド システムに接続するために API を構成する"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/25/2015"
   ms.author="guayan"/>

# Azure Active Directory ドメイン上のバックエンド リソースに接続するために API を構成する
Azure Active Directory (AAD) 上にドメインを作成するユーザーが増えているため、バックエンド リソースも、そのような AAD ドメインに追加されつつあります。これらのバックエンド リソースに接続できるように、API を作成し構成することができます。

#### 開始のための前提条件

- [PowerApps Enterprise](powerapps-get-started-azure-portal.md) にサインアップします。
- [アプリ サービス環境](powerapps-get-started-azure-portal.md)を作成します。
- [Azure PowerShell][11] 1.0 プレビュー以上をインストールする。
- [アプリ サービス環境](powerapps-register-api-hosted-in-app-service.md)に API を登録する。

## 手順 1: Active Directory アプリケーションを作成し、アクセス許可を付与する

AAD ドメイン上のバックエンド システムにアクセスするには、AAD アプリケーションを作成し、既存のバックエンド (AAD アプリケーションでもある) への適切なアクセス許可を付与します。手順:

1. [Azure クラシック ポータル][13] で、目的の Azure Active Directory テナントに進み、テナント (またはディレクトリ) を開き、**[アプリケーション]** タブをクリックします。![][14]
2. 下部にある **[追加]** ボタンをクリックします。次の操作を行います。  

	a) **[組織で開発中のアプリケーションを追加]** を選択します。b) アプリケーションの名前を入力し、**[Web アプリケーションや Web API]** を選択します。c) **[サインオン URL]** と **[アプリケーション ID/URI]** で、AAD 内の一意の URL と、組織にとって意味のある URL を入力します。たとえば、http://powerappssignon.contoso.com または http://powerappsappid.contoso.com を入力できます。組織の AAD ドメイン内の URL を使用することをお勧めします。URL は識別子として使用され、存在する上で必要な要件はありません。入力した URL は誰からも参照されません。HTTP または HTTPS を入力することができます。

3. 新しく作成した AAD アプリケーション ページで、**[構成]** タブに移動します。![][15]
4. **[キー]** セクションで、ドロップダウン リストを使用して期間を選択します。キーが表示されるのは、**[保存]** を選択した後です。![][16]
5. **[シングル サインオン]** で、``https://<your App Service Environment name>.azure-apim.net:456/redirect`` を **応答 URL** として追加します。
6. **[他のアプリケーションに対するアクセス許可]** で次の操作を行います。  

	a) **[アプリケーションの追加]** を選択します。ポップアップ ウィンドウで、既存のバックエンドをセキュリティで保護する AAD アプリケーションを選択します。![][17]

	b) ドロップダウン リストを使用して、アクセス許可を追加します。![][18]

7. 下部にある **[保存]** を選択します。
8. **[クライアント ID]** と **[キー]** をコピーし、保存します。Azure ポータルを閉じると、キーは二度と表示されません。 

AAD アプリケーションの詳細については、「[Azure Active Directory とアプリケーションの統合](../active-directory-integrating-applications.md)」を参照してください。

## 手順 2: Azure PowerShell を使用して API を構成する

この時点で、API に必要な構成を初期化することは Azure ポータルでサポートされていません。Azure ポータルで API を構成するには、次の Auzre PowerShell スクリプトを使用します。

> [AZURE.TIP]Azure PowerShell をインストール、構成、および実行する方法については、「[Azure PowerShell のインストールおよび構成の方法][11]」を参照してください。次のスクリプトは、Azure PowerShell 1.0 プレビュー以上で機能します。

```powershell
# get the API resource
$api = Get-AzureRmResource -ResourceType Microsoft.Web/apiManagementAccounts/apis -ResourceName <App Service Environment name>/<API name> -ResourceGroupName <resource group name>

# configure the API resource for AAD authentication
$connectionParameters = @{
    token = @{
        type = "oauthSetting";
        oAuthSettings = @{
            identityProvider = "aad";
            clientId = "<your AAD app client id>";
            clientSecret = "<your AAD app key>";
            customParameters = @{
                TenantId = @{ # this property is optional
                    value = "<your AAD tenant ID>"
                };
                ResourceUri = @{ # this property is required
                    value = "<the app ID URI of the AAD app protecting your backend>"
                }
            }
        }
    }
}
Add-Member -InputObject $api.Properties -MemberType NoteProperty -Name ConnectionParameters -Value $connectionParameters -Force

# update the API resource
New-AzureRmResource -Location $api.Location -ResourceId $api.ResourceId -Properties $api.Properties
```

接続パラメーター名 **token** は重要ですので**注意**してください。Camel ケースである限り、独自の名前を選択できます。この名前は後で、バックエンド コードまたは API ポリシーの中で使用することになります。

次に、[Azure ポータル][19]に移動し、API の **[全般]** 設定ブレードに進みます。追加の構成オプションを確認してください。![][21]


## 実際に使ってみる

PowerApps でアプリを開きます。**[利用できる接続]** に、新しい API が一覧表示されます。**[接続]** を選択すると、AAD サインイン ウィンドウが表示されます。組織の AAD アカウントの詳細を入力して、接続を作成します。

この接続を使用してアプリから API をランタイム呼び出しすると、バックエンドは次の [Base64 エンコード][20]形式の **x-ms-apim-tokens** HTTP ヘッダーでユーザーの AAD トークンを受信します。

```json
{
  "token": {
    "AccessToken": ""
    // ...
  }
}
```

プロパティ名 **token** が、設定を構成するときに使用する接続パラメーター名と一致することに**注目**してください。

必要に応じて、バックエンド コードで **AccessToken** プロパティから、AAD トークンを取得して使用することができます。トークンはアプリ サービス環境によって自動的に更新されます。

## API ポリシーの構成

オプションとして、API ポリシーを使用して、AAD トークンを標準の HTTP **Authorization** ヘッダーに設定することもできます。このように、バックエンド コードで AAD トークンを使用する必要がある場合は、カスタム HTTP ヘッダーを検索するのでなく標準的な方法で AAD トークンを取得し、Base64 デコードを実行することができます。これを行うには、Azure ポータルに移動し、API の **[ポリシー]** ブレードに移動して、次のポリシーを設定します。

```xml
<policies>
	<inbound>
		<base/>
		<choose>
			<when condition="@(context.Variables.ContainsKey(";tokens";) &amp;&amp; ((JObject)context.Variables[";tokens";])[";token";] != null &amp;&amp; !String.IsNullOrEmpty((string)((JObject)context.Variables[";tokens";])[";token";][";AccessToken";]))">
				<set-header exists-action="override" name="Authorization">
					<value>@("Bearer " + (string)((JObject)context.Variables["tokens"])[";token";]["AccessToken"])</value>
				</set-header>
			</when>
		</choose>
	</inbound>
	<backend>
		<base/>
	</backend>
	<outbound>
		<base/>
	</outbound>
</policies>
```

このポリシーを見ると、基本的に、このポリシーでは **x-ms-apim-tokens** ヘッダーの値を、**tokens** 変数を使用するデコードされた JObject として参照することができます。さらに、**set-header** ポリシーを使用して、実際の AAD トークンを取得し、それを **Authorization** ヘッダーに設定することができます。このポリシーは、[Azure API Management](https://azure.microsoft.com/services/api-management/) で使用するポリシーと同じです。詳細については、「[Azure API Management のポリシー](../api-management-howto-policies.md)」を参照してください。

プロパティ名 **token** が、設定を構成するときに使用した接続パラメーター名と一致することに**注目**してください。

## まとめと次のステップ

このトピックでは、Azure Active Directory ドメイン上のバックエンド リソースに接続 (および認証) するために API を構成する方法について見てきました。PowerApps についての関連トピックとリソースがあります。

- [PowerApps のための API を開発する](powerapps-develop-api.md)


<!--References-->
[11]: ../powershell-install-configure.md
[13]: https://manage.windowsazure.com
[14]: ./media/powerapps-configure-apis-aad/aad-applications-tab.png
[15]: ./media/powerapps-configure-apis-aad/aad-application-configure-tab.png
[16]: ./media/powerapps-configure-apis-aad/aad-application-configure-keys.png
[17]: ./media/powerapps-configure-apis-aad/aad-application-add-other-application.png
[18]: ./media/powerapps-configure-apis-aad/aad-application-add-permissions.png
[19]: https://portal.azure.com
[20]: https://tools.ietf.org/html/rfc4648
[21]: ./media/powerapps-configure-apis-aad/api-settings-aad.png

<!---HONumber=AcomDC_1203_2015-->