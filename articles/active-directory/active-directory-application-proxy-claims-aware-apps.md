<properties
	pageTitle="アプリケーション プロキシで要求に対応するアプリケーションを利用する"
	description="Azure AD アプリケーション プロキシをセットアップする方法について説明します。"
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/09/2015"
	ms.author="kgremban"/>



# アプリケーション プロキシで要求に対応するアプリケーションを利用する


要求に対応するアプリケーションは、STS へのリダイレクトを行います。STS は、ユーザーをアプリケーションにリダイレクトする前に、ユーザーにトークンの代わりの資格情報を要求します。これらのリダイレクトが動作するようにアプリケーション プロキシを有効にするには、以下の手順を行って、要求に対応するアプリケーションをアプリケーション プロキシが操作できるようにする必要があります。

> [AZURE.IMPORTANT]アプリケーション プロキシは、Azure Active Directory の Premium または Basic エディションにアップグレードしている場合にのみ利用できる機能です。詳細については、「[Azure Active Directory のエディション](active-directory-editions.md)」をご覧ください。


## 前提条件

この手順を実行する前に、要求に対応するアプリのリダイレクト先の STS が、オンプレミス ネットワークの外部でも使用できるようになっていることを確認してください。

### Azure ポータルの構成

1. 「[アプリケーション プロキシを使用したアプリケーションの発行](active-directory-application-proxy-publish.md)」で説明されている手順に従って、アプリケーションを発行します。
2. アプリケーションの一覧で、要求に対応するアプリケーションを選択し、**[構成]** をクリックします。
3. **[事前認証方法]** で **[パススルー]** を選択した場合は、**[外部 URL]** スキームとして **[HTTPS]** を選択してください。
4. **[事前認証方法]** として Azure Active Directory を選択した場合は、**[内部認証方法]** として **[なし]** を選択してください。

### ADFS 構成

1. **[ADFS 管理]** を開きます。
2. **[証明書利用者信頼]** に移動して、アプリケーション プロキシで発行しているアプリを右クリックし、**[プロパティ]** を選択します。

![[証明書利用者信頼] でアプリ名を右クリックしているスクリーンショット](./media/active-directory-application-proxy-claims-aware-apps/appproxyrelyingpartytrust.png)

3. **[エンドポイント]** タブの **[エンドポイントの種類]** で、**[WS-Federation]** を選択します。
4. **[信頼された URL]** で、アプリケーション プロキシの **[外部 URL]** で入力した URL を入力し、**[OK]** をクリックします。

![エンドポイントを追加し、信頼された URL 値を設定しているスクリーン ショット](./media/active-directory-application-proxy-claims-aware-apps/appproxyendpointtrustedurl.png)

<!---HONumber=AcomDC_1125_2015-->