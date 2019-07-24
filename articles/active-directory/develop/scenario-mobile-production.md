---
title: Web API を呼び出すモバイル アプリ (運用環境への移行) - Microsoft ID プラットフォーム
description: Web API を呼び出すモバイル アプリを構築する方法について説明します (運用環境への移行)
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d8b6a5c2a29228de806088ea93e197d42bf1ab47
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65962357"
---
# <a name="mobile-app-that-calls-web-apis---move-to-production"></a>Web API を呼び出すモバイル アプリ - 運用環境への移行

この記事では、運用環境に移行する前にアプリの品質と信頼性を向上させる方法に関する詳細情報を提供します。

## <a name="handling-errors-in-mobile-applications"></a>モバイル アプリケーションでのエラーの処理

この時点でいくつかのエラー状態がアプリ内で発生することがあります。 処理する主なシナリオは、サイレント障害と対話へのフォールバックです。 また、ネットワークが利用できない状況、サービスの停止、管理者の同意の要件、およびその他のシナリオ固有のケースなどの他の条件についても、運用に際して検討する必要があります。

各 MSAL ライブラリには、これらの条件を処理する方法を詳しく説明するサンプル コードと wiki コンテンツがあります。

- [MSAL Android Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [MSAL iOS Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigating-and-investigating-issues"></a>問題の緩和と調査

アプリの問題を診断するには、データの収集が役立ちます。 収集できるデータの種類に関する情報は、MSAL プラットフォームの wiki を参照してください。

- ユーザーは、問題が発生したときに支援を求めることがあります。 ベスト プラクティスは、ログをキャプチャして一時的に格納し、ユーザーがそれらをアップロードできる場所を提供することです。 MSAL には、認証に関する詳細情報をキャプチャするログの拡張機能があります。
- 利用できる場合は、MSAL を介してテレメトリを有効にして、ユーザーがアプリにサインインしている方法についてデータを収集します。

## <a name="next-steps"></a>次の手順

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
