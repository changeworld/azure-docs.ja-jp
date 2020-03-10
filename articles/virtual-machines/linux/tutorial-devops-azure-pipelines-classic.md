---
title: チュートリアル - Azure における IaaS と PaaS のために統合された DevOps
description: このチュートリアルでは、Azure Pipelines を使用して、Azure VM に対するアプリの継続的インテグレーション (CI) と継続的デプロイ (CD) を設定する方法について説明します。
author: ushan
manager: jpconnock
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 1/16/2020
ms.author: ushan
ms.custom: devops
ms.openlocfilehash: 5707a99b329915b35131fe793b0dfabd02348677
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/28/2020
ms.locfileid: "77912530"
---
# <a name="tutorial-integrated-devops-for-iaas-and-paas-on-azure"></a>チュートリアル:Azure における IaaS と PaaS のために統合された DevOps

チームは、Azure 上のエンドツーエンドのソリューションを通じて、アプリケーション ライフサイクルの各フェーズ (計画、開発、デリバリー、運用) に DevOps プラクティスを導入することができます。 

以下に示すのは、クラウド ワークロードを省力化する Azure サービスの例です。組み合わせると、きわめて有益なシナリオを実現することができます。
これらのテクノロジに人やプロセスを組み合わせることで、チームは顧客に絶えず価値を提供することができます。 

- Azure: https://portal.azure.com - クラウド ワークロードを構築するためのポータル。 シンプルな Web アプリから複雑なクラウド アプリケーションまで、すべてを管理、監視します。 
- Azure DevOps: https://dev.azure.com - 最新の一連の開発サービスにより、計画のスマート化、コラボレーションの促進、出荷の短期間化が実現します。 
- Azure Machine Learning Studio: https://ml.azure.com - データを準備し、機械学習モデルをトレーニングしてデプロイします。 
 

Azure DevOps は、Azure リソースの継続的インテグレーションと継続的デリバリーによって DevOps プロセスの各段階を自動化する、組み込みの Azure サービスです。
Azure と Azure DevOps を使用すれば、仮想マシン、Web アプリ、Kubernetes など、アプリで使用されるリソースが何であれ、Infrastructure as Code、継続的インテグレーション、継続的なテスト、継続的デリバリー、継続的監視を導入することができます。  
![AzDevOps_portalView](media/tutorial-devops-azure-pipelines-classic/azdevops-view.png) 
 
 
## <a name="iaas---configure-cicd"></a>IaaS - CI/CD の構成 
Azure Pipelines では、仮想マシンにデプロイするための機能が完備された CI/CD 自動化ツールのセットが提供されます。 Azure VM に対する継続的デリバリー パイプラインは、Azure portal から直接構成できます。 このドキュメントでは、Azure portal から複数マシンをデプロイするための CI/CD パイプラインの設定に関連した手順を説明します。 仮想マシンで CI/CD を構成してください。

仮想マシンは、[デプロイ グループ](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups)にターゲットとして追加することができます。また、複数マシンのローリング アップデートの対象にすることができます。 デプロイ グループ内のデプロイ履歴ビューを使うと、VM からパイプライン、さらにコミットまでの追跡が可能になります。 
 
**ローリング アップデート**: ローリング デプロイでは、反復するたびに固定された一連のマシン (ローリング セット) を対象に、アプリケーションの以前のバージョンのインスタンスがそのアプリケーションの新しいバージョンのインスタンスに置き換えられます。 仮想マシンへのローリング アップデートを構成する方法を順を追って見ていきましょう。  
"**仮想マシン**" へのローリング アップデートは、Azure portal 内で継続的デリバリー オプションを使用して構成することができます。 

その詳細な手順は次のとおりです。 
1. Azure portal にサインインして仮想マシンに移動します。 
2. VM の左ペインで、 **[継続的デリバリー]**   メニューに移動します。 次に、 **[構成]** をクリックします。 
   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azdevops-configure.png) 
3. 構成パネルで、[Azure DevOps 組織] をクリックして既存のアカウントを選択するか、新たに作成します。 次に、パイプラインを構成するプロジェクトを選択します。  
   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azdevops-project.png) 
4. デプロイ グループは、"開発"、"テスト"、"UAT"、"運用" などの物理環境を表す、デプロイ ターゲット マシンの論理的な集まりです。 新しいデプロイ グループを作成することも、既存のデプロイ グループを選択することもできます。 マシンには、必要に応じてロールのタグを付けることができます。 たとえば、"web"、"db" があります。  
5. 継続的デリバリー パイプラインを構成するには、ダイアログで **[OK]** をクリックします。 
6. 完了すると、仮想マシンにデプロイするよう継続的デリバリー パイプラインが構成されます。  
   ![AzDevOps_pipeline](media/tutorial-devops-azure-pipelines-classic/azdevops-pipeline.png)
7. 仮想マシンへのデプロイが進行中であることがわかります。 リンクをクリックすると、パイプラインに移動できます。 **Release-1** をクリックしてデプロイを表示します。 または、 **[編集]** をクリックして、リリース パイプラインの定義を変更することもできます。 
8. 構成する VM が複数ある場合は、他の VM がデプロイ グループに追加されるように手順 2 から手順 5 を繰り返します。 
9. 完了したら、パイプラインの定義をクリックして、Azure DevOps 組織に移動し、リリース パイプラインの **[編集]** をクリックします。 
   ![AzDevOps_edit_pipeline](media/tutorial-devops-azure-pipelines-classic/azdevops-edit-pipeline.png)
10. **[dev]\(開発\)** ステージの **[1 job, 1 task]\(1 ジョブ、1 タスク\)** というリンクをクリックします。 **[デプロイ]** フェーズをクリックします。  
   ![AzDevOps_deploymentGroup](media/tutorial-devops-azure-pipelines-classic/azdevops-deployment-group.png)
11. 右側の構成ペインから、既定では、すべてのターゲットへのローリング アップデートが並列で実行されるようにパイプラインが構成されていることがわかります。 デプロイは、1 つずつ、またはスライダーを使用してパーセンテージを基準にして行われるように構成することができます。  
  
  
**カナリア**では、一部の少数のユーザーにゆっくり変更を展開することによりリスクを軽減します。 新バージョンへの信頼度が上がってきたら、インフラストラクチャ内のより多くのサーバーへのリリースを開始し、より多くのユーザーを誘導することができます。 "**仮想マシン**" へのカナリア デプロイは、Azure portal から継続的デリバリー オプションを使用して構成することができます。 その詳細な手順は次のとおりです。 
1. Azure portal にサインインして仮想マシンに移動します。 
2. 前のセクションの手順 2 から手順 5 に従って、デプロイ グループに複数の VM を追加します。 
3. カナリア デプロイに含める VM にユーザー定義タグを追加します。 たとえば、"canary" とします。
4. それらの VM のパイプラインが構成されたら、パイプラインをクリックし、Azure DevOps 組織を起動して、パイプラインを**編集**し、**開発**ステージに移動します。 "canary" というフィルターにタグを追加します。 
5. デプロイ グループ フェーズをもう 1 つ追加し、そのデプロイ グループ内の他の VM をターゲットにするためのタグを使用してそのフェーズを構成します。  
6. 必要に応じて、カナリア デプロイを昇格または拒否できる手動の検証手順を構成します。 
   ![AzDevOps_Canary](media/tutorial-devops-azure-pipelines-classic/azdevops-canary-deploy.png)

**ブルーグリーン**では、同一のスタンバイ環境を設けることでデプロイのダウンタイムを短くします。 どの時点でも、いずれか 1 つの環境が有効になります。 新リリースに向けた準備の過程で、テストの最終ステージをグリーン環境で行います。 グリーン環境でソフトウェアが正常に動作していれば、トラフィックを切り替えて、すべて受信要求をグリーン環境に誘導します。この時点で、ブルー環境はアイドルとなります。
"**仮想マシン**" へのブルーグリーン デプロイは、Azure portal から継続的デリバリー オプションを使用して構成することができます。 

その詳細な手順は次のとおりです。 

1. Azure portal にサインインして仮想マシンに移動します。 
2. 「**ローリング アップデート**」セクションの手順 2 から手順 5 に従って、デプロイ グループに複数の VM を追加します。 ブルーグリーン デプロイに含める VM にユーザー定義タグを追加します。 たとえば、スタンバイ ロールの VM に "blue" または "green" を使用します。 
3. それらの VM のパイプラインが構成されたら、パイプラインをクリックし、Azure DevOps 組織を起動して、パイプラインを**編集**し、**開発**ステージに移動します。 "green" というフィルターにタグを追加します。 
4. エージェントレスのフェーズを追加し、手動検証ステップとタグを入れ替えるための REST API の呼び出しステップを使用してそのフェーズを構成します。 
   ![AzDevOps_BlueGreen](media/tutorial-devops-azure-pipelines-classic/azdevops-blue-green-deploy.png)
 
 
## <a name="azure-devops-project"></a>Azure DevOps プロジェクト 
Azure は、これまでよりも簡単に始められるようになりました。
 
DevOps Projects を使用すれば、3 つのステップ (アプリケーション言語、ランタイム、Azure サービスの選択) だけで、どの Azure サービスでもアプリケーションの実行を開始できます。
 
[詳細については、こちらを参照してください](https://azure.microsoft.com/features/devops-projects/ )。
 
## <a name="additional-resources"></a>その他のリソース 
- [DevOps プロジェクトを使用して Azure 仮想マシンにデプロイする](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Azure 仮想マシン スケール セットへのアプリの継続的デプロイを導入する](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
