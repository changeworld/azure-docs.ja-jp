---
title: アプリケーションの同意のしくみ
description: Azure AD の同意フレームワークについて、Azure AD でアプリケーションを開発する場合の使用方法とそのしくみを詳しく説明します。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2021
ms.author: ryanwi
ROBOTS: NOINDEX
ms.openlocfilehash: 2b68be9da11bc1f868b6bf30d3794ee6a3bc9ab6
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/28/2021
ms.locfileid: "129154206"
---
# <a name="how-application-consent-works"></a>アプリケーションの同意のしくみ

この記事では、アプリケーションをより効果的に開発するための Azure AD の同意フレームワークのしくみについて詳しく説明します。

## <a name="recommended-documents"></a>推奨されるドキュメント

- [アプリケーションによるリソースへのアクセスの管理を同意でリソース所有者に許可する方法](./developer-glossary.md#consent)に関する概要を理解してください。
- [Azure AD の同意フレームワークの実装方法](./quickstart-register-app.md)の詳細な手順を入手します。
- 詳細については、[マルチテナント アプリケーションが同意フレームワークを使用して](./howto-convert-app-to-be-multi-tenant.md)多層アプリケーションのパターンのサポートが強化されている、"user" と "admin" の同意を実装する方法について学習します。
- 詳細については、[認証コードの付与フロー中に、OAuth 2.0 プロトコル層で同意がどのようにサポートされるか、学習してください。](../azuread-dev/v1-protocols-oauth-code.md#request-an-authorization-code)

## <a name="next-steps"></a>次のステップ
[AzureAD Microsoft Q&A](/answers/topics/azure-active-directory.html)
