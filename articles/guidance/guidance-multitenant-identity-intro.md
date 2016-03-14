<properties
   pageTitle="マルチテナント アプリケーションの ID 管理 | Microsoft Azure"
   description="マルチテナント アプリケーションの ID 管理の概要"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="mwasson"/>

# マルチテナント アプリケーションの ID 管理: 概要

この記事は[シリーズの一部]です。このシリーズに付属する完成した[サンプル アプリケーション]もあります。

たとえば、クラウドでホストされるエンタープライズ SaaS アプリケーションを作成するとします。当然ながら、アプリケーションにはユーザーがいます。

![ユーザー](media/guidance-multitenant-identity/users.png)

ただし、ユーザーは組織に所属しています。

![組織のユーザー](media/guidance-multitenant-identity/org-users.png)

例: Tailspin は、SaaS アプリケーションへのサブスクリプションを販売しています。Contoso と Fabrikam がアプリにサインアップします。Alice (`alice@contoso`) がサインインすると、アプリケーションは Alice が Contoso に属していることを認識します。

- Alice には、Contoso データへのアクセス権が_付与されます_。
- Alice には、Fabrikam データへのアクセス権は_付与されません_。

このガイドでは、[Azure Active Directory][AzureAD] (Azure AD) を使用してサインインと認証を処理し、マルチテナント アプリケーションのユーザー ID を管理する方法について説明します。

## マルチテナントとは

_テナント_は、ユーザーのグループです。SaaS アプリケーションのテナントとは、アプリケーションのサブスクライバーまたは顧客です。_マルチテナント_は、複数のテナントがアプリの同じ物理インスタンスを共有するアーキテクチャです。テナントは物理リソース (VM や記憶域など) を共有しますが、各テナントにはアプリの論理インスタンスが付与されます。

通常、アプリケーション データは、テナント内のユーザー間で共有されますが、他のテナントとは共有されません。

![マルチテナント](media/guidance-multitenant-identity/multitenant.png)

このアーキテクチャをシングルテナント アーキテクチャと比較してみましょう。シングルテナント アーキテクチャの場合、各テナントには専用の物理インスタンスがあります。シングルテナント アーキテクチャでテナントを追加するには、新しいインスタンスのアプリを作成します。

![シングル テナント](media/guidance-multitenant-identity/single-tenant.png)

### マルチテナントと水平スケーリング

クラウドでスケーリングを達成するには、物理インスタンスを追加する方法が一般的です。これは_水平スケーリング_または_スケール アウト_と呼ばれます。たとえば、Web アプリがあるとします。多くのトラフィックを処理する場合、サーバー VM を追加し、ロード バランサーの背後に配置します。各 VM では、別物理インスタンスの Web アプリを実行します。

![Web サイトの負荷分散](media/guidance-multitenant-identity/load-balancing.png)

任意の要求を任意のインスタンスにルーティングできます。その結果、システムは 1 つの論理インスタンスとして機能します。ユーザーに影響を与えることなく、VM を増減することができます。このアーキテクチャでは、各物理インスタンスがマルチ テナントであり、インスタンスを追加することで拡張できます。1 つのインスタンスが停止しても、テナントに影響は及びません。

## マルチテナント アプリの ID

マルチテナント アプリの場合、テナントのコンテキストでユーザーを考慮する必要があります。

**認証**

- ユーザーは、組織の資格情報を使用してアプリにサインインします。ユーザーがアプリの新しいユーザー プロファイルを作成する必要はありません。
- 同じ組織内のユーザーは、同じテナントに属します。
- ユーザーがサインインすると、アプリケーションユーザーが属するテナントを認識します。

**承認**

- ユーザーのアクション (リソースの表示など) を承認する場合、アプリはユーザーのテナントを考慮する必要があります。
- ユーザーには、アプリケーション内のロール ("Admin"、"Standard User" など) が割り当てられていることがあります。ロールの割り当ては、SaaS プロバイダーではなく、顧客が管理する必要があります。

**例:** Contoso の従業員である Alice は、ブラウザーでアプリケーションを開き、[ログイン] ボタンをクリックします。ログイン画面にリダイレクトされ、Alice は会社の資格情報 (ユーザー名とパスワード) を入力します。この時点で、`alice@contoso.com` としてアプリにログインされます。アプリケーションは、Alice がこのアプリケーションの管理者であることも認識します。Alice は管理者なので、Contoso に属するすべてのリソースの一覧を表示できます。ただし、自分のテナント内でのみ管理者なので、Fabrikam のリソースは表示できません。

このガイドでは、ID 管理のために Azure AD を使用することに注目します。

- ここでは、顧客が Azure AD (Office 365 テナントや Dynamics CRM テナントなど) にユーザー プロファイルを保存しているとします。
- オンプレミスの Active Directory (AD) がある顧客は、[Azure AD Connect][ADConnect] を使用して、オンプレミス AD を Azure AD と同期できます。

オンプレミスの AD がある顧客が (会社の IT ポリシーなどの理由で) Azure AD Connect を使用できない場合、SaaS プロバイダーは、Active Directory Federation Services (AD FS) を介して顧客の AD と連携できます。このオプションについては、「[顧客の AD FS とのフェデレーション]」を参照してください。

このガイドでは、データのパーティション分割、テナントごとの構成など、マルチテナントの他の側面について考慮していません。

<!-- Links -->
[ADConnect]: ../active-directory/active-directory-aadconnect.md
[AzureAD]: https://azure.microsoft.com/documentation/services/active-directory/
[シリーズの一部]: guidance-multitenant-identity.md
[顧客の AD FS とのフェデレーション]: guidance-multitenant-identity-adfs.md
[サンプル アプリケーション]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->