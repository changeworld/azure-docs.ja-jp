---
title: Colony を使用してアプリケーション中心の環境を作成する
description: この記事では、Colony と Azure を使用して、アプリケーション中心の環境を作成する方法について説明します。
ms.topic: how-to
ms.date: 11/09/2021
ms.openlocfilehash: 9ce80a7467ac94a69227cc9616c72c622a6f575f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132347245"
---
# <a name="create-an-application-centric-environment-with-colony"></a>Colony を使用してアプリケーション中心の環境を作成する

[Quali CloudShell Colony](https://azuremarketplace.microsoft.com/marketplace/apps/quali_systems.cloudshell_colony?tab=Overview) は、インフラストラクチャの自動化を大規模に提供する SaaS (サービスとしてのソフトウェア) プラットフォームです。 Colony を利用することで、Azure や Kubernetes のような複雑なクラウド環境に開発者が効率よくアプリケーションをデプロイすることができます。 アプリケーションの運用までのデプロイ プロセス全体にわたって、Azure DevTest Labs が Colony によって補完されます。 この記事では、Colony と Azure を使用して、アプリケーション中心の環境を作成する方法について説明します。

## <a name="set-up-the-environment-with-colony-and-microsoft-azure"></a>Colony と Microsoft Azure を使用して環境を設定する

1. [Colony](https://azuremarketplace.microsoft.com/marketplace/apps/quali_systems.cloudshell_colony?tab=Overview) の無料試用版にサインアップします。

    :::image type="content" source="./media/create-application-centric-environment/free-trial.png" alt-text="Colony の無料試用版サインアップ画面のスクリーンショット。":::
1. [Azure アカウントをリンク](https://colonysupport.quali.com/hc/articles/360008222234)させます。

    :::image type="content" source="./media/create-application-centric-environment/welcome.png" alt-text="Colony のウェルカム画面のスクリーンショット。":::
1. 自分のスペースにユーザーを招待します。
1. [初めてのブループリントを YAML ファイルを使用して作成](https://colonysupport.quali.com/hc/articles/360001680807-Steps-to-Developing-a-Blueprint)します。
    1. GitHub または BitBucket のブループリント リポジトリを Colony にリンクさせます。
    1. Colony のサンプル ブループリントをひな形として適宜変更を加えます。

        :::image type="content" source="./media/create-application-centric-environment/performance-stress-tests.png" alt-text="ストレス テストを示すスクリーンショット。":::
    1. 他のユーザーが使用できるようにブループリントを公開します。
1. Colony を使用して、サンドボックスでアプリケーション環境を起動します。

    :::image type="content" source="./media/create-application-centric-environment/blueprints.png" alt-text="Colony を使用して、サンドボックスでアプリケーション環境を起動する画面のスクリーンショット。":::

Azure Pipelines の継続的インテグレーションと継続的デリバリー (CI/CD) ワークフローの一部としてブループリントを統合することもできます。 手順については、「[Azure DevOps (VSTS) からサンドボックスを起動する](https://colonysupport.quali.com/hc/articles/360008464234)」を参照してください。

:::image type="content" source="./media/create-application-centric-environment/devops-pipeline.png" alt-text="Azure Pipelines パイプラインへの接続を示すスクリーンショット。":::

## <a name="next-steps"></a>次のステップ

[Colony のデモを依頼する](https://info.quali.com/cloudshell-colony-demo-request)
