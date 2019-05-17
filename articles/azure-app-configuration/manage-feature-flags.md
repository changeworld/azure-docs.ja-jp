---
title: Azure App Configuration を使用して機能フラグを管理するためのチュートリアル |Microsoft Docs
description: このチュートリアルでは、Azure App Configuration を使用して、お使いのアプリケーションとは別に機能フラグを管理する方法について説明します
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: d995a2e9f0d05dc3b0853036e8fb3c04ccdfab96
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/08/2019
ms.locfileid: "65412291"
---
# <a name="tutorial-manage-feature-flags-in-azure-app-configuration"></a>チュートリアル:Azure App Configuration で機能フラグを管理する

すべての機能フラグを Azure App Configuration に保存して、1 つの場所から管理できます。 機能フラグ専用に設計された、**機能マネージャー**と呼ばれるポータル UI があります。 さらに、App Configuration は .NET Core 機能フラグのデータ スキーマをネイティブにサポートしています。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * App Configuration で機能フラグを定義および管理する。
> * お使いのアプリケーションから機能フラグにアクセスする。

## <a name="create-feature-flags"></a>機能フラグの作成

Azure portal 内の App Configuration 用の**機能マネージャー**は、お使いのアプリケーションで使用する機能フラグを作成および管理するための UI を提供します。 新しい機能フラグを追加するには、次の手順に従います。

1. **[Feature Manager]\(機能マネージャー\)** > **[+ Create]\(+ 作成\)** を選択し、機能フラグを追加します。

    ![機能フラグの一覧](./media/azure-app-configuration-feature-flags.png)

2. 機能フラグの一意のキー名を入力します。 コード内のフラグを参照するには、この名前が必要です。

3. 必要に応じて、機能フラグにわかりやすい説明を指定します。

4. 機能フラグの初期状態を設定します。 通常は *On* または *Off* のみです。

    ![機能フラグの作成](./media/azure-app-configuration-feature-flag-create.png)

5. 状態が *On* の場合は、必要に応じて **[Add filter]\(フィルターの追加\)** を使用して修飾する追加条件を指定します。 組み込みまたはカスタムのフィルター キーを入力して、パラメーターを関連付けます。 組み込みフィルターには次のものがあります。

    | キー | JSON パラメーター |
    |---|---|
    | Microsoft.Percentage | {"Value":0 - 100 パーセント} |
    | Microsoft.TimeWindow | {"Start":UTC 時間, "End":UTC 時間} |

    ![機能フラグのフィルター](./media/azure-app-configuration-feature-flag-filter.png)

## <a name="update-feature-flag-states"></a>機能フラグの状態の更新

機能フラグの状態の値を変更するには、次の手順に従います。

1. **[Feature Manager]\(機能マネージャー\)** を選択します。

2. 変更する機能フラグの右側の **[...]** > **[Edit]\(編集\)** をクリックします。

3. 機能フラグの新しい状態を設定します。

## <a name="access-feature-flags"></a>機能フラグへのアクセス

**機能マネージャー**で作成された機能フラグは、通常のキー値として保存および取得されます。 これは、特別な名前空間プレフィックス *.appconfig.featureflag* の下に保持されます。 **Configuration Explorer** を使用して、基になるキー値を表示できます。 お使いのアプリケーションは、App Configuration の構成プロバイダー、SDK、コマンドライン拡張機能および REST API を使用してこれらを取得できます。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、App Configuration を使用して、機能フラグとその状態を管理する方法について説明しました。 App Configuration と ASP.NET Core での機能管理サポートの詳細については、次のリソースをご覧ください。

* [.NET Core アプリ内で機能フラグを使用する](./use-feature-flags-dotnet-core.md)
