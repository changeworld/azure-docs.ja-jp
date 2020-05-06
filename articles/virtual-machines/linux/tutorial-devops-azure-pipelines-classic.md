---
title: チュートリアル - Azure Linux Virtual Machines のローリング デプロイを構成する
description: このチュートリアルでは、ローリング デプロイ戦略を使用して、Azure Linux Virtual Machines のグループを増分更新する継続的デプロイ (CD) パイプラインのセットアップ方法について説明します。
author: moala
manager: jpconnock
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 4/10/2020
ms.author: moala
ms.custom: devops
ms.openlocfilehash: 75888b1ebbda33891296fe0b54c5d204955e32a3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/29/2020
ms.locfileid: "82113487"
---
# <a name="tutorial---configure-rolling-deployment-strategy-for-azure-linux-virtual-machines"></a>チュートリアル - Azure Linux Virtual Machines のローリング デプロイ戦略を構成する

Azure DevOps は、Azure リソースの継続的インテグレーションと継続的デリバリーによって DevOps プロセスの各段階を自動化する、組み込みの Azure サービスです。
Azure と Azure DevOps を使用すれば、仮想マシン、Web アプリ、Kubernetes など、アプリで使用されるリソースが何であれ、Infrastructure as Code、継続的インテグレーション、継続的なテスト、継続的デリバリー、継続的監視を導入することができます。  

![AzDevOps_portalView](media/tutorial-devops-azure-pipelines-classic/azdevops-view.png) 


## <a name="iaas---configure-cicd"></a>IaaS - CI/CD の構成 
Azure Pipelines では、仮想マシンにデプロイするための機能が完備された CI/CD 自動化ツールのセットが提供されます。 Azure VM に対する継続的デリバリー パイプラインは、Azure portal から直接構成できます。 このドキュメントでは、Azure portal から複数マシンをローリング デプロイするための CI/CD パイプラインの設定に関連した手順を説明します。 [カナリア](https://aka.ms/AA7jdrz)や[ブルーグリーン](https://aka.ms/AA83fwu)など、特別な設定なしに Azure portal から使用できる他の戦略もご覧いただけます。 


**仮想マシンで CI/CD を構成する**

仮想マシンは、[デプロイ グループ](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups)にターゲットとして追加することができます。また、複数マシンの更新の対象にすることができます。 デプロイ後は、デプロイ グループ内の**デプロイ履歴**を使用して、VM からパイプライン、さらにコミットまでの追跡が可能になります。 
 

**ローリング デプロイ**: ローリング デプロイでは、反復するたびに固定された一連のマシン (ローリング セット) を対象に、アプリケーションの以前のバージョンのインスタンスがそのアプリケーションの新しいバージョンのインスタンスに置き換えられます。 仮想マシンへのローリング アップデートを構成する方法を順を追って見ていきましょう。  
"**仮想マシン**" へのローリング アップデートは、Azure portal 内で継続的デリバリー オプションを使用して構成することができます。 

その詳細な手順は次のとおりです。 
1. Azure portal にサインインして仮想マシンに移動します。 
2. VM の左ペインで、 **[継続的デリバリー]**   メニューに移動します。 次に、 **[構成]** をクリックします。 

   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png) 
3. 構成パネルで、[Azure DevOps 組織] をクリックして既存のアカウントを選択するか、新たに作成します。 次に、パイプラインを構成するプロジェクトを選択します。  


   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png) 
4. デプロイ グループは、"開発"、"テスト"、"UAT"、"運用" などの物理環境を表す、デプロイ ターゲット マシンの論理的な集まりです。 新しいデプロイ グループを作成することも、既存のデプロイ グループを選択することもできます。 
5. 仮想マシンにデプロイするパッケージを発行するビルド パイプラインを選択します。 発行されたパッケージには、パッケージ ルートの `deployscripts` フォルダーに _deploy.ps1_ または _deploy.sh_ のデプロイ スクリプトが含まれている必要があることに注意してください。 このデプロイ スクリプトは、実行時に Azure DevOps パイプラインによって実行されます。
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

15. デプロイ スクリプトの実行タスクでは、既定で、発行されたパッケージのルート ディレクトリの "deployscripts" フォルダー内の _deploy.ps1_ または _deploy.sh_ デプロイ スクリプトが実行されます。  
![AzDevOps_publish_package](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>その他のデプロイ戦略

- [カナリア デプロイ戦略を構成する](https://aka.ms/AA7jdrz)
- [ブルーグリーン デプロイ戦略を構成する](https://aka.ms/AA83fwu)

 
## <a name="azure-devops-project"></a>Azure DevOps プロジェクト 
Azure は、これまでよりも簡単に始められるようになりました。
 
DevOps Projects を使用すれば、3 つのステップ (アプリケーション言語、ランタイム、Azure サービスの選択) だけで、どの Azure サービスでもアプリケーションの実行を開始できます。
 
[詳細については、こちらを参照してください](https://azure.microsoft.com/features/devops-projects/ )。
 
## <a name="additional-resources"></a>その他のリソース 
- [DevOps プロジェクトを使用して Azure 仮想マシンにデプロイする](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Azure 仮想マシン スケール セットへのアプリの継続的デプロイを導入する](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
