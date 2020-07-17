---
title: 外部 ID に対する ID プロバイダー - Azure AD
description: Azure Active Directory B2B コラボレーションでは、会社のアプリケーションへの選択的なアクセスのために、多要素認証 (MFA) をサポートしています
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 395473e70137ead2b7185d54d165f82e9b3f1f04
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83595711"
---
# <a name="identity-providers-for-external-identities"></a>外部 ID に対する ID プロバイダー

"*ID プロバイダー*" では、ID 情報の作成、保守、管理、およびアプリケーションへの認証サービスの提供が行われます。 アプリとリソースを外部ユーザーと共有する場合は、Azure AD が共有のための既定の ID プロバイダーです。 つまり、Azure AD アカウントまたは Microsoft アカウントを既に持っている外部ユーザーを招待すると、そのユーザーは自分でそれ以上構成を行わなくても自動的にサインインできます。

ただし、ユーザーがさまざまな ID プロバイダーを使用してサインインできるようにすることができます。 たとえば、Google や Facebook など、Azure AD でサポートされているソーシャル ID プロバイダーとのフェデレーションを設定できます。 また、SAML または WS-Fed プロトコルをサポートする任意の外部 ID プロバイダーとフェデレーションすることもできます。 外部 ID プロバイダーのフェデレーションを使用すると、既存のソーシャル アカウントまたはエンタープライズ アカウントを使用してアプリにサインインする機能を、外部ユーザーに提供できます。

## <a name="how-it-works"></a>しくみ

Azure AD External Identities は、Google および Facebook とのフェデレーション用に事前に構成されています。 Azure AD テナントでこれらの ID プロバイダーを設定するには、各 ID プロバイダーでアプリケーションを作成し、資格情報を構成します。 クライアントまたはアプリの ID と、クライアントまたはアプリのシークレットを取得して、Azure AD テナントに追加できます。

Azure AD テナントに ID プロバイダーを追加した後、次のようにします。

- 組織内のアプリまたはリソースに外部ユーザーを招待すると、外部ユーザーはその ID プロバイダーでの自分のアカウントを使用してサインインできるようになります。
- アプリに対して[セルフサービス サインアップ](self-service-sign-up-overview.md)を有効にすると、外部ユーザーは、追加した ID プロバイダーでの自分のアカウントを使用して、アプリにサインアップできます。 

招待を利用する場合、またはアプリにサインアップする場合、外部ユーザーは、ソーシャル ID プロバイダーを使用してサインインおよび認証を行うことができます。

![Google と Facebook のオプションが表示されたサインイン画面のスクリーンショット](media/identity-providers/sign-in-with-social-identity.png)

最適なサインイン エクスペリエンスにするには、可能な限り ID プロバイダーとフェデレーションします。これにより、招待されたゲストがアプリにアクセスしたときに、シームレスなサインイン エクスペリエンスを提供できるようになります。  

## <a name="next-steps"></a>次のステップ

アプリケーションへのサインイン用に ID プロバイダーを追加する方法については、次の記事を参照してください。

- ソーシャル ID プロバイダーの一覧に [Google を追加する](google-federation.md)
- ソーシャル ID プロバイダーの一覧に [Facebook を追加する](facebook-federation.md)
- ID プロバイダーが SAML 2.0 または WS-Fed プロトコルをサポートしている組織との[直接フェデレーションを設定する](direct-federation.md)。 セルフサービス サインアップのユーザー フローでは直接フェデレーションを使用できないことに注意してください。
