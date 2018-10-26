---
title: Azure Active Directory 重大な変更リファレンス | Microsoft Docs
description: アプリケーションに影響を与える可能のある Azure AD プロトコルの変更について説明します。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 68517c83-1279-4cc7-a7c1-c7ccc3dbe146
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 266d9ff9ceb4aa71429a9afc3056d23d222a9c7b
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2018
ms.locfileid: "48247373"
---
# <a name="whats-new-for-authentication"></a>認証の新機能 

>このページの更新に関する通知を受け取るには、 [この URL](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20for%20authentication%22&locale=en-us) を RSS フィード リーダーに追加してください。

認証システムは、セキュリティの向上と規格への準拠を確保するために、継続的に機能の変更と追加を行います。 常に最新の開発情報を把握していただけるよう、この記事では以下に関する情報を提供します。

- 最新の機能
- 既知の問題
- プロトコルの変更
- 非推奨の機能

> [!TIP] 
> このページは頻繁に更新されるため、定期的にアクセスしてご確認ください。 特に明記されていない限り、これらの変更は新規に登録されたアプリケーションに対してのみ適用されます。  

## <a name="upcoming-changes"></a>今後の変更

### <a name="authorization-codes-can-no-longer-be-reused"></a>認証コードを再利用できなくなりました

**有効日**: 2018 年 10 月 10 日**影響を受けるエンドポイント**: v1.0 と v2.0 の両方**影響を受けるプロトコル**: [コード フロー](v2-oauth2-auth-code-flow.md)

2018 年 10 月 10 日以降、Azure AD では、以前使用されていた、アプリの認証コードの受け入れが停止されます。 このセキュリティの変更により、Azure AD と OAuth の仕様が一致するようになります。この変更は、v1 と v2 両方のエンドポイントに適用されます。

お使いのアプリで承認コードを再利用して複数のリソースに対するトークンを取得している場合は、コードを使用して更新トークンを取得した後、その更新トークンを使用して他のリソース用のトークンを追加取得することお勧めします。 承認コードは 1 回しか使用できませんが、更新トークンは複数のリソースで複数回使用できます。 OAuth コード フローの使用時に新しいアプリで認証コードを再利用しようとすると、invalid_grant エラーが発生します。

更新トークンについて詳しくは、「[アクセス トークンの更新](v1-protocols-oauth-code.md#refreshing-the-access-tokens)」をご覧ください。

> [!NOTE]
> 影響を与えるアプリを最小限に抑えるため、この機能に依存する既存のアプリケーションにはこの要件の例外が与えられています。  このパターンに依存しており、1 日あたりのログイン回数が 10 回を超えるアプリは、依存していると見なされます。  

## <a name="may-2018"></a>2018 年 5 月

### <a name="id-tokens-cannot-be-used-for-the-obo-flow"></a>ID トークンは OBO フローに使用できません

**日付**: 2018 年 5 月 1 日 **影響を受けるエンドポイント**: v1.0 と v2.0 の両方 **影響を受けるプロトコル**: 暗黙のフローと [OBO フロー](v1-oauth2-on-behalf-of-flow.md)

2018 年 5 月 1 日以降、id_tokens は新しいアプリケーションの OBO フローでアサーションとして使用できません。 代わりに、アクセス トークンを使用して、同じアプリケーションのクライアントと中間層の間でも、API のセキュリティを確保する必要があります。 2018 年 5 月 1 日より前に登録されたアプリは、引き続き動作し、アクセス トークンの id_tokens を交換することができますが、これはベスト プラクティスとはみなされません。

この変更を回避するには、次の操作を行います。

1. 1 つ以上のスコープを使用して、中間層アプリケーション用の Web API を作成します。 これにより、きめ細かな制御とセキュリティが可能になります。
1. アプリのマニフェスト、[Azure portal](https://portal.azure.com)、または[アプリ登録ポータル](https://apps.dev.microsoft.com)で、アプリが暗黙のフローを介してアクセス トークンを発行できることを確認します。 これは `oauth2AllowImplicitFlow` キーによって制御されます。
1. クライアント アプリケーションで `response_type=id_token` を使用して id_token を要求した場合、上記で作成した Web API に対してもアクセス トークン (`response_type=token`) を要求します。 したがって、v2.0 エンドポイントを使用する場合、`scope` パラメータは `api://GUID/SCOPE` と同様のものになります。 v1.0 エンドポイントでは、`resource` パラメータを Web API のアプリ URI にする必要があります。
1. このアクセストークンを、id_token の代わりに、中間層に渡します。  
