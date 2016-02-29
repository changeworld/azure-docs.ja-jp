<properties 
	pageTitle="Azure Mobile Engagement 認証 API の概要"
	description="これは、Capptain API による認証を使用していたユーザーが新しい Azure Mobile Engagement API を認証に使用する必要がある場合の移行ガイドです。" 
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="wesmc7777"
	manager="erikre"
	editor=""/>

<tags
	ms.service="mobile-engagement"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="mobile-multiple"
	ms.workload="mobile" 
	ms.date="02/17/2016"
	ms.author="wesmc"/>

# Azure Mobile Engagement - 認証への API の使用

## 概要

このドキュメントは、API を使用していた Capptain ユーザーが Azure Mobile Engagement に移行する場合のガイドです。認証メカニズムを新しい API で動作させる方法について詳しく説明します。

有効な Azure サブスクリプションを持っており、いずれかの[開発者チュートリアル](mobile-engagement-windows-store-dotnet-get-started.md)を使用して Mobile Engagement アプリを作成してあることが前提です。

## 認証

Capptain API から新しい Azure Mobile Engagement API に移行するときの主な相違点は、認証に関する部分です。以前の API は、基本認証をサポートしていました。新しい API では、Microsoft Azure Active Directory ベースの OAuth トークンを認証に使用する必要があります。

API の要求を認証するには、すべての要求に Authorization ヘッダーが追加されている必要があります。次のような形式でなければなりません。

	Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGmJlNmV2ZWJPamg2TTNXR1E...


>[AZURE.NOTE] Azure Active Directory のトークンは 1 時間で有効期限が切れます。

トークンを入手するにはいくつかの方法があります。API は一般にクラウド サービスから呼び出されるので、通常は API キーを使用します。Azure の用語では、API キーはサービス プリンシパル パスワードと呼ばれます。次の手順では、手動で設定する方法について説明します。

> [AZURE.WARNING] Azure Active Directory で表示されるキーは、ポータルで示される Mobile Engagement API キーではありません。Mobile Engagement API キーの概念は使用されなくなり、このドキュメントで説明する AAD 認証に置き換えられました。

#### 1 回限りのセットアップ (手動)

この手順の過程で、次の情報を記録しておいてください。後で必要になります。
	
1. [このガイド](../resource-group-create-service-principal-portal.md)を使用して、Azure Active Directory アプリケーションを作成します。

	- 選択したアプリケーション名。このドキュメントでは `{AD_APP_NAME}` と呼びます。
	- 構成メニューに表示されるクライアント ID。このドキュメントでは `{CLIENT_ID}` と呼びます。
	- 保存後に 1 回だけ表示されるキー。このドキュメントでは `{CLIENT_SECRET}` と呼びます。
	- 下部のバーの **[エンドポイントの表示]** をクリックして、**[OAuth 2.0 トークン エンドポイント URL]** をコピーします。このドキュメントでは `https://login.microsoftonline.com/{TENANT_ID}/oauth2/token` と呼びます。 <br/>                                    
2. [Azure CLI](../xplat-cli-install.md) を使用して、サービス プリンシパルに Reader や Owner などのロールを割り当てます。

	Windows 上にいる場合は、`PATH` 環境変数を変更して `C:\Program Files (x86)\Microsoft SDKs\Azure\CLI\bin` を追加し、Azure コマンドを使用できるようにします。

	Azure コマンド ライン インターフェイス (CLI) で次のコマンドを実行してアカウントをセットアップします。

		azure config mode arm 
		azure login

	次のコマンドを使用して、アプリケーションのオブジェクト ID を検索します。

		$ azure ad sp show --search {AD_APP_NAME} 
		info: Executing command ad sp show 
		+ Getting active directory service principals
		data: Object Id: 71785194-b7f5-4701-a9d6-fefb6cd32d18 
		data: Display Name: {AD_APP_NAME} 
		data: Service Principal Names:
		data: {AD_APP_URI}
		data: 8cdaf270-763c-4577-b2a2-ce559b47d353 
		data: 
		info: ad sp show command OK

	出力の `Object Id` を記録します。

	次のコマンドを使用して、`Owner` ロールをサービス プリンシパルに割り当てます。
  
		$ azure role assignment create --objectId {OBJECT_ID} -o Owner 
		info: Executing command role assignment create
		+ Finding role with specified name
		-data: RoleAssignmentId :
		/subscriptions/{SUBSCRIPTION_ID}/providers/Microsoft.Authorization/roleAssignm
		ents/009392b1-2b7c-4de8-8f70-1fccb2e0a331 
		data: RoleDefinitionName : Reader
		data: RoleDefinitionId : acdd72a7-3385-48ef-bd42-f606fba81ae7 
		data: Scope : /subscriptions/{SUBSCRIPTION_ID} 
		data: Display Name : {AD_APP_NAME}
		data: SignInName :
		data: ObjectId : {OBJECT_ID} 
		data: ObjectType : ServicePrincipal

#### 1 回限りのセットアップ (スクリプトを使用)

これは、PowerShell スクリプトを使用する前述の手順を実行するもう 1 つの方法です。

1. Azure PowerShell の最新バージョンを入手します。

	最新バージョンの 1.2.1 は、[こちら](https://github.com/Azure/azure-powershell/releases/tag/v1.2.1-February2016)からダウンロードできます。

2. 管理者モードで Windows PowerShell を開き、[Azure リソース マネージャー コマンドレット](https://msdn.microsoft.com/library/mt125356.aspx)がインストールされていることを確認します。

		Install-Module AzureRM
		Install-AzureRM

3. 次のコマンドを実行します。

		Import-Module AzureRM.profile

4. 次のコマンドを実行してログイン ダイアログを起動します。ログインすると、「アクティブな」サブスクリプション、つまり実行するコマンドの影響を受けるサブスクリプションが表示されます。

		Add-AzureRmAccount

5. 次のコマンドを実行して、すべてのサブスクリプションを一覧表示します。使用するサブスクリプションの GUID をコピーします。

		Get-AzureRmSubscription

6. サブスクリプションの GUID を指定して次のコマンドを実行し、使用するサブスクリプションを構成します。これは、複数のサブスクリプションがある場合に特に便利です。

		Select-AzureRmSubscription –SubscriptionId <subscriptionId>

7. [New-AzureRmServicePrincipalOwner.ps1](https://raw.githubusercontent.com/matt-gibbs/azbits/master/src/New-AzureRmServicePrincipalOwner.ps1) スクリプトのテキストをローカル コンピューターにコピーして実行します。

	>[Azure.Note] 既定のセキュリティ ポリシーにより、PowerShell スクリプトの実行がブロックされる可能性があります。その場合は、次のコマンドを使用してスクリプトの実行を許可する実行ポリシーを一時的に構成します。

	>	Set-ExecutionPolicy RemoteSigned

	このスクリプトでは、ServicePrincipal に割り当てる「名前」を求められます。任意の名前を指定できます。

	スクリプトが完了すると、プログラムを使用して AD で認証を行うために必要な 4 つの値、**TenantId**、**SubscriptionId**、**ApplicationId**、**Secret** が表示されます。

	これらの値を参照できるようにコピーします。アクセス トークンを取得するには、`{TENANT_ID}` として TenantId を、`{CLIENT_ID}` として ApplicationId を、`{CLIENT_SECRET}` として Secret を使用します。


8. Microsoft Azure 管理ポータルで、新しい AD アプリケーションが **[会社が所有するアプリケーションを表示する]** で表示されることを確認します。


#### 有効なトークンを取得する手順

新しいトークンを取得するには、次の API を呼び出します。

	https://login.microsoftonline.com/{TENANT_ID}/oauth2/token 

要求の例を次に示します。

	POST /{TENANT_ID}/oauth2/token HTTP/1.1
	Host: login.microsoftonline.com
	Content-Type: application/x-www-form-urlencoded Content-Length: 195
	grant_type=client_credentials&client_id={CLIENT_ID}&client_secret={CLIENT_SECRET}&reso
	urce=https%3A%2F%2Fmanagement.core.windows.net%2F

次は応答の例です。

	HTTP/1.1 200 OK
	Content-Type: application/json; charset=utf-8
	Content-Length: 1234
	
	{"token_type":"Bearer","expires_in":"3599","expires_on":"1445395811","not_before":"144
	5391911","resource":"https://management.core.windows.net/","access_token":{ACCESS_T
	OKEN}}

この例には、POST パラメーターの URL エンコードが含まれました。`resource` の値は実際には `https://management.core.windows.net/` です。URL エンコード `{CLIENT_SECRET}` にも注意してください。特殊文字が含まれることがあります。

すべての API 呼び出しに、次の Authorization 要求ヘッダーを含めます。

	Authorization: Bearer {ACCESS_TOKEN}

401 ステータス コードが返される場合は、応答本文を確認します。トークンの有効期限が切れている可能性があります。その場合は、新しいトークンを取得します。


##API の使用


有効なトークンが手に入ったので、API 呼び出しを行う準備ができました。

1. 各 API 要求では、前のセクションで取得した有効な期限が切れていないトークンを渡す必要があります。

2. アプリケーションを示す URI にいくつかのパラメーターを組み込む必要があります。要求 URI は次のようになります。

		https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/MobileEngagement/
		providers/Microsoft.MobileEngagement/appcollections/{app-collection}/apps/{app-resource-name}/

	パラメーターを取得するには、アプリケーション名をクリックして [ダッシュボード] をクリックすると、次のようなページに 3 つのパラメーターがすべて表示されます。

	- **1** `{subscription-id}`
	- **2** `{app-collection}`
	- **3** `{app-resource-name}`

	![](./media/mobile-engagement-api-authentication/mobile-engagement-api-uri-params.png)


>[AZURE.NOTE] <br/> 1.[API ルート アドレス] は以前の API のためのものなので無視してください。<br/> 2. アプリケーション名自体とは異なるアプリケーション リソース名を使用する必要があります。

<!---HONumber=AcomDC_0218_2016-->