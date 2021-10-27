---
title: Microsoft Learn で Bicep を検出する
description: Bicep について Microsoft Learn で使用できるユニットの概要について説明します。
ms.topic: conceptual
ms.date: 10/18/2021
ms.openlocfilehash: 086e9e4c9552e48ef4b9e8cf8a737cd2624ddd66
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2021
ms.locfileid: "130176303"
---
# <a name="bicep-on-microsoft-learn"></a>Microsoft Learn での Bicep

Bicep が Azure へのデプロイを簡略化し、加速するためにどのように役立つかを確認できます。 Microsoft Learn に関する多くのハンズオン コースをご覧ください。

## <a name="get-started"></a>はじめに

この 2 つのラーニング パスから始められます。

:::row:::
:::column:::
<img src="media/learn-bicep/bicep-deploy-manage.svg" width="101" height="120" alt="The trophy for the Deploy and manage resources in Azure by using Bicep learning path." role="presentation"></img>

[パート 1: Bicep を使用して Azure でリソースをデプロイして管理する](/learn/paths/bicep-deploy/)

:::column-end:::
:::column:::
<img src="media/learn-bicep/bicep-collaborate.svg" width="101" height="120" alt="The trophy for the Build Azure infrastructure in a team environment by using Bicep learning path." role="presentation"></img>

[パート 2: Bicep を使用してチーム環境で Azure インフラストラクチャを構築する](/learn/paths/bicep-collaborate/)

:::column-end:::
:::row-end:::

## <a name="azure-pipelines-and-github-actions-modules"></a>Azure Pipelines と GitHub Actions モジュール

前のラーニング パスに加えて、次のモジュールには Azure Pipelines および GitHub Actions に関連する Bicep コンテンツが含まれています。

| Learn モジュール | 説明 |
| ------------ | ----------- |
| [Azure Pipelines を使用して最初の Bicep デプロイ パイプラインを作成する](/learn/modules/build-first-bicep-deployment-pipeline-using-azure-pipelines/) | Bicep コード用の基本的なデプロイ パイプラインを構築します。 サービス接続を使用して、Azure へのパイプラインを安全に識別します。 トリガーを使用してパイプラインを実行するタイミングを構成します。 |
| [GitHub Actions を使用して最初の Bicep デプロイ ワークフローを作成する](/learn/modules/build-first-bicep-deployment-pipeline-using-github-actions/) | Bicep コード用の基本的なデプロイ ワークフローを構築します。 シークレットを使用し、Azure に対する GitHub Actions ワークフローを安全に識別し、トリガーとスケジュールを使用してワークフローを実行するタイミングを設定します。 |
| [サービス プリンシパルを使用して Azure デプロイ パイプラインを認証する](/learn/modules/authenticate-azure-deployment-pipeline-service-principals/) | サービス プリンシパルを使用すると、Azure でデプロイ パイプラインを安全に認証できます。 このモジュールでは、サービス プリンシパルの概要、動作方法、作成方法について説明します。 また、それらに Azure リソースへのアクセス許可を付与する方法についても説明します。これにより、パイプラインで Bicep ファイルをデプロイできます。 |
| [Azure Pipelines を使用して Bicep のコードをテストする](/learn/modules/test-bicep-code-using-azure-pipelines/) | デプロイ パイプラインで Bicep コードを検証してテストします。 デプロイする前に、リンティング、プレフライト検証、What-If 操作を使用して Azure の変更を検証し、各デプロイの後でリソースをテストします。 |
| [Bicep と Azure Pipelines を使用して複数の環境を管理する](/learn/modules/manage-multiple-environments-using-bicep-azure-pipelines/) | Bicep コードを複数の環境にデプロイすることで、デプロイの品質を向上させます。 パイプライン テンプレートを使用してコードの重複を減らし、環境ごとにパラメーター値を設定します。 |

## <a name="next-steps"></a>次のステップ

* Bicep の概要については、[Bicep のクイックスタート](quickstart-create-bicep-use-visual-studio-code.md)に関する記事を参照してください。
* Bicep ファイルを向上させるための推奨事項については、「[Bicep のベスト プラクティス](best-practices.md)」を参照してください。
