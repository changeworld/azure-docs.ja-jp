---
title: アプリケーションの自動スケーリングを設定する
description: この記事では、Microsoft Azure portal または Azure CLI を使用してアプリケーションの自動スケーリングの設定をセットアップする方法について説明します。
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/03/2021
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 67b5976e8df650ac8961cdb428338ca8147878d4
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131846636"
---
# <a name="set-up-autoscale-for-applications"></a>アプリケーションの自動スケーリングを設定する

**この記事の適用対象:** ✔️ Java ✔️ C#

この記事では、Microsoft Azure portal または Azure CLI を使用してアプリケーションの自動スケーリングの設定をセットアップする方法について説明します。

自動スケーリングは、需要の変化に応じてアプリケーションで最高のパフォーマンスが実現されるようにする Azure Spring Cloud の組み込み機能です。 Azure Spring Cloud では、スケールアウトとスケールインがサポートされています。これには、アプリ インスタンスの数と負荷分散の変更が含まれます。
 
## <a name="prerequisites"></a>前提条件

以下の手順に従うためには、次の要件を満たす必要があります。

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。
* デプロイされた Azure Spring Cloud サービス インスタンス。 [Azure CLI を使用したアプリのデプロイに関するクイックスタート](./quickstart.md)に従って作業を開始してください。
* サービス インスタンスで既に作成してある少なくとも 1 つのアプリケーション。

## <a name="navigate-to-the-autoscale-page-in-the-azure-portal"></a>Azure portal で自動スケーリングのページに移動する

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. Azure Spring Cloud の **[概要]** ページに移動します。
3. サービスが含まれるリソース グループを選択します。
4. 左側のナビゲーション ペインのメニューにある **[設定]** の **[アプリ]** タブを選択します。
5. 自動スケーリングを設定するアプリケーションを選択します。 この例では、**demo** という名前のアプリケーションを選択します。 アプリケーションの **[概要]** ページが表示されます。
6. 左側のナビゲーション ペインのメニューにある **[設定]** の **[スケールアウト]** タブに移動します。
7. 自動スケーリングを設定するデプロイを選択します。 次のセクションで示す自動スケーリングのオプションが表示されます。

![自動スケーリング メニュー](./media/spring-cloud-autoscale/autoscale-menu.png)

## <a name="set-up-autoscale-settings-for-your-application-in-the-azure-portal"></a>Azure portal でアプリケーションに対する自動スケーリングの設定をセットアップする

自動スケーリングの要求管理には、次の 2 つのオプションがあります。

* 手動スケーリング: 固定のインスタンス数を維持します。 Standard レベルでは、最大 500 インスタンスにスケールアウトできます。 この値は、アプリケーションの個別の実行インスタンスの数を変更します。
* カスタム自動スケーリング: 任意のメトリックに基づき、任意のスケジュールでスケーリングします。

Azure portal でスケーリング方法を選択します。  次の図では、 **[カスタム自動スケーリング]** オプションとモードの設定を示します。

![カスタム自動スケーリング](./media/spring-cloud-autoscale/custom-autoscale.png)

## <a name="set-up-autoscale-settings-for-your-application-in-azure-cli"></a>Azure CLI でアプリケーションに対する自動スケーリングの設定をセットアップする

Azure CLI を使用して自動スケーリング モードを設定することもできます。  次のコマンドを使用すると、自動スケーリング設定と自動スケーリング規則が作成されます。

* 自動スケーリング設定を作成します。

   ```azurecli
   az monitor autoscale create -g demo-rg --resource /subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourcegroups/demo-rg/providers/Microsoft.AppPlatform/Spring/autoscale/apps/demo/deployments/default --name demo-setting --min-count 1 --max-count 5 --count 1
   ```

* 自動スケーリング規則を作成します。

   ```azurecli
   az monitor autoscale rule create -g demo-rg --autoscale-name demo-setting --scale out 1 --cooldown 1 --condition "tomcat.global.request.total.count > 100 avg 1m where AppName == demo and Deployment == default"
   ```

## <a name="upgrade-to-the-standard-tier"></a>Standard レベルにアップグレードする

Basic レベルを使用していて、その 1 つ以上の制限により制約を受けている場合は、Standard レベルにアップグレードできます。 そのためには、まず **[Standard レベル]** 列を選択し、次に **[アップグレード]** ボタンを選択して、 **[価格]** レベル メニューに移動します。

## <a name="next-steps"></a>次のステップ

* [Microsoft Azure の自動スケーリングの概要](../azure-monitor/autoscale/autoscale-overview.md)
* [Azure CLI での自動スケーリングの監視](/cli/azure/monitor/autoscale)
