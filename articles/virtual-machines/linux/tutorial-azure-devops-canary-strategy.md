---
title: チュートリアル - Azure Linux Virtual Machines のカナリア デプロイを構成する
description: このチュートリアルでは、カナリア デプロイ戦略を使用して、Azure Linux Virtual Machines のグループを更新する継続的デプロイ (CD) パイプラインのセットアップ方法について説明します。
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
ms.openlocfilehash: b51b4aed85f737e436565ce8ba1ab4a295714734
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/29/2020
ms.locfileid: "82120530"
---
# <a name="tutorial---configure-canary-deployment-strategy-for-azure-linux-virtual-machines"></a>チュートリアル - Azure Linux Virtual Machines のカナリア デプロイ戦略を構成する


## <a name="iaas---configure-cicd"></a>IaaS - CI/CD の構成 
Azure Pipelines では、仮想マシンにデプロイするための機能が完備された CI/CD 自動化ツールのセットが提供されます。 Azure VM に対する継続的デリバリー パイプラインは、Azure portal から直接構成できます。 このドキュメントでは、カナリア戦略を使用して複数マシンをデプロイする CI/CD パイプラインの設定に関連した手順を説明します。 [ローリング](https://aka.ms/AA7jlh8)や[ブルーグリーン](https://aka.ms/AA83fwu)など、特別な設定なしに Azure portal から使用できる他の戦略もご覧いただけます。 


**仮想マシンで CI/CD を構成する**

仮想マシンは、[デプロイ グループ](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups)にターゲットとして追加することができます。また、複数マシンの更新の対象にすることができます。 デプロイ後は、デプロイ グループ内の**デプロイ履歴**を使用して、VM からパイプライン、さらにコミットまでの追跡が可能になります。 
 
  
**カナリア デプロイ**: カナリア デプロイでは、一部の少数のユーザーにゆっくり変更を展開することによりリスクを軽減します。 新バージョンへの信頼度が上がってきたら、インフラストラクチャ内のより多くのサーバーへのリリースを開始し、より多くのユーザーを誘導することができます。 仮想マシンへのカナリア デプロイは、Azure portal から継続的デリバリー オプションを使用して構成することができます。 その詳細な手順は次のとおりです。 
1. Azure portal にサインインして仮想マシンに移動します。 
2. VM の左ペインで、 **[継続的デリバリー]**   メニューに移動します。 **[構成]** をクリックします。 

   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png) 
3. 構成パネルで、 **[Azure DevOps 組織]** をクリックして既存のアカウントを選択するか、新たに作成します。 次に、パイプラインを構成するプロジェクトを選択します。  


   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png) 
4. デプロイ グループは、"開発"、"テスト"、"UAT"、"運用" などの物理環境を表す、デプロイ ターゲット マシンの論理的な集まりです。 新しいデプロイ グループを作成することも、既存のデプロイ グループを選択することもできます。 
5. 仮想マシンにデプロイするパッケージを発行するビルド パイプラインを選択します。 発行されたパッケージには、パッケージ ルートの `deployscripts` フォルダーに _deploy.ps1_ または _deploy.sh_ のデプロイ スクリプトが含まれている必要があることに注意してください。 このデプロイ スクリプトは、実行時に Azure DevOps パイプラインによって実行されます。
6. 任意のデプロイ方法を選択します。 **[カナリア]** を選択します。
7. カナリア デプロイに含める VM に "canary (カナリア)" タグを追加します。また、カナリア デプロイが正常に完了した後にデプロイの一部となった VM に "prod (運用)" タグを追加します。 タグは、特定のロールのみを持つ VM をターゲットにするのに役立ちます。
![AzDevOps_configure_canary](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure-canary.png)

8. 継続的デリバリー パイプラインを構成するには、ダイアログで **[OK]** をクリックします。 これで仮想マシンにデプロイするよう継続的デリバリー パイプラインが構成されます。
![AzDevOps_canary_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-pipeline.png)


9. Azure DevOps のリリース パイプラインの **[編集]** をクリックして、パイプラインの構成を確認します。 パイプラインは、3 つのフェーズで構成されています。 最初のフェーズはデプロイ グループ フェーズで、"_canary (カナリア)_ " タグが付けられた VM にデプロイされます。 2 番目のフェーズでは、パイプラインを一時停止し、実行を再開するための手動介入を待機します。 ユーザーは、カナリア デプロイが安定していることを確認した後、パイプラインの実行を再開できます。これにより、"_prod (運用)_ " タグが付けられた VM にデプロイする 3 番目のフェーズが実行されます。![AzDevOps_canary_task](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-task.png)

10. パイプラインの実行を再開する前に、少なくとも 1 つの VM が "_prod (運用)_ " としてタグ付けされていることを確認してください。パイプラインの 3 番目のフェーズでは、"_prod (運用)_ " タグが付けられた VM にのみアプリケーションがデプロイされます。

11. デプロイ スクリプトの実行タスクでは、既定で、発行されたパッケージのルート ディレクトリの "deployscripts" フォルダー内の _deploy.ps1_ または _deploy.sh_ デプロイ スクリプトが実行されます。 選択したビルド パイプラインによって、パッケージのルート フォルダーにそれが発行されていることを確認してください。 
![AzDevOps_publish_package](media/tutorial-deployment-strategy/package.png)




## <a name="other-deployment-strategies"></a>その他のデプロイ戦略
- [ローリング デプロイ戦略を構成する](https://aka.ms/AA7jlh8)
- [ブルーグリーン デプロイ戦略を構成する](https://aka.ms/AA83fwu)

## <a name="azure-devops-project"></a>Azure DevOps プロジェクト 
Azure は、これまでよりも簡単に始められるようになりました。
 
DevOps Projects を使用すれば、3 つのステップ (アプリケーション言語、ランタイム、Azure サービスの選択) だけで、どの Azure サービスでもアプリケーションの実行を開始できます。
 
[詳細については、こちらを参照してください](https://azure.microsoft.com/features/devops-projects/ )。
 
## <a name="additional-resources"></a>その他のリソース 
- [DevOps プロジェクトを使用して Azure 仮想マシンにデプロイする](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Azure 仮想マシン スケール セットへのアプリの継続的デプロイを導入する](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
