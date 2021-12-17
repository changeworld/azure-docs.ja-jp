---
title: コンピューティング インスタンスを作成および管理する
titleSuffix: Azure Machine Learning
description: Azure Machine Learning コンピューティング インスタンスを作成して管理する方法について説明します。 開発環境として、または Dev/Test 用のコンピューティング ターゲットとして使用します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.custom: devx-track-azurecli, references_regions
ms.author: sgilley
author: sdgilley
ms.reviewer: sgilley
ms.date: 10/21/2021
ms.openlocfilehash: 8515ad8c9a3db24c8504094cf7273cd149b7ab2d
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131559183"
---
# <a name="create-and-manage-an-azure-machine-learning-compute-instance"></a>Azure Machine Learning コンピューティング インスタンスを作成して管理する

Azure Machine Learning ワークスペースで[コンピューティング インスタンス](concept-compute-instance.md)を作成および管理する方法について説明します。

コンピューティング インスタンスは、クラウド内で完全に構成され管理される自分の開発環境として使用します。 開発およびテストの場合、このインスタンスを[トレーニング コンピューティング ターゲット](concept-compute-target.md#train)として、または[推論ターゲット](concept-compute-target.md#deploy)に対して使用することもできます。   コンピューティング インスタンスは複数のジョブを並列に実行でき、ジョブ キューを備えています。 ご利用のワークスペース内の他のユーザーと、コンピューティング インスタンスを開発環境として共有することはできません。

この記事では、次のことについて説明します。

* コンピューティング インスタンスを[作成](#create)する
* コンピューティング インスタンスを[管理](#manage)する (開始、停止、再起動、削除)
* コンピューティング インスタンスを自動的に開始および停止する[スケジュールを作成](#schedule)する (プレビュー)
* [セットアップ スクリプトを使用](#setup-script)してコンピューティング インスタンスをカスタマイズおよび構成する

コンピューティング インスタンスは、企業で SSH ポートを開かなくても、[仮想ネットワーク環境](how-to-secure-training-vnet.md)でジョブを安全に実行できます。 ジョブはコンテナー化された環境で実行され、モデルの依存関係が Docker コンテナーにパッケージ化されます。

## <a name="prerequisites"></a>前提条件

* Azure Machine Learning ワークスペース。 詳細については、[Azure Machine Learning ワークスペースの作成](how-to-manage-workspace.md)に関するページをご覧ください。

* [Machine Learning サービス向けの Azure CLI 拡張機能](reference-azure-machine-learning-cli.md)、[Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro)、または [Azure Machine Learning Visual Studio Code 拡張機能](how-to-setup-vs-code.md)。

## <a name="create"></a>作成

> [!IMPORTANT]
> 以下に ("プレビュー") と付記されている項目は、現在、パブリック プレビュー段階です。
> プレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

**推定所要時間**: 約 5 分です。

コンピューティング インスタンスの作成は、ワークスペースに対する 1 回限りのプロセスです。 このコンピューティングは、開発ワークステーションとして、またはトレーニング用のコンピューティング先として再利用できます。 ワークスペースには複数のコンピューティング インスタンスをアタッチすることができます。 

コンピューティング インスタンスの作成に適用されるリージョンあたりの専用コア数は、VM ファミリ クォータ別およびリージョン合計クォータ別に、Azure Machine Learning コンピューティング クラスターのクォータと統合され、共有されます。 コンピューティング インスタンスを停止しても、コンピューティング インスタンスを再起動できるように、クォータは解放されません。 コンピューティング インスタンスを一旦作成したら、その仮想マシンのサイズは変更できなくなります。

<a name="create-instance"></a>次の例では、コンピューティング インスタンスの作成方法を示します。

# <a name="python"></a>[Python](#tab/python)

```python
import datetime
import time

from azureml.core.compute import ComputeTarget, ComputeInstance
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your instance
# Compute instance name should be unique across the azure region
compute_name = "ci{}".format(ws._workspace_id)[:10]

# Verify that instance does not exist already
try:
    instance = ComputeInstance(workspace=ws, name=compute_name)
    print('Found existing instance, use it.')
except ComputeTargetException:
    compute_config = ComputeInstance.provisioning_configuration(
        vm_size='STANDARD_D3_V2',
        ssh_public_access=False,
        # vnet_resourcegroup_name='<my-resource-group>',
        # vnet_name='<my-vnet-name>',
        # subnet_name='default',
        # admin_user_ssh_public_key='<my-sshkey>'
    )
    instance = ComputeInstance.create(ws, compute_name, compute_config)
    instance.wait_for_completion(show_output=True)
```

この例で使われているクラス、メソッド、パラメーターの詳細については、次のリファレンス ドキュメントをご覧ください。

* [ComputeInstance クラス](/python/api/azureml-core/azureml.core.compute.computeinstance.computeinstance)
* [ComputeTarget.create](/python/api/azureml-core/azureml.core.compute.computetarget#create-workspace--name--provisioning-configuration-)
* [ComputeInstance.wait_for_completion](/python/api/azureml-core/azureml.core.compute.computeinstance(class)#wait-for-completion-show-output-false--is-delete-operation-false-)


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az ml computetarget create computeinstance  -n instance -s "STANDARD_D3_V2" -v
```

詳細については、[az ml computetarget create computeinstance](/cli/azure/ml(v1)/computetarget/create#az_ml_computetarget_create_computeinstance) のリファレンスを参照してください。

# <a name="studio"></a>[スタジオ](#tab/azure-studio)

1. [Azure Machine Learning Studio](https://ml.azure.com) に移動します。
1. __[管理]__ の下にある、 __[コンピューティング]__ を選択します。
1. 最上部の **コンピューティング インスタンス** を選択します。
1. コンピューティング インスタンスがない場合は、ページの中央にある **[作成]** を選択します。
  
    :::image type="content" source="media/how-to-create-attach-studio/create-compute-target.png" alt-text="コンピューティング先を作成する":::

1. コンピューティング リソースの一覧が表示されたら、一覧の上にある **[+新規]** を選択します。

    :::image type="content" source="media/how-to-create-attach-studio/select-new.png" alt-text="[新規] の選択":::
1. 以下をフォームに入力します。

    |フィールド  |説明  |
    |---------|---------|
    |コンピューティング名     |  <ul><li>名前は必須であり、3 文字から 24 文字の長さにする必要があります。</li><li>有効な文字は、大文字、小文字、数字、 **-** 文字です。</li><li>名前の先頭は文字である必要があります</li><li>名前は、Azure リージョン内のすべての既存のコンピューティングで一意である必要があります。 選択した名前が一意でない場合は、アラートが表示されます</li><li>名前に **-** 文字が使用されている場合は、その後に少なくとも 1 文字を続ける必要があります</li></ul>     |
    |仮想マシンのタイプ |  CPU または GPU を選択します。 このタイプは作成後に変更することはできません     |
    |仮想マシンのサイズ     |  サポートされる仮想マシンのサイズがお客様のリージョンで制限されている可能性があります。 [利用可能な製品のリスト](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)に関するページを確認してください     |

1. コンピューティング インスタンスの詳細設定を構成しない場合は、 **[作成]** を選択します。
1. 必要に応じて <a name="advanced-settings"></a> **[詳細設定] の [次へ]** を選択します。

    * SSH アクセスを有効にします。  以下の[詳細な SSH アクセス手順](#enable-ssh)に従います。
    * 仮想ネットワークを有効にします。 **[リソース グループ]** 、 **[仮想ネットワーク]** 、および **[サブネット]** を指定して、Azure Virtual Network (vnet) 内にコンピューティング インスタンスを作成します。 __[No public IP]\(パブリック IP なし\)__ (プレビュー) を選択して、プライベート リンク ワークスペースを必要とするパブリック IP アドレスを作成しないようにすることもできます。 また、仮想ネットワークのセットアップに必要なこれらの[ネットワーク要件](./how-to-secure-training-vnet.md)も満たしている必要があります。 
    * コンピューターを別のユーザーに割り当てます。 他のユーザーに割り当てる方法の詳細については「、[代理作成](#on-behalf)」を参照してください。
    * セットアップ スクリプトを使用したプロビジョニング (プレビュー) - セットアップ スクリプトを作成して使用する方法の詳細については、「[スクリプトを使用したコンピューティング インスタンスのカスタマイズ](#setup-script)」を参照してください。
    * スケジュールを追加する (プレビュー)。 コンピューティング インスタンスが自動的に起動またはシャットダウンするために時間をスケジュール設定します。 以下の「[スケジュールの詳細](#schedule)」を参照してください。


---

また、[Azure Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.machinelearningservices/machine-learning-compute-create-computeinstance)を使用してコンピューティング インスタンスを作成することもできます。

## <a name="enable-ssh-access"></a><a name="enable-ssh"></a>SSH アクセスを有効にする

SSH アクセスは既定では無効になっています。  作成後に SSH アクセスの設定を変更することはできません。 [VS Code Remote](how-to-set-up-vs-code-remote.md) を使用して対話形式でデバッグする場合は、アクセスを有効にする必要があります。  

[!INCLUDE [amlinclude-info](../../includes/machine-learning-enable-ssh.md)]

コンピューティング インスタンスが作成され、実行中になったら、「[SSH アクセスを使って接続する](how-to-create-attach-compute-studio.md#ssh-access)」を参照してください。

## <a name="create-on-behalf-of-preview"></a><a name="on-behalf"></a> 代理作成 (プレビュー)

管理者は、データ科学者に代わってコンピューティング インスタンスを作成し、次のようにして彼らにそのインスタンスを割り当てることができます。

* Studio で、[詳細設定](?tabs=azure-studio#advanced-settings)を使用

* [Azure Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.machinelearningservices/machine-learning-compute-create-computeinstance)  このテンプレートに必要な TenantID と ObjectID を見つける方法の詳細については、「[認証構成のための ID オブジェクト ID を見つける](../healthcare-apis/azure-api-for-fhir/find-identity-object-ids.md)」を参照してください。  これらの値は Azure Active Directory ポータルでも見つけることができます。

* REST API

データ科学者向けにコンピューティング インスタンスを作成する場合は、次に示す [Azure ロールベースのアクセス制御 (Azure RBAC)](../role-based-access-control/overview.md) アクセス許可が必要です。

* *Microsoft.MachineLearningServices/workspaces/computes/start/action*
* *Microsoft.MachineLearningServices/workspaces/computes/stop/action*
* *Microsoft.MachineLearningServices/workspaces/computes/restart/action*
* *Microsoft.MachineLearningServices/workspaces/computes/applicationaccess/action*
* *Microsoft.MachineLearningServices/workspaces/computes/updateSchedules/action*

データ科学者は、コンピューティング インスタンスを開始、停止、再起動できます。 コンピューティング インスタンスは、次に使用できます。
* Jupyter
* JupyterLab
* RStudio
* 統合されたノートブック

## <a name="schedule-automatic-start-and-stop-preview"></a><a name="schedule"></a>自動開始と停止をスケジュール設定する (プレビュー)

自動シャットダウンと自動開始のスケジュールを複数定義します。 たとえば、午前 9 時に開始し、午後 6 時に停止する月曜日から木曜日のスケジュールを作成し、2 番目のスケジュールを金曜日の午前 9 時に開始して午後 4 時に停止するように作成します。  コンピューティング インスタンスごとに合計 4 つのスケジュールを作成できます。

コンピューティング インスタンスの[代理作成](#on-behalf)のためのスケジュールを定義することもできます。 スケジュールを作成して、停止状態のコンピューティング インスタンスを作成できます。 これは、ユーザーが別のユーザーの代わりにコンピューティング インスタンスを作成する場合に特に便利です。

### <a name="create-a-schedule-in-studio"></a>Studio でスケジュールを作成する

1. [フォームに記入します](?tabs=azure-studio#create-instance)。
1. フォームの 2 ページ目で、 **[詳細設定の表示]** を開きます。
1. **[スケジュールの追加]** を選択し、新しいスケジュールを追加します。

    :::image type="content" source="media/how-to-create-attach-studio/create-schedule.png" alt-text="スクリーンショット: 詳細設定でスケジュールを追加します。":::

1. **[コンピューティング インスタンスを開始する]** または **[コンピューティング インスタンスを停止する]** を選択します。
1. **[タイム ゾーン]** を選択します。
1. **[開始時刻]** または **[シャットダウン時刻]** を選択します。
1. このスケジュールがアクティブになる日を選択します。

    :::image type="content" source="media/how-to-create-attach-studio/stop-compute-schedule.png" alt-text="スクリーンショット: コンピューティング インスタンスのシャットダウンをスケジュールします。":::

1. 別のスケジュールを作成する場合は、 **[スケジュールの追加]** をもう一度選択します。

コンピューティング インスタンスが作成されたら、コンピューティング インスタンスの詳細セクションからスケジュールを表示、編集、または新しいスケジュールを追加できます。
タイムゾーン ラベルでは、夏時間については考慮されません。 たとえば、(UTC + 01:00) のアムステルダム、ベルリン、ベルン、ローマ、ストックホルム、ウィーンは、夏時間中は、実際は UTC + 02:00 になります。

### <a name="create-a-schedule-with-a-resource-manager-template"></a>Resource Manager テンプレートでスケジュールを作成する

Resource Manager [テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.machinelearningservices/machine-learning-compute-create-computeinstance)を使用して、コンピューティング インスタンスの自動の開始と停止をスケジュール設定できます。

Resource Manager テンプレートに、以下を追加します。

```
"schedules": "[parameters('schedules')]"
```

次に、cron または LogicApps 式を使用して、パラメーター ファイル内のインスタンスを開始または停止するスケジュールを定義します。
 
```json
        "schedules": {
        "value": {
        "computeStartStop": [
          {
            "triggerType": "Cron",
            "cron": {              
              "timeZone": "UTC",
              "expression": "0 18 * * *"
            },
            "action": "Stop",
            "status": "Enabled"
          },
          {
            "triggerType": "Cron",
            "cron": {              
              "timeZone": "UTC",
              "expression": "0 8 * * *"
            },
            "action": "Start",
            "status": "Enabled"
          },
          { 
            "triggerType": "Recurrence", 
            "recurrence": { 
              "frequency": "Day", 
              "interval": 1, 
              "timeZone": "UTC", 
              "schedule": { 
                "hours": [17], 
                "minutes": [0]
              } 
            }, 
            "action": "Stop", 
            "status": "Enabled" 
          } 
        ]
      }
    }
```

* アクションには、"Start" または "Stop" の値を指定できます。
* `Recurrence` のトリガーの種類では、この[繰り返しスキーマ](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger)と一緒に、ロジック アプリと同じ構文を使用します。
* `cron` のトリガーの種類には、次の標準の cron 構文を使用します。  

    ```cron
    // Crontab expression format: 
    // 
    // * * * * * 
    // - - - - - 
    // | | | | | 
    // | | | | +----- day of week (0 - 6) (Sunday=0) 
    // | | | +------- month (1 - 12) 
    // | | +--------- day of month (1 - 31) 
    // | +----------- hour (0 - 23) 
    // +------------- min (0 - 59) 
    // 
    // Star (*) in the value field above means all legal values as in 
    // braces for that column. The value column can have a * or a list 
    // of elements separated by commas. An element is either a number in 
    // the ranges shown above or two numbers in the range separated by a 
    // hyphen (meaning an inclusive range). 
    ```
### <a name="azure-policy-support-to-default-a-schedule"></a>1 つのスケジュールを既定にするための Azure Policy サポート
Azure Policy を使用して、サブスクリプション内のすべてのコンピューティング インスタンスに対して存在するシャットダウン スケジュールを適用するか、何も存在しない場合のスケジュールを既定値として設定します。
午後 10 時 (太平洋標準時) のシャットダウン スケジュールを既定にするためのサンプル ポリシーを次に示します。
```json
{
    "mode": "All",
    "policyRule": {
     "if": {
      "allOf": [
       {
        "field": "Microsoft.MachineLearningServices/workspaces/computes/computeType",
        "equals": "ComputeInstance"
       },
       {
        "field": "Microsoft.MachineLearningServices/workspaces/computes/schedules",
        "exists": "false"
       }
      ]
     },
     "then": {
      "effect": "append",
      "details": [
       {
        "field": "Microsoft.MachineLearningServices/workspaces/computes/schedules",
        "value": {
         "computeStartStop": [
          {
           "triggerType": "Cron",
           "cron": {
            "startTime": "2021-03-10T21:21:07",
            "timeZone": "Pacific Standard Time",
            "expression": "0 22 * * *"
           },
           "action": "Stop",
           "status": "Enabled"
          }
         ]
        }
       }
      ]
     }
    }
}    
```

## <a name="customize-the-compute-instance-with-a-script-preview"></a><a name="setup-script"></a> スクリプトを使用したコンピューティング インスタンスのカスタマイズ (プレビュー)

プロビジョニング時にコンピューティング インスタンスを自動的にカスタマイズして構成するには、セットアップ スクリプトを使用します。 管理者は、要件に従ってワークスペース内のすべてのコンピューティング インスタンスをプロビジョニングするために使用する、カスタマイズ スクリプトを作成できます。

セットアップ スクリプトを使用して実行できる操作の例を次に示します。

* パッケージ、ツール、およびソフトウェアのインストール
* データのマウント
* カスタムの Conda 環境と Jupyter カーネルの作成
* Git リポジトリの複製と git 構成の設定
* ネットワーク プロキシの設定
* 環境変数の設定
* JupyterLab 拡張機能のインストール

### <a name="create-the-setup-script"></a>セットアップ スクリプトを作成する

このセットアップ スクリプトは、*rootuser* として実行されるシェル スクリプトです。  **ノートブック** ファイルにスクリプトを作成するか、アップロードします。

1. [スタジオ](https://ml.azure.com)にサインインし、お使いのワークスペースを選択します。
2. 左側にある **[ノートブック]** を選択します
3. **[ファイルの追加]** ツールを使用して、セットアップ シェル スクリプトを作成またはアップロードします。  スクリプトのファイル名の末尾が ".sh" であることを確認してください。  新しいファイルを作成する場合は、**ファイルの種類** も *bash(.sh)* に変更します。

:::image type="content" source="media/how-to-create-manage-compute-instance/create-or-upload-file.png" alt-text="スタジオのノートブック ファイルにセットアップ スクリプトを作成またはアップロード":::

スクリプトを実行すると、スクリプトの現在の作業ディレクトリは、そのアップロード先のディレクトリになります。 たとえば、スクリプトを **Users > admin** にアップロードした場合、スクリプトの実行時のコンピューティング インスタンスと現在の作業ディレクトリ上のスクリプトの場所は */home/azureuser/cloudfiles/code/Users/admin* になります。これにより、スクリプトで相対パスを使用できます。

スクリプトの引数は、スクリプト内で $1、$2 などのように参照することができます。

conda 環境や jupyter カーネルのインストールなど、スクリプトで azureuser 固有の処理を行っていた場合は、次のように *sudo -u azureuser* ブロック内に置く必要があります

:::code language="bash" source="~/azureml-examples-main/setup-ci/install-pip-package.sh":::

*sudo -u azureuser* コマンドを実行すると、現在の作業ディレクトリが */home/azureuser* に変更されます。 また、このブロック内のスクリプト引数にアクセスすることはできません。

その他のスクリプトの例については、[azureml-examples](https://github.com/Azure/azureml-examples/tree/main/setup-ci)を参照してください。

スクリプトでは、次の環境変数も使用できます。

1. CI_RESOURCE_GROUP
2. CI_WORKSPACE
3. CI_NAME
4. CI_LOCAL_UBUNTU_USER。 これは azureuser を示しています

セットアップ スクリプトを **Azure Policy と組み合わせて使用すると、すべてのコンピューティング インスタンスの作成に対してセットアップ スクリプトを強制的に適用するか、既定値として設定できます**。 セットアップ スクリプトのタイムアウトの既定値は 15 分です。 これは、Studio UI または ARM テンプレートで DURATION パラメーターを使用して変更できます。
DURATION は、省略可能なサフィックスを持つ浮動小数点数です。秒の場合は 's' (既定値)、分の場合は 'm'、時の場合は 'h'、日の場合は 'd' です。

### <a name="use-the-script-in-the-studio"></a>スタジオでスクリプトを使用する

スクリプトを保存したら、コンピューティング インスタンスの作成時にそれを指定します。

1. [スタジオ](https://ml.azure.com/)にサインインし、お使いのワークスペースを選択します。
1. 左側にある **[コンピューティング]** を選択します。
1. **[新規]** を選択して、新しいコンピューティング インスタンスを作成します。
1. [フォームに記入します](?tabs=azure-studio#create-instance)。
1. フォームの 2 ページ目で、 **[詳細設定の表示]** を開きます。
1. **[セットアップ スクリプトを使用したプロビジョニング]** をオンにします。
1. 保存したシェル スクリプトを参照します。  または、コンピューターからスクリプトをアップロードします。
1. 必要に応じてコマンド引数を追加します。

:::image type="content" source="media/how-to-create-manage-compute-instance/setup-script.png" alt-text="スタジオのセットアップ スクリプトでコンピューティング インスタンスをプロビジョニングします。":::

ワークスペース ストレージが仮想ネットワークに接続されている場合、仮想ネットワーク内から Studio にアクセスしている場合を除き、セットアップ スクリプト ファイルにアクセスできない可能性があります。

### <a name="use-script-in-a-resource-manager-template"></a>Resource Manager テンプレートでスクリプトを使用する

Resource Manager [テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.machinelearningservices/machine-learning-compute-create-computeinstance)に `setupScripts` を追加して、コンピューティング インスタンスがプロビジョニングされたときにセットアップ スクリプトを呼び出すようにします。 次に例を示します。

```json
"setupScripts":{
    "scripts":{
        "creationScript":{
        "scriptSource":"workspaceStorage",
        "scriptData":"[parameters('creationScript.location')]",
        "scriptArguments":"[parameters('creationScript.cmdArguments')]"
        }
    }
}
```
上の *scriptData* では、*Users/admin/testscript.sh* などのノートブック ファイル共有作成スクリプトの場所を指定しています。上の *scriptArguments* では作成スクリプトの引数を指定していますが、必須ではありません。

代わりに、Resource Manager テンプレートでスクリプトをインラインで指定することもできます。  shell コマンドを使用して、ノートブック ファイル共有にアップロードされた任意の依存関係を参照できます。  インライン文字列を使用する場合、スクリプトの作業ディレクトリは */mnt/batch/tasks/shared/LS_root/mounts/clusters/**ciname**/code/Users* になります。

たとえば、次のように `scriptData` の base64 でエンコードされたコマンド文字列を指定します。

```json
"setupScripts":{
    "scripts":{
        "creationScript":{
        "scriptSource":"inline",
        "scriptData":"[base64(parameters('inlineCommand'))]",
        "scriptArguments":"[parameters('creationScript.cmdArguments')]"
        }
    }
}
```

### <a name="setup-script-logs"></a>セットアップ スクリプトのログ

セットアップ スクリプトの実行のログは、コンピューティング インスタンスの詳細ページにある [ログ] フォルダーに表示されます。 ログは、ログ \<compute instance name> フォルダーの下のノートブック ファイル共有に保存されます。 特定のコンピューティング インスタンスのスクリプト ファイルとコマンド引数は、詳細ページに表示されます。


## <a name="manage"></a>管理する

コンピューティング インスタンスを開始、停止、再起動、削除します。 コンピューティング インスタンスは自動的にスケールダウンされません。そのため、継続して課金されることがないように、リソースを必ず停止してください。 コンピューティング インスタンスを停止すると、そのインスタンスは解放されます。 その後、必要なときにもう一度開始します。 コンピューティング インスタンスを停止すると、コンピューティング時間の課金は停止しますが、ディスク、パブリック IP、および Standard Load Balancer に対しては引き続き課金されます。 

コンピューティング インスタンスの[スケジュールを作成](#schedule)して、時刻と曜日に基づいて自動的に開始および停止することができます。

> [!TIP]
> コンピューティング インスタンスには 120 GB の OS ディスクがあります。 ディスク領域が不足する場合は、コンピューティング インスタンスを停止または再起動する前に、[ターミナルを使用して](how-to-access-terminal.md)少なくとも 1 から 2 GB をクリアしてください。 コンピューティング インスタンスは、sudo シャットダウンをターミナルから発行して停止しないでください。 コンピューティング インスタンスの一時ディスク サイズは、選択した VM サイズによって異なります。これは /mnt にマウントされます。

# <a name="python"></a>[Python](#tab/python)

次の例では、コンピューティング インスタンスの名前は **instance** になります

* 状態を取得する

    ```python
    # get_status() gets the latest status of the ComputeInstance target
    instance.get_status()
    ```

* Stop

    ```python
    # stop() is used to stop the ComputeInstance
    # Stopping ComputeInstance will stop the billing meter and persist the state on the disk.
    # Available Quota will not be changed with this operation.
    instance.stop(wait_for_completion=True, show_output=True)
    ```

* [開始]

    ```python
    # start() is used to start the ComputeInstance if it is in stopped state
    instance.start(wait_for_completion=True, show_output=True)
    ```

* やり直し

    ```python
    # restart() is used to restart the ComputeInstance
    instance.restart(wait_for_completion=True, show_output=True)
    ```

* 削除

    ```python
    # delete() is used to delete the ComputeInstance target. Useful if you want to re-use the compute name
    instance.delete(wait_for_completion=True, show_output=True)
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

次の例では、コンピューティング インスタンスの名前は **instance** になります

* Stop

    ```azurecli-interactive
    az ml computetarget stop computeinstance -n instance -v
    ```

    詳細については、[az ml computetarget の computeinstance の停止](/cli/azure/ml(v1)/computetarget/computeinstance#az_ml_computetarget_computeinstance_stop)に関する記事をご覧ください。

* [開始]

    ```azurecli-interactive
    az ml computetarget start computeinstance -n instance -v
    ```

    詳細については、[az ml computetarget の computeinstance の開始](/cli/azure/ml(v1)/computetarget/computeinstance#az_ml_computetarget_computeinstance_start)に関する記事をご覧ください。

* やり直し

    ```azurecli-interactive
    az ml computetarget restart computeinstance -n instance -v
    ```

    詳細については、[az ml computetarget の computeinstance 再起動](/cli/azure/ml(v1)/computetarget/computeinstance#az_ml_computetarget_computeinstance_restart)に関する記事をご覧ください。

* 削除

    ```azurecli-interactive
    az ml computetarget delete -n instance -v
    ```

    詳細については、[az ml computetarget の computeinstance の削除](/cli/azure/ml(v1)/computetarget#az_ml_computetarget_delete)に関する記事をご覧ください。

# <a name="studio"></a>[スタジオ](#tab/azure-studio)
<a name="schedule"></a>

Azure Machine Learning Studio 内のご利用のワークスペースで、 **[コンピューティング]** を選択してから、上部にある **[コンピューティング インスタンス]** を選択します。

![コンピューティング インスタンスを管理する](./media/concept-compute-instance/manage-compute-instance.png)

次の操作を行うことができます。

* 新しいコンピューティング インスタンスの作成
* コンピューティング インスタンス タブを更新する。
* コンピューティング インスタンスを開始、停止、再起動する。  インスタンスは、実行されるたびに支払いが発生します。 コンピューティング インスタンスを使用していないときは、コストを削減するために、コンピューティング インスタンスを停止します。 コンピューティング インスタンスを停止すると、そのインスタンスは解放されます。 その後、必要なときにもう一度開始します。 コンピューティング インスタンスが開始および停止される時刻をスケジュールすることもできます。
* コンピューティング インスタンスを削除する。
* コンピューティング インスタンスのリストをフィルター処理して、作成したもののみを表示する。

ワークスペース内で作成した (または作成してもらった) 各コンピューティング インスタンスに対しては、次のことを行うことができます。

* コンピューティング インスタンス上の Jupyter、JupyterLab、RStudio にアクセスする。
* コンピューティング インスタンスに SSH 接続する。 SSH アクセスは既定で無効になっていますが、コンピューティング インスタンスの作成時に有効にすることができます。 SSH アクセスは、公開/秘密キーのメカニズムを通じて実行されます。 このタブには、SSH 接続の詳細が表示されます (IP アドレス、ユーザー名、ポート番号など)。 仮想ネットワークのデプロイで SSH を無効にすると、パブリック インターネットからの SSH アクセスができなくなりますが、その状態でも、コンピューティング インスタンス ノードのプライベート IP アドレスとポート 22 を使用して、仮想ネットワーク内から SSH 接続できます。
* 次のためのコンピューティング名を選択します。
    * 特定のコンピューティング インスタンスに関する詳細 (IP アドレス、リージョンなど) を表示します。
    * コンピューティング インスタンスを開始および停止するスケジュールを作成または変更します (プレビュー)。  ページの下部までスクロールして、スケジュールを編集します。

---

[Azure RBAC](../role-based-access-control/overview.md) を使用すると、ワークスペース内のどのユーザーにコンピューティング インスタンスの作成、削除、開始、停止、再起動を許可するかを制御できます。 ワークスペースの共同作成者および所有者ロール内のユーザーはすべて、ワークスペース全体でコンピューティング インスタンスを作成、削除、開始、停止、および再起動することができます。 ただし、特定のコンピューティング インスタンスの作成者、またはその作成者に代わって作成された場合は割り当てられたユーザーのみが、そのコンピューティング インスタンス上の Jupyter、JupyterLab、および RStudio にアクセスすることが許可されます。 コンピューティング インスタンスは、ルート アクセス権を持つ 1 人のユーザー専用で、Jupyter/JupyterLab/RStudio を介してターミナルを使用できます。 コンピューティング インスタンスには、シングルユーザー ログインが用意され、すべてのアクションで、そのユーザーの ID が Azure RBAC と実験実行の属性で使用されます。 SSH アクセスは、公開/秘密キーのメカニズムを通じて制御されます。

Azure RBAC によって、次のアクションを制御できます。
* *Microsoft.MachineLearningServices/workspaces/computes/read*
* *Microsoft.MachineLearningServices/workspaces/computes/write*
* *Microsoft.MachineLearningServices/workspaces/computes/delete*
* *Microsoft.MachineLearningServices/workspaces/computes/start/action*
* *Microsoft.MachineLearningServices/workspaces/computes/stop/action*
* *Microsoft.MachineLearningServices/workspaces/computes/restart/action*
* *Microsoft.MachineLearningServices/workspaces/computes/updateSchedules/action*

コンピューティング インスタンスを作成するには、次のアクションのためのアクセス許可が必要です。
* *Microsoft.MachineLearningServices/workspaces/computes/write*
* *Microsoft.MachineLearningServices/workspaces/checkComputeNameAvailability/action*


## <a name="next-steps"></a>次のステップ

* [コンピューティング インスタンス ターミナルにアクセスする](how-to-access-terminal.md)
* [ファイルの作成と管理](how-to-manage-files.md)
* [トレーニングの実行を送信する](how-to-set-up-training-targets.md)
