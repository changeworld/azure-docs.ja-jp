<properties
pageTitle="Azure Active Directory のアプリケーション オブジェクトとサービス プリンシパル オブジェクト | Microsoft Azure"
description="Azure Active Directory におけるアプリケーション オブジェクトとサービス プリンシパル オブジェクトのリレーションシップについての説明"
documentationCenter="dev-center-name"
authors="bryanla"
manager="mbaldwin"
services="active-directory"
editor=""/>

<tags
ms.service="active-directory"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="identity"
ms.date="08/10/2016"
ms.author="bryanla;mbaldwin"/>

# Azure Active Directory のアプリケーション オブジェクトとサービス プリンシパル オブジェクト
Azure Active Directory (AD) "アプリケーション" についての記事を読むと、著者がアプリケーションと呼んでいるものが正確には何を指しているのか、わかりにくい場合があります。この記事の目的は、Azure AD アプリケーションの統合について概念的な側面と具体的な側面を定義し、[マルチテナント アプリケーション](active-directory-dev-glossary.md#multi-tenant-application)に対する登録および同意の例を示すことにより、不明確な部分をよりわかりやすくすることです。

## Overview
Azure AD アプリケーションとは、単なるソフトウェアのことではありません。これは、アプリケーション ソフトウェアだけでなく、その Azure AD への登録 (ID 構成とも呼ばれる) も意味する概念的な用語です。Azure AD に登録することで、そのアプリケーション ソフトウェアは、実行時に認証および承認の "対話" に参加できるようになります。定義上、アプリケーションは[クライアント](active-directory-dev-glossary.md#client-application) ロール (リソースの使用)、[リソース サーバー](active-directory-dev-glossary.md#resource-server) ロール (クライアントへの API の公開)、またはその両方のロールで動作できます。対話プロトコルは、[OAuth 2.0 承認付与フロー](active-directory-dev-glossary.md#authorization-grant)によって定義されており、その目的は、クライアントとリソースが、それぞれリソースのデータに対してアクセス/保護を実行できるようにすることです。次はもう 1 段階掘り下げ、Azure AD アプリケーション モデルにおいて、アプリケーションが内部的にはどのように表されるのかを見てみましょう。

## アプリケーションの登録
[Azure クラシック ポータル][AZURE-Classic-Portal]でアプリケーションを登録すると、Azure AD テナントに、アプリケーション オブジェクトとサービス プリンシパル オブジェクトという 2 つのオブジェクトが作成されます。

#### アプリケーション オブジェクト
Azure AD アプリケーションは、その唯一のアプリケーション オブジェクトによって "*定義*" されています。アプリケーション オブジェクトは、アプリケーションの登録先であり、そのアプリケーションの "ホーム" テナントと呼ばれる Azure AD テナント内にあります。アプリケーション オブジェクトは、ID に関連するアプリケーションの情報を提供するオブジェクトであり、対応するサービス プリンシパル オブジェクトを "*派生*" させて実行時に使用するためのテンプレートでもあります。

アプリケーション オブジェクトはアプリケーションの "*グローバル*" な表現 (すべてのテナントで使用する)、サービス プリンシパル オブジェクトはアプリケーションの "*ローカル*" な表現 (特定のテナントで使用する) と考えることができます。アプリケーション オブジェクトのスキーマは、Azure AD Graph [Application エンティティ][AAD-Graph-App-Entity]によって定義されています。そのため、アプリケーション オブジェクトには、ソフトウェア アプリケーションとの間に 1:1 のリレーションシップがあり、対応する *n* 個のサービス プリンシパル オブジェクトとの間に 1:*n* のリレーションシップがあります。

#### サービス プリンシパル オブジェクト
サービス プリンシパル オブジェクトは、アプリケーション用のポリシーとアクセス許可を定義しており、実行時のリソースへのアクセスの際にアプリケーションを表す、セキュリティ プリンシパルの基となる情報を提供します。サービス プリンシパル オブジェクトのスキーマは、Azure AD Graph [ServicePrincipal エンティティ][AAD-Graph-Sp-Entity]によって定義されています。

サービス プリンシパル オブジェクトは、アプリケーションのインスタンスの使用方法を表す必要のある各テナントに必須であり、これによって、それらのテナントからユーザー アカウントが所有するリソースに安全にアクセスできます。シングルテナント アプリケーションは、サービス プリンシパルが 1 つのみ (ホーム テナント内に) 存在します。また、マルチテナント [Web アプリケーション](active-directory-dev-glossary.md#web-client)は各テナントにサービス プリンシパルがあります。そのテナントからの管理者やユーザーにはリソースにアクセスできるように同意が与えられています。同意を得た後、サービス プリンシパル オブジェクトは将来の承認要求のために参照されることになります。

> [AZURE.NOTE] アプリケーション オブジェクトに加えたすべての変更は、アプリケーションのホーム テナント (アプリケーションが登録されたテナント) にだけ存在するサービス プリンシパル オブジェクトにも反映されます。マルチテナント アプリケーションの場合は、コンシューマー テナントでアクセス権を削除し、もう一度アクセス権を付与するまで、そのコンシューマー テナントのサービス プリンシパル オブジェクトにアプリケーション オブジェクトへの変更が反映されることはありません。

## 例
次の図は、**HR アプリ**という名前のサンプル マルチテナント アプリケーションを基に、アプリケーションのアプリケーション オブジェクトと、対応するサービス プリンシパル オブジェクトの間のリレーションシップを表しています。このシナリオには、次の 3 つの Azure AD テナントがあります。

- **Adatum** - **HR アプリ**を開発した会社が使用するテナント
- **Contoso** - **HR アプリ**のコンシューマーである、Contoso という組織が使用するテナント
- **Fabrikam** - Contoso と同じく **HR アプリ**のコンシューマーである、Fabrikam という組織が使用するテナント

![Relationship between an application object and a service principal object](./media/active-directory-application-objects/application-objects-relationship.png)

前の図でのステップ 1. は、アプリケーションとサービス プリンシパル オブジェクトを、アプリケーションのホーム テナント内に作成するプロセスです。

ステップ 2. では、Contoso と Fabrikam の管理者が同意を終えると、それぞれの会社の Azure AD テナント内にサービス プリンシパル オブジェクトが作成され、それに管理者が付与したアクセス許可が割り当てられます。HR アプリは、個々のユーザー用として、ユーザーによる同意を許可するように構成/設計することができる点にも注目してください。

ステップ 3 では、HR アプリケーション (Contoso と Fabrikam) のコンシューマー テナントにそれぞれ独自のサービス プリンシパル オブジェクトが作成されます。それぞれ実行時におけるアプリケーションのインスタンスの使用を表し、それぞれの管理者によって同意されたアクセス許可によって管理されます。

## 次のステップ
Azure AD Graph API を通じて、その OData [Application エンティティ][AAD-Graph-App-Entity]によって表される、アプリケーションのアプリケーション オブジェクトにアクセスできます。

Azure AD Graph API を通じて、その OData [ServicePrincipal エンティティ][AAD-Graph-Sp-Entity]によって表される、アプリケーションのサービス プリンシパル オブジェクトにアクセスできます。



<!--Image references-->

<!--Reference style links -->
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AZURE-Classic-Portal]: https://manage.windowsazure.com

<!---HONumber=AcomDC_0810_2016-->