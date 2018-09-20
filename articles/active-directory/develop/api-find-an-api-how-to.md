---
title: 独自に開発したアプリケーションに必要な特定の API を見つける方法 | Microsoft Docs
description: 独自に開発した Azure AD アプリケーションで、特定の API にアクセスするのに必要なアクセス許可を構成する方法
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
ms.openlocfilehash: 8d0b9f219d7a0bc61e3d12acfaae6015963401f9
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/12/2018
ms.locfileid: "44723758"
---
# <a name="how-to-find-a-specific-api-needed-for-a-custom-developed-application"></a>独自に開発したアプリケーションに必要な特定の API を見つける方法

API にアクセスするには、アクセス スコープとアクセス ロールを構成する必要があります。 リソース アプリケーション Web API をクライアント アプリケーションに公開する場合は、API のアクセス スコープとアクセス ロールを構成する必要があります。 クライアント アプリケーションから Web API にアクセスする場合は、アプリの登録で API にアクセスするためのアクセス許可を構成する必要があります。

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>Web API を公開するためのリソース アプリケーションの構成

Web API を公開すると、アクセス許可をアプリの登録に追加する際に **[API を選択します]** リストに API が表示されます。 アクセス スコープを追加するには、「[リソース アプリケーションへのアクセス スコープの追加](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#adding-access-scopes-to-your-resource-application)」で説明されている手順に従ってください。

## <a name="configuring-a-client-application-to-access-web-apis"></a>Web API にアクセスするためのクライアント アプリケーションの構成

アクセス許可をアプリの登録に追加すると、公開されている Web API に **API アクセスを追加**できます。 Web API にアクセスするには、「[Web API にアクセスするための資格情報またはアクセス許可を追加するには](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#to-add-credentials-or-permissions-to-access-web-apis)」で説明されている手順に従ってください。

## <a name="next-steps"></a>次の手順

-   [Azure Active Directory とアプリケーションの統合](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

-   [Azure Active Directory のアプリケーション マニフェストについて](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest)


