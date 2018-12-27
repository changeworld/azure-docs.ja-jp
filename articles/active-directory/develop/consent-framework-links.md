---
title: アプリケーションの同意のしくみ | Microsoft ドキュメント
description: Azure AD の同意フレームワークについて、Azure AD でアプリケーションを開発する場合の使用方法とそのしくみを詳しく説明します。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: celested
ms.openlocfilehash: 5d8d526e67a69959687fb79ac6406d8313a417a8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46998442"
---
# <a name="how-application-consent-works"></a>アプリケーションの同意のしくみ

この記事では、アプリケーションをより効果的に開発するための Azure AD の同意フレームワークのしくみについて詳しく説明します。

## <a name="recommended-documents"></a>推奨されるドキュメント

- [アプリケーションによるリソースへのアクセスの管理を同意でリソース所有者に許可する方法](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#consent)に関する概要を理解してください。
- [Azure AD の同意フレームワークの実装方法](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#overview-of-the-consent-framework)の詳細な手順を入手します。
- 詳細については、[マルチテナント アプリケーションが同意フレームワークを使用して](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent)多層アプリケーションのパターンのサポートが強化されている、"user" と "admin" の同意を実装する方法について学習します。
- 詳細については、[認証コードの付与フロー中に、OAuth 2.0 プロトコル層で同意がどのようにサポートされるか、学習してください。](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code#request-an-authorization-code)

## <a name="next-steps"></a>次の手順
[Azure AD StackOverflow](http://stackoverflow.com/questions/tagged/azure-active-directory)
