---
title: Azure Resource Manager テンプレートを使用して Azure Migrate プロジェクトを作成するためのクイックスタート。
description: このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用して Azure Migrate プロジェクトを作成する方法について説明します。
ms.date: 04/23/2021
author: rahulg1190
manager: bsiva
ms.author: rahugup
ms.topic: quickstart
ms.custom: subject-armqs, mode-arm
ms.openlocfilehash: e128a9dda0cbf6bfbda4a4918ce9a4538e5fcbdb
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131084579"
---
# <a name="quickstart-create-an-azure-migrate-project-using-an-arm-template"></a>クイックスタート: ARM テンプレートを使用して Azure Migrate プロジェクトを作成する

このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用して、Azure Migrate プロジェクト リカバリーを設定する方法について説明します。 Azure Migrate では、オンプレミスのサーバー、インフラストラクチャ、アプリケーション、データを評価し、Azure への移行を行うための一元化されたハブが提供されます。 Azure Migrate は、オンプレミスの VMware VM、Hyper-V VM、物理サーバー、その他の仮想化された VM、データベース、Web アプリ、仮想デスクトップの評価と移行をサポートしています。

このテンプレートでは、Azure のオンプレミスのサーバー、インフラストラクチャ、アプリケーション、データを評価して移行するための Azure Migrate プロジェクトが作成されます。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.migrate%2Fmigrate-project-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

アクティブな Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="review-the-template"></a>テンプレートを確認する

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/migrate-project-create/)からのものです。

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.migrate/migrate-project-create/azuredeploy.json":::

## <a name="deploy-the-template"></a>テンプレートのデプロイ

テンプレートをデプロイするには、**サブスクリプション**、**リソース グループ**、**プロジェクト名**、**場所** が必要です。

1. Azure にサインインしてテンプレートを開くには、 **[Azure へのデプロイ]** イメージを選択します。

   [![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.migrate%2Fmigrate-project-create%2Fazuredeploy.json)

2. 次の値を選択または入力します。

   :::image type="content" source="media/quickstart-create-migrate-project-template/create-migrate-project.png" alt-text="Azure Migrate プロジェクトを作成するためのテンプレート。":::

   - **サブスクリプション**:Azure サブスクリプションを選択します。
   - **リソース グループ**: 既存のグループを選択するか、 **[新規作成]** を選択してグループを追加します。
   - **リージョン**: 既定でリソース グループの場所になります。リソース グループの選択後は利用できなくなります。
   - **移行プロジェクトの名前**: コンテナーの名前を指定します。
   - **場所**: Azure Migrate プロジェクトとそのリソースのデプロイ先を選択します。

3. **[Review + create]\(確認と作成\)** ボタンをクリックしてデプロイを開始します。

## <a name="validate-the-deployment"></a>デプロイの検証

Azure Migrate プロジェクトが作成されたことを確認するには、Azure portal を使用します。


1. Azure portal の検索バーで「**Azure Migrate**」を検索して Azure Migrate に移動します。
2. [Servers, databases and web apps]\(サーバー、データベース、および Web アプリ\) タイルで、[**検出**、**評価**、**移行**] ボタンをクリックします。
3. デプロイに指定した値に従って、 **[Azure サブスクリプション]** と **[プロジェクト]** を選択します。


## <a name="next-steps"></a>次の手順

このクイックスタートでは、Azure Migrate プロジェクトを作成しました。 Azure Migrate とその機能について詳しくは、引き続き Azure Migrate の概要に関するページに進んでください。

> [!div class="nextstepaction"]
> [Azure Migrate の概要](migrate-services-overview.md)
>
