<properties
	pageTitle="Azure Active Directory に接続されている Office 365 とその他のアプリへのアクセスの保護 | Microsoft Azure"  
    description="条件を利用してアクセスを制御する Azure Active Directory は、ユーザーの認証時、アプリケーションにアクセスを与える前に、選択された特定の条件を確認します。条件が満たされていれば、ユーザーは承認され、アプリケーションにアクセスできます。"  
    services="active-directory" 
	keywords="アプリへの条件付きアクセス, Azure AD での条件付きアクセス, 企業リソースへの安全なアクセス, 条件付きアクセス ポリシー" 
	documentationCenter=""
	authors="femila"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
	ms.date="06/15/2016"
	ms.author="femila"/>


# Azure Active Directory に接続されている Office 365 とその他のアプリへのアクセスの保護  
  
会社のリソースへのアクセスをセキュリティで保護することは、すべての組織にとって重要です。クラウド サービスとモバイル デバイスの登場によって、ユーザーが会社のリソースにアクセスする方法が大きく変化しました。それによって、会社のリソースをセキュリティで保護するための戦略も変更が必要になりました。
  
## 条件付きアクセスが必要な理由  
 Azure Active Directory の条件付きアクセス制御機能を使用すると、企業はクラウドとオンプレミスの両方のリソースを簡単に保護することができます。"盗まれたパスワードによるリソースへのアクセスを防止する" というようなニーズも、"企業コンテンツにアクセスするための正常な管理されたデバイスが必要" というようなニーズも、Azure Active Directory で満たすことができます。

## 条件付きアクセス制御の適用のしくみ  
 Azure Active Directory は、条件付きアクセス制御によって、ユーザー認証時にアプリケーションへのアクセスを許可する前に、選択されている特定の条件を確認します。アクセス要件が満たされていれば、ユーザーは承認され、アプリケーションにアクセスできます。
   
![](./media/active-directory-conditional-access/conditionalaccess-overview.png)

## リソースへのユーザー ベースのアクセス
  
- **ユーザー属性**: ユーザー属性レベルでは、承認されたユーザーだけが会社のリソースにアクセスできるように、ポリシーを適用することができます。   
- **ユーザーのグループ メンバーシップ**: グループへのメンバーシップに基づいて、ユーザーに提供されるアクセスのレベルを制御することもできます。   
- **多要素認証 (MFA)**: ユーザーが多要素認証システムを使用して自分の ID の認証を受けなければならないというポリシーを強制することもできます。たとえば、追加のセキュリティ レイヤーを確実に使用するために、ユーザーに個人用携帯電話で PIN を確認するように強制することができます。MFA 認証は、認証されていないユーザーが有効なユーザーのユーザー名とパスワードを入手してリソースにアクセスすることがないように、リソースを保護します。 
- **サインインとユーザーのリスク**: Azure AD Identity Protection では、リスクに基づく条件付きアクセス ポリシーが用意されており、リスク イベントと疑わしいサインイン アクティビティに基づいて高度な保護を提供します。 
 

## デバイス ベースの条件付きアクセス 

**登録済みデバイス**: デバイス レベルでは、モバイル デバイス管理 (MDM) に登録されたデバイスだけがアクセスを許可されるように強制するポリシーを設定できます。Microsoft Intune を使用して、デバイスが登録されていて準拠していることが検証されます。次に、デバイス レベルの条件付きアクセスにより、デバイスでのファイル暗号化の強制などのポリシーに準拠しているデバイスだけがアクセスを許可されていることが確認されます。また、MDM ソリューションを使用することで、紛失したり盗難に遭ったりしたデバイス上の企業データをリモートで消去できるようにすることもできます。
  

これらのポリシーを使用して設定できるアクセスのレベルは、クラウドまたはオンプレミスのリソースに適用できます。クラウドのリソースは、Office 365 やサード パーティの SaaS アプリなどのアプリです。また、クラウドでホストしている基幹業務アプリである場合もあります。
  
## 条件付きアクセスのコンテンツ マップ  
以下のコンテンツ マップは、現在のデプロイで条件付きアクセスを有効にする方法を詳しく学習する場合に参照する必要があるドキュメントの一覧です。


| シナリオ | 記事 |
|------------------------------------------------------|----------|
| 認証の強度またはユーザーに基づいてリソースを保護する |[Getting started with conditional access to Azure AD SaaS apps with MFA and extranet (MFA とエクストラネットを使用した Azure AD SaaS アプリに対する条件付きアクセスの概要)](active-directory-conditional-access-azuread-connected-apps.md)<br><br>[What applications are supported (サポートされるアプリケーション)](active-directory-conditional-access-supported-apps.md)|
| デバイスの紛失、盗難時に企業データを保護する |[Help protect your data with full or selective wipe using Microsoft Intune (Microsoft Intune を使用した全体または選択的ワイプによるデータの保護)](https://docs.microsoft.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-microsoft-intune)|
|サインイン リスクに基づいてリソースを保護する |[Azure AD Identity Protection](active-directory-identityprotection.md) |
| 追加情報 |[条件付きアクセスのよく寄せられる質問](active-directory-conditional-faqs.md)|

<!---HONumber=AcomDC_0615_2016-->