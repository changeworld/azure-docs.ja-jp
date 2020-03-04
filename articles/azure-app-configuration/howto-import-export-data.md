---
title: Azure App Configuration でのデータのインポートまたはエクスポート
description: Azure App Configuration でのデータのインポートまたはエクスポート方法について説明します
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: lcozzens
ms.openlocfilehash: 2c074cbd99620a482b18cbe2dfcce8f987d78bd5
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/26/2020
ms.locfileid: "77619212"
---
# <a name="import-or-export-configuration-data"></a>構成データのインポートまたはエクスポート

Azure App Configuration はデータのインポート操作とエクスポート操作をサポートします。 これらの操作を使用して、構成データを一括処理し、お使いの App Configuration ストアとコード プロジェクト間でデータを交換することができます。 たとえば、1 つの App Configuration ストアをテスト用に、もう 1 つの App Configuration を実稼働用に設定することができます。 それらの間でアプリケーション設定をコピーできるので、データを 2 回入力する必要がなくなります。

この記事では、App Configuration でデータをインポートおよびエクスポートするためのガイドを提供します。

## <a name="import-data"></a>データのインポート

インポートでは、既存のソースから App Configuration ストアに構成データが取り込まれます。 インポート機能は、データを App Configuration ストアに移行したり、複数ソースのデータを集計したりするために使用します。 App Configuration は、JSON、YAML、またはプロパティ ファイルからのインポートをサポートしています。

データのインポートには、[Azure portal](https://portal.azure.com) または [Azure CLI](./scripts/cli-import.md) のいずれかを使用します。 Azure portal から次の手順を実行します。

1. お使いの App Configuration ストアを参照し、 **[操作]** メニューから **[インポート/エクスポート]** を選択します。

1. **[インポート]** タブで、 **[Source service]\(ソース サービス\)**  >  **[構成ファイル]** を選択します。

1. **[言語]** を選択し、目的の入力の種類を選択します。

1. **フォルダー** アイコンを選択し、インポートするファイルを参照します。

    ![ファイルのインポート](./media/import-file.png)

1. **[区切り記号]** を選択し、必要に応じて、インポートされるキー名に使用する **[プレフィックス]** を入力します。

1. 必要に応じて、 **[ラベル]** を選択します。

1. **[適用]** を選択してインポートを完了します。

    ![完了したファイルのインポート](./media/import-file-complete.png)

## <a name="export-data"></a>データのエクスポート

エクスポートでは、App Configuration に保存されている構成データが別の保存先に書き込まれます。 エクスポート機能は、お使いの App Configuration ストア内のデータを、お使いのアプリケーション コードに埋め込まれるファイルにデプロイ時に保存するときなどに使用します。

データのエクスポートには、[Azure portal](https://portal.azure.com) または [Azure CLI](./scripts/cli-export.md) のいずれかを使用します。 Azure portal から次の手順を実行します。

1. お使いの App Configuration ストアを参照し、 **[インポート/エクスポート]** を選択します。

1. **[エクスポート]** タブで、 **[Target service]\(ターゲット サービス\)**  >  **[構成ファイル]** を選択します。

1. 必要に応じて **[プレフィックス]** を入力し、エクスポートするキーの **[ラベル]** と特定の時点を選択します。

1. **[ファイルの種類]**  >  **[区切り記号]** を選択します。

1. **[適用]** を選択してエクスポートを完了します。

    ![完了したファイルのエクスポート](./media/export-file-complete.png)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [ASP.NET Core Web アプリケーションの作成](./quickstart-aspnet-core-app.md)  
