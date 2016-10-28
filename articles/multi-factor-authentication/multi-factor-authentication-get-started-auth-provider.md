<properties
	pageTitle="Microsoft Azure Multi-Factor Auth プロバイダーの概要"
	description="Azure Multi-Factor Auth プロバイダーの作成方法について説明します。"
	services="multi-factor-authentication"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor="curtand"/>

<tags
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/04/2016"
	ms.author="kgremban"/>



# Azure Multi-Factor Auth プロバイダーの概要
Azure Active Directory を持つグローバル管理者と Office 365 ユーザーは、既定で多要素認証を使用できます。ただし、[高度な機能](multi-factor-authentication-whats-next.md)が必要である場合は、通常版の Azure MFA をご購入ください。

> [AZURE.NOTE]  Azure Multi-Factor Auth プロバイダーは、通常版の Azure MFA に備わっている機能を活用するために使用されます。その対象となるのは、**Azure MFA、Azure AD Premium、EMS のいずれのライセンスも持たないユーザー**です。Azure MFA、Azure AD Premium、EMS には、通常版の Azure MFA が既定で含まれています。ライセンスを所有している場合、Azure Multi-Factor Auth プロバイダーは必要ありません。

SDK をダウンロードする場合は、Azure Multi-Factor Auth プロバイダーが必要です。

> [AZURE.IMPORTANT]  SDK をダウンロードする場合、Azure MFA、AAD Premium、または EMS のライセンスを所有していても、Azure Multi-Factor Auth プロバイダーを作成する必要があります。この目的で Azure Multi-Factor Auth プロバイダーを作成する予定で、かつ各種ライセンスを既に所有している場合、プロバイダーを**有効化されたユーザーごと**のモデルで作成し、それを Azure MFA、Azure AD Premium、EMS ライセンスを含むディレクトリにリンクする必要があります。これで、SDK を利用する一意のユーザーの数が所有ライセンス数を上回らない限り、課金されないようになります。

Azure Multi-Factor Auth プロバイダーを作成するには、次の手順に従います。

## Multi-Factor Auth プロバイダーを作成するには
--------------------------------------------------------------------------------

1. **Azure クラシック ポータル**に管理者としてログオンします。
2. 左側で、**[Active Directory]** を選択します。
3. [Active Directory] ページの上部で **[Multi-Factor Authentication プロバイダー]** をクリックします。![Creating an MFA Provider](./media/multi-factor-authentication-get-started-auth-provider/authprovider1.png)
4. 下部にある **[新規]** をクリックします。![Creating an MFA Provider](./media/multi-factor-authentication-get-started-auth-provider/authprovider2.png)
5. **[App Services]** の下の **[Multi-Factor Authentication プロバイダー]** を選択します。![Creating an MFA Provider](./media/multi-factor-authentication-get-started-auth-provider/authprovider3.png)
6. **[簡易作成]** を選択します。![Creating an MFA Provider](./media/multi-factor-authentication-get-started-auth-provider/authprovider4.png)
5. 次の各フィールドに入力し、**[作成]** をクリックします。
	1. **名前** - Multi-Factor Auth プロバイダーの名前。
	2. **使用モデル** - Multi-Factor Authentication プロバイダーの使用モデル。
		- 認証ごと – 認証ごとに課金される購入モデル。通常、コンシューマー向けのアプリケーションで Azure Multi-factor Authentication を使用するシナリオで使用されます。
		- 有効ユーザーごと – 有効ユーザーごとに課金される購入モデル。通常、Office 365 などのアプリケーションにアクセスに従業員向けに使用されます。
	2. **ディレクトリ** - Multi-Factor Authentication プロバイダーに関連付けられている Azure Active Directory テナント。次の点に注意してください。
		- Multi-Factor Auth プロバイダーの作成に、Azure AD ディレクトリは必要はありません。Azure Multi-Factor Authentication Server または SDK を使用するだけであれば、ブランクにしておいてください。
		- 高度な機能を利用するためには、Azure AD ディレクトリに Multi-Factor Auth プロバイダーを関連付ける必要があります。
		- Azure AD Connect、AAD Sync、または DirSync は、オンプレミスの Active Directory 環境を Azure AD ディレクトリと同期している場合にのみ必要です。非同期の Azure AD ディレクトリしか使用していない場合、これは不要です。 ![Creating an MFA Provider](./media/multi-factor-authentication-get-started-auth-provider/authprovider5.png)
5. [作成] をクリックすると、Multi-Factor Authentication プロバイダーが作成され、**"Multi-Factor Authentication プロバイダーが正常に作成されました"** というメッセージが表示されます。**[OK]** をクリックします。![Creating an MFA Provider](./media/multi-factor-authentication-get-started-auth-provider/authprovider6.png)

<!---HONumber=AcomDC_0921_2016-->