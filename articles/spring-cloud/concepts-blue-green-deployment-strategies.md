---
title: Azure Spring Cloud におけるブルーグリーン デプロイ戦略
description: このトピックでは、Azure Spring Cloud におけるブルーグリーン デプロイへの 2 つのアプローチについて説明します。
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/12/2021
ms.custom: devx-track-java
ms.openlocfilehash: 8b019c5cc8e7e7a8ed132cc6adbf0d4c7d0495a4
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132486268"
---
# <a name="blue-green-deployment-strategies-in-azure-spring-cloud"></a>Azure Spring Cloud におけるブルーグリーン デプロイ戦略

この記事では、Azure Spring Cloud でのブルーグリーン デプロイのサポートについて説明します。

Azure Spring Cloud (Standard レベル以上) では、すべてのアプリについて 2 つのデプロイが許可されており、いずれか 1 つのみが運用環境のトラフィックを受信します。 このパターンは、通常、「ブルーグリーン デプロイ」と呼ばれます。 Azure Spring Cloud では、ブルーグリーン デプロイが、[継続的デリバリー (CD)](/devops/deliver/what-is-continuous-delivery) パイプラインおよび厳格な自動テストと共にサポートされているため、信頼性の高いアジャイル アプリケーション デプロイが可能になります。

## <a name="alternating-deployments"></a>交互のデプロイ

Azure Spring Cloud でブルーグリーン デプロイを実装する最も簡単な方法は、2 つの固定デプロイを作成し、運用環境のトラフィックを受信していないデプロイに、常にデプロイすることです。 [Azure Pipelines の Azure Spring Cloud タスク](/azure/devops/pipelines/tasks/deploy/azure-spring-cloud)を使用すると、`UseStagingDeployment` フラグを `true` に設定するだけで、このようなデプロイを実現できます。

ここでは、交互のデプロイのアプローチが実際にどのように機能するかを示します。

アプリケーションに `deployment1` と `deployment2` の 2 つのデプロイがあると想定します。 現在、`deployment1` が運用デプロイとして設定されており、アプリケーションのバージョン `v3` を実行しています。

そのため、`deployment2` がステージング環境のデプロイになります。 したがって、継続的デリバリー (CD) パイプラインの実行準備が整うと、アプリの次のバージョン、すなわち、バージョン `v4` が、ステージング環境のデプロイ `deployment2` にデプロイされます。

![2 つのデプロイ: deployment1 が運用環境のトラフィックを受信する](media/spring-cloud-blue-green-patterns/alternating-deployments-1.png)

`deployment2` で `v4` が起動された後、プライベート テスト エンドポイントを使用して、それに対して自動および手動のテストを実行し、すべての期待値が `v4` によって満たされることを確認できます。

![この時点で、deployment2 に V4 が デプロイされ、テストを受ける](media/spring-cloud-blue-green-patterns/alternating-deployments-2.png)

`v4` を信頼できる場合は、`deployment2` を運用デプロイとして設定して、すべての運用トラフィックを受信するように設定することができます。 ロール バックを必要とする重大な問題が検出された場合は、`deployment1` での `v3` の実行が維持されます。

![deployment2 上の V4 によって、運用環境のトラフィックが受信されるようになる](media/spring-cloud-blue-green-patterns/alternating-deployments-3.png)

この時点では、`deployment1` がステージング環境のデプロイです。 そのため、デプロイ パイプラインの次の実行は `deployment1` にデプロイされます。

![V5 が deployment1 にデプロイされる](media/spring-cloud-blue-green-patterns/alternating-deployments-4.png)

この時点で、`deployment1` のプライベート テスト エンドポイントで `V5` をテストできます。

![V5 が deployment1 でテストされる](media/spring-cloud-blue-green-patterns/alternating-deployments-5.png)

すべての期待値が `v5` によって満たされた後、最終的に、運用デプロイとして `deployment1` をもう一度設定します。これにより、運用環境のすべてのトラフィックは `v5` によって受信されるようになります。

![deployment1 で V5 によって トラフィックが受信される](media/spring-cloud-blue-green-patterns/alternating-deployments-6.png)

### <a name="tradeoffs-of-the-alternating-deployments-approach"></a>交互のデプロイ アプローチのトレードオフ

交互のデプロイ アプローチは、新しいデプロイを作成する必要がないため、シンプルで高速です。 ただし、次のセクションで説明するように、いくつかの欠点があります。

#### <a name="persistent-staging-deployment"></a>永続的なステージング環境のデプロイ

ステージング環境のデプロイは常に実行されたままであり、そのため、Azure Spring Cloud インスタンスのリソースが消費されます。 このため、Azure Spring Cloud 上の各アプリケーションのリソース要件は実質的に 2 倍になります。

#### <a name="the-approval-race-condition"></a>承認の競合状態

上記のアプリケーションで、アプリケーションの新しい各バージョンが運用環境のトラフィックを受信できるようになる前に、リリース パイプラインによって手動での承認が要求されると想定してください。 ここでは、1 つのバージョン (`v6`) がステージング環境のデプロイで手動承認を待つ間に、デプロイ パイプラインが再度実行され、新しいバージョン (`v7`) で上書きされるリスクが生じます。 その後、`v6` の承認が付与されると、`v6` をデプロイしたパイプラインによってステージング環境のデプロイが運用として設定されます。 しかし、この時点では、それは承認されている `v6` ではなく、承認されていない `v7` になります。当該のデプロイにはそれがデプロイされ、トラフィックの受信が行われます。

![承認の競合状態](media/spring-cloud-blue-green-patterns/alternating-deployments-race-condition.png)

以前のすべてのバージョンのデプロイ フローが完了するか中止されるまで、1 つのバージョンのデプロイ フローを開始できないようにすることで、競合状態を防止できる場合があります。 承認の競合状態を回避するもう 1 つの方法は、以下で説明する名前付きデプロイ アプローチの使用です。

## <a name="named-deployments"></a>名前付きデプロイ

名前付きデプロイ アプローチでは、デプロイされるアプリケーションの新しいバージョンごとに新しいデプロイが作成されます。 そのアプリケーション用にカスタマイズされたデプロイでアプリケーションがテストされた後、そのデプロイが運用デプロイとして設定されます。 以前のバージョンを含むデプロイについて、ロールバックが不要であることを確信できるだけの十分な時間、存続を許可することができます。

次の図では、デプロイ `deployment-v5` でバージョン `v5` が実行されています。 デプロイがこのバージョン専用に作成されたため、ここでは、デプロイの名前にバージョンが含まれます。 当初、これ以外のデプロイは存在しません。 ここで、バージョン `v6` をデプロイするために、デプロイ パイプラインによって新しいデプロイ `deployment-v6` が作成され、そこにアプリのバージョン `v6` が配置されます。

![名前付きデプロイでの新しいバージョンのデプロイ](media/spring-cloud-blue-green-patterns/named-deployment-1.png)

別のバージョンが同時にデプロイされるリスクはありません。 まず、2 つのデプロイが既に存在している間は、Azure Spring Cloud では、3 つ目のデプロイの作成は許可されません。 次に、2 つを超えるデプロイを持つことができたとしても、各デプロイは、それが含むアプリケーションのバージョンによって識別されます。 したがって、`v6` のデプロイを調整するパイプラインによって運用デプロイとしての設定が試みられるのは `deployment-v6` のみです。

![新しいバージョンが、名前付きデプロイで運用環境のトラフィックを受信する](media/spring-cloud-blue-green-patterns/named-deployment-2.png)

新しいバージョン用に作成されたデプロイによって運用環境のトラフィックが受信された後、将来のデプロイに向けてスペースを確保するために、以前のバージョンを含むデプロイを削除する必要があります。 新しいバージョンで重大な問題を検出した場合に、前のバージョンにロールバックできるように、数分または数時間遅らせることをお勧めします。

![フォールバック期間の後に、以前のデプロイを削除する](media/spring-cloud-blue-green-patterns/named-deployment-3.png)

### <a name="tradeoffs-of-the-named-deployments-approach"></a>名前付きデプロイ アプローチのトレードオフ

名前付きデプロイ アプローチには、次の利点があります。

* 承認の競合状態が回避されます。
* ステージング環境のデプロイが使用されない場合に、それを削除することで、リソースの消費量が削減されます。

ただし、次のセクションで説明するように、欠点もあります。

#### <a name="deployment-pipeline-failures"></a>デプロイ パイプラインのエラー

デプロイが開始され、ステージング環境のデプロイが削除されるまでの間に、別のデプロイ パイプラインの実行が試みられると、失敗します。 パイプラインは新しいデプロイの作成を試みますが、それにより、エラーが発生します。これは、Azure Spring Cloud アプリケーションごとに許可されるデプロイが 2 つのみであるためです。

そのため、デプロイ オーケストレーションには、失敗したデプロイ プロセスを後で再試行する手段、または、以前のすべてのバージョンでフローが完了するまで、各バージョンのデプロイ フローをキューに残す手段を持たせる必要があります。

## <a name="next-steps"></a>次の手順

* [Azure Spring Cloud へのアプリケーション デプロイを自動化する](./how-to-cicd.md)
