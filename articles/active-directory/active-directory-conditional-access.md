<properties
	pageTitle="Azure Active Directory の条件付きアクセス | Microsoft Azure"  
    description="条件を利用してアクセスを制御する Azure Active Directory は、ユーザーの認証時、アプリケーションにアクセスを与える前に、選択された特定の条件を確認します。条件が満たされていれば、ユーザーは承認され、アプリケーションにアクセスできます。"  
    services="active-directory" 
	keywords="アプリへの条件付きアクセス, Azure AD での条件付きアクセス, 企業リソースへの安全なアクセス, 条件付きアクセス ポリシー" 
	documentationCenter=""
	authors="markusvi"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
	ms.date="07/26/2016"
	ms.author="markvi"/>


# Azure Active Directory の条件付きアクセス   
  
会社のリソースへのアクセスをセキュリティで保護することは、すべての組織にとって重要です。クラウド サービスとモバイル デバイスの登場によって、ユーザーが会社のリソースにアクセスする方法が大きく変化しました。それにより、セキュリティに対する新しいアプローチが求められています。
  
## 条件付きアクセスが必要な理由  

Azure Active Directory の条件付きアクセス制御機能を使用すると、企業はクラウドとオンプレミスのリソースを簡単に保護することができます。条件付きアクセス ポリシーを使用すると、多要素認証を必須にすることで、資格情報の盗難やフィッシングの危険性から保護できます。また、機密性の高いサービスへのアクセスを許可する Intune の管理対象デバイスを必須にすることで、会社のデータを安全に保つことができます。



## ライセンス要件

条件付きアクセスは、[Azure AD Premium](http://www.microsoft.com/identity) の機能です。条件付きアクセス ポリシーが適用されたアプリケーションにアクセスするすべてのユーザーは、Azure AD Premium のライセンスを取得する必要があります。使用状況については、[ライセンスのないユーザーのレポート](https://aka.ms/utc5ix)で確認できます。





## 条件付きアクセス制御の適用のしくみ  

Azure Active Directory は、条件付きアクセス制御によって、ユーザー認証時にアプリケーションへのアクセスを許可する前に、選択されている特定の条件を確認します。アクセス要件が満たされていれば、ユーザーは承認され、アプリケーションにアクセスできます。
   
![](./media/active-directory-conditional-access/conditionalaccess-overview.png)

## 条件
  
- **グループ メンバーシップ**: グループへのメンバーシップに基づいて、ユーザーのアクセスのレベルを制御できます。

- **場所**: ユーザーが信頼されたネットワーク上にいないときに、ユーザーの場所を使用して MFA をトリガーし、コントロールをブロックできます。

- **デバイス プラットフォーム**: iOS、Android、Windows Mobile、Windows などのデバイス プラットフォームの種類を、ポリシーを適用するための条件として使用できます。

- **デバイスの有効/無効**: デバイス ポリシーの評価中にデバイスの有効/無効状態が検証されます。紛失したデバイスや盗難されたデバイスをディレクトリ内で無効にすることで、そのデバイスはポリシーの要件を満たすために使用することができなくなります。

- **サインインとユーザーのリスク**: Azure AD Identity Protection では、リスクに基づく条件付きアクセス ポリシーが用意されており、リスク イベントと疑わしいサインイン アクティビティに基づいて高度な保護を提供します。


## コントロール
   
- **多要素認証 (MFA)**: MFA による強力な認証を必須にすることができます。MFA は、ADFS を使用して、Azure MFA またはオンプレミス MFA プロバイダーによって提供できます。MFA 認証は、承認されていないユーザーが有効なユーザーのユーザー名とパスワードを入手してリソースにアクセスするのを防ぐことで、リソースの保護を助けます。

- **ブロック**: ユーザーの場所のような条件に基づいてアクセス権を適用できます。たとえば、ユーザーが信頼されたネットワーク上にいない場合にアクセスをブロックできます。

- **登録済み/準拠デバイス**: デバイス レベルでは、モバイル デバイス管理 (MDM) に登録された準拠デバイスだけがアクセスを許可されるように強制するポリシーを設定できます。Microsoft Intune を使用して、デバイスが登録されていて準拠していることが検証されます。デバイス レベルの条件付きアクセスにより、MDM ポリシーに準拠しているデバイスだけがアクセスを許可されます。
 

## アプリケーション

- これらのポリシーを使用して設定できるアクセスのレベルは、クラウドまたはオンプレミスのアプリケーションとサービスに適用できます。ポリシーは、Web サイトまたはサービスに直接適用されます。ポリシーは、ブラウザー アクセスだけでなく、サービスにアクセスするアプリケーションに対しても適用されます。ポリシーを適用できるサービスの一覧を示します。


  
## 条件付きアクセスのコンテンツ マップ  
以下のコンテンツ マップは、現在のデプロイで条件付きアクセスを有効にする方法を詳しく学習する場合に参照する必要があるドキュメントの一覧です。


### MFA と場所のポリシー

- [グループ、場所、MFA ポリシーに基づく Azure AD 接続アプリへの条件付きアクセスの基本](active-directory-conditional-access-azuread-connected-apps.md)
- [サポートされるアプリケーションの種類](active-directory-conditional-access-supported-apps.md)


### デバイスのポリシー

[Microsoft Intune を使用してデータを保護する](https://docs.microsoft.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-microsoft-intune)


### サインイン リスクに基づいてリソースを保護する

[Azure AD Identity Protection](active-directory-identityprotection.md)

### 追加情報

- [条件付きアクセスのよく寄せられる質問](active-directory-conditional-faqs.md)
- [テクニカル リファレンス](active-directory-conditional-access-technical-reference.md)

<!---HONumber=AcomDC_0727_2016-->