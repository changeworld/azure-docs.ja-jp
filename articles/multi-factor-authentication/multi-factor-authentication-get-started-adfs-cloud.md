<properties 
	pageTitle="Azure Multi-Factor Authentication および AD FS を使用したクラウド リソースのセキュリティ保護"
	description="クラウドで Azure MFA および AD FS を開始する方法について説明する Azure Multi-Factor Authentication のページです。"
	services="multi-factor-authentication"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/24/2015"
	ms.author="billmath"/>

# Azure Multi-Factor Authentication および AD FS を使用したクラウド リソースのセキュリティ保護

組織が Azure Active Directory とフェデレーションしており、Azure AD によってアクセスされるリソースがある場合、Azure Multi-Factor Authentication または Active Directory フェデレーション サービス (AD FS) を使用してこれらのリソースをセキュリティ保護します。Azure Multi-factor Authentication または Active Directory フェデレーション サービスで Azure Active Directory リソースをセキュリティ保護するには、以下の手順を使用します。

## AD FS を使用して Azure AD リソースをセキュリティ保護するには、次の手順に従います。 



1. ユーザーがアカウントを有効にするには、「[多要素認証をオンにする](active-directory/multi-factor-authentication-get-started-cloud.md#turn-on-multi-factor-authentication-for-users)」で説明されている手順を使用します。
2. 要求規則をセットアップするには、次の手順を使用します。

![クラウド](./media/multi-factor-authentication-get-started-adfs-cloud/adfs1.png)

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

## フェデレーション ユーザー用の信頼できる IP
管理者は、信頼できる IP を使用して、特定の IP アドレスまたはイントラネット内から要求が送信されているフェデレーション ユーザーの多要素認証をバイパスできます。次のセクションで、要求がフェデレーション ユーザーのイントラネット内から送信されている場合に、信頼できる IP とフェデレーション ユーザーを Azure Multi-Factor Authentication にどのように構成し、多要素認証をどのようにバイパスするかについて説明します。これは、要求の種類 [企業ネットワーク内] で [入力方向の要求をパススルーするかフィルター処理する] テンプレートを使用するように AD FS を構成することによって実現されます。ここで示す例では、証明書利用者信頼で Office 365 を使用します。

### AD FS 要求規則を構成する

最初に実行する必要があるのは、AD FS の要求を構成することです。ここでは、2 つの要求規則を作成します。1 つは [企業ネットワーク内] という要求の種類用であり、もう 1 つはユーザーのサインイン状態を維持するためのものです。

1. AD FS 管理を開きます。
2. 左側で、[証明書利用者信頼] を選択します。
3. 中央で、[Microsoft Office 365 ID プラットフォーム] を右クリックし、**[要求規則の編集…]** を選択します。![クラウド](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip1.png)
4. [発行変換規則] で、**[規則の追加]** をクリックします。![クラウド](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip2.png)
5. 変換要求規則追加ウィザードで、ドロップダウンから [入力方向の要求をパススルーするかフィルター処理する] を選択し、[次へ] をクリックします。![クラウド](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip3.png)
6. [要求規則名] の横にあるボックスに、規則の名前を入力します。例: InsideCorpNet。
7. [入力方向の要求の種類] の横にあるドロップダウンから、[企業ネットワーク内] を選択します。![クラウド](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip4.png)
8. [完了] をクリックします。
9. [発行変換規則] で、**[規則の追加]** をクリックします。
10. 変換要求規則の追加ウィザードで、ドロップダウンから [カスタムの規則を使用して要求を送信する] を選択し、[次へ] をクリックします。
11. [要求規則名] の下のボックスに 「Keep Users Signed In」 (ユーザーをサインインしたままにする) と入力します。
12. [カスタムの規則] ボックスに次のように入力します。
	    
		c:[Type == "http://schemas.microsoft.com/2014/03/psso"]
			=> issue(claim = c);
![クラウド](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip5.png)
13. **[完了]** をクリックします。
14. **[Apply]** をクリックします。
15. **[OK]** をクリックします。
16. AD FS 管理を閉じます。



### Azure Multi-Factor Authentication の信頼できる IP とフェデレーション ユーザーを構成する
これで要求が準備できたので、信頼できる IP を構成できます。

1. Azure 管理ポータルにサインインします。
2. 左側の [Active Directory] をクリックします。
3. [ディレクトリ] で、信頼できる IP を設定するディレクトリをクリックします。
4. 選択したディレクトリで、[構成] をクリックします。
5. [多要素認証] セクションで、[サービス設定の管理を] クリックします。
6. [サービス設定] ページの [信頼できる IP] で、**[イントラネットから送信されるフェデレーション ユーザーからの要求]** を選択します。![クラウド](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip6.png)
7. [保存] をクリックします。
8. 更新が適用されたら、[閉じる] をクリックします。


これで終了です。 この時点で、Office 365 のフェデレーション ユーザーは、企業のイントラネットの外部から要求を送信するときに、MFA のみを使用するだけですみます。

<!---HONumber=August15_HO9-->