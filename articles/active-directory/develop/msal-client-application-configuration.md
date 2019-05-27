---
title: クライアント アプリケーションの構成 (Microsoft Authentication Library) | Azure
description: Microsoft Authentication Library (MSAL) でのパブリック クライアント アプリケーションと機密クライアント アプリケーションの構成オプションについて説明します。
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d731a8153dc6a70382c0d87cc20d8c961d9fe24
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/11/2019
ms.locfileid: "65546008"
---
# <a name="application-configuration-options"></a>アプリケーション構成オプション

コードで、認証してトークンを取得するために、新しいパブリックまたは機密クライアント (または MSAL.js のユーザー エージェント) を初期化します。  MSAL でクライアント アプリケーションを初期化するときに設定できる構成オプションは数多くあります。 これらのオプションは、2 つのグループに分けることができます。

- 登録オプション。以下が含まれます。
    - [機関](#authority) (アプリケーション用の ID プロバイダー [インスタンス](#cloud-instance)とサインイン[対象ユーザー](#application-audience)で構成されます。場合により、テナント ID も含まれます)。
    - [クライアント ID](#client-id)
    - [リダイレクト URI](#redirect-uri)
    - [クライアント シークレット](#client-secret) (機密クライアント アプリケーション用)
- [ログ オプション](#logging) (ログ レベル、個人データの制御、およびライブラリを使用するコンポーネントの名前など)。

## <a name="authority"></a>Authority
機関は、MSAL がトークンを要求できるディレクトリを示す URL です。 通常の機関を次に示します。

- https://login.microsoftonline.com/&lt;tenant&gt;/。&lt;tenant&gt; は Azure AD テナントのテナント ID または Azure AD テナントに関連付けられたドメインです。  特定の組織のユーザーをサインインする場合にのみ使用されます。
- https://login.microsoftonline.com/common/ 職場および学校アカウント、または Microsoft 個人アカウントを持つユーザーをサインインする場合に使用されます。
- https://login.microsoftonline.com/organizations/ 職場および学校アカウントを持つユーザーをサインインする場合に使用されます。
- https://login.microsoftonline.com/consumers/ Microsoft の個人アカウント (ライブ) のみを持つユーザーをサインインする場合に使用されます。

機関の設定は、アプリケーション登録ポータルで宣言されているものと一致している必要があります。

機関 URL は、インスタンスと対象ユーザーで構成されます。

以下の機関が可能です。
- Azure Active Directory クラウド機関
- Azure AD B2C 機関 「[B2C specifics (B2C の詳細)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics)」を参照してください。
- ADFS 機関 「[ADFS support (ADFS のサポート)](https://aka.ms/msal-net-adfs-support)」を参照してください。

Azure AD クラウド機関には、2 つの部分があります。
- ID プロバイダー **インスタンス**
- アプリケーションのサインイン**対象ユーザー**

インスタンスと対象ユーザーを連結して、機関 URL として指定できます。 MSAL 3.x より前のバージョンの MSAL.NET では、ターゲットにしたいクラウドに応じて機関を自分で構成して、対象ユーザーのサインインを実行する必要がありました。  次の図に、機関 URL がどのように構成されるかを示します。

![Authority](media/msal-client-application-configuration/authority.png)

## <a name="cloud-instance"></a>クラウド インスタンス
**インスタンス**を使用して、アプリケーションでサインインするユーザーが Microsoft Azure パブリック クラウドのユーザーであるか各国のクラウドのユーザーであるかを指定します。 コードで MSAL を使用して、列挙を使用するか、URL がわかっている場合は URL を [各国のクラウド インスタンス](authentication-national-cloud.md#azure-ad-authentication-endpoints) に `Instance` メンバーとして渡すことで、Azure クラウド インスタンスを設定できます。

`Instance` と `AzureCloudInstance` の両方が指定された場合、MSAL.NET では明示的な例外がスローされます。 

インスタンスの指定がない場合、アプリのターゲットは Azure パブリック クラウド インスタンスになります (URL `https://login.onmicrosoftonline.com` インスタンス)。

## <a name="application-audience"></a>アプリケーションの対象ユーザー

サインイン対象ユーザーは、アプリケーションのビジネス ニーズによって異なります。
- 基幹業務 (LOB) 開発者の場合は、高い確率で、組織内でのみ使用されるシングル テナント アプリケーションを作成します。 その場合は、テナント ID (Azure Active Directory の ID) または Azure Active Directory に関連付けられているドメイン名のいずれかを使用して、組織を指定する必要があります。
- ISV の場合は、ユーザーの職場および学校アカウントを使用して、任意の組織または複数の組織 (マルチテナント アプリ) にサインインしますが、ユーザーの Microsoft 個人アカウントを使用してサインインする場合もあります。

### <a name="how-to-specify-the-audience-in-your-codeconfiguration"></a>コード/構成で対象ユーザーを指定する方法
コードで MSAL を使用して、対象ユーザーを指定します (次のいずれかを使用します)。
- Azure AD 機関の対象ユーザーの列挙。 
- テナント ID。以下が可能です。
  - シングル テナント アプリケーションの GUID (Azure Active Directory の ID)
  - Azure Active Directory に関連付けられているドメイン名 (これもシングル テナント アプリケーション用)
- Azure AD 機関の対象ユーザーの列挙に代わる テナント ID としての次のいずれかのプレース ホルダー:
    - `organizations`: マルチテナント アプリケーションの場合
    - `consumers`: ユーザーの個人アカウントでのみサインインする場合
    - `common`: ユーザーの職場および学校アカウント、または Microsoft 個人アカウントでサインインする場合

Azure AD 機関の対象ユーザーとテナント ID の両方が指定された場合、MSAL では意味のある例外がスローされます。 

対象ユーザーの指定がない場合、アプリでは Azure AD と対象ユーザーとして Microsoft 個人アカウントが対象になります (つまり `common` が使用されます)。

### <a name="effective-audience"></a>有効な対象ユーザー
アプリケーションで有効な対象ユーザーは、アプリケーションに設定する対象ユーザーとアプリケーション登録に指定された対象ユーザーの最小値 (共通集合がある場合) になります。 実際、アプリケーション登録エクスペリエンス ([アプリの登録](https://aka.ms/appregistrations)) で、アプリケーションの対象ユーザー (サポートされるアカウントの種類) を指定できます。 「[クイック スタート:Microsoft ID プラットフォームにアプリケーションを登録する](quickstart-register-app.md)」で詳細を参照してください。

現時点では、Microsoft 個人アカウントのみを持っているユーザーのサインインをアプリケーションで実行する唯一の方法は、次のように設定することです。
- アプリの登録で、対象ユーザーを "職場および学校アカウント、および個人アカウント" に設定する
- コード/構成で、対象ユーザーを `AadAuthorityAudience.PersonalMicrosoftAccount` (または `TenantID ` = "consumers") に設定する

## <a name="client-id"></a>クライアント ID
アプリの登録時に Azure AD によってアプリに割り当てられた一意のアプリケーション (クライアント) ID。

## <a name="redirect-uri"></a>リダイレクト URI
リダイレクト URI は、ID プロバイダーがセキュリティ トークンを送り返す URI です。 

### <a name="redirect-uri-for-public-client-applications"></a>パブリック クライアント アプリケーション用のリダイレクト URI
MSAL を使用するパブリック クライアント アプリケーション開発者の場合:
- ``RedirectUri`` は MSAL によって自動的に計算されるため、それを渡す必要はありません。 このリダイレクト URI には、プラットフォームに応じて、次の値が設定されます。

- ``urn:ietf:wg:oauth:2.0:oob``: すべての Windows プラットフォーム用
- ``msal{ClientId}://auth``: Xamarin Android および iOS 用

ただし、このリダイレクト URI を、[アプリの登録](https://aka.ms/appregistrations)で構成する必要があります。

![ポータルでのリダイレクト URI](media/msal-client-application-configuration/redirect-uri.png)

`RedirectUri` プロパティを使用して、このリダイレクト URI をオーバーライドできます (たとえばブローカーを使用する場合)。 その場合のリダイレクト URI の例を次に示します。

- `RedirectUriOnAndroid` = "msauth-5a434691-ccb2-4fd1-b97b-b64bcfbc03fc://com.microsoft.identity.client.sample";
- `RedirectUriOnIos` = $"msauth.{Bundle.ID}://auth";

詳細については、Android の詳細と [iOS の詳細](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS)を参照してください。

### <a name="redirect-uri-for-confidential-client-applications"></a>機密クライアント アプリケーション用のリダイレクト URI
Web アプリの場合、リダイレクト URI (または応答 URI) は、Azure AD から アプリケーションにトークンが戻される URI です。 これは、機密クライアントが Web アプリケーション/Web API のいずれかの場合は、それらの URL を使用できます。  アプリの登録では、このリダイレクト URI を登録する必要があります。 これは、最初にローカルでテストを行ったアプリケーションをデプロイするときに特に重要です。 アプリケーション登録ポータルで、デプロイされるアプリケーションの応答 URL を追加する必要があります。

デーモン アプリの場合は、リダイレクト URI を指定する必要はありません。

## <a name="client-secret"></a>クライアント シークレット
機密クライアント アプリケーション用のクライアント シークレットです。 このシークレット (アプリケーション パスワード) は、アプリケーション登録ポータルによって提供されるか、PowerShell AzureAD、PowerShell AzureRM、または Azure CLI を使用したアプリケーションの登録時に Azure AD に対して提供されます。

## <a name="logging"></a>ログの記録
ログ記録とトラブルシューティングを有効にするその他のオプションがあります。 詳細については、使用方法に関する「[Logging (ログ記録)](msal-logging.md)」を参照してください。

## <a name="next-steps"></a>次の手順
[MSAL.NET を使用したクライアント アプリケーションのインスタンス化](msal-net-initializing-client-applications.md)を確認します。

[MSAL.js を使用したクライアント アプリケーションのインスタンス化](msal-js-initializing-client-applications.md)を確認します。
