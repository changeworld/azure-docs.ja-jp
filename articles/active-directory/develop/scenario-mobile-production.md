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
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d37d2de561a6f5841bf17a47fef86ad7639750d5
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2019
ms.locfileid: "65080111"
---
# <a name="mobile-app-that-calls-web-apis---move-to-production"></a>Web API を呼び出すモバイル アプリ - 運用環境への移行

この記事では、アプリの品質と信頼性を向上させて運用環境に移行することに関する詳細情報を提供します。

## <a name="handling-errors-in-mobile-applications"></a>モバイル アプリケーションでのエラーの処理

これまで取り上げてきた各種のフローには、発生する可能性があるさまざまなエラー条件があります。 処理する主なシナリオは、サイレント障害とフォールバックの相互作用です。 また、ネットワークが利用できない状況、サービスの停止、管理者の同意が必要、およびその他のシナリオ固有のケースなどの追加条件についても、運用に際して検討する必要があります。

各 MSAL ライブラリには、これらの条件の処理について詳しく説明するサンプル コードと wiki コンテンツがあります。

- [MSAL Android Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [MSAL iOS Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigating-and-investigating-issues"></a>問題の緩和と調査

データの収集は、アプリでの問題の診断に役立ちます。 収集できるデータの種類の詳細については、それぞれの MSAL プラットフォームの wiki を参照してください。

- 問題が発生しているときにユーザーがヘルプを求めることがあります。 ベスト プラクティスは、一時的にログをキャプチャして格納し、ユーザーにそれらをどこかにアップロードさせることです。 MSAL には、認証に関する詳細情報をキャプチャするログの拡張機能があります。
- 利用できる場合は、MSAL を介してテレメトリを有効にして、ユーザーがアプリにサインインしている方法についてデータを収集します。

## <a name="testing-your-app"></a>アプリケーションのテスト

アプリは必ず、[統合チェックリスト](identity-platform-integration-checklist.md)に照らしてテストしてください。

## <a name="next-steps"></a>次の手順

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
