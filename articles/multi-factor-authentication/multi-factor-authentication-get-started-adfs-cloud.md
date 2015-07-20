<properties 
	pageTitle="Azure Multi-Factor Authentication および AD FS を使用したクラウド リソースのセキュリティ保護" 
	description="クラウドで Azure MFA および AD FS を開始する方法について説明する Azure Multi-Factor Authentication のページです。" 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="bryanla"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/02/2015" 
	ms.author="billmath"/>

# Azure Multi-Factor Authentication および AD FS を使用したクラウド リソースのセキュリティ保護

組織が Azure Active Directory とフェデレーションしており、Azure AD によってアクセスされるリソースがある場合、Azure Multi-Factor Authentication または Active Directory フェデレーション サービス (AD FS) を使用してこれらのリソースをセキュリティ保護します。Azure Multi-factor Authentication または Active Directory フェデレーション サービスで Azure Active Directory リソースをセキュリティ保護するには、以下の手順を使用します。

## AD FS を使用して Azure AD リソースをセキュリティ保護するには、次の手順に従います。 



1. ユーザーがアカウントを有効にするには、「[多要素認証をオンにする](multi-factor-authentication-get-started-cloud/#turn-on-multi-factor-authentication-for-users)」で説明されている手順を使用します。
2. 要求規則をセットアップするには、次の手順を使用します。

<center>![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/adfs1.png)</center>

- 	AD FS 管理コンソールを起動します。
- 	[証明書利用者信頼] に移動し、[証明書利用者信頼] を右クリックします。[要求ルールの編集...] を選択します。
- 	[ルールの追加...] をクリックします。
- 	ドロップダウンから、[カスタム規則を使ってクレームを送信する] を選択し、[次へ] をクリックします。
- 	要求ルールの名前を入力します。
- 	[カスタム規則:] の下に、以下を追加します。


		=> issue(Type = "http://schemas.microsoft.com/claims/authnmethodsreferences", Value = "http://schemas.microsoft.com/claims/multipleauthn");

	対応する要求:

		<saml:Attribute AttributeName="authnmethodsreferences" AttributeNamespace="http://schemas.microsoft.com/claims">
		<saml:AttributeValue>http://schemas.microsoft.com/claims/multipleauthn</saml:AttributeValue>
		</saml:Attribute>
- [OK] をクリックします。[完了] をクリックします。AD FS 管理コンソールを閉じます。

ユーザーはオンプレミスの方式 (スマート カードなど) を使用してサインインを完了することができます。


 

<!---HONumber=July15_HO2-->