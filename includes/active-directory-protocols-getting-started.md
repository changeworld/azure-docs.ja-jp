<properties
	pageTitle="Azure AD .NET プロトコルの概要 |Microsoft Azure"
	description="Azure AD を利用してテナントの Web アプリケーションと Web API へのアクセスを承認するために HTTP メッセージを使用する方法。"
	services="active-directory"
	documentationCenter=".net"
	authors="priyamohanram"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="priyamo"/>

<!--TODO: Introduction -->

## AD テナントへのアプリケーションの登録

まず、Active Directory テナントにアプリケーションを登録する必要があります。これにより、アプリケーションのクライアント ID を取得でき、トークンを受信できるようになります。

- Microsoft Azure の管理ポータルにサインインします。

- 左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

- アプリケーションの登録先となるテナントを選択します。

- **[アプリケーション]** タブをクリックし、下部のドロアーで **[追加]** をクリックします。

- 画面の指示に従い、新しいアプリケーションを作成します。このチュートリアルでは、Web アプリケーションであるかネイティブ アプリケーションであるかは重要ではありませんが、Web アプリケーションまたはネイティブ アプリケーションの具体的な例を確認する場合は、[このクイック スタート](../articles/active-directory/active-directory-developers-guide.md)をチェックしてください。

- Web アプリケーションの場合は、**サインオン URL** (ユーザーが `http://localhost:12345` などにサインインできる、アプリのベース URL) を入力します。**[アプリケーション ID/URI]** は、アプリケーションの一意識別子です。形式は、`https://<tenant-domain>/<app-name>` (たとえば、`https://contoso.onmicrosoft.com/my-first-aad-app`) です。

- ネイティブ アプリケーションの場合は、**リダイレクト URI** (Azure AD がトークン応答を返すために使用する) を入力します。アプリケーション固有の値 (たとえば、`http://MyFirstAADApp`) を入力します。

- 登録が完了すると、AAD により、アプリケーションに一意のクライアント ID が割り当てられます。この値は次のセクションで必要になるので、アプリケーションの **[構成]** タブでコピーします。

<!---HONumber=AcomDC_0601_2016-->