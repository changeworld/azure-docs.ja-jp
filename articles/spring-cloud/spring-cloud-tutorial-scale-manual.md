---
title: チュートリアル:Azure Spring Cloud でアプリケーションをスケーリングする | Microsoft Docs
description: このチュートリアルでは、Azure portal で Azure Spring Cloud のアプリケーションをスケーリングする方法を学習します
ms.service: spring-cloud
ms.topic: tutorial
ms.author: jeconnoc
author: jpconnock
ms.date: 10/06/2019
ms.openlocfilehash: cce7562c74577f6fd545bcaed3ee3e0968fd40b4
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132889"
---
# <a name="tutorial-scale-an-application-in-azure-spring-cloud"></a>チュートリアル:Azure Spring Cloud でアプリケーションをスケーリングする

このチュートリアルでは、Azure portal で Azure Spring Cloud ダッシュボードを使用して、マイクロサービス アプリケーションをスケーリングする方法を示します。 アプリケーションをスケールアップおよびスケールダウンするには、仮想 CPU (vCPU) の数とメモリの量を変更します。 アプリケーションをスケールインおよびスケールアウトするには、アプリケーションのインスタンスの数を変更します。 完了すると、サービス内の各アプリケーションをすばやく手動で調整する方法がわかるようになります。 スケーリングは数秒で有効になり、コードの変更や再デプロイは不要です。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次のものが必要です。
* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。 
* デプロイされた Azure Spring Cloud サービス インスタンス。  [クイックスタート](spring-cloud-quickstart-launch-app-cli.md)に従って始めてください。
* そのサービス インスタンスで既に作成してある少なくとも 1 つのアプリケーション。


## <a name="navigate-to-the-scale-page-in-the-azure-portal"></a>Azure portal で [スケール] ページに移動する

1. [Azure Portal](https://portal.azure.com) にログインします。

1. Azure Spring Cloud の **[概要]** ページに移動します。

1. サービスが含まれるリソース グループを選択します。

1. 左側のメニューにある **[設定]** 見出しの下の **[アプリ]** タブに移動します。

1. スケーリングするアプリケーションを選択します。 この例では、"account-service" という名前のアプリケーションをスケーリングします。 アプリケーションの **[概要]** ページが表示されます。

1. 左側のメニューにある **[設定]** 見出しの下の **[スケール]** タブに移動します。 次のセクションに示されているスケーリング属性のオプションが表示されます。

## <a name="scale-your-application"></a>アプリケーションのスケーリング

スケーリング属性を変更できます。 次の点に注意してください。

* **CPU**: 許可される CPU の最大数は、アプリケーション インスタンスごとに 4 つです。 アプリケーションの CPU の総数は、ここで設定した値にアプリケーション インスタンスの数を掛けた値になります。

* **メモリ (GB)** : 許可されるメモリの最大量は、アプリケーション インスタンスごとに 8 GB です。  アプリケーションのメモリの総量は、ここで設定した値にアプリケーション インスタンスの数を掛けた値になります。

* **App instance count (アプリ インスタンス数)** :Standard レベルでは、最大 20 個のインスタンスをスケールアウトできます。 この値は、マイクロサービス アプリケーションの個別の実行インスタンスの数を変更します。

スケーリング設定を適用するには、必ず **[保存]** ボタンをクリックしてください。

![Azure portal のスケール サービス](media/spring-cloud-tutorial-scale-manual/scale-up-out.png)

数秒後、行った変更が **[概要]** ページに表示され、詳細は **[アプリケーション インスタンス]** タブで確認できるようになります。スケーリングには、コードの変更や再デプロイは不要です。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Spring Cloud アプリケーションを手動でスケーリングする方法について学習しました。  アプリケーションを監視する方法については、次のチュートリアルに進みます。

> [!div class="nextstepaction"]
> [アプリケーションを監視する方法を学習する](spring-cloud-tutorial-distributed-tracing.md)
