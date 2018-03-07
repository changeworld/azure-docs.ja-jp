---
title: "Azure Analysis Services サーバーのサンプル表形式モデルを作成する | Microsoft Docs"
description: "Azure Analysis Services でサンプル モデルを作成する方法を説明します。"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/27/2018
ms.author: owend
ms.openlocfilehash: 1cc36ebf4c410d4764eb28ab89d7f2a5e7749ae5
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/28/2018
---
# <a name="tutorial-create-a-sample-model"></a>チュートリアル: サンプル モデルを作成する

このチュートリアルでは、Adventure Works のサンプル モデルを作成します。 このサンプル モデルは、Adventure Works Internet Sales (1200) データ モデリング チュートリアルの完成版です。 サンプル モデルは、モデル管理のテスト、ツールおよびクライアント アプリケーションとの接続、モデル データのクエリなどに便利です。

## <a name="before-you-begin"></a>開始する前に

このチュートリアルを完了するには、次のものが必要です。

- Azure Analysis Services サーバー
- サーバー管理者の権限

## <a name="sign-in-to-the-azure-portal"></a>Azure ポータルにサインインします。

[Azure Portal](https://portal.azure.com/) にサインインします。

## <a name="create-a-sample-model"></a>サンプル モデルを作成する

1. サーバーの **[概要]** で、**[新しいモデル]** をクリックします。

    ![サンプル モデルを作成する](./media/analysis-services-create-sample-model/aas-create-sample-new-model.png)

2. **[新しいモデル]** > **[Choose a datasource]\(データソースの選択\)** で、**[サンプル データ]** が選ばれていることを確認して、**[追加]** をクリックします。

    ![サンプル データを選択する](./media/analysis-services-create-sample-model/aas-create-sample-data.png)

3. **[概要]** で、`adventureworks` サンプルが作成されたことを確認します。

    ![サンプル データを選択する](./media/analysis-services-create-sample-model/aas-create-sample-verify.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

サンプル モデルはキャッシュ メモリ リソースを使っています。 テスト用にサンプル モデルを使っていない場合は、サーバーからモデルを削除する必要があります。

> [!NOTE]
> 次の手順では、SSMS を使ってサーバーからモデルを削除する方法について説明します。 プレビュー段階の Web デザイナー機能を使ってモデルを削除することもできます。

1. SSMS の**オブジェクト エクスプローラー**で、**[接続]** > **[Analysis Services]** の順にクリックします。

2. **[サーバーに接続]** で、サーバー名を貼り付け、**[認証]** で **[Active Directory - MFA サポートで汎用]** を選び、ユーザー名を入力して、**[接続]** をクリックします。

    ![[サインイン]](./media/analysis-services-create-sample-model/aas-create-sample-cleanup-signin.png)

3. **オブジェクト エクスプローラー**で `adventureworks` サンプル データベースを右クリックして、**[削除]** をクリックします。

    ![サンプル データベースを削除する](./media/analysis-services-create-sample-model/aas-create-sample-cleanup-delete.png)

## <a name="next-steps"></a>次の手順 

[Power BI Desktop での接続](analysis-services-connect-pbi.md)   
[データベース ロールとユーザーの管理](analysis-services-database-users.md)


