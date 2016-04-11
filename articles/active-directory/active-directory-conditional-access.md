<properties
	pageTitle="Azure Active Directory に接続されている Office 365 とその他のアプリへのアクセスの保護 | Microsoft Azure"  
    description="条件を利用してアクセスを制御する Azure Active Directory は、ユーザーの認証時、アプリケーションにアクセスを与える前に、選択された特定の条件を確認します。条件が満たされていれば、ユーザーは承認され、アプリケーションにアクセスできます。"  
    services="active-directory" 
	keywords="アプリへの条件付きアクセス, Azure AD での条件付きアクセス, 企業リソースへの安全なアクセス, 条件付きアクセス ポリシー" 
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
	ms.date="03/23/2016"
	ms.author="femila"/>


# Azure Active Directory に接続されている Office 365 とその他のアプリへのアクセスの保護  
  
会社のリソースへのアクセスをセキュリティで保護することは、すべての組織にとって重要です。クラウド サービスとモバイル デバイスの登場によって、ユーザーが会社のリソースにアクセスする方法が大きく変化しました。それによって、会社のリソースをセキュリティで保護するための戦略も変更が必要になりました。
  
## 条件付きアクセスが必要な理由  
 Azure Active Directory の条件付きアクセス制御機能を使用すると、企業はクラウドとオンプレミスの両方のリソースを簡単に保護することができます。"盗まれたパスワードによるリソースへのアクセスを防止する" というようなニーズも、"企業コンテンツにアクセスするための正常な管理されたデバイスが必要" というようなニーズも、Azure Active Directory で満たすことができます。

## 条件付きアクセス制御の適用のしくみ  
 Azure Active Directory は、条件付きアクセス制御によって、ユーザー認証時にアプリケーションへのアクセスを許可する前に、選択されている特定の条件を確認します。条件が満たされていれば、ユーザーは承認され、アプリケーションにアクセスできます。
   
![](./media/active-directory-conditional-access/conditionalaccess-overview.png)

## リソースへのユーザー ベースのアクセス
  
- **ユーザー属性**: ユーザー属性レベルでは、承認されたユーザーだけが会社のリソースにアクセスできるように、ポリシーを適用することができます。   
- **ユーザーのグループ メンバーシップ**: グループへのメンバーシップに基づいて、ユーザーに提供されるアクセスのレベルを制御することもできます。   
- **多要素認証 (MFA)**: また、ユーザーが多要素認証システムを使用して自分の ID の認証を受けなければならないというポリシーを強制することもできます。たとえば、追加のセキュリティ レイヤーを確実に使用するために、ユーザーに個人用携帯電話で PIN を確認するように強制することができます。MFA 認証は、認証されていないユーザーが有効なユーザーのユーザー名とパスワードを入手してリソースにアクセスすることがないように、リソースを保護します。 

## デバイス ベースの条件付きアクセス 

- **登録済みデバイス**: デバイス レベルでは、登録済みか既知のデバイスだけがアクセスを許可されるように強制するポリシーを設定できます。さらに、Microsoft Intune などのモバイル デバイス管理 (MDM) ソリューションを使用して、管理されているデバイスだけがリソースにアクセスできるようにすることが可能です。デバイス レベルの条件付きアクセスでは、デバイスに対する PIN の強制などのポリシーに準拠しているデバイスだけがアクセスを許可されます。また、MDM ソリューションを使用することで、紛失したり盗難に遭ったりしたデバイス上の企業データをリモートで消去できるようにすることもできます。  
- **デバイス ポリシー**: アプリケーションごとにだけアクセスを制限するポリシーを設定することもできます。さらに、デバイスの物理的な場所に基づいて (つまり、要求が既知の企業ネットワークから来たか外部から来たかに応じて) アクセス レベルを設定することもできます。  

これらのポリシーを使用して設定できるアクセスのレベルは、クラウドまたはオンプレミスのリソースに適用できます。クラウドのリソースは、Office 365 やサード パーティの SaaS アプリなどのアプリです。また、クラウドでホストしている基幹業務アプリである場合もあります。
  
## 条件付きアクセスのコンテンツ マップ  
以下のコンテンツ マップは、現在のデプロイで条件付きアクセスを有効にする方法を詳しく学習する場合に参照する必要があるドキュメントの一覧です。


| シナリオ | 記事 |
|------------------------------------------------------|----------|
| フィッシング攻撃から企業リソースを保護する |[Azure Conditional Access Preview for SaaS Apps](active-directory-conditional-access-azuread-connected-apps.md)<br><br>[Conditional access to Azure AD apps (Azure AD アプリへの条件付きアクセス)](active-directory-conditional-access-technical-reference.md)<br><br>[クラウドでの Azure Multi-Factor Authentication Server の概要](multi-factor-authentication-get-started-cloud.md)<br><br>[AD FS の追加の認証方法の構成](https://technet.microsoft.com/library/dn758113.aspx)<br><br>[Azure Multi-Factor Authentication での問題](multi-factor-authentication-end-user-manage-settings.md)<br><br>[Azure Multi-Factor Authentication および AD FS を使用したクラウド リソースのセキュリティ保護](multi-factor-authentication-get-started-adfs-cloud.md)|
| デバイスの紛失、盗難時に企業データを保護する |[Azure Active Directory Device Registration の概要](active-directory-conditional-access-device-registration-overview.md)<br><br> [組織での Azure AD 参加の設定](active-directory-azureadjoin-setup.md)<br><br> [Azure Active Directory Device Registration を使用したオンプレミスの条件付きアクセスの設定](active-directory-conditional-access-on-premises-setup.md) <br><br>[Windows 7 ドメイン参加済みデバイスの自動デバイス登録の構成](active-directory-conditional-access-automatic-device-registration-windows7.md) <br><br>[Windows 8.1 ドメイン参加済みデバイスの自動デバイス登録の構成](active-directory-conditional-access-automatic-device-registration-windows8_1.md) <br><br>[Office 365 サービス用条件付きアクセスのデバイス ポリシー](active-directory-conditional-access-device-policies.md)|
| 追加情報 |[条件付きアクセスのよく寄せられる質問](active-directory-conditional-faqs.md)|

<!---HONumber=AcomDC_0330_2016------>