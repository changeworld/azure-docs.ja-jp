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
	ms.date="08/17/2016"
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

- **準拠デバイス**: デバイス レベルでは、ドメインに参加しているコンピューター、またはモバイル デバイス管理 (MDM) に登録されているモバイル デバイスで、コンプライアンスを満たしている場合にのみアクセスを許可するポリシーを設定できます。Microsoft Intune は、アプリケーションのアクセス中に適用するために、デバイスのコンプライアンスを確認し、Azure Active Directory にその報告を返します。
 

## アプリケーション

- これらのポリシーを使用して設定できるアクセスのレベルは、クラウドまたはオンプレミスのアプリケーションとサービスに適用できます。ポリシーは、Web サイトまたはサービスに直接適用されます。ポリシーは、ブラウザー アクセスだけでなく、サービスにアクセスするアプリケーションに対しても適用されます。ポリシーを適用できるサービスの一覧を示します。


## デバイス ベースの条件付きアクセス

Azure AD に登録されていて、特定の条件を満たすデバイスからのアプリケーションのアクセスを制限することもできます。これは、次のデバイスからこれらのリソースにアクセスする有効なユーザーから組織のリソースを保護するのに便利です。

- 不明なデバイスや管理されていないデバイス
- 組織で定義されているセキュリティ ポリシーを満たしていないデバイス

ポリシーは、次の要件に基づいて設定できます。

- **ドメイン参加デバイス** - オンプレミスの Active Directory ドメインに参加しており、Azure AD に登録されているデバイスのアクセスを制限するポリシーを設定できます。このポリシーは、Azure AD に登録されており、オンプレミスの Active Directory ドメインに属している Windows のデスクトップ、ラップトップ、またはエンタープライズ タブレットに適用されます。Azure AD へのドメイン参加デバイスの自動登録をセットアップする方法の詳細については、「[Azure Active Directory への Windows ドメイン参加済みデバイスの自動登録の設定方法](active-directory-conditional-access-automatic-device-registration-setup.md)」を参照してください。

- **準拠デバイス** - 管理システムによってディレクトリで**準拠**とマークされているデバイスのアクセスを制限するポリシーを設定できます。このポリシーにより、デバイスにファイルの暗号化を強制するなど、セキュリティ ポリシーを満たすデバイスのみアクセスが許可されます。このポリシーは、次のデバイスからのアクセスを制限するために使用できます。

    - ハイブリッド構成でデプロイされた System Center Configuration Manager 2016 によって管理されている **Windows ドメイン参加デバイス**。

    - Microsoft Intune またはサポート対象のサード パーティのモバイル デバイス管理 (MDM) システムによって管理されている**業務用または個人用の Windows 10 Mobile デバイス**。

    - Microsoft Intune で管理されている **iOS および Android のデバイス**。


デバイス ベースの条件付きアクセス ポリシーによって保護されたアプリケーションにアクセスするユーザーは、このポリシーを満たすデバイスからこれを実行する必要があります。このポリシーを満たしていないデバイスからこれを実行すると、アクセスが拒否されます。

Azure AD でデバイス ベースの条件付きアクセス ポリシーを構成する方法については、[Azure Active Directory に接続されたアプリケーションのアクセスを制御するデバイス ベースの条件付きアクセス ポリシーを設定する方法](active-directory-conditional-access-policy-connected-applications.md)に関する記事を参照してください。

## Azure Active Directory の条件付きアクセスの記事インデックス
  
以下のコンテンツ マップは、現在のデプロイで条件付きアクセスを有効にする方法を詳しく学習する場合に参照する必要があるドキュメントの一覧です。


### MFA と場所のポリシー

- [グループ、場所、MFA ポリシーに基づく Azure AD 接続アプリへの条件付きアクセスの基本](active-directory-conditional-access-azuread-connected-apps.md)

- [サポートされるアプリケーションの種類](active-directory-conditional-access-supported-apps.md)


### デバイス ベースの条件付きアクセス

- [Azure Active Directory に接続されたアプリケーションのアクセスを制御するデバイス ベースの条件付きアクセス ポリシーを設定する方法](active-directory-conditional-access-policy-connected-applications.md)

- [Azure Active Directory への Windows ドメイン参加済みデバイスの自動登録の設定方法](active-directory-conditional-access-automatic-device-registration-setup.md)

- [Azure AD デバイス ベースの条件付きアクセスで保護されたアプリケーションにアクセスする際のユーザーによる修復](active-directory-conditional-access-device-remediation.md)

- [Microsoft Intune を使用してデータを保護する](https://docs.microsoft.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-microsoft-intune)


### サインイン リスクに基づいてリソースを保護する

[Azure AD Identity Protection](active-directory-identityprotection.md)

### 追加情報

- [条件付きアクセスのよく寄せられる質問](active-directory-conditional-faqs.md)
- [テクニカル リファレンス](active-directory-conditional-access-technical-reference.md)

<!---HONumber=AcomDC_0817_2016-->