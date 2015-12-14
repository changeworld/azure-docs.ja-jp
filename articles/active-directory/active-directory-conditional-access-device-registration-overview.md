<properties
	pageTitle="Azure Active Directory Device Registration の概要 | Microsoft Azure"
	description="は、デバイスに基づいて条件付きでアクセスを許可するというシナリオの基礎となる機能です。デバイスが登録されると、Azure Active Directory Device Registration によってそのデバイスの ID がプロビジョニングされます。この ID は、ユーザーのサインイン時のデバイス認証に使用されます。"
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/24/2015"
	ms.author="femila"/>

# Azure Active Directory Device Registration の概要

Azure Active Directory Device Registration は、デバイスに基づいて条件付きでアクセスを許可するというシナリオの基礎となる機能です。デバイスが登録されると、Azure Active Directory Device Registration によってそのデバイスの ID がプロビジョニングされます。この ID は、ユーザーのサインイン時のデバイス認証に使用されます。認証済みのデバイスおよびデバイスの属性を使用して、クラウドおよびオンプレミスでホストされるアプリケーションに条件付きアクセス ポリシーを適用できます。Intune などのモバイル デバイス管理ソリューションと組み合わせて使用すると、Azure Active Directory のデバイスの属性は、デバイスに関する情報が追加されて更新されます。これにより、条件付きアクセス規則を作成できます。この規則に従い、デバイスからのアクセス時にセキュリティおよび法令遵守の基準を満たす必要があります。Azure Active Directory Device Registration は、Azure Active Directory で使用できます。このサービスには、iOS デバイス、Android デバイス、および Windows デバイスのサポートが含まれています。Azure Active Directory Device Registration を使用する個々のシナリオでは、より具体的な要件とプラットフォームのサポートが存在する場合があります。現在実現できる特定のシナリオについて説明します。

## Azure Active Directory Device Registration で実現されるシナリオ

Azure AD Device Registration は、さまざまなシナリオの基盤として考えることができます。通常、このサービスには、iOS デバイス、Android デバイス、および Windows デバイスのサポートが含まれています。Azure AD Device Registration を使用する個々のシナリオでは、より具体的な要件とプラットフォームのサポートが存在する場合があります。このようなシナリオとして、次のものが挙げられます。オンプレミスでホストされるアプリケーションへの条件付きアクセス: Windows Server 2012 R2 で AD FS を使用するように構成されたアプリケーションに対して、アクセス ポリシーの登録済みのデバイスを使用できます。オンプレミスの条件付きアクセスを設定する方法の詳細については、「Azure Active Directory Device Registration を使用したオンプレミスの条件付きアクセスの設定」を参照してください。

Microsoft Intune を使用した Office 365 アプリケーションへの条件付きアクセス: IT 管理者は、条件付きアクセスのデバイス ポリシーをプロビジョニングすることで、会社リソースをセキュリティで保護し、同時にインフォメーション ワーカーが準拠デバイスでサービスにアクセスできるようになります。詳細については、「Office 365 サービス用条件付きアクセスのデバイス ポリシー」を参照してください。

##Azure Active Directory Device Registration の設定

Azure Active Directory Device Registration サービスでは、次の設定を使用できます。Azure ポータルで Azure AD Device Registration を有効にします。

Windows デバイスにより、既知の DNS レコードが検索されてサービスが検出されます。Windows 7 デバイスおよび Windows 8.1 デバイスでサービスが検出されて使用されるように、会社の DNS を構成する必要があります。

Azure Active Directory の管理者ポータルを使用して、登録されているデバイスを表示し、有効または無効にすることができます。

## Azure Active Directory Device Registration を有効にする
次のセクションでは、ディレクトリの Azure Active Directory Device Registration サービスを有効にする方法について説明します。
Azure Active Directory Device Registration サービスを有効にするには
-------------------------------------------------------------
1. Azure ポータルに管理者としてサインインします。
1. 左ウィンドウで、**[Active Directory]** を選択します。
1. **[ディレクトリ]** タブで、ディレクトリを選択します。
1. **[構成]** タブをクリックします。
1. **[デバイス]** セクションまでスクロールします。
1. **[デバイスの社内参加が許可されるユーザー]** で **[すべて]** を選択します。
1. ユーザーごとに承認するデバイスの最大数を選択します。

>[AZURE.NOTE]Office 365 用に Microsoft Intune またはモバイル デバイス管理を使用して登録するには、社内参加が必要です。これらのサービスのいずれかを構成している場合、[すべて] が選択され、[なし] は無効になります。


既定では、サービスに対する 2 要素認証は有効になっていません。ただし、デバイスを登録するときに 2 要素認証を使用することをお勧めします。

* このサービスに対して 2 要素認証を必要とするように設定する場合は、事前に Azure Active Directory で 2 要素認証プロバイダーを構成し、Multi-Factor Authentication に対してユーザー アカウントを構成する必要があります。「Multi-Factor Authentication を Azure Active Directory に追加する」を参照してください。

* Windows Server 2012 R2 で AD FS を使用している場合は、AD FS で 2 要素認証モジュールを構成する必要があります。「Multi-Factor Authentication を Active Directory フェデレーション サービスと共に使用する」を参照してください。

## Azure Active Directory Device Registration の検出を構成する
Windows 7 デバイスおよび Windows 8.1 デバイスでは、ユーザー アカウント名と既知のデバイス登録サーバー名を組み合わせた、デバイス登録サーバーが検出されます。Azure Active Directory Device Registration サービスに関連付けられた A レコードを参照する DNS CNAME レコードを作成する必要があります。CNAME レコードでは、既知のプレフィックス enterpriseregistration の後に、組織のユーザー アカウントで使用されている UPN サフィックスを使用する必要があります。組織で複数の UPN サフィックスを使用している場合は、DNS に複数の CNAME レコードを作成する必要があります。

たとえば、組織で @contoso.com と @region.contoso.com の 2 つの UPN サフィックスを使用している場合は、次の DNS レコードを作成します。
 
| エントリ | 型 | Address |
|-------------------------------------------|-------|------------------------------------|
| enterpriseregistration.contoso.com | CNAME | enterpriseregistration.windows.net |
| enterpriseregistration.region.contoso.com | CNAME | enterpriseregistration.windows.net |

## Azure Active Directory のデバイス オブジェクトを表示および管理する
1. Azure 管理者ポータルから、デバイスの表示、ブロック、およびブロック解除を行うことができます。ブロックされたデバイスからは、登録済みデバイスのみを許可するように構成されたアプリケーションにアクセスできなくなります。
1. Microsoft Azure ポータルに管理者としてサインインします。
1. 左ウィンドウで、**[Active Directory]** を選択します。
1. ディレクトリを選択します。
1. **[ユーザー]** タブをクリックします。デバイスを表示するユーザーを選択します。
1. **[デバイス]** タブを選択します。
1. ドロップダウン メニューから **[登録済みのデバイス]** を選択します。
1. ここで、ユーザーの登録済みデバイスを表示、ブロック、またはブロック解除することができます。 

## 関連トピック

Azure AD Device Registration を使用して、ドメイン参加済みの Windows 7 デバイスおよび Windows 8.1 デバイスを登録することができます。次のトピックでは、Windows 7 デバイスおよび Windows 8.1 デバイス上でデバイス登録を構成するために必要な前提条件と手順について詳しく説明します。

- [Azure Active Directory への Windows ドメイン参加済みデバイスの自動デバイス登録](active-directory-conditional-access-automatic-device-registration.md) 
- [Windows 7 ドメイン参加済みデバイスの自動デバイス登録の構成](active-directory-conditional-access-automatic-device-registration-windows7.md)
- [Windows 8.1 ドメイン参加済みデバイスの自動デバイス登録の構成](active-directory-conditional-access-automatic-device-registration-windows8_1.md)

<!---HONumber=AcomDC_1203_2015-->