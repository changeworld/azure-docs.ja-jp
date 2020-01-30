---
title: Web API を呼び出しているモバイル アプリを運用環境に移動する - Microsoft ID プラットフォーム | Azure
description: Web API を呼び出すモバイル アプリを構築する方法について説明します (運用環境への移行)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 1a82fc7dc1b18fa21657170af29f7de7e84d7c1f
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/23/2020
ms.locfileid: "76702030"
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

## <a name="next-steps"></a>次のステップ

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

「[サンプル | デスクトップおよびモバイルのパブリック クライアント アプリ](sample-v2-code.md#desktop-and-mobile-public-client-apps)」から利用できる追加のサンプルをお試しください。
