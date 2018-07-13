---
title: クイック スタート - Azure portal を使用して Analysis Services サーバーを作成する | Microsoft Docs
description: Azure で Analysis Services サーバーのインスタンスを作成する方法について説明します。
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 07/03/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 92209d82a6db18b7f1e1a5adbb52c1899bf4593f
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "37950701"
---
# <a name="quickstart-create-a-server---portal"></a>クイック スタート: サーバーを作成する - ポータル

このクイック スタートでは、ポータルを使って、Azure サブスクリプションに Analysis Services サーバー リソースを作成する方法について説明します。

## <a name="prerequisites"></a>前提条件 

* **Azure サブスクリプション**: [Azure 無料試用版](https://azure.microsoft.com/offers/ms-azr-0044p/)でアカウントを作成します。
* **Azure Active Directory**: サブスクリプションは、Azure Active Directory テナントに関連付けられている必要があります。 また、その Azure Active Directory でアカウントを使用して Azure にサインインしている必要があります。 詳細については、「[認証とユーザーのアクセス許可](analysis-services-manage-users.md)」を参照してください。

## <a name="log-in-to-the-azure-portal"></a>Azure Portal にログインする 

[ポータルにログインします](https://portal.azure.com)


## <a name="create-a-server"></a>サーバーの作成

1. **[リソースの作成]** > **[データ + 分析]** > **[Analysis Services]** の順にクリックします。

    ![ポータル](./media/analysis-services-create-server/aas-create-server-portal.png)

2. **[Analysis Services]** で、必要なフィールドを入力して **[作成]** をクリックします。
   
   * **[サーバー名]**: サーバーの参照に使われる一意の名前を入力します。
   * **[サブスクリプション]**: このサーバーを関連付けるサブスクリプションを選びます。
   * **[リソース グループ]**: 新しいリソース グループを作成するか、既存のリソース グループを選択します。 リソース グループは、Azure リソースのコレクション管理をサポートするように設計されています。 詳しくは、「[Azure リソース マネージャーの概要](../azure-resource-manager/resource-group-overview.md)」をご覧ください。
   * **[場所]**: この Azure データセンターの場所でサーバーをホストします。 最大のユーザー ベースに最も近い場所を選びます。
   * **[価格レベル]**: 価格レベルを選びます。 サンプル モデルのデータベースをテストしてインストールする場合、無料の **D1** 層を選択します。 詳しくは、「[Azure Analysis Services の価格](https://azure.microsoft.com/pricing/details/analysis-services/)」をご覧ください。 
    * **管理者**: 既定では、これはログインで使用するアカウントです。 Azure Active Directory から別のアカウントを選択できます。
    * **Backup Storage setting (バックアップ ストレージの設定)**: 省略可能です。 [ストレージ アカウント](../storage/common/storage-introduction.md)が既に存在する場合、モデル データベースのバックアップの既定値として指定することができます。 後で[バックアップと復元](analysis-services-backup.md)の設定を指定することもできます。
    * **ストレージ キーの有効期限**: 省略可能です。 ストレージ キーの有効期限を指定します。

通常、サーバーの作成にかかる時間は 1 分以下です。 **[Add to Portal]** (ポータルに追加) を選んだ場合は、ポータルに移動して新しいサーバーを確認します。 または、**[すべてのサービス]** > **[Analysis Services]** に移動して、サーバーの準備ができていることを確認します。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

不要になったら、サーバーを削除します。 サーバーの **[概要]** で、**[削除]** をクリックします。 

 ![クリーンアップ](./media/analysis-services-create-server/aas-create-server-cleanup.png)


## <a name="next-steps"></a>次の手順
このクイック スタートでは、Azure サブスクリプションにサーバーを作成する方法について学習しました。 サーバーを作成したら、(必要に応じて) サーバー ファイアウォールを構成することによってサーバーをセキュリティで保護できます。 また、ポータルからサーバーにデータ モデルの基本的なサンプルを追加することもできます。 サンプル モデルがあると、モデル データベース ロールの構成とクライアント接続のテストについて理解するのに役立ちます。 詳しくは、サンプル モデルの追加のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [クイック スタート: サーバーのファイアウォールの構成 - ポータル](analysis-services-qs-firewall.md)   
> [!div class="nextstepaction"]
> [チュートリアル: サーバーにサンプル モデルを追加する](analysis-services-create-sample-model.md)
