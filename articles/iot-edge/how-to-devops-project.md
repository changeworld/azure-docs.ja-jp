---
title: Azure DevOps Projects を使用した CI/CD パイプライン - Azure IoT Edge | Microsoft Docs
description: Azure DevOps Projects を利用すると、Azure を使い始めるのが簡単になります。 いくつかの簡単な手順によって、Azure IoT Edge アプリを起動するのに役立ちます。
author: shizn
manager: ''
ms.author: xshi
ms.date: 12/04/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: ebb7e515f9d9205f364d50b3d686c68a2988f86a
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53074216"
---
# <a name="create-a-cicd-pipeline-for-iot-edge-with-azure-devops-projects-preview"></a>Azure DevOps Projects (プレビュー) を使用して IoT Edge 用の CI/CD パイプラインを作成する

DevOps Projects を使用して、IoT Edge アプリケーション用に継続的インテグレーション (CI) と継続的デリバリー (CD) を構成します。 DevOps Projects によって、Azure Pipelines のビルドおよびリリース パイプラインの初期構成が簡略化されます。

アクティブな Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free)を作成してください。

## <a name="sign-in-to-the-azure-portal"></a>Azure ポータルにサインインします。

DevOps Projects によって、Azure DevOps に CI/CD パイプラインが作成されます。 新しい Azure DevOps 組織を作成するか、既存の組織を使用できます。 DevOps Projects では、選択した Azure サブスクリプションに Azure リソースも作成されます。

1. [Microsoft Azure ポータル](https://portal.azure.com)にサインインします。

1. 左側のウィンドウで **[リソースの作成]** アイコンを選択し、**DevOps Projects** を検索します。  

1.  **作成**を選択します。

## <a name="select-a-sample-application-and-azure-service"></a>サンプル アプリケーションと Azure サービスを選択する

1. Azure IoT Edge モジュールは、[C#](tutorial-csharp-module.md)、[Node.js](tutorial-node-module.md)、[Python](tutorial-python-module.md)、[C](tutorial-c-module.md)、および [Java](tutorial-java-module.md) で記述できます。 新しいアプリケーションを始めるために使用する言語を選択します。 それに応じて、**.NET**、**Node.js**、**Python**、**C**、または **Java** を選択してから、**[次へ]** を選択できます。

    ![言語を選択して新しいアプリケーションを作成する](./media/how-to-devops-project/select-language.png)

2. **[単純な IoT (プレビュー)]** を選択してから、**[次へ]** をクリックします。

    ![単純な IoT フレームワークを選択する](media/how-to-devops-project/select-iot.png)

3. **[IoT Edge]** を選択して、**[次へ]** をクリックします。

    ![IoT Edge サービスを選択する](media/how-to-devops-project/select-iot-edge.png)

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure DevOps と Azure サブスクリプションを構成する

1. 新しい無料の Azure DevOps 組織を作成するか、既存の組織を選択します。

    a. プロジェクトの名前を選択します。 

    b. Azure サブスクリプションと場所を選択し、アプリケーションの名前を選択して、**[完了]** を選択します。  

    ![アプリケーションに名前を付けて作成する](media/how-to-devops-project/select-devops.png)

1. 数分後、DevOps Projects ダッシュボードが Azure portal に表示されます。 サンプルの IoT Edge アプリケーションが Azure DevOps 組織内のリポジトリに設定され、ビルドが実行され、アプリケーションが IoT Edge デバイスにデプロイされます。 このダッシュボードでは、コード リポジトリ、CI/CD パイプライン、および Azure のアプリケーションが可視化されます。

    ![DevOps ポータルにアプリケーションを表示する](./media/how-to-devops-project/devops-portal.png)


## <a name="commit-code-changes-and-execute-cicd"></a>コードの変更をコミットし、CI/CD を実行する

DevOps Projects によって、Azure Repos または GitHub に Git リポジトリが作成されました。 リポジトリを表示して、アプリケーションにコード変更を加えるには、次の手順に従います。

1. DevOps Projects ダッシュボードの左側にある**マスター**分岐のリンクを選択します。  
このリンクは、新しく作成された Git リポジトリのビューを開きます。

1. リポジトリのクローン URL を表示するには、ブラウザーの右上から **[複製]** を選択します。 VS Code またはその他のお好みのツールで Git リポジトリを複製することができます。 次のいくつかの手順では、Web ブラウザーを使用してマスター ブランチに直接コード変更を行い、コミットします。

    ![Git リポジトリの複製](media/how-to-devops-project/clone.png)

1. ブラウザーの左側で、**modules/FilterModule/module.json** ファイルに移動します。

1. **[編集]** を選択して、`"tag"` の下で `"version"` に変更を加えます。 たとえば、お使いの Azure IoT Edge モジュールのイメージ タグの一部として [Azure DevOps ビルド変数](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=vsts#build-variables)を使用するように、`"version": "${BUILD_BUILDID}"` に更新することができます。

    ![バージョンを編集してビルド変数を許可する](media/how-to-devops-project/update-module-json.png)

1. **[コミット]** を選択し、変更を保存します。

1. ブラウザーで、Azure DevOps Projects ダッシュボードに移動します。  ビルドが進行中であることが表示されます。 行った変更は、CI/CD パイプラインを介して自動的にビルドおよびデプロイされます。

    ![進行中のステータスを表示する](media/how-to-devops-project/ci-cd-in-progress.png)

## <a name="examine-the-cicd-pipeline"></a>CI/CD パイプラインを確認する

前の手順で、Azure DevOps Projects によって、お使いの IoT Edge アプリケーション用の完全な CI/CD パイプラインが自動的に構成されました。 パイプラインを探索し、必要に応じてカスタマイズします。 Azure DevOps のビルドおよびリリース パイプラインについて理解するには、次の手順を行います。

1. DevOps Projects ダッシュボードの上部の **[ビルド パイプライン]** を選択します。  
このリンクによって、ブラウザーのタブが開かれ、新しいプロジェクトの Azure DevOps ビルド パイプラインが表示されます。

1. **[編集]** を選択します。

    ![ビルド パイプラインの編集](media/how-to-devops-project/click-edit-button.png)

1. このウィンドウで、ビルド パイプラインのさまざまなタスクを調べることができます。 ビルドでは、Git リポジトリからのソースのフェッチ、IoT Edge モジュール イメージの構築、IoT Edge モジュールのプッシュ、デプロイに使用された出力の発行など、さまざまなタスクが実行されます。 CI 用の Azure IoT Edge タスクに関する詳細を確認するには、「[継続的インテグレーションのための Azure Pipelines の構成](https://docs.microsoft.com/azure/iot-edge/how-to-ci-cd#configure-azure-pipelines-for-continuous-integration)」を参照してください。

    ![継続的インテグレーション タスクの表示](media/how-to-devops-project/ci.png)

1. ビルド パイプラインの上部で、ビルド パイプラインの名前を選択します。

1. ビルド パイプラインの名前をよりわかりやすい名前に変更し、**[保存してキューに登録]** を選択して、**[保存]** を選択します。

1. ご自身のビルド パイプラインの名前の下で、**[履歴]** を選択します。   
**[履歴]** ウィンドウに、ビルドの最近の変更の監査証跡が表示されます。  ビルド パイプラインに対するすべての変更が Azure Pipelines によって追跡されるため、各バージョンを比較できます。

1. **[トリガー]** を選択します。 DevOps Projects では、CI トリガーが自動的に作成され、リポジトリに対してコミットするたびに新しいビルドが開始されます。  必要に応じて、CI プロセスのブランチを含めるか除外するかを選択できます。

1. **[保持]** を選択します。 シナリオに基づいて、特定の数のビルドを保持または削除するポリシーを指定できます。

1. **[パイプライン]** の下の **[リリース]** を選択します。 DevOps Projects により、Azure IoT Edge へのデプロイを管理するリリース パイプラインが作成されます。

    ![リリース パイプラインの表示](media/how-to-devops-project/release-pipeline.png)

1. **[編集]** を選択します。 リリース パイプラインには、リリース プロセスを定義するパイプラインが含まれています。  

1. **[成果物]** で、**[ドロップ]** を選択します。 前の手順で調べたビルド パイプラインでは、成果物に使用される出力が生成されます。 

1. **[ドロップ]** アイコンの横にある **[継続的配置トリガー]** を選択します。  
このリリース パイプラインには、新しいビルド成果物が使用可能になるたびにデプロイを実行する有効な CD トリガーがあります。 必要に応じて、手動でのデプロイが必須となるように、トリガーを無効にすることができます。  

1. 左側の **[タスク]** を選択します。 タスクは、デプロイ プロセスによって実行されるアクティビティです。 この例では、Azure IoT Edge にモジュール イメージをデプロイするために、タスクが作成されました。 CD 用の Azure IoT Edge タスクに関する詳細を確認するには、「[継続的配置のための Azure Pipelines の構成](https://docs.microsoft.com/azure/iot-edge/how-to-ci-cd#configure-azure-pipelines-for-continuous-deployment)」を参照してください。

    ![継続的配置タスクの表示](media/how-to-devops-project/dev-release.png)

1. 右側の **[リリースの表示]** を選択します。 このビューには、リリースの履歴が表示されます。

1. いずれかのリリースの横にある省略記号 (...) を選択し、**[開く]** を選択します。  
リリース概要、関連付けられた作業項目、テストなど、調べる必要があるいくつかのメニューがあります。

1. **[コミット]** を選択します。 このビューには、特定のデプロイに関連付けられているコードのコミットが表示されます。 

1. **[ログ]** を選択します。 ログには、デプロイ プロセスに関する有用な情報が含まれます。 これらは、デプロイ中とデプロイ後の両方に表示できます。


## <a name="clean-up-resources"></a>リソースのクリーンアップ

作成した Azure App Service とその他の関連リソースが必要なくなったら、削除してかまいません。 DevOps Projects ダッシュボードで**削除**機能を使用します。

## <a name="next-steps"></a>次の手順
* 「[Azure IoT Edge に対する継続的インテグレーションと継続的配置](how-to-ci-cd.md)」で Azure DevOps 上の Azure IoT Edge のタスクについて確認します。
* 「[1 台のデバイスまたは一群のデバイスを対象とした IoT Edge デプロイについて](module-deployment-monitoring.md)」で IoT Edge デプロイについて理解します。
* 「[Deploy and monitor IoT Edge modules at scale (IoT Edge モジュールを大規模にデプロイして監視する)](how-to-deploy-monitor.md)」で、デプロイを作成、更新、または削除するための手順を学習してください。
