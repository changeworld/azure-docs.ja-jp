<properties
   pageTitle="Tailspin Surveys アプリケーションについて | Microsoft Azure"
   description="Tailspin Surveys アプリケーションの概要"
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

# Tailspin Surveys アプリケーションについて

この記事は[シリーズの一部]です。このシリーズに付属する完成した[サンプル アプリケーション]もあります。

Tailspin は、Surveys という名前の SaaS アプリケーションを開発している仮の会社です。このアプリケーションを使用すると、オンライン アンケートを作成および発行することができます。

- 組織はアプリケーションにサインアップできます。
- 組織がサインアップした後、ユーザーはその組織の資格情報でアプリケーションにサインインできます。
- ユーザーはアンケートを作成、編集、および発行することができます。

> [AZURE.NOTE] アプリケーションを開始するには、「[Running the Surveys application]」 (Surveys アプリケーションの実行) を参照してください。

このスクリーンショットには、[アンケートの編集] ページが表示されます。

![アンケートの編集](media/guidance-multitenant-identity/edit-survey.png)

ユーザーが自分の組織 ID を使用してサインインされていることに注意してください。`bob@contoso.com`

ユーザーは、同じテナント内で他のユーザーによって作成されたアンケートを表示できます。

![テナントのアンケート](media/guidance-multitenant-identity/tenant-surveys.png)

ユーザーがアンケートを作成する場合は、アンケートの共同作成者に他のユーザーを招待できます。共同作成者はアンケートを編集することはできますが、削除または公開することはできません。

![共同作成者の追加](media/guidance-multitenant-identity/add-contributor.png)

ユーザーは他のテナントから共同作成者を追加できます。これによってテナント間でリソースを共有できます。このスクリーンショットでは、Bob (`bob@contoso.com`) が Alice (`alice@fabrikam.com`) を、Bob が作成したアンケートの共同作成者として追加しています。

Alice がログインすると、[共同作成できるアンケート] の下にアンケートが表示されます。

![アンケートの共同作成者](media/guidance-multitenant-identity/contributor.png)

Alice が Contoso テナントのゲストとしてではなく、自分のテナントにサインインすることに注意してください。Alice は、そのアンケートに対してのみ共同作成者権限を持ちます - Contoso テナントからの他のアンケートは表示できません。

## アーキテクチャ

Surveys アプリケーションは、Web フロント エンドおよび Web API バックエンドで構成されます。両方とも [ASP.NET Core 1.0] を使用して実装されます。

Web アプリケーションでは、Azure Active Directory (Azure AD) を使用して、ユーザーを認証します。また、Web アプリケーションでは Azure AD を呼び出して、Web API の OAuth 2 アクセス トークンを取得します。アクセス トークンは、Azure Redis Cache でキャッシュされます。キャッシュを使用すると、複数のインスタンスで同じトークンのキャッシュ (たとえば、サーバー ファーム) を共有できます。

![アーキテクチャ](media/guidance-multitenant-identity/architecture.png)

<!-- Links -->
[シリーズの一部]: guidance-multitenant-identity.md
[Running the Surveys application]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md
[ASP.NET Core 1.0]: https://docs.asp.net/en/latest/
[サンプル アプリケーション]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->