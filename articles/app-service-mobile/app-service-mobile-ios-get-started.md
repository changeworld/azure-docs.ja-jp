---
title: iOS アプリの作成
description: このチュートリアルに従って、Objective-C または Swift で iOS を開発するための Azure モバイル アプリ バックエンドの使用を開始します。
ms.assetid: 6461a899-9340-42dd-b118-ffc5ba00e846
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: 1b1114a22d33689f485aa228a8a1cf65eba719da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461489"
---
# <a name="create-an-ios-app"></a>iOS アプリの作成

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]
## <a name="overview"></a>概要

このチュートリアルでは、[Azure App Service Mobile Apps](app-service-mobile-value-prop.md) (クラウドのバックエンド サービス) を iOS アプリに追加する方法について説明します。 最初の手順で、Azure 上に新しいモバイル バックエンドを作成します。 次に、Azure にデータを保存する単純な "*Todo リスト*" iOS サンプル アプリをダウンロードします。

このチュートリアルを完了するには、Mac と [Azure アカウント](https://azure.microsoft.com/pricing/free-trial/) が必要です。

## <a name="create-a-new-azure-mobile-app-backend"></a>新しい Azure モバイル アプリ バックエンドを作成する

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>データベース接続を作成し、クライアントとサーバー プロジェクトを構成する
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-ios-app"></a>iOS アプリを実行する

[!INCLUDE [app-service-mobile-ios-run-app](../../includes/app-service-mobile-ios-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
