---
title: Visual Studio 2015 を使用した Azure App Services アプリケーションの展開 | Microsoft Docs
description: この記事では、Visual Studio 2015 と Azure SDK を使用して、API アプリケーション、Web アプリ、または モバイル アプリを Azure Germany に展開する方法について説明します。
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfdocs
ms.openlocfilehash: 1bf7bc84eed4303140753d9bcde85b3a68585155
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2021
ms.locfileid: "117029125"
---
# <a name="deploy-an-azure-app-service-app-by-using-visual-studio-2015"></a>Visual Studio 2015 を使用した Azure App Services の展開

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

この記事では、Visual Studio 2015 を使用して、Azure App Service アプリ (API アプリケーション、Web アプリ、または モバイル アプリ) を Azure Germany に展開する方法について説明します。

## <a name="prerequisites"></a>前提条件
* Visual Studio 2015 と Azure SDK をインストールして構成するには、「[Visual Studio prerequisites](../app-service/quickstart-dotnetcore.md#prerequisites)」 (Visual Studio の前提条件) を参照してください。
* [これらの手順](./germany-get-started-connect-with-vs.md)に従って、Visual Studioを構成し、Azure Germany アカウントへ接続します。 

## <a name="open-an-app-project-in-visual-studio"></a>Visual Studio でアプリケーションのプロジェクトを開く
Visual Studio でアプリの既存のアプリケーションのソリューションまたはプロジェクトを開くか、[プロジェクトを作成](../app-service/quickstart-dotnetcore.md?tabs=netframework48#create-an-aspnet-web-app)します。 次に、Visual Studio でアプリケーションを実行して、ローカルで動作することを確認します。

## <a name="deploy-to-azure-germany"></a>Azure Germany の展開
Visual Studio を Azure Germany アカウントに接続するように構成した後 (前提条件として実行済み)は、 [App Service アプリを展開する手順](../app-service/quickstart-dotnetcore.md)はグローバル Azure の場合とまったく同じです。

## <a name="next-steps"></a>次のステップ
* [Visual Studio を使用して ASP.NET Web アプリケーションを Azure App Service へ展開します。](../app-service/quickstart-dotnetcore.md)
* 一般的な App Service の情報については、「[App Service - API Apps documentation](../app-service/index.yml)」 (アプリケーションのサービス - API アプリケーションのドキュメント) を参照してください。
* 補足情報と更新情報については、[Azure Germany のブログ](/archive/blogs/azuregermany/)を参照してください。