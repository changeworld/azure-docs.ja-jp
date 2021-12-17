---
title: インクルード ファイル
description: インクルード ファイル
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: ae9b749f62dc74a6cb0dfea3701fd4ebac11c188
ms.sourcegitcommit: bd1a4e4df613ff24e954eb3876aebff533b317ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/23/2021
ms.locfileid: "107931578"
---
## <a name="enable-logging"></a>ログの有効化

デバッグと認証エラーのトラブルシューティングのシナリオを支援するために、Microsoft Authentication Library は組み込みのログ記録をサポートしています。 各ライブラリでのログ記録については、次の記事で説明されています。

:::row:::
    :::column:::
        - [MSAL.NET でのログ](../articles/active-directory/develop/msal-logging-dotnet.md)
        - [Android 用の MSAL でのログ記録](../articles/active-directory/develop/msal-logging-android.md)
        - [MSAL.js でのログ記録](../articles/active-directory/develop/msal-logging-js.md)
    :::column-end:::
    :::column:::
        - [iOS および macOS 用の MSAL でのログ記録](../articles/active-directory/develop/msal-logging-ios.md)
        - [MSAL for Java でのログ記録](../articles/active-directory/develop/msal-logging-java.md)
        - [Python 用の MSAL でのログ記録](../articles/active-directory/develop/msal-logging-python.md)
    :::column-end:::
:::row-end:::

データ収集に関する推奨事項を次に示します。

- ユーザーは、問題があるときに支援を求めることがあります。 ログをキャプチャして一時的に保存することをお勧めします。 ユーザーがログをアップロードできる場所を指定します。 MSAL には、認証に関する詳細情報をキャプチャするログの拡張機能があります。

- テレメトリが利用できる場合は、MSAL を介して有効にして、ユーザーがアプリにサインインしている方法についてデータを収集します。


## <a name="validate-your-integration"></a>統合の検証

[Microsoft ID プラットフォームの統合チェックリスト](../articles/active-directory/develop/identity-platform-integration-checklist.md)に従って、統合をテストします。

## <a name="build-for-resilience"></a>回復性のための構築

アプリの回復性を向上させる方法について説明します。 詳細については、「[開発する認証と認可のアプリケーションの回復性を向上させる](../articles/active-directory/fundamentals/resilience-app-development-overview.md)」を参照してください。
