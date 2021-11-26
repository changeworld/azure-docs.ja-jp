---
title: 環境を Azure Pipelines に統合する
description: Azure DevTest Labs 環境を Azure DevOps の継続的インテグレーション (CI) および継続的デリバリー (CD) パイプラインに統合する方法について説明します。
ms.topic: how-to
ms.date: 11/15/2021
ms.openlocfilehash: b3478242dddd7141100f05ae44a09902a2546ffd
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132488107"
---
# <a name="integrate-environments-into-your-cicd-pipelines"></a>環境を CI/CD パイプラインに組み込む 
この記事では、すべて 1 つの完全なパイプラインの中で、環境を作成して配置し、そして削除する方法を説明します。 この環境を使って、継続的インテグレーション (CI)/継続的デリバリー (CD) リリース パイプラインを Azure DevTest Labs と統合することができます。 統合には Azure DevOps Services の中の Azure DevTest Labs Tasks 拡張機能を使用します。 これらの拡張機能により、特定のテスト タスク用の[環境](devtest-lab-test-env.md)の迅速な配置と、その後テストが終了したときの環境の削除を、より簡単に行えます。 

通常は、これらの各タスクを独自のカスタムのビルド・テスト・展開パイプラインで個別に実行します。 この記事で使用されている拡張機能は、これらの [DTL VM の作成/削除タスク](devtest-lab-integrate-ci-cd.md)に追加されたものです。

- 環境の作成
- 環境の削除

## <a name="prerequisites"></a>前提条件
CI/CD パイプラインを Azure DevTest Labs に組み込む前に、次の作業を行う必要があります: 
1. [Azure DevTest Labs Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) の拡張機能を Visual Studio Marketplace からインストールします。 
1. [ラボを作成し](devtest-lab-create-lab.md)、**パブリック環境** を使用するように構成されていることを確認します。この環境は初期設定では使うように構成されています。

## <a name="create-a-release-definition--environment"></a>リリース定義と環境を作成する
リリース定義を作成するには、次の手順に従います:

1. Azure DevOps プロジェクトで、 **[パイプライン]**  セクションの下の **[リリース]**  を選択します。
1. **[リリース]**  タブでは、 **[新しいパイプライン]**  を選択します。  右側の **[テンプレートを選択する]**  ウィンドウでは、一般的なデプロイ パターンに関する特徴的なテンプレートのリストが表示されます。 
1. このパイプラインでは、 **[空のジョブ]** を選択して、開発またはテスト目的で使われる環境の作成を開始します。
1. 空のジョブで、ツール バーの **[タスク]**  を選択し、次に **[ステージ 1]**  を選択します。

   :::image type="content" source="./media/integrate-environments-devops-pipeline/new-release-pipeline-stage.png" alt-text="このスクリーンショットはパイプライン リリース ステージを開くときの様子を示しています":::。

1. ステージにタスクを追加するには、 **[エージェント ジョブ]**  セクションでプラス記号 (+) を選択 します。 使用可能なタスクを検索できるリストが表示されます。 
1. **[タスクの追加]**  ウィンドウで、`Azure DevTest Labs Create Environment` を検索します。
1. 検索結果の中で `Azure DevTest Labs Create Environment` タスクを選択し、 **[追加]**  を選択します。
1. 先ほど追加したタスクを選択します。 **[Azure DevTest Labs 作成環境 (プレビュー)]**  ウィンドウが表示されます。

   :::image type="content" source="./media/integrate-environments-devops-pipeline/new-release-pipeline-environment.png" alt-text="このスクリーンショットは Azure DevTest Labs の Azure Pipelines 環境に必要となるフィールドを示しています。":::

2. **[タスク]**  タブで、次のように環境を構成します:

   |フィールド|説明|
   |-----|-----------|
   |**Azure RM サブスクリプション**|実行する前に構成する Azure Resource Manager サブスクリプションです。 **[使用可能な Azure Service]**  のリストから接続を選択するか、Azure サブスクリプションへのアクセス許可がさらに制限される接続を作成します。 詳しくは、「[Azure Resource Manager service endpoint](/azure/devops/pipelines/library/service-endpoints)」(Azure Resource Manager のサービス エンドポイント) をご覧ください。|
   |**ラボ名**|以前作成したラボの名前を選択します (前提条件を参照)。 実際には、デプロイしたいラボを選択する必要があります。 リソースは、このラボで作成されます。  変数、`$(labName)`も使えます。  フレンドリ名を手動で入力するとエラーが発生します。ドロップダウン リストを使用して情報を選択してください。|
   |**環境名**|選択したラボ内に作成される環境の名前です。|
   |**リポジトリ名**|テンプレートを含むソース コード リポジトリの名前です。 既定のリポジトリ、`Public Environment Repo`、 または使いたいテンプレートを含む別のリポジトリ を選択できます。 リポジトリはラボ ポリシーで設計されます。  フレンドリ名を手動で入力するとエラーが発生します。ドロップダウン リストを使用して情報を選択してください。|
   |**テンプレート名**|環境の作成に使うテンプレートの名前です。 環境テンプレートの名前を選択します。 フレンドリ名を手動で入力するとエラーが発生します。ドロップダウン リストを使用して情報を選択してください。| 
   |**環境名**|ラボ内で環境のインスタンスを一意に識別するための名前を入力します。  ラボ内で一意にする必要があります。|
   |**パラメーター ファイル** & **パラメーターのオーバーライド**|カスタム パラメーターを環境に渡すために使います。 どちらか一方を使用しても両方を使用してもパラメーター値を設定できます。 たとえば、これらのフィールドを使って、暗号化されたパスワードを渡すことができます。 また、変数を使用して、ログ内のにシークレット情報を渡すのを避け、さらにその情報を Azure Key Vault と結びつけることもできます。|

## <a name="delete-the-environment"></a>環境の削除
最後の段階では、Azure DevTest Labs インスタンスに展開した環境を削除します。 通常は、開発タスクを実行した後、または展開したリソースに対して必要なテストを実行した後に、環境を削除します。

リリース定義で、**[タスクの追加]** を選択し、**[展開]** タブで、**[Azure DevTest Labs Delete Environment]\(Azure DevTest Labs: 環境の削除\)** タスクを追加します。 SAP コネクタを次のように構成します。

1. VM を削除するには、「[Azure DevTest Labs タスク](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)」を参照してください。
    1. **[Azure RM サブスクリプション]** で、**[利用可能な Azure サービス接続]** の一覧から接続を選択するか、さらに制限されたアクセス許可を持つ Azure サブスクリプションへの接続を作成します。 詳しくは、「[Azure Resource Manager service endpoint](/azure/devops/pipelines/library/service-endpoints)」(Azure Resource Manager のサービス エンドポイント) をご覧ください。
    2. **[ラボ名]** で、環境が存在するラボを選択します。
    3. **[環境名]** で、削除する環境の名前を入力します。
2. リリース定義の名前を入力し、それを保存します。

## <a name="next-steps"></a>次のステップ
次の記事をご覧ください。 
- [Resource Manager テンプレートを使用してマルチ VM 環境を作成する](devtest-lab-create-environment-from-arm.md)。
- [DevTest Labs GitHub リポジトリ](https://github.com/Azure/azure-quickstart-templates)にある、DevTest Labs 自動化のためのクイック スタート Resource Manager テンプレート。
- [VSTS のトラブルシューティングのページ](/azure/devops/pipelines/troubleshooting)
