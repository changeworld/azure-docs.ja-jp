---
title: CI-CD、Azure DevOps、GitHub の問題のトラブルシューティング
titleSuffix: Azure Data Factory & Azure Synapse
description: さまざまな方法を使用して、Azure Data Factory および Azure Synapse Analytics での CI-CD の問題のトラブルシューティングを行います。
author: ssabat
ms.author: susabat
ms.reviewer: susabat
ms.service: data-factory
ms.subservice: ci-cd
ms.custom: synapse
ms.topic: troubleshooting
ms.date: 11/09/2021
ms.openlocfilehash: 283285045de43f147eefe68b380b1f6819c9b80a
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132720096"
---
# <a name="troubleshoot-ci-cd-azure-devops-and-github-issues-in-azure-data-factory-and-synapse-analytics"></a>Azure Data Factory および Azure Synapse Analytics での CI-CD、Azure DevOps、GitHub に関する問題のトラブルシューティング 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory および Azure Synapse Analytics での、継続的インテグレーションと継続的デプロイ (CI-CD)、Azure DevOps、GitHub の問題に関する一般的なトラブルシューティング方法について説明します。

ソース管理または DevOps 手法の使用に関する質問や問題がある場合は、役に立つ記事がいくつかあります。

- サービスでのソース管理の方法については、[ソース管理](source-control.md)に関するページをご覧ください。 
- サービスでの DevOps CI-CD の詳細については、[CI-CD](continuous-integration-delivery.md) に関するページをご覧ください。

## <a name="common-errors-and-messages"></a>一般的なエラーとメッセージ

### <a name="connect-to-git-repository-failed-due-to-different-tenant"></a>テナントが異なるため、Git リポジトリへの接続に失敗しました

#### <a name="issue"></a>問題

HTTP ステータス 401 のような認証の問題が発生することがあります。 特に、ゲストアカウントを持つテナントが複数ある場合は、問題がさらに複雑になる可能性があります。

#### <a name="cause"></a>原因

トークンは元のテナントから取得されましたが、サービスはゲスト テナントにあり、そのトークンを使用してゲスト テナントの DevOps にアクセスしようとしています。 これは正しい動作ではありません。

#### <a name="recommendation"></a>推奨

ゲスト テナントから発行されたトークンを使用する必要があります。 たとえば、同じ Azure Active Directory をゲスト テナントと DevOps に割り当てる必要があります。これにより、トークンの動作を正しく設定し、正しいテナントを使用できるようになります。

### <a name="template-parameters-in-the-parameters-file-are-not-valid"></a>パラメーター ファイルのテンプレート パラメーターが有効ではありません

#### <a name="issue"></a>問題

**同じ** 構成 (頻度や間隔など) でテストまたは運用ブランチで既に使用できるトリガーを Dev ブランチで削除すると、リリース パイプラインのデプロイは成功し、対応するトリガーはそれぞれの環境で削除されます。 しかし、テスト/運用環境のトリガーに対して **異なる** 構成 (頻度や間隔など) をしている場合に、Dev で同じトリガーを削除すると、デプロイは失敗し、エラーが発生します。

#### <a name="cause"></a>原因

CI/CD パイプラインは次のエラーで失敗します。

`
2020-07-20T11:19:02.1276769Z ##[error]Deployment template validation failed: 'The template parameters 'Trigger_Salesforce_properties_typeProperties_recurrence_frequency, Trigger_Salesforce_properties_typeProperties_recurrence_interval, Trigger_Salesforce_properties_typeProperties_recurrence_startTime, Trigger_Salesforce_properties_typeProperties_recurrence_timeZone' in the parameters file are not valid; they are not present in the original template and can therefore not be provided at deployment time. The only supported parameters for this template are 'factoryName, PlanonDWH_connectionString, PlanonKeyVault_properties_typeProperties_baseUrl
`

#### <a name="recommendation"></a>推奨

このエラーは、パラメーター化されたトリガーを削除することが多いために発生します。パラメーターは Azure Resource Manager (ARM) テンプレートでは使用できなくなります (トリガーが存在しなくなったため)。 パラメーターが ARM テンプレートになくなるため、DevOps パイプラインでオーバーライドされたパラメーターを更新する必要があります。 そうしないと、ARM テンプレート内のパラメーターが変更されるたびに、DevOps パイプラインでオーバーライドされたパラメーターを更新する必要があります (デプロイ タスク)。

### <a name="updating-property-type-is-not-supported"></a>プロパティの型の更新はサポートされていません

#### <a name="issue"></a>問題

CI/CD リリース パイプラインが次のエラーで失敗しています。

```output
2020-07-06T09:50:50.8716614Z There were errors in your deployment. Error code: DeploymentFailed.
2020-07-06T09:50:50.8760242Z ##[error]At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.
2020-07-06T09:50:50.8771655Z ##[error]Details:
2020-07-06T09:50:50.8772837Z ##[error]DataFactoryPropertyUpdateNotSupported: Updating property type is not supported.
2020-07-06T09:50:50.8774148Z ##[error]DataFactoryPropertyUpdateNotSupported: Updating property type is not supported.
2020-07-06T09:50:50.8775530Z ##[error]Check out the troubleshooting guide to see if your issue is addressed: https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment#troubleshooting
2020-07-06T09:50:50.8776801Z ##[error]Task failed while creating or updating the template deployment.
```

#### <a name="cause"></a>原因

このエラーは、ターゲット サービス インスタンスに、同じ名前だが、型が異なる統合ランタイムがあるためです。 Integration Runtime は、デプロイの間に同じ型である必要があります。

#### <a name="recommendation"></a>推奨

- [CI/CD のベスト プラクティス](continuous-integration-delivery.md#best-practices-for-cicd)を参照してください

- 統合ランタイムは頻繁に変更されることはなく、ご自身の CI/CD 内のすべてのステージで類似しているため、サービスは CI/CD のすべてのステージで同じ名前と型の統合ランタイムを使用すると想定します。 名前と型およびプロパティが異なる場合は、ソースとターゲットの統合ランタイム構成が一致していることを確認してから、リリース パイプラインをデプロイしてください。

- すべてのステージで統合ランタイムを共有する場合は、共有の統合ランタイムを含めるためだけに三項ファクトリを使用することを検討してください。 この共有ファクトリは、すべての環境で、リンクされた統合ランタイムの種類として使用できます。

### <a name="document-creation-or-update-failed-because-of-invalid-reference"></a>参照が無効なため、ドキュメントの作成または更新に失敗しました

#### <a name="issue"></a>問題

変更を発行しようとすると、次のエラー メッセージが表示されます。

`
"error": {
        "code": "BadRequest",
        "message": "The document creation or update failed because of invalid reference '<entity>'.",
        "target": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/<rgname>/providers/Microsoft.DataFactory/factories/<datafactory>/pipelines/<pipeline>",
        "details": null
    }
`
### <a name="cause"></a>原因

Git 構成をデタッチし、"リソースのインポート" フラグをオンにして再設定しました。これにより、サービスが "同期" として設定されます。 これは、発行の間に変更が行われないことを意味します。

#### <a name="resolution"></a>解決策

Git 構成をデタッチして再度設定し、[import existing resources]\(既存のリソースをインポートする\) チェックボックスをオンにしないようにします。

### <a name="data-factory-move-failing-from-one-resource-group-to-another"></a>リソース グループから別のリソース グループにデータ ファクトリを移動できません

#### <a name="issue"></a>問題

データ ファクトリをリソース グループ間で移動できないため、次のエラーが発生して失敗します: `
{
    "code": "ResourceMoveProviderValidationFailed",
    "message": "Resource move validation failed. Please see details. Diagnostic information: timestamp 'xxxxxxxxxxxxZ', subscription id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', tracking id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', request correlation id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'.",
    "details": [
        {
            "code": "BadRequest",
            "target": "Microsoft.DataFactory/factories",
            "message": "One of the resources contain integration runtimes that are either SSIS-IRs in starting/started/stopping state, or Self-Hosted IRs which are shared with other resources. Resource move is not supported for those resources."
        }
    ]
}
`

#### <a name="resolution"></a>解決方法

SSIS-IR と共有 IR を削除することで、移動できるようにすることができます。 統合ランタイムを削除しない場合は、コピーとクローンのドキュメントに従ってコピーを行い、完了後に古いデータ ファクトリを削除することをお勧めします。

###  <a name="unable-to-export-and-import-arm-template"></a>ARM テンプレートをエクスポートおよびインポートできません

#### <a name="issue"></a>問題

ARM テンプレートをエクスポートおよびインポートできません。 ポータルでエラーは発生しませんでしたが、ブラウザーのトレースでは、次のエラーが表示されます。

`Failed to load resource: the server responded with a status code of 401 (Unauthorized)`

#### <a name="cause"></a>原因

ユーザーとして顧客ロールが作成されましたが、必要なアクセス許可がありませんでした。 UI が読み込まれると、一連の露出調整値がチェックされます。 この場合、ユーザーのアクセス ロールには、*queryFeaturesValue* API へのアクセス許可がありません。 この API にアクセスするために、グローバル パラメーター機能がオフになっています。 ARM エクスポート コード パスは、グローバル パラメーター機能に部分的に依存しています。

#### <a name="resolution"></a>解決方法

この問題を解決するには、ロールに次のアクセス許可を追加する必要があります。*Microsoft.DataFactory/factories/queryFeaturesValue/action* このアクセス許可は、Data Factory の場合は既定で **Data Factory 共同作成者** ロールに、また Synapse Analytics の場合は **共同作成者** ロールに含まれている必要があります。

###  <a name="cannot-automate-publishing-for-cicd"></a>CI/CD の発行を自動化できない 

#### <a name="cause"></a>原因

最近まで、パイプラインをデプロイのために発行する唯一の方法は、ポータル内で UI をクリックすることでした。 現在、このプロセスは自動化できます。

#### <a name="resolution"></a>解決方法

CI/CD プロセスが拡張されました。 **自動** 発行機能によって、UI からすべての ARM テンプレート機能が取得、検証、エクスポートされます。 これにより、一般公開されている npm パッケージ [@microsoft/azure-data-factory-utilities](https://www.npmjs.com/package/@microsoft/azure-data-factory-utilities) を介してそのロジックが使用可能になります。 この方法により、UI に移動してボタンをクリックする代わりに、プログラムによってこれらのアクションをトリガーできます。 この方法を使用すると、CI/CD パイプラインで **真の** 継続的インテグレーション エクスペリエンスを利用できます。 詳細については、[CI/CD 発行の機能強化](./continuous-integration-delivery-improvements.md)に関するページに従ってください。 

###  <a name="cannot-publish-because-of-4-mb-arm-template-limit"></a>4 MB の ARM テンプレート制限が原因で発行できない  

#### <a name="issue"></a>問題

4 MB の合計テンプレート サイズという Azure Resource Manager の制限に達したため、デプロイできません。 この制限を超えた後にデプロイするための解決策が必要です。 

#### <a name="cause"></a>原因

Azure Resource Manager では、テンプレート サイズは 4 MB に制限されています。 テンプレートのサイズを 4 MB に、各パラメーター ファイルのサイズを 64 KB に制限します。 4 MB の制限は、反復的なリソースの定義と変数およびパラメーターの値で拡張された後のテンプレートの最終的な状態に適用されます。 しかし、その制限を超えています。 

#### <a name="resolution"></a>解決方法

中小規模のソリューションの場合、テンプレートを 1 つにするとわかりやすく、保守も簡単になります。 すべてのリソースと値を 1 つのファイルで参照できます。 高度なシナリオの場合、リンクされたテンプレートを使用することで、対象となるコンポーネントにソリューションを分割することができます。 [リンクされた、およびネストされたテンプレートの使用](../azure-resource-manager/templates/linked-templates.md?tabs=azure-powershell)に関する記事のベスト プラクティスに従ってください。

### <a name="cannot-connect-to-git-enterprise"></a>Git Enterprise に接続できない  

##### <a name="issue"></a>問題

アクセス許可の問題のため、Git Enterprise に接続できません。 "**422 - 処理できないエンティティです**" のようなエラーが表示される場合があります。

#### <a name="cause"></a>原因

* サービスに OAuth が構成されていません。 
* URL が正しく構成されていません。 repoConfiguration は [FactoryGitHubConfiguration](/dotnet/api/microsoft.azure.management.datafactory.models.factorygithubconfiguration?view=azure-dotnet&preserve-view=true) 型でなければなりません

#### <a name="resolution"></a>解決方法 

最初に、サービスへの OAuth アクセスを許可します。 次に、正しい URL を使用して Git Enterprise に接続する必要があります。 構成は、顧客組織に設定する必要があります。 たとえば、サービスはまず *https://hostname/api/v3/search/repositories?q=user%3&lt;customer credential&gt;....* を試行し、失敗します。 その後、 *https://hostname/api/v3/orgs/&lt;org&gt;/&lt; repo&gt;...* を試行し、成功します。 
 
### <a name="cannot-recover-from-a-deleted-instance"></a>削除されたインスタンスから復旧できない

#### <a name="issue"></a>問題
サービスのインスタンス、またはそれを含むリソース グループが削除され、復旧する必要があります。

#### <a name="cause"></a>原因

インスタンスを復旧できるのは、そのためにソース管理が DevOps または Git を使用してで構成されている場合のみです。 このアクションにより、発行された最新のリソースはすべて復旧されますが、発行されていないパイプライン、データセット、リンク サービスは復元 **されません**。 ソース管理がない場合、削除されたインスタンスを Azure バックエンドから復旧することはできません。これは、サービスが一度削除コマンドを受け取ると、インスタンスはバックアップなしで完全に削除されるためです。

#### <a name="resolution"></a>解決策

ソース管理が構成されている削除済みサービス インスタンスを復旧するには、以下の手順に従ってください。

 * サービスの新しいインスタンスを作成します。

 * 同じ設定で Git を再構成します。ただし、既存のリソースを、選択したリポジトリにインポートし、[新しいブランチ] を選択してください。

 * 変更をコラボレーション ブランチにマージして発行する pull request を作成します。

 * 削除されたデータ ファクトリまたは Synapse ワークスペース内にセルフホステッド統合ランタイムがあった場合は、新しいファクトリまたはワークスペース内に IR の新しいインスタンスを作成する必要があります。  オンプレミスまたは仮想マシンの IR インスタンスは、アンインストールしてから再インストールし、さらに新しいキーを取得する必要があります。 新しい IR の設定が完了した後、新しい IR を指すようにリンク サービスを更新し、再度接続をテストする必要があります。これを行わないと、**無効な参照** エラーで失敗します。

### <a name="cannot-deploy-to-different-stage-using-automatic-publish-method"></a>自動発行方法を使用して別のステージにデプロイできない

#### <a name="issue"></a>問題
お客様は、NPMパッケージのインストールや Azure DevOps を使用した上位ステージの設定など、必要なすべての手順を実行しましたが、デプロイはまだ失敗します。

#### <a name="cause"></a>原因

npm パッケージはさまざまな方法で使用できますが、主な利点の 1 つは、Azure パイプラインを介して使用されることです。 コラボレーション ブランチへの各マージでは、最初にすべてのコードを検証してから、リリース パイプラインで使用できるビルド成果物に ARM テンプレートをエクスポートするパイプラインをトリガーできます。 スタート パイプラインでは、YAML ファイルが有効で完全である必要があります。


#### <a name="resolution"></a>解決方法

package.json フォルダーが有効ではないため、次のセクションは無効です。

```
- task: Npm@1
  inputs:
    command: 'custom'
    workingDir: '$(Build.Repository.LocalPath)/<folder-of-the-package.json-file>' #replace with the package.json folder
    customCommand: 'run build validate $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName'
  displayName: 'Validate'
```
次のように、DataFactory が customCommand に含まれている必要があります: *'run build validate $(Build.Repository.LocalPath)/DataFactory/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName'* 。 上位ステージ用に生成された YAML ファイルに、必要な JSON アーティファクトがあることを確認します。

### <a name="git-repository-or-purview-connection-disconnected"></a>Git リポジトリまたは Purview 接続が切断される

#### <a name="issue"></a>問題
サービス インスタンスをデプロイすると、Git リポジトリまたは Purview 接続が切断されます。

#### <a name="cause"></a>原因
グローバル パラメーターをデプロイするために **[ARM テンプレートに含める]** を選択している場合、お使いのサービス インスタンスは ARM テンプレートに含まれます。 その結果、他のプロパティはデプロイ時に削除されます。

#### <a name="resolution"></a>解決方法
CI/CD でのグローバル パラメーターで説明されているように、 **[Include in ARM template]\(ARM テンプレートに含める\)** を選択解除し、PowerShell を使用してグローバル パラメーターをデプロイします。 
 
### <a name="extra--left--displayed-in-published-json-file"></a>発行された JSON ファイルに左の "[" が余分に表示される

#### <a name="issue"></a>問題
DevOps を使用して発行すると、"[" が余分にもう 1 つ表示されます。 サービスによって DevOps の ARMTemplate 内に、"[" がもう 1 つ自動的に追加されます。 JSON ファイルに "[[" のような式が表示されます。

#### <a name="cause"></a>原因
[ は ARM 用に予約された文字であるため、"[" をエスケープするために追加の [ が自動的に追加されます。

#### <a name="resolution"></a>解決方法
これは、CI/CD の発行プロセス時の通常の動作です。
 
### <a name="perform-cicd-during--progressqueued-stage-of-pipeline-run"></a>パイプライン実行の進捗/キューに登録されている段階で **CI/CD** を実行します

#### <a name="issue"></a>問題
パイプライン実行の進捗/キューに登録されている段階で CI/CD を実行する必要があります。

#### <a name="cause"></a>原因
パイプラインが進行中またはキューに登録されている場合は、最初にパイプラインとアクティビティを監視する必要があります。 次に、パイプラインが終了するまで待機するか、パイプラインの実行をキャンセルするかを選択できます。 
 
#### <a name="resolution"></a>解決方法
**SDK**、**Azure Monitor**、[Monitor](./monitor-visually.md) のいずれかを使ってパイプラインを監視できます。 次の操作は、[CI/CD ベスト プラクティス](./continuous-integration-delivery.md#best-practices-for-cicd)を参考にできます。 

### <a name="perform-unit-testing-during-development-and-deployment"></a>開発とデプロイ時に **単体テスト** を行います

#### <a name="issue"></a>問題
お使いのパイプラインの開発およびデプロイ中に単体テストを実行する必要があります。

#### <a name="cause"></a>原因
開発とデプロイのサイクル中に、パイプラインの単体テストを行ってから、パイプラインを手動または自動で発行できます。 テストの自動化により、再現性が保証され、より短い時間でより多くのテストを実行できます。 デプロイ前にすべてのパイプラインを自動的に再テストすることにより、回帰エラーからの保護を行うことができます。 自動テストは CI/CD ソフトウェア開発アプローチの重要なコンポーネントです。CI/CD デプロイ パイプラインに自動テストを含めることで、品質を大幅に向上させることができます。 長期的には、テストされたパイプライン成果物を再利用してコストと時間を節約します。  
 
#### <a name="resolution"></a>解決策
顧客にはさまざまなスキルセットによる異なる単体テストの要件がある場合があるため、通常は、次の手順に従います。

1. Azure DevOps CI/CD をセットアップするか、.NET/PYTHON/REST タイプの SDK ドリブン テスト戦略を開発します。
2. CI/CD の場合は、すべてのスクリプトを含むビルド成果物を作成し、リリース パイプラインにリソースをデプロイします。 SDK ドリブンなアプローチでは、Python で PyTest を使ってテスト ユニットを開発したり、.NET SDK を使って C# で Nunit を開発したりします。
3. 単体テストは、リリース パイプラインの一部として、または ADF Python/PowerShell/.NET/REST SDK とは別に実行します。 

たとえば、ファイル内の重複部分を削除し、キュレーションされたファイルをテーブルとしてデータベースに格納するとします。 パイプラインをテストするには、Azure DevOps を使用して CI/CD プロジェクトを設定します。
開発したパイプラインをデプロイするテスト パイプライン ステージを設定します。 テーブル データが期待どおりのものであることを確認するために、テスト ステージを構成して、Python テストを実行します。 CI/CD を使用していない場合は、**Nunit** を使用して、必要なテストでデプロイされたパイプラインをトリガーします。 結果に問題がなければ、最後にパイプラインを運用インスタンスに公開できます。 


### <a name="pipeline-runs-temporarily-fail-after-cicd-deployment-or-authoring-updates"></a>CI/CD のデプロイまたは作成の更新後、パイプラインの実行が一時的に失敗する

#### <a name="issue"></a>問題
一時的な障害が発生しても、しばらくするとユーザー操作なしで新しいパイプラインの実行が成功し始めます。

#### <a name="cause"></a>原因

この動作を引き起こすシナリオはいくつかありますが、いずれも古いバージョンの親リソースによって呼び出される新しいバージョンの依存リソースが関係しています。 たとえば、"パイプラインの実行" によって呼び出された既存の子パイプラインが、必要なパラメーターを持つように更新され、既存の親パイプラインが、これらのパラメーターを渡すように更新されたとします。 親パイプラインの実行中、ただし **パイプラインの実行** アクティビティの前にデプロイが行われると、新しいバージョンの子パイプラインは、古いバージョンのパイプラインによって呼び出され、予測されるパラメーターは渡されません。 これにより、パイプラインは *UserError* で失敗します。 これは、リンクされたサービスを参照するパイプラインの実行中、リンクされたサービスに対して破壊的変更が行われた場合など、他の種類の依存関係で発生する可能性もあります。 

#### <a name="resolution"></a>解決策

親パイプラインの新しい実行は自動的に成功し始めるため、通常、特に対処する必要はありません。 ただし、このようなエラーを防ぐには、お客様が作成やデプロイを計画するときに、破壊的変更が発生しないように依存関係を考慮する必要があります。 

### <a name="cannot-parameterize-integration-run-time-in-linked-service"></a>リンクされたサービスで統合ランタイムをパラメーター化できない

#### <a name="issue"></a>問題
リンクされたサービス統合のランタイムをパラメーター化する必要がある

#### <a name="cause"></a>原因
この機能はサポートされていません。 

#### <a name="resolution"></a>解決方法
手動で選択し、統合ランタイムを設定する必要があります。 PowerShell API を使用して変更することもできます。  この変更は、ダウンストリームに影響を与える可能性があります。 

### <a name="updatechange-integration-runtime-during-cicd"></a>CI/CD 中に統合ランタイムを更新/変更します。 
 
#### <a name="issue"></a>問題
CI/CD のデプロイ中に統合ランタイム名を変更する。  
 
#### <a name="cause"></a>原因
エンティティ参照のパラメーター化 (リンクされたサービスの統合ランタイム、アクティビティ内のデータセット、データセット内のリンクされたサービス) はサポートされていません。  デプロイ中にランタイム名を変更すると、依存リソース (統合ランタイムを参照するリソース) の形式が無効な参照で無効になります。  
 
#### <a name="resolution"></a>解決方法
Data Factory、CI/CD のすべてのステージで同じ名前と種類の統合ランタイムを使用する必要があります。 

### <a name="arm-template-deployment-failing-with-error-datafactorypropertyupdatenotsupported"></a>ARM テンプレートのデプロイが DataFactoryPropertyUpdateNotSupported エラーで失敗する

##### <a name="issue"></a>問題
ARM テンプレートのデプロイが DataFactoryPropertyUpdateNotSupported (プロパティの種類の更新はサポートされていません) などのエラーで失敗します。 

##### <a name="cause"></a>原因
ARM テンプレートのデプロイで、既存の統合ランタイムの種類を変更しようとしています。 データ ファクトリでは CI/CD のすべてのステージで同じ名前と種類の統合ランタイムが必要になるため、これは許可されず、デプロイ エラーが発生します。

##### <a name="resolution"></a>解決策
すべてのステージで統合ランタイムを共有する場合は、共有の統合ランタイムを含めるためだけに三項ファクトリを使用することを検討してください。 この共有ファクトリは、すべての環境で、リンクされた統合ランタイムの種類として使用できます。 詳細については、[継続的インテグレーションとデリバリー - Azure Data Factory](./continuous-integration-delivery.md#best-practices-for-cicd) に関する記事を参照してください

## <a name="next-steps"></a>次のステップ

トラブルシューティングの詳細について、次のリソースを参照してください。

*  [Data Factory ブログ](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory の機能のリクエスト](/answers/topics/azure-data-factory.html)
*  [Azure のビデオ](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Data Factory に関する Stack overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory に関する Twitter 情報](https://twitter.com/hashtag/DataFactory)