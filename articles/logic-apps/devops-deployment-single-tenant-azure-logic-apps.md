---
title: シングルテナントの Azure Logic Apps への DevOps のデプロイ
description: シングルテナントの Azure Logic Apps への DevOps のデプロイについて説明します。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: conceptual
ms.date: 09/13/2021
ms.openlocfilehash: b7e418c6e8fd282bc04f7a4a95c90d085e86deae
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130067081"
---
# <a name="devops-deployment-for-single-tenant-azure-logic-apps"></a>シングルテナントの Azure Logic Apps への DevOps のデプロイ

ネイティブな分散クラウド アプリが増えており、組織で扱うコンポーネントも、より多くの環境に分散したものが多くなっています。 制御と一貫性を維持するため、DevOps ツールとプロセスを使用することにより、環境を自動化し、より多くのコンポーネントをより迅速かつ確実にデプロイできます。

この記事では、シングルテナントの Azure Logic Apps 向けの現在の継続的インテグレーションと継続的配置 (CI/CD) エクスペリエンスの概要について説明します。

<a name="single-tenant-versus-multi-tenant"></a>

## <a name="single-tenant-versus-multi-tenant"></a>シングルテナントとマルチテナント

"*マルチテナント*" の Azure Logic Apps でのリソースのデプロイは、Azure Resource Manager テンプレート (ARM テンプレート) が基になっており、それによってロジック アプリとインフラストラクチャの両方のリソース プロビジョニングが組み合わされて処理されます。 "*シングルテナント*" の Azure Logic Apps の場合は、アプリとインフラストラクチャの間でリソースのプロビジョニングを分離できるため、デプロイが簡単になります。

**ロジック アプリ (Standard)** リソースの種類を使用してロジック アプリを作成すると、ワークフローは、再設計されたシングルテナント用 Azure Logic Apps ランタイムによって実行されます。 [Azure Functions 機能拡張モデル](../azure-functions/functions-bindings-register.md)が使用されているこのランタイムは、[Azure Functions ランタイムの拡張機能としてホスト](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-runtime-deep-dive/ba-p/1835564)されます。 この設計により、ロジック アプリの移植性、柔軟性、パフォーマンス向上に加え、Azure Functions プラットフォームと Azure App Service エコシステムから受け継がれたその他の機能とベネフィットが提供されます。

たとえば、再設計されたコンテナー化されたランタイムとワークフローを、ロジック アプリの一部としてまとめてパッケージ化できます。 ロジック アプリ リソースをビルド、アセンブル、zip 圧縮してデプロイできる状態の成果物を生成する、一般的な手順またはタスクを使用できます。 アプリをデプロイするには、成果物をホスト環境にコピーしてから、アプリを開始してワークフローを実行します。 または、既に使ったことのあるツールとプロセスを使用して、成果物をデプロイ パイプラインに統合します。 たとえば、コンテナーが必要なシナリオの場合は、ロジック アプリをコンテナー化し、既存のパイプラインに統合することができます。

仮想ネットワークや接続などのインフラストラクチャ リソースを設定してデプロイするには、引き続き ARM テンプレートを使用でき、それらのリソースを、その目的に使用する他のプロセスやパイプラインと共に個別にプロビジョニングすることができます。

ビルドとデプロイの標準オプションを使用することにより、インフラストラクチャのデプロイから切り離して、アプリの開発に集中できます。 その結果、プロジェクト モデルはより汎用的になり、汎用アプリに使用する多くの類似したまたは同じデプロイ オプションを適用できます。 また、アプリ プロジェクト中心のデプロイ パイプラインの構築と、運用環境に発行する前に必要なテストと検証の実行に関して、エクスペリエンスがいっそう一貫したものになるというベネフィットもあります。 使用するテクノロジ スタックに関係なく、独自に選択したツールを使用してロジック アプリをデプロイできます。

<a name="devops-deployment-features"></a>

## <a name="devops-deployment-capabilities"></a>DevOps のデプロイ機能

シングルテナントの Azure Logic Apps は、Azure Functions プラットフォームと Azure App Service エコシステムから、多くの機能とベネフィットを受け継いでいます。 これらの更新には、まったく新しいデプロイ モデルと、ロジック アプリのワークフローに DevOps を使用するいっそう多くの方法が含まれています。

<a name="local-development-testing"></a>

### <a name="local-development-and-testing"></a>ローカル環境での開発とテスト

Visual Studio Code と Azure Logic Apps (Standard) 拡張機能を使用すると、ローカルな開発環境でシングルテナント ベースのロジック アプリ ワークフローを開発、ビルド、実行でき、Azure にデプロイする必要はありません。 シナリオでコンテナーが必要な場合は、[Azure Arc 対応 Logic Apps](azure-arc-enabled-logic-apps-overview.md) を使用して作成とデプロイを実行できます。

これは大きな機能の向上であり、Azure に配置されて実行されているリソースで開発を行う必要があるマルチテナント モデルと比較すると、大きなベネフィットになります。

<a name="separate-concerns"></a>

### <a name="separate-concerns"></a>懸案事項を分離する

シングルテナント モデルを使用すると、アプリと基になるインフラストラクチャの間で、懸案事項を分離することができます。 たとえば、アプリを、異なる環境を対象とする変更できない成果物として個別に、開発、ビルド、zip 圧縮、デプロイできます。 ロジック アプリ ワークフローには、通常、基になるインフラストラクチャより頻繁に更新される "アプリケーション コード" があります。 これらのレイヤーを分離することで、ロジック アプリのワークフローの構築にいっそう集中でき、必要なリソースを複数の環境にデプロイする労力が減ります。

![アプリとインフラストラクチャで分けられたデプロイ パイプラインを示す概念図。](./media/devops-deployment-single-tenant/deployment-pipelines-logic-apps.png)

<a name="resource-structure"></a>

### <a name="logic-app-resource-structure"></a>ロジック アプリのリソースの構造

[!INCLUDE [Visual Studio Code - logic app resource structure](../../includes/logic-apps-single-tenant-resource-structure.md)]

### <a name="logic-app-project-structure"></a>ロジック アプリのプロジェクトの構造

[!INCLUDE [Visual Studio Code - logic app project structure](../../includes/logic-apps-single-tenant-project-structure-visual-studio-code.md)]

<a name="deployment-containers"></a>

### <a name="container-deployment"></a>コンテナーのデプロイ

シングルテナント Azure Logic Apps では、コンテナーへのデプロイがサポートされています。つまり、ロジック アプリのワークフローをコンテナー化し、コンテナーを実行できる場所で実行できます。 アプリをコンテナー化した後のデプロイは、デプロイして管理する他のコンテナーとほとんど同じように機能します。

Azure DevOps が含まれる例については、「[コンテナーの CI/CD](https://azure.microsoft.com/solutions/architecture/cicd-for-containers/)」をご確認ください。

<a name="app-settings-parameters"></a>

### <a name="app-settings-and-parameters"></a>アプリの設定とパラメーター

マルチテナントの Azure Logic Apps で ARM テンプレートを使用すると、異なる開発、テスト、運用環境の間でロジック アプリの環境変数を維持する必要がある場合に、問題になります。 ARM テンプレート内のすべては、デプロイ時に定義されます。 1 つの変数を変更するだけでよい場合も、すべてを再デプロイする必要があります。

シングルテナントの Azure Logic Apps の場合は、アプリの設定とパラメーターを使用することで、実行時に環境変数を呼び出して参照できるので、頻繁に再デプロイする必要はありません。

<a name="managed-connectors-built-in-operations"></a>

## <a name="managed-connectors-and-built-in-operations"></a>マネージド コネクタと組み込み操作

Azure Logic Apps のエコシステムには、拡大し続けるコレクションの一部として、[数百の Microsoft マネージド コネクタ](/connectors/connector-reference/connector-reference-logicapps-connectors)と組み込み操作が用意されており、シングルテナントの Azure Logic Apps で使用できます。 Microsoft によるこれらのコネクタと組み込み操作のメンテナンス方法は、シングルテナントの Azure Logic Apps でもほとんど同じです。

最も重要な改善点は、シングルテナント サービスを使用すると、いっそう多くの一般的なマネージド コネクタも、組み込み操作として利用できることです。 たとえば、Azure Service Bus、Azure Event Hubs、SQL などに対して組み込み操作を使用できます。 一方、マネージド コネクタのバージョンは現在も利用でき、引き続き機能します。

組み込み操作を使用して作成する接続は、組み込み接続または "*サービス プロバイダー接続*" と呼ばれます。 組み込み操作とその接続は、ワークフローが実行されるのと同じプロセスで、ローカルに実行されます。 どちらも、再設計された Logic Apps ランタイムでホストされます。 これに対し、マネージド接続つまり API 接続は、ARM テンプレートを使用してデプロイする Azure リソースとして、別個に作成されて実行されます。 その結果、組み込み操作とその接続では、ワークフローとの近接性のためにパフォーマンスが向上します。 サービス プロバイダーの接続が同じビルド成果物にパッケージされるため、この設計はデプロイ パイプラインでも適切に機能します。

Visual Studio Code でデザイナーを使用してワークフローを開発したり変更したりすると、Logic Apps エンジンによって、プロジェクトの **connections.json** ファイルに、必要な接続メタデータが自動的に生成されます。 以下のセクションでは、ワークフローで作成できる 3 種類の接続について説明します。 接続の種類ごとに JSON 構造が異なり、環境間を移動するとエンドポイントが変わるため、これを理解しておくことが重要です。

<a name="service-provider-connections"></a>

### <a name="service-provider-connections"></a>サービス プロバイダーの接続

Azure Service Bus や Azure Event Hubs などのサービス用の組み込み操作をシングルテナントの Azure Logic Apps で使用する場合は、ワークフローと同じプロセスで実行されるサービス プロバイダー接続を作成します。 この接続インフラストラクチャは、ロジック アプリ リソースの一部としてホストおよび管理されます。ワークフローで使用するサービス プロバイダー ベースの組み込み操作のための接続文字列は、アプリの設定で格納します。

ロジック アプリ プロジェクトにはワークフローごとに workflow.json があり、ワークフローの基になる JSON 定義が含まれます。 このワークフロー定義で、プロジェクトの connections.json ファイル内の必要な接続文字列を参照します。

次の例は、組み込みの Service Bus 操作のためのサービス プロバイダー接続が、プロジェクトの connections.json ファイルでどのように表されるかを示したものです。

```json
"serviceProviderConnections": {
   "{service-bus-connection-name}": {
      "parameterValues": {
         "connectionString": "@appsetting('servicebus_connectionString')"
      },
      "serviceProvider": {
         "id": "/serviceProviders/serviceBus"
      },
      "displayName": "{service-bus-connection-name}"
   },
   <...>
}
```

<a name="managed-connections"></a>

### <a name="managed-connections"></a>マネージド接続

ワークフローでマネージド コネクタを初めて使用すると、ターゲットのサービスまたはシステムに対するマネージド API 接続を作成し、ID の認証を行うように求めるメッセージが表示されます。 これらのコネクタは、Azure の共有コネクタ エコシステムによって管理されます。 API 接続は、Azure では個別のリソースとして存在し、実行されます。

Visual Studio Code のデザイナーを使用してワークフローの作成と開発を続けている間に、Logic Apps エンジンによって、ワークフロー内のマネージド コネクタに必要なリソースが、Azure に自動的に作成されます。 ユーザーがロジック アプリを格納するために設計した Azure リソース グループに、エンジンによってこれらの接続リソースが自動的に追加されます。

次の例は、マネージド Service Bus コネクタのための API 接続が、プロジェクトの connections.json ファイルでどのように表されるかを示したものです。

```json
"managedApiConnections": {
   "{service-bus-connection-name}": { 
      "api": {
         "id": "/subscriptions/{subscription-ID}/providers/Microsoft.Web/locations/{region}/managedApis/servicebus"
      },
      "connection": { 
         "id": "/subscriptions/{subscription-ID}/resourcegroups/{resource-group-name}/providers/Microsoft.Web/connections/servicebus"
      }, 
      "connectionRuntimeUrl": "{connection-runtime-URL}",
      "authentication": { 
         "type": "Raw",
         "scheme": "Key",
         "parameter": "@appsetting('servicebus_1-connectionKey')"
      },
   },
   <...>
}
```

<a name="azure-functions-connections"></a>

### <a name="azure-functions-connections"></a>Azure Functions 接続

Azure Functions で作成およびホストされている関数を呼び出すには、組み込みの Azure Functions 操作を使用します。 Azure Functions の呼び出しのための接続メタデータは、他の組み込み接続とは異なります。 このメタデータは、ロジック アプリ プロジェクトの connections.json ファイルに格納されますが、見た目は異なります。

```json
"functionConnections": {
   "{function-operation-name}": {
      "function": { 
         "id": "/subscriptions/{subscription-ID}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{function-app-name}/functions/{function-name}"
      },
      "triggerUrl": "{function-url}",
      "authentication": {
        "type": "QueryString",
         "name": "Code",
         "value": "@appsetting('azureFunctionOperation_functionAppKey')"
      }, 
      "displayName": "{functions-connection-display-name}"
   },
   <...>
}
```

## <a name="authentication"></a>認証

シングルテナントの Azure Logic Apps でのロジック アプリ ワークフロー用のホスティング モデルは単一のテナントであり、マルチテナント モデルより高い分離性はワークロードにとってベネフィットがあります。 さらに、シングルテナントの Azure Logic Apps ランタイムは移植可能です。つまり、ワークフローを他の環境で、たとえば Visual Studio Code でローカルに実行できます。 それでも、この設計でロジック アプリが Azure のマネージド コネクタ エコシステムにアクセスするには、ロジック アプリの ID の認証を行うための方法が必要です。 アプリには、マネージド接続を使用しているときに操作を実行するための適切なアクセス許可も必要です。

シングルテナント ベースのロジック アプリごとに、既定で、システム割り当てマネージド ID が自動的に有効になります。 この ID は、接続の作成時に使用される認証資格情報または接続文字列とは異なります。 実行時には、ロジック アプリにより、この ID を使用して、Azure アクセス ポリシーによる接続の認証が行われます。 この ID を無効にした場合、接続は実行時に機能しません。

次のセクションでは、ロジック アプリの実行場所に基づいて、マネージド接続の認証に使用できる認証の種類の詳細について説明します。 ロジック アプリ プロジェクトの connections.json ファイルに、マネージド接続ごとに存在する `authentication` オブジェクトで、そのマネージド接続の認証にロジック アプリで使用できる認証の種類が指定されています。

### <a name="managed-identity"></a>マネージド ID

Azure でホストされて実行されるロジック アプリの場合、[マネージド ID](create-managed-service-identity.md) が、Azure でホストおよび実行されるマネージド接続の認証に使用する、既定の推奨される認証の種類です。 ロジック アプリ プロジェクトの connections.json ファイルに含まれるマネージド接続の `authentication` オブジェクトで、認証の種類として `ManagedServiceIdentity` が指定されています。

```json
"authentication": {
   "type": "ManagedServiceIdentity"
}
```

### <a name="raw"></a>Raw

Visual Studio Code を使用してローカル開発環境で実行されるロジック アプリの場合は、Raw 認証キーが、Azure でホストされて実行されるマネージド接続の認証に使用されます。 これらのキーは、運用環境ではなく開発専用に設計されており、有効期限は 7 日です。 ロジック アプリ プロジェクトの connections.json ファイルに含まれるマネージド接続の `authentication` オブジェクトで、次の認証情報が指定されています。

```json
"authentication": {
   "type": "Raw", 
   "scheme": "Key", 
   "parameter": "@appsetting('connectionKey')"
 }
```

## <a name="next-steps"></a>次の手順

- [シングルテナントの Azure Logic Apps 用に DevOps のデプロイを設定する](set-up-devops-deployment-single-tenant-azure-logic-apps.md)
