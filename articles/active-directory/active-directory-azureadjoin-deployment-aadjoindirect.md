<properties
	pageTitle="Azure AD 参加の使用シナリオとデプロイに関する考慮事項 | Microsoft Azure"
	description="管理者がエンド ユーザー (従業員、学生、他のユーザー) のために Azure AD 参加を設定する方法を説明します。また、Azure AD 参加を使用するさまざまな実際のシナリオについても説明します。"
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""
	tags="azure-classic-portal"/>

<tags ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/26/2016"

	ms.author="femila"/>

# Azure AD 参加の使用シナリオとデプロイに関する考慮事項

## Azure AD 参加の使用シナリオ
### シナリオ 1: 業務の多くでクラウドを利用している企業

現在、業務や ID 管理の多くでクラウドを利用している企業や、近いうちにクラウドに移行しようとしている企業は、Azure Active Directory 参加 (Azure AD 参加) を利用することでメリットがあります。Azure AD に作成したアカウントを使用して Windows 10 にサインインすることができます。[最初の実行エクスペリエンス (FRX) プロセス](active-directory-azureadjoin-user-frx.md)、つまり[設定メニュー](active-directory-azureadjoin-user-upgrade.md)から Azure AD に参加することにより、ユーザーは、Azure AD にコンピューターを参加させることができます。ユーザーは、ブラウザーまたは Office アプリケーション内で、Office 365 のようなクラウド リソースへのシングル サインオン (SSO) アクセスを利用することもできます。

### シナリオ 2: 教育機関

通常、教育機関のユーザーは、教員と学生の 2 種類です。教員メンバーは組織に長期に所属するメンバーと考えられるため、このようなメンバーにはオンプレミスのアカウントを作成するのが望ましいことです。一方、学生は、組織やアカウントに属する期間が短いため、Azure AD で管理できます。つまり、ディレクトリ スケールをオンプレミスに保存するのではなく、クラウドにプッシュできます。また、学生は Azure AD アカウントで Windows にサインインし、Office アプリケーションで Office 365 のリソースにアクセスできます。

### シナリオ 3: 小売業

小売業には、季節従業員と長期雇用の従業員がいます。通常、長期雇用のフルタイム従業員に対しては、オンプレミスのアカウントを作成し、ドメインに参加しているコンピューターを使用します。一方、季節従業員については、組織への所属期間が短いため、ユーザー ライセンスをより簡単に移動できるアカウントを管理する方が適しています。Office 365 のライセンスを使用してクラウドにこのようなユーザー アカウントを作成すると、ユーザーには Azure AD アカウントで Windows と Office アプリケーションにサインインできるメリットがあり、管理者はユーザーが退職した後でのライセンス管理の柔軟性が増します。

### シナリオ 4: その他のシナリオ

これまでに説明したようなメリットだけでなく、ユーザーのデバイスを Azure AD に参加させると、簡単な参加エクスペリエンス、効率的なデバイス管理、モバイル デバイス管理の自動登録、Azure AD およびオンプレミスのリソースへのシングル サインオンなどのさまざまな利点があります。


## Azure AD 参加のデプロイに関する考慮事項

### ユーザーが会社所有のデバイスを直接 Azure AD に参加させることができるようにする


企業では、パートナーの企業や組織にクラウドのみのアカウントを提供できます。これらのパートナーは、シングル サインオンを使用して会社のアプリとリソースに簡単にアクセスできます。このシナリオは、認証に Azure AD を利用している Office 365 アプリや SaaS アプリなど、主にクラウドのリソースにアクセスするユーザーに適用されます。

### 前提条件
**企業レベル (管理者)**

*	Azure サブスクリプションと Azure Active Directory  

**ユーザー レベル**

*	Windows 10 (Professional および Enterprise エディション)

### 管理者の作業
* [デバイスの登録の設定](active-directory-azureadjoin-setup.md)

### ユーザーの作業
* [セットアップ中に新しい Windows 10 デバイスを Azure AD に設定する](active-directory-azureadjoin-user-frx.md)
* [Windows 10 デバイスを [設定] メニューから Azure AD に設定する](active-directory-azureadjoin-user-upgrade.md)
* [個人の Windows 10 デバイスを組織に参加させる](active-directory-azureadjoin-personal-device.md)



## Windows 10 で組織の BYOD を有効にする
会社のユーザーと従業員が個人の Windows デバイスを使用して (BYOD) 会社のアプリとリソースにアクセスできるように設定できます。ユーザーは、Azure AD のアカウント (職場または学校アカウント) を個人の Windows デバイスに追加することで、安全かつポリシーに準拠する方法で、リソースにアクセスできます。

### 前提条件
**企業レベル (管理者)**

*	Azure AD サブスクリプション

**ユーザー レベル**

*	Windows 10 (Professional および Enterprise エディション)


### 管理者の作業

* [デバイスの登録の設定](active-directory-azureadjoin-setup.md)

### ユーザーの作業
* [個人の Windows 10 デバイスを組織に参加させる](active-directory-azureadjoin-personal-device.md)


## 追加情報
* [エンタープライズ向け Windows 10: デバイスを仕事に使用する方法](active-directory-azureadjoin-windows10-devices-overview.md)
* [Azure Active Directory 参加を使用したクラウド機能の Windows 10 デバイスへの拡張](active-directory-azureadjoin-user-upgrade.md)
* [Microsoft Passport 経由でのパスワードを使用しない ID の認証](active-directory-azureadjoin-passport.md)
* [Azure AD 参加の使用シナリオについて](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Windows 10 エクスペリエンスのためのドメイン参加済みデバイスの Azure AD への接続](active-directory-azureadjoin-devices-group-policy.md)
* [Azure AD 参加の設定](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_0302_2016-->