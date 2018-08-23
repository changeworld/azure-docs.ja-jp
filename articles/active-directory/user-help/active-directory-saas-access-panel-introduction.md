---
title: Azure Active Directory のアクセス パネルの概要 | Microsoft Docs
description: アクセス パネルの各種構成 (Web ブラウザー、Android アプリ、iPhone および iPad アプリ) を使用して、SaaS アプリにアクセスする方法について説明します。
services: active-directory
author: eross-msft
manager: mtillman
ms.assetid: c0252d01-7e6e-4f79-a70e-600479577dfd
ms.service: active-directory
ms.component: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 05/11/18
ms.author: lizross
ms.reviewer: asteen
ms.openlocfilehash: f69830deb4d2c74b5fe1c85ad8c9452097dd2a1e
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2018
ms.locfileid: "42146600"
---
# <a name="what-is-the-access-panel"></a>アクセス パネルの概要

アクセス パネルは Web ベースのポータルです。 Azure Active Directory (Azure AD) の職場または学校アカウントがある場合は、アクセス パネルを使用して、Azure AD 管理者がアクセスを許可したクラウドベースのアプリケーションを表示および起動できます。 また、アクセス パネルからは、セルフサービスのグループおよびアプリ管理機能を利用することもできます。

アクセス パネルは Azure Portal とは別のものです。 Azure サブスクリプションを持っている必要はありません。

![アクセス パネル][1]アクセス パネルを使用すると、プロファイル設定を編集し、次の作業を行うことができます。

- 職場または学校アカウントに関連付けられているパスワードを変更する。

- パスワードのリセット設定を編集する。

- Multi-Factor Authentication に関連する連絡先および基本設定 (管理者が使用する必要があるアカウントの設定) を編集する。

- ユーザー ID、連絡用メール アドレス、携帯電話番号、会社電話番号、デバイスなどのアカウント詳細を表示する。

- Azure AD 管理者によってアクセスを許可されたクラウドベースのアプリケーションを表示および起動する。 

- グループを自己管理する。 管理者がセキュリティ グループを作成および管理し、Azure AD のセキュリティ グループのメンバーシップを要求できます。 詳細については、[Azure AD でのユーザーのセルフ サービス グループ管理](../users-groups-roles/groups-self-service-management.md)に関する記事および[グループの管理](../fundamentals/active-directory-manage-groups.md)に関する記事をご覧ください。




## <a name="access-the-access-panel"></a>アクセス パネルへのアクセス

アクセス パネルにアクセスするには、`http://myapps.microsoft.com` に移動します。

サインイン ページ用にカスタム ブランドを構成している場合は、URL に組織のドメインを追加することで、ブランドを読み込むことができます (例: `http://myapps.microsoft.com/<your domain>.com`)。

次に示すように、Azure portal で構成されている任意のアクティブなドメイン名または検証済みドメイン名を使用できます。![Wingtip Toys ドメイン名][2]  

URL は、Azure AD と統合されたアプリケーションにサインインするすべてのユーザーに配布します。

## <a name="authentication"></a>Authentication

アクセス パネルにアクセスするには、Azure AD で職場または学校アカウントによる認証を行う必要があります。 Azure AD で直接認証を行うことができます。 または、組織が Active Directory フェデレーション サービス (AD FS) などのテクノロジを使ってフェデレーションを構成している場合は、Windows Server Active Directory で認証を行うこともできます。

Azure または Office 365 のサブスクリプションを持っていて、Azure Portal または Office 365 アプリケーションを使っている場合は、サインインし直さなくてもアプリケーションの一覧が表示されます。 認証を行っていない場合は、Azure AD アカウントのユーザー名とパスワードを使ってサインインするように求められます。 組織がフェデレーションを構成している場合は、ユーザー名を入力するだけで十分です。

認証を行うと、管理者によってディレクトリに統合されているアプリケーションを使うことができるようになります。 アプリケーションを Azure AD に統合する方法については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="web-browser-requirements"></a>Web ブラウザーの要件

アクセス パネルには、少なくとも、JavaScript をサポートする CSS 対応のブラウザーが必要です。 パスワード ベースのシングル サインオン (SSO) を使ってアプリケーションにサインインするには、ブラウザーにアクセス パネルの拡張機能をインストールする必要があります。 この拡張機能は、パスワード ベースの SSO 用に構成されているアプリケーションを選ぶと、自動的にダウンロードされます。

インストーラーはアーキテクチャ固有です。 ダウンロード リンクをクリックした場合、現在実行している OS アーキテクチャ向けのインストーラーのみが取得されます。 64 ビットと 32 ビットのインストーラーを取得するには、アプリケーション デプロイ管理者は、必ず両方のデバイスからダウンロード リンクにアクセスしてください。


現在、アクセス パネルの拡張機能は次で使用できます。
- **Microsoft Edge**: Windows 10 Anniversary Edition 以降。 
- **Chrome**: Windows 7 以降、MacOS X 以降。
- **Firefox 26.0 以降**: Windows XP SP2 以降、Mac OS X 10.6 以降。
- **Internet Explorer 11**: Windows 7 以降 (制限付きサポート)。

## <a name="my-apps-secure-sign-in-extension"></a>マイ アプリによるセキュリティで保護されたサインイン拡張機能
パスワード ベースのシングル サインオンにサインインするには、拡張機能を使用する必要があります。 拡張機能のインストール後、サインインして追加の機能を有効にするには、**[開始するにはサインインしてください]** を選択します。 

- アプリに直接サインインするには、そのアプリの**サインオン URL** を使用します。 アプリの URL を使用すると、そのアクションが拡張機能によって検出され、サインインするためのオプションが提供されます。
- 拡張機能の "*クイック検索*" 機能を使用して、アクセス パネルから任意のアプリを起動することもできます。 
- **[最近の使用]** セクションには、起動した直近のアプリケーションが拡張機能によって 3 つ表示されます。
- 遠くにいる場合、[アプリケーション プロキシ](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started)を利用して社内 URL を利用できます。

> [!NOTE]
> その他の機能は、Microsoft Edge、Chrome、および Firefox でのみ使用できます。
>
拡張は次のサイトから直接ダウンロードできます。
- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)

`https://myapps.microsoft.com` 以外のマイ アプリの URL を使用している場合は、次の手順に従って既定の URL を構成します。
1. 拡張機能にサインインして "*いない*" ときに、拡張機能のアイコンを右クリックします。
2. メニューから **[My Apps URL]\(マイ アプリの URL)** を選択します。
3. 既定の URL を選択します。
4. 拡張機能のアイコンを選択します。
5. **[開始するにはサインインしてください]** を選択します。

拡張を利用してリモート時に社内の URL を利用するのには、次の操作を行います。
1. テナントで[アプリケーション プロキシを構成します](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-enable)。
2. アプリケーション プロキシ経由で[アプリケーションと URL を発行します](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal)。
3. 拡張機能をインストールし、[開始するにはサインインしてください] を選択して拡張機能にサインインします。
4. これで、リモート時にも社内 URL を参照できます。

> [!NOTE]
> 社内 URL への自動リダイレクトをオフにするには、メイン メニューで設定の歯車アイコンを選択し、社内 URL へのリダイレクト オプションで**オフ**を選択します。


## <a name="mobile-app-support"></a>モバイル アプリのサポート

マイ アプリは、Azure Active Directory チームが発行するモバイル アプリです。 アプリをインストールすると、iOS および Android デバイス上のパスワード ベースの SSO アプリケーションにサインインできます。

> [!NOTE]
> Azure AD とのフェデレーションをサポートするアプリケーション (Salesforce、Google Apps、Dropbox、Box、Concur、Workday、Office 365 など 70 を超えるアプリケーション) には、プラグインやモバイル アプリがなくても、事実上すべてのデバイスのすべての Web ブラウザーからサインインすることができます。 他の[アクセス パネル エクスペリエンス](https://myapps.microsoft.com/)についても、マイ アプリ モバイル アプリがインストールされていないモバイル デバイスで使用できます。
>
>

### <a name="my-apps-for-android"></a>Android 用のマイ アプリ

Android 用のマイ アプリは、Android バージョン 4.1 以降を実行するすべての Android デバイスでサポートされています。  

これは、[Google Play ストア](https://play.google.com/store/apps/details?id=com.microsoft.myapps)で入手できます。

![Android 用のマイ アプリ][3]   

### <a name="my-apps-for-iphone-and-ipad"></a>IPhone および iPad 用のマイ アプリ

iOS 用のマイ アプリは、iOS バージョン 7 以降を実行するすべての iPhone および iPad でサポートされています。  

これは、[Apple App Store](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8) で入手できます。

![iOS 用のマイ アプリ][4]    


## <a name="managed-browser-for-my-apps"></a>マイ アプリ用の Managed Browser

マイ アプリは、Intune Managed Browser にも統合されています。 iOS および Android デバイス用の Intune Managed Browser は、モバイル デバイス上のデータの安全性を確保するうえで重要な役割を果たします。 ブラウザーを使用すると、会社の情報が含まれる Web ページを安全に表示して Web ページ内を移動できます。これはセキュリティで保護された Web 閲覧環境を提供するのに役立ちます。  

Managed Browser のホーム ページおよびブックマークではマイ アプリに簡単にアクセスでき、目的のアプリケーションに到達するまでのクリック数が少なくなります。

Intune Managed Browser は、[Apple App Store](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8) および [Google Play ストア](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser&hl=en)で入手できます。

![マイ アプリ用の Managed Browser][5]    


## <a name="tips-for-testing-the-user-experience"></a>エンド ユーザー エクスペリエンスをテストするためのヒント

Azure 管理者がディレクトリのアカウントを使って Azure Portal にサインインしている場合は、現在のアカウントとしてアクセス パネルに自動的にサインインされます。 このビューには、自身に割り当てられているすべてのアプリケーションが表示されます。

"*別の*" ユーザー アカウントをテストするには、次の操作を行います。

1. Azure Portal またはアクセス パネルの右上で、**[サインアウト]** を選択します。 
2. [アクセス パネル](http://myapps.microsoft.com)に移動します。
3. サインイン ページで、テストするディレクトリのアカウントのユーザー名とパスワードを入力します。


## <a name="starting-applications"></a>アプリケーションの起動

このセクションでは、アクセス パネルに表示できる複数のアプリケーションの種類について説明します。

### <a name="office-365-applications"></a>Office 365 アプリケーション

組織が Office 365 アプリケーションを使っていて、ユーザーがこれらのアプリケーションのライセンスを持っている場合、Office 365 アプリケーションがユーザーのアクセス パネルに表示されます。

Office 365 アプリケーションのアプリケーション タイルを選択すると、アプリケーションにリダイレクトされて自動的にサインインされます。

### <a name="microsoft-and-third-party-applications-configured-with-federation-based-sso"></a>フェデレーション ベースの SSO で構成されたマイクロソフトとサード パーティのアプリケーション

管理者は、SSO モードを **Azure AD のシングル サインオン**に設定することで、Azure Portal の Active Directory セクションにアプリケーションを追加できます。 こうしたアプリケーションは、アプリケーションへのアクセス権が管理者によって明示的に付与されている場合にのみ表示されます。

アプリケーションのタイルを選択すると、そこにリダイレクトされ、自動的にサインインされます。

### <a name="password-based-sso-without-identity-provisioning"></a>パスワード ベースの SSO (ID プロビジョニングなし)

管理者は、SSO モードを**パスワードベースのシングル サインオン**に設定することで、Azure Portal の Active Directory セクションにアプリケーションを追加できます。 ディレクトリ内のすべてのユーザーに、このモードで構成されているすべてのアプリケーションが表示されます。

アプリケーション タイルを選択するのが初めての場合は、Internet Explorer または Chrome のパスワード SSO プラグインをインストールするように求められます。 インストールするには、Web ブラウザーの再起動が必要になる場合があります。 アクセス パネルに戻り、アプリケーション タイルを再度選択すると、アプリケーションのユーザー名とパスワードの入力を求められます。 ユーザー名とパスワードを入力すると、資格情報はセキュリティで保護されて格納され、Azure AD のアカウントにリンクされます。

次にこのアプリケーション タイルを選択すると、アプリケーションに自動的にサインインされます。  

資格情報を再入力したり、パスワード SSO プラグインをインストールしたりする必要はありません。

ターゲットのサードパーティ アプリケーションで資格情報を変更した場合は、Azure AD に格納されている資格情報も更新する必要があります。 

資格情報を更新するには、次の操作を行います。

1. アプリケーションのタイルでアイコンを選びます。
2. **[資格情報の更新]** を選び、アプリケーションのユーザー名とパスワードを再入力します。


### <a name="password-based-sso-with-identity-provisioning"></a>パスワード ベースの SSO (ID プロビジョニングあり)

管理者は、SSO モードを**パスワード ベースのシングル サインオン**に設定し、ID プロビジョニングを設定することで、Azure Portal の Active Directory セクションにアプリケーションを追加できます。

アプリケーション タイルを選択するのが初めての場合は、Internet Explorer または Chrome のパスワード SSO プラグインをインストールするように求められます。 インストールするには、Web ブラウザーの再起動が必要になる場合があります。  

アクセス パネルに戻り、アプリケーション タイルを再度選択すると、アプリケーションに自動的にサインインされます。

一部のアプリケーションでは、最初のサインイン時にパスワードを変更する必要があります。 ターゲットのサードパーティ アプリケーションで資格情報を変更した場合は、Azure AD に格納されている資格情報も更新する必要があります。 

資格情報を更新するには、次の操作を行います。

1. アプリケーションのタイルでアイコンを選びます。
2. **[資格情報の更新]** を選び、アプリケーションのユーザー名とパスワードを再入力します。


### <a name="application-with-existing-sso-solutions"></a>既存の SSO ソリューションとアプリケーション

Azure Portal でアプリケーションの SSO を構成する場合、第 3 のオプションとして既存のシングル サインオンが用意されています。 このオプションを選んだ場合、管理者は、アプリケーションへのリンクを作成し、これを選んだユーザーのアクセス パネルに配置できます。

たとえば、AD FS 2.0 を使ってユーザーを認証するように構成されたアプリケーションがある場合、管理者は、[既存のシングル サインオン] オプションを使って、アクセス パネルにそのアプリケーションへのリンクを作成することができます。 ユーザーがこのリンクにアクセスすると、ユーザーは AD FS 2.0 またはアプリケーションによって提供された任意の既存の SSO ソリューションを使って認証されます。


## <a name="next-steps"></a>次の手順

- アプリケーション管理に関するトピックの一覧については、[Azure Active Directory でのアプリケーション管理に関する記事の索引](../active-directory-apps-index.md)をご覧ください。
 
- SaaS アプリを Azure AD に統合する方法については、[SaaS アプリの統合方法に関するチュートリアルの一覧](../saas-apps/tutorial-list.md)をご覧ください。
 
- Azure AD でのアプリの管理について詳しくは、[Azure Active Directory でのシングル サインオンとアプリのアクセスの管理の概要](../manage-apps/what-is-single-sign-on.md)に関する記事をご覧ください。
 
- ユーザーのプロビジョニングについて詳しくは、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../active-directory-saas-app-provisioning.md)」をご覧ください。

<!--Image references-->
[1]: ./media/active-directory-saas-access-panel-introduction/01.png
[2]: ./media/active-directory-saas-access-panel-introduction/02.png
[3]: ./media/active-directory-saas-access-panel-introduction/03.png
[4]: ./media/active-directory-saas-access-panel-introduction/04.png
[5]: ./media/active-directory-saas-access-panel-introduction/05.png
