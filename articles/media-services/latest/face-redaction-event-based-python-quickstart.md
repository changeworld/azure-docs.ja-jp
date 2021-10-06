---
title: イベントベースの顔編集
description: このクイックスタートでは、Azure Media Services のジョブを使用して受信ビデオを変換するイベントベースのソリューションを Azure にデプロイする方法を示します。
services: media-services
author: harmke
manager: ervandeh
ms.service: media-services
ms.workload: ''
ms.topic: quickstart
ms.date: 5/21/2021
ms.author: inhenkel
ms.openlocfilehash: 198009abab7e1c5f663a454eb23debc55c81d7f3
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124730809"
---
# <a name="event-based-face-redaction"></a>イベントベースの顔編集

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="introduction"></a>はじめに
 
一部のシナリオや使用例では、ビデオがデータ ストアに到着した時点で、Azure Media Services によってビデオを処理または分析する必要があります。 ユース ケースの例として、チームで現場や生産設備のビデオを分析して、現場の人が保安指示 (ヘルメットの着用など) に従っているかどうかを確認する場合などが考えられます。 このユース ケースでは、現場のエッジ デバイスで動きを検出したときにビデオをキャプチャし、その後 Azure に送信します。 プライバシー基準を遵守するため、チームで分析する前に、ビデオでキャプチャされた人の顔を編集する必要があります。 強化されたビデオをできるだけ早くチームで共有できるようにするには、ビデオが Azure に到着した時点で顔編集の手順を実行する必要があります。 このクイックスタートでは、Azure 上のこのようなイベントベースのシナリオで Azure Media Services を使用する方法について説明します。 ストレージ アカウントにアップロードされたビデオは、Azure Media Services のジョブを使用して変換されます。 Media Service v3 API が使用されます。

使用される具体的な変換は、[Face Redactor](./analyze-face-redaction-concept.md) と呼ばれます。 これは、選択された個人の顔をぼかすことでビデオを変更できる、Azure Media Analytics のプリセットです。

このクイックスタートを終了すると、ビデオに含まれている顔を編集できるようになります。

:::image type="content" source="./media/face-redaction-event-based-python-quickstart/output-redacted.gif" alt-text="出力例":::

## <a name="solution-overview"></a>ソリューションの概要

:::image type="content" source="./media/face-redaction-event-based-python-quickstart/architecture.png" alt-text="ソリューションのアーキテクチャの概要":::

このクイックスタートでは、上記のソリューション概要に含まれるソリューションをデプロイする方法について説明します。 最初にストレージ アカウント (Azure Data Lake Storage Gen2) を作成し、それにイベント リスナー (Event Grid) を接続して、ストレージ アカウントに新しい .mp4 ファイルがアップロードされたときに Azure 関数をトリガーします。 この Azure 関数では、Azure Media Services の事前に構成された変換にジョブを送信します。 この結果得られた編集済みのビデオは、BLOB Storage アカウントに格納されます。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。
- このクイックスタートで使用するリソース グループを作成します。

## <a name="get-the-sample-and-understand-its-deployment"></a>サンプルを取得し、そのデプロイを理解する

[Python サンプル リポジトリ](https://github.com/Azure-Samples/media-services-v3-python)のフォークを作成します。 このクイックスタートでは、FaceRedactorEventBased サンプルを使用します。

このサンプルのデプロイは、ソリューション全体をセットアップするための Azure サービスのデプロイ、新しいファイルのアップロード時にジョブを Azure Media Services に送信する関数アプリのデプロイ、Event Grid トリガーの構成という 3 つの異なる手順で構成されています。 これらの手順を実行する GitHub Actions ワークフローは既に作成されています。 したがって、このソリューションは、必要な変数を GitHub 環境に追加することでデプロイできます。つまり、ローカルの開発ツールは必要ありません。

## <a name="create-a-service-principal"></a>サービス プリンシパルを作成する

この GitHub Actions ワークフローを実行する前に、リソース グループに対する "*共同作成者*" および "*ストレージ BLOB データ閲覧者*" ロールを持つサービス プリンシパルを作成する必要があります。 このサービス プリンシパルは、GitHub Actions に代わってすべての Azure サービスをプロビジョニングして構成するアプリです。 このサービス プリンシパルは、ソリューションのデプロイ後に、処理する必要があるビデオの SAS トークンを生成するためにも使用されます。

サービス プリンシパルを作成し、リソース グループに対する必要なロールを割り当てるには、次の bash コマンドの変数を入力し、それを Cloud Shell で実行します。
```bash
# Replace <subscription-id>, <name-of-resource-group> and <name-of-app> with the corresponding values. 
# Make sure to use a unique name for the app name parameter.

app_name="<name-of-app>"
resource_group="<name-of-resource-group>"
subscription_id="<subscription-id>"

az ad sp create-for-rbac --name $app_name --role contributor \
                     --scopes /subscriptions/$subscription_id/resourceGroups/$resource_group \
                     --sdk-auth

object_id=$(az ad sp list --display-name $app_name --query [0].objectId -o tsv)

az role assignment create --assignee $object_id --role "Storage Blob Data Reader" \
                      --scope /subscriptions/$subscription_id/resourceGroups/$resource_group
```
  
このコマンドによって、次のような JSON オブジェクトが出力されます。
   
```json
{
  "clientId": "<GUID>",
  "clientSecret": "<GUID>",
  "subscriptionId": "<GUID>",
  "tenantId": "<GUID>",
  (...)
}
```
必ず出力をコピーし、次の手順で使用できるようにしてください。
 
## <a name="add-service-principal-details-to-github-secrets"></a>サービス プリンシパルの詳細を GitHub シークレットに追加する 

GitHub Actions で Azure 内に必要なサービスをデプロイして構成できるようにするため、サービス プリンシパルの詳細を [GitHub シークレット](https://docs.github.com/en/actions/reference/encrypted-secrets)として格納する必要があります。 フォークしたリポジトリの [Repo Settings] -> [Secrets] に移動し、[Create New Secrets] をクリックします。 次のシークレットを作成します。
 - 'AZURE_CREDENTIALS' を作成し、前の手順の出力 (完全な JSON) を貼り付けます。 このシークレットは、GitHub Actions ワークフローで Azure への接続を作成するために使用されます。 
 - 'CLIENT_ID' を作成し、前の手順の 'clientId' の値を貼り付けます。
 - 'CLIENT_SECRET' を作成し、前の手順の 'clientSecret' の値を貼り付けます。
 - 'TENANT_ID' を作成し、前の手順の 'tenantId' の値を貼り付けます。
 
## <a name="create-the-env-file"></a>.env ファイルを作成する

VideoAnalytics/FaceRedactorEventBased フォルダー内のフォークしたリポジトリに含まれる sample.env ファイルの内容をコピーします。 次に、[ファイルの追加] -> [新しいファイルの作成] の順にクリックして、独自の .env ファイルを作成します。 ファイル名に *.env* を付け、変数を入力します。 完了したら、[新しいファイルのコミット] をクリックします。 これでソリューションをデプロイする準備ができましたが、最初に、使用するコード ファイルを確認します。

## <a name="examine-the-code-for-provisioning-the-azure-resources"></a>Azure リソースをプロビジョニングするコードを確認する

以下の bash スクリプトでは、このソリューションで使用される Azure サービスをプロビジョニングします。 この bash スクリプトでは、Azure CLI を使用して次のアクションを実行します。
- 環境変数をローカル変数に読み込む。
- ADLSgen2、汎用の Azure ストレージ アカウント、Azure Media Services、Azure 関数アプリ、Event Grid のシステム トピックとサブスクリプションの名前を定義する。
- 定義された Azure サービスをプロビジョニングする。

[!code-bash[Main](../../../media-services-v3-python/VideoAnalytics/FaceRedactorEventBased/AzureServicesProvisioning/deploy_resources.azcli)]

## <a name="examine-azure-function-code"></a>Azure 関数コードを確認する

Azure リソースのプロビジョニングが正常に完了すると、Python コードを Azure 関数にデプロイする準備が整います。 **/azure-function/EventGrid_AMSJob/__init__.py** ファイルには、Azure Data Lake Gen2 ファイル システムにファイルが到着するたびに AMS ジョブをトリガーするロジックが含まれています。 このスクリプトでは、次のステップを実行します。
- 依存関係とライブラリをインポートする。
- 関数バインダーを使用して Azure Event Grid をリッスンする。
- イベント スキーマの変数を取得して定義する。
- AMS ジョブの入力/出力アセットを作成する。
- DataLakeService クライアントを使用して Azure Data Lake Gen2 に接続し、AMS ジョブ入力の認証として使用する SAS トークンを生成する。
- ジョブを構成して作成する。

[!code-python[Main](../../../media-services-v3-python/VideoAnalytics/FaceRedactorEventBased/AzureFunction/EventGrid_AMSJob/__init__.py)]

## <a name="examine-the-code-for-configuring-the-azure-resources"></a>Azure リソースを構成するコードを確認する 

以下の bash スクリプトは、プロビジョニング後のリソースを構成するために使用されます。 このスクリプトの実行は、ソリューションのデプロイの最後の手順であり、関数コードのデプロイ後に行われます。 このスクリプトでは、次の手順を実行します。
- 関数アプリのアプリ設定を構成する。
- Azure Event Grid のシステム トピックを作成する。
- ADLSg2 Raw フォルダーに BLOB が作成されたときに Azure 関数をトリガーするイベント サブスクリプションを作成する。
- REST API 呼び出しを使用して Azure Media Services の変換を作成する。 この変換は Azure 関数で呼び出される。

> [!NOTE]
> この時点では、Azure Media Services v3 Python SDK でも Azure CLI でも顔編集変換の作成はサポートされていませんでした。 そのため、REST API メソッドを使用して変換ジョブを作成します。

[!code-bash[Main](../../../media-services-v3-python/VideoAnalytics/FaceRedactorEventBased/AzureServicesProvisioning/configure_resources.azcli)]
 
## <a name="enable-github-actions-pipeline"></a>GitHub Actions パイプラインを有効にする
 このリポジトリのワークフロー ファイルには、このソリューションのデプロイを実行する手順が含まれています。 このワークフローを開始するには、独自のリポジトリに対してこれを有効にする必要があります。 これを有効にするには、リポジトリの [Actions] タブに移動し、[I understand my workflows, go ahead and enable them] を選択します。
:::image type="content" source="./media/face-redaction-event-based-python-quickstart/activate-workflow.png" alt-text="ワークフローを有効にする":::

GitHub Actions を有効にした後、ワークロード ファイルは [.github/workflows/main.yml](https://github.com/Azure-Samples/media-services-v3-python/blob/main/.github/workflows/main.yml) にあります。  トリガーとは別に、いくつかの手順を含むビルド ジョブがあります。 次の手順が含まれています。
- **env**: ここでは、前に追加した GitHub シークレットを参照する複数の環境変数が定義されます。
- **環境ファイルの読み取り**: ビルド ジョブの環境ファイルが読み取られます。
- **Resolve Project Dependencies Using Pip**: Azure 関数に必要なライブラリが GitHub Actions 環境に読み込まれます
- **Azure Login**: この手順では、サービス プリンシパルの詳細を使用して Azure CLI にログインするために、GitHub シークレットを使用します。
- **Deploy Azure resources using Azure CLI script file**: Azure リソースをプロビジョニングするデプロイ スクリプトを実行します
- **Deploy Azure Function code**: この手順では、Azure 関数をパッケージ化してディレクトリ './azure-function' にデプロイします。 この Azure 関数は、正常にデプロイされると、Azure portal に「EventGrid_AMSJob」という名前で表示されます。

:::image type="content" source="./media/face-redaction-event-based-python-quickstart/azurefunction.png" alt-text="Azure portal に表示される Azure 関数":::

- **Configure Azure resources using Azure CLI script file**: すべて正しい場合、最後の手順として、イベント リスナーをアクティブ化するように、デプロイされた Azure サービスを構成します。

ワークフローを有効にした後、[Deploy Azure Media Service FaceRedaction solution] ワークフローを選択し、[Run workflow] を選択します。 これで、前の手順で追加した変数を使用してソリューションがデプロイされます。 数分待って、正常に実行されたことを確認します。

:::image type="content" source="./media/face-redaction-event-based-python-quickstart/run-workflow.png" alt-text="ワークフローの実行":::

## <a name="test-your-solution"></a>ソリューションのテスト
Azure portal で、ADLS Gen2 のストレージ エクスプローラーに移動します。 raw コンテナーにファイルをアップロードします。 テスト ビデオをお探しの場合は、[この Web サイト](https://www.pexels.com/search/videos/group/)からダウンロードしてください。 ADLS Gen2 ストレージ アカウントにビデオをアップロードする方法については、下の図を参照してください。

:::image type="content" source="./media/face-redaction-event-based-python-quickstart/upload-test-data.png" alt-text="ビデオのアップロード":::

Azure Media Services アカウントに移動し、メニューから [変換とジョブ] を選択して、Azure Media Services インスタンスでジョブが作成されたことを確認します。 その後、Face Redactor 変換を選択します。

:::image type="content" source="./media/face-redaction-event-based-python-quickstart/ams-transform.png" alt-text="AMS の変換":::

このページには、Azure 関数によって起動されたジョブが表示されます。 ジョブは完了しているか、まだ処理中である可能性があります。

:::image type="content" source="./media/face-redaction-event-based-python-quickstart/ams-job.png" alt-text="AMS のジョブ":::

ジョブを選択すると、特定のジョブに関する詳細が表示されます。 出力アセット名を選択してから、それにリンクされているストレージ コンテナーへのリンクを使用すると、ジョブの完了時に処理済みのビデオが表示されます。

:::image type="content" source="./media/face-redaction-event-based-python-quickstart/ams-output.png" alt-text="AMS の出力":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

クイックスタートを完了したら、リソース グループ内に作成したリソースを削除します。 また、フォークされたリポジトリも削除できます。

## <a name="next-steps"></a>次のステップ

この例を変更するのは、おそらくローカルでコードを実行したい場合です。 ローカル開発では、ユーザー アカウントがローカルにインストールされている Azure CLI にログインするときに、サービス プリンシパルが必要ないため、sample.env ファイル内の変数で十分です。 ローカルでの Azure 関数の使用に関するガイダンスについては、[これらのドキュメント](../../azure-functions/create-first-function-vs-code-python.md)を参照してください。
