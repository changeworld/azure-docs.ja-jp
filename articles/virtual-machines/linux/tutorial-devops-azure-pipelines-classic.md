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
ms.openlocfilehash: 4159bf27c39087926d982552c49c606f3484de77
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885907"
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
Azure Pipelines では、仮想マシンにデプロイするための機能が完備された CI/CD 自動化ツールのセットが提供されます。 Azure VM に対する継続的デリバリー パイプラインは、Azure portal から直接構成できます。 このドキュメントでは、Azure portal から複数マシンをデプロイするための CI/CD パイプラインの設定に関連した手順を説明します。 


**仮想マシンで CI/CD を構成する**

仮想マシンは、[デプロイ グループ](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups)にターゲットとして追加することができます。また、複数マシンの更新の対象にすることができます。 要件に基づいて、すぐに使用できるデプロイ方法のいずれか ("_ローリング_"、"_カナリア_"、"_ブルーグリーン_") を選択することも、これらをカスタマイズすることもできます。 デプロイ後は、デプロイ グループ内のデプロイ履歴ビューを使用して、VM からパイプライン、さらにコミットまでの追跡が可能になります。 
 
**ローリング デプロイ**: ローリング デプロイでは、反復するたびに固定された一連のマシン (ローリング セット) を対象に、アプリケーションの以前のバージョンのインスタンスがそのアプリケーションの新しいバージョンのインスタンスに置き換えられます。 仮想マシンへのローリング アップデートを構成する方法を順を追って見ていきましょう。  
"**仮想マシン**" へのローリング アップデートは、Azure portal 内で継続的デリバリー オプションを使用して構成することができます。 

その詳細な手順は次のとおりです。 
1. Azure portal にサインインして仮想マシンに移動します。 
2. VM の左ペインで、 **[継続的デリバリー]**   メニューに移動します。 次に、 **[構成]** をクリックします。 

   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png) 
3. 構成パネルで、[Azure DevOps 組織] をクリックして既存のアカウントを選択するか、新たに作成します。 次に、パイプラインを構成するプロジェクトを選択します。  


   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png) 
4. デプロイ グループは、"開発"、"テスト"、"UAT"、"運用" などの物理環境を表す、デプロイ ターゲット マシンの論理的な集まりです。 新しいデプロイ グループを作成することも、既存のデプロイ グループを選択することもできます。 
5. 仮想マシンにデプロイするパッケージを発行するビルド パイプラインを選択します。 発行されたパッケージには、パッケージ ルートの _deployscripts_ フォルダーに _deploy.ps1_ または _deploy.sh_ のデプロイ スクリプトが含まれている必要があることに注意してください。 このデプロイ スクリプトは、実行時に Azure DevOps パイプラインによって実行されます。
6. 任意のデプロイ方法を選択します。 ここでは [ローリング] を選択します。
7. 必要に応じて、マシンにロールのタグを付けることができます。 たとえば、"web"、"db" があります。これは、特定のロールのみを持つ VM をターゲットにするのに役立ちます。
8. 継続的デリバリー パイプラインを構成するには、ダイアログで **[OK]** をクリックします。 
9. 完了すると、仮想マシンにデプロイするよう継続的デリバリー パイプラインが構成されます。  


   ![AzDevOps_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-deployment-history.png)
10. 仮想マシンへのデプロイが進行中であることがわかります。 リンクをクリックすると、パイプラインに移動できます。 **Release-1** をクリックしてデプロイを表示します。 または、 **[編集]** をクリックして、リリース パイプラインの定義を変更することもできます。 
11. 構成する VM が複数ある場合は、手順 2 から手順 4 を繰り返して、他の VM をデプロイ グループに追加します。 パイプライン実行が既に存在するデプロイ グループを選択した場合、新しいパイプラインが作成されることなく単に VM がデプロイ グループに追加されることに注意してください。 
12. 完了したら、パイプラインの定義をクリックして、Azure DevOps 組織に移動し、リリース パイプラインの **[編集]** をクリックします。 
   ![AzDevOps_edit_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline.png)
13. **[dev]\(開発\)** ステージの **[1 job, 1 task]\(1 ジョブ、1 タスク\)** というリンクをクリックします。 **[デプロイ]** フェーズをクリックします。
   ![AzDevOps_deploymentGroup](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline-tasks.png)
14. 右側の構成ペインで、各反復処理で並列にデプロイするマシンの数を指定できます。 複数のマシンに一度にデプロイする場合は、スライダーを使用してパーセンテージでこれを指定できます。  

15. デプロイ スクリプトの実行タスクでは、既定で、発行されたパッケージのルート ディレクトリの _deployscripts_ フォルダー内の _deploy.ps1_ または _deploy.sh_ デプロイ スクリプトが実行されます。
  
**カナリア デプロイ**: カナリア デプロイでは、一部の少数のユーザーにゆっくり変更を展開することによりリスクを軽減します。 新バージョンへの信頼度が上がってきたら、インフラストラクチャ内のより多くのサーバーへのリリースを開始し、より多くのユーザーを誘導することができます。 "**仮想マシン**" へのカナリア デプロイは、Azure portal から継続的デリバリー オプションを使用して構成することができます。 その詳細な手順は次のとおりです。 
1. Azure portal にサインインして仮想マシンに移動します。 
2. 「**ローリング デプロイ**」セクションの手順 2 から手順 7 に従って、デプロイ グループに複数の VM を追加します。 [deployment strategy]\(デプロイ方法\) ドロップダウンで、[カナリア] を選択します。
![AzDevOps_configure_canary](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure-canary.png)

3. カナリア デプロイに含める VM に "canary (カナリア)" タグを追加します。また、カナリア デプロイが正常に完了した後にデプロイの一部となった VM に "prod (運用)" タグを追加します。
4. 完了すると、仮想マシンにデプロイするよう継続的デリバリー パイプラインが構成されます。
![AzDevOps_canary_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-pipeline.png)


5. 「**ローリング デプロイ**」の場合と同様に、Azure DevOps のリリース パイプラインの **[編集]** をクリックして、パイプラインの構成を確認できます。 パイプラインは 3 つのフェーズで構成されています。最初のフェーズは DG フェーズで、"_canary (カナリア)_ " タグが付けられた VM にデプロイされます。 2 番目のフェーズでは、パイプラインを一時停止し、実行を再開するための手動介入を待機します。 ユーザーは、カナリア デプロイが安定していることを確認した後、パイプラインの実行を再開できます。これにより、"_prod (運用)_ " タグが付けられた VM にデプロイする 3 番目のフェーズが実行されます。![AzDevOps_canary_task](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-task.png)



**ブルーグリーン デプロイ**: ブルーグリーン デプロイでは、同一のスタンバイ環境を設けることでダウンタイムを短くします。 どの時点でも、いずれか 1 つの環境が有効になります。 新リリースに向けた準備の過程で、テストの最終ステージをグリーン環境で行います。 グリーン環境でソフトウェアが正常に動作していれば、トラフィックを切り替えて、すべて受信要求をグリーン環境に誘導します。この時点で、ブルー環境はアイドルとなります。
"**仮想マシン**" へのブルーグリーン デプロイは、Azure portal から継続的デリバリー オプションを使用して構成することができます。 

その詳細な手順は次のとおりです。

1. Azure portal にサインインして仮想マシンに移動します。 
2. 「**ローリング デプロイ**」セクションの手順 2 から手順 7 に従って、デプロイ グループに複数の VM を追加します。 ブルーグリーン デプロイに含める VM に "blue (ブルー)" または "green (グリーン)" タグを追加します。 VM がスタンバイロール用である場合は、"green (グリーン)" のタグを付ける必要があります。
![AzDevOps_bluegreen_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-configure.png)

4. 完了すると、仮想マシンにデプロイするよう継続的デリバリー パイプラインが構成されます。
![AzDevOps_bluegreen_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-pipeline.png)

5. 「**ローリング デプロイ**」の場合と同様に、Azure DevOps のリリース パイプラインの **[編集]** をクリックして、パイプラインの構成を確認できます。 パイプラインは 3 つのフェーズで構成されています。最初のフェーズは DG フェーズで、"_green (グリーン)_ " (スタンバイ VM) タグが付けられた VM にデプロイされます。 2 番目のフェーズでは、パイプラインを一時停止し、実行を再開するための手動介入を待機します。 ユーザーは、デプロイが安定していることを確認した後、"_green (グリーン)_ " VM にトラフィックをリダイレクトし、パイプラインの実行を再開できます。これにより、VM の "_blue (ブルー)_ " タグと "_green (グリーン)_ " タグが入れ替えられます。 これにより、アプリケーションのバージョンが古い VM に "_green (グリーン)_ " のタグが付けられ、次のパイプライン実行でのデプロイ先になります。
![AzDevOps_bluegreen_task](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-tasks.png)

6. このデプロイ戦略では、それぞれ blue (ブルー) と green (グリーン) のタグが付いた VM が少なくとも 1 台必要であることに注意してください。 手動介入ステップでパイプラインの実行を再開する前に、少なくとも 1 台の VM に "_blue (ブルー)_ " のタグが付けられていることを確認してください。





 
## <a name="azure-devops-project"></a>Azure DevOps プロジェクト 
Azure は、これまでよりも簡単に始められるようになりました。
 
DevOps Projects を使用すれば、3 つのステップ (アプリケーション言語、ランタイム、Azure サービスの選択) だけで、どの Azure サービスでもアプリケーションの実行を開始できます。
 
[詳細については、こちらを参照してください](https://azure.microsoft.com/features/devops-projects/ )。
 
## <a name="additional-resources"></a>その他のリソース 
- [DevOps プロジェクトを使用して Azure 仮想マシンにデプロイする](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Azure 仮想マシン スケール セットへのアプリの継続的デプロイを導入する](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
