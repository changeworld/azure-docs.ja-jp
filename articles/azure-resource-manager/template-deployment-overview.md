---
title: Azure Resource Manager のテンプレート
description: リソースのデプロイに Azure Resource Manager テンプレートを使用する方法を説明します。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/07/2019
ms.author: tomfitz
ms.openlocfilehash: 61e9bbabee969280c07521edb05d67ba68c0c58e
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/08/2019
ms.locfileid: "70801981"
---
# <a name="azure-resource-manager-templates"></a>Azure Resource Manager のテンプレート

クラウドに移行するとき、多くのチームでアジャイル開発方法が採用されてきました。 このようなチームでは反復が迅速に行われます。 ソリューションをクラウドに繰り返しデプロイし、インフラストラクチャが信頼できる状態であることを把握する必要があります。 インフラストラクチャが反復的なプロセスの一部になると、運用と開発の間の区切りはなくなります。 チームは、統合されたプロセスを通してインフラストラクチャとアプリケーション コードを管理する必要があります。

これらの課題に対応するには、デプロイを自動化し、インフラストラクチャの手法をコードとして使用することができます。 コードでは、デプロイする必要があるインフラストラクチャを定義します。 インフラストラクチャのコードは、プロジェクトの一部になります。 アプリケーションのコードと同様に、インフラストラクチャのコードをソース リポジトリに格納し、そのバージョンを管理します。 チーム内の誰でもコードを実行して、同様の環境をデプロイできます。

Azure ソリューション用にインフラストラクチャをコードとして実装するには、Azure Resource Manager テンプレートを使用します。 テンプレートは JavaScript Object Notation (JSON) ファイルであり、プロジェクトのインフラストラクチャと構成が定義されています。 このテンプレートでは、デプロイしようとしているものを、それを作成する一連のプログラミング コマンドを記述しなくても記述できる、宣言型の構文を使用しています。 このテンプレートでは、デプロイするリソースとそれらのリソースのプロパティを指定します。

## <a name="benefits-of-resource-manager-templates"></a>Resource Manager のテンプレートの利点

Resource Manager テンプレートには、次のような利点があります。

* ソリューションのリソースを個別に処理するのではなく、すべてのリソースをグループとしてデプロイ、管理、監視します。

* ソリューションを開発のライフサイクル全体で繰り返しデプロイし、常にリソースを一貫した状態でデプロイします。

* スクリプトではなく宣言型のテンプレートを使用してインフラストラクチャを管理します。

Resource Manager テンプレートを使用するか、それとも他のコードとしてのインフラストラクチャ サービスのいずれかを使用するかを決定しようとしている場合は、それらのサービスよりテンプレートの方が優れている以下の点について検討してください。

* テンプレートでは、Azure の新しいサービスと機能をすぐに利用できます。 リソース プロバイダーによって新しいリソースが導入されたらすぐに、テンプレートを使用してこれらのリソースをデプロイできます。 他のコードとしてのインフラストラクチャ サービスでは、サードパーティが新しいリソース用のインターフェイスを実装するまで待つ必要があります。

* テンプレートのデプロイは、複数の命令型コマンドによってではなく、テンプレートの 1 回の送信によって処理されます。 Resource Manager では、相互に依存するリソースのデプロイが調整され、正しい順序で作成されます。 テンプレートが解析され、リソース間の参照に基づいてデプロイの正しい順序が決定されます。

   ![テンプレートのデプロイの比較](./media/template-deployment-overview/template-processing.png)

* テンプレートのデプロイは、Azure portal で追跡されます。 デプロイ履歴を確認し、テンプレートのデプロイに関する情報を取得できます。 デプロイされたテンプレート、渡されたパラメーター値、およびすべての出力値を確認できます。 他のコードとしてのインフラストラクチャ サービスは、ポータルでは追跡されません。

   ![デプロイ履歴](./media/template-deployment-overview/deployment-history.png)

* テンプレートのデプロイが事前に検証されます。 Resource Manager によって、デプロイ開始前にテンプレートが調べられ、デプロイが成功することが確認されます。 デプロイが半分完了した状態で停止する可能性が低くなります。

* [Azure ポリシー](../governance/policy/overview.md)を使用している場合、テンプレートでデプロイすると、非準拠リソースではポリシーの修復が行われます。

* Microsoft からは、規制とコンプライアンスの基準を満たすためのデプロイの[ブループリント](../governance/blueprints/overview.md)が提供されます。 これらのブループリントには、さまざまなアーキテクチャ用の既成のテンプレートが含まれています。

## <a name="idempotent"></a>べき等

べき等とは、同じ操作を何度実行しても同じ結果が得られることを意味します。 Resource Manager テンプレートのデプロイはべき等です。 同じテンプレートを何度でもデプロイし、同じリソースの種類を同じ状態で作成することができます。 この概念は、テンプレートを既存のリソース グループに再デプロイするか、テンプレートを新しいリソース グループにデプロイするかに関係なく、一貫した結果が得られることを意味するため重要です。

たとえば、3 つのリソースをリソース グループにデプロイしてあり、4 番目のリソースを追加する必要があるとします。 新しいリソースのみを含む新しいテンプレートを作成するのではなく、4 番目のリソースを既存のテンプレートに追加できます。 既に 3 つのリソースがあるリソース グループに新しいテンプレートをデプロイするときは、実行すべきアクションが Resource Manager によって決定されます。

リソースがリソース グループ内に存在し、要求にプロパティへの更新が含まれていない場合、アクションは実行されません。 リソースは存在しても、プロパティが変更されている場合は、既存のリソースが更新されます。 リソースが存在しない場合は、新しいリソースが作成されます。

デプロイが完了すると、リソースは常に期待どおりの状態に確実になっています。

## <a name="template-file"></a>テンプレート ファイル

テンプレート内では、JSON の機能を拡張する[テンプレート式](template-expressions.md)を記述できます。 これらの式では、Resource Manager によって提供される[関数](resource-group-template-functions.md)を使用します。

テンプレートには次のセクションがあります。

* [パラメーター](template-parameters.md) - 同じテンプレートを異なる環境で使用できるように、デプロイ時に値を指定します。

* [変数](template-variables.md) - テンプレートで再利用される値を定義します。 パラメーター値から構築できます。

* [ユーザー定義関数](template-user-defined-functions.md) - テンプレートを簡略化するカスタマイズされた関数を作成します。

* [リソース](resource-group-authoring-templates.md#resources) - デプロイするリソースを指定します。

* [出力](template-outputs.md) - デプロイされたリソースから値を返します。

## <a name="template-features"></a>テンプレートの機能

Resource Manager によって依存関係が分析され、確実に正しい順序でリソースが作成されます。 ほとんどの依存関係は暗黙的に決定されます。 ただし、あるリソースが別のリソースの前にデプロイされるように、依存関係を明示的に設定することができます。 詳細については、「 [Azure Resource Manager のテンプレートでの依存関係の定義](resource-group-define-dependencies.md) 」に関するページを参照してください。

テンプレートにリソースを追加し、必要に応じてデプロイすることができます。 通常、リソースをデプロイする必要があるかどうかを示すパラメーター値を渡します。 詳しくは、「[Resource Manager テンプレートでの条件付きデプロイ](conditional-resource-deployment.md)」をご覧ください。

テンプレートで JSON のブロックを何度も繰り返すのではなく、copy 要素を使用して、変数、プロパティ、またはリソースの複数のインスタンスを指定できます。 詳しくは、「[Azure Resource Manager テンプレートでのリソース、プロパティ、または変数の反復](resource-group-create-multiple.md)」をご覧ください。

## <a name="export-templates"></a>テンプレートのエクスポート

リソース グループの現在の状態をエクスポートするか、特定のデプロイに使用されたテンプレートを表示することで、既存のリソース グループのテンプレートを取得できます。 [エクスポートしたテンプレート](export-template-portal.md)を表示すると、テンプレートの構文について理解するのに役立ちます。

ポータルからソリューションを作成すると、ソリューションには自動的にデプロイ テンプレートが含まれます。 最初からテンプレートを作成する必要はありません。はじめにソリューション向けのテンプレートを使用して、それを特定のニーズに合わせてカスタマイズできます。 サンプルについては、「[クイック スタート:Azure portal を使用した Azure Resource Manager テンプレートの作成とデプロイ](./resource-manager-quickstart-create-templates-use-the-portal.md)」を参照してください。

## <a name="template-deployment-process"></a>テンプレートのデプロイ手順

テンプレートをデプロイするときは、Resource Manager によってテンプレートが REST API 操作に変換されます。 たとえば、Resource Manager が次のリソース定義を含むテンプレートを受け取ったとします。

```json
"resources": [
  {
    "apiVersion": "2016-01-01",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "mystorageaccount",
    "location": "westus",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {
    }
  }
]
```

Resource Manager は、この定義を次の REST API 操作に変換し、Microsoft.Storage リソース プロバイダーに送信します。

```HTTP
PUT
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/mystorageaccount?api-version=2016-01-01
REQUEST BODY
{
  "location": "westus",
  "properties": {
  }
  "sku": {
    "name": "Standard_LRS"
  },
  "kind": "Storage"
}
```

## <a name="template-design"></a>テンプレートの設計

テンプレートとリソース グループをどのように定義するかは、ソリューションの管理方法に応じてユーザーが自由に決定できます。 たとえば、1 つのリソース グループに 1 つのテンプレートで 3 層のアプリケーションをデプロイできます。

![3 層のテンプレート](./media/template-deployment-overview/3-tier-template.png)

ただし、インフラストラクチャ全体を 1 つのテンプレートで定義する必要はありません。 多くの場合、対象を絞って目的を特化した一連のテンプレートにデプロイの要件を分類することが合理的です。 これらのテンプレートは、さまざまなソリューションで簡単に再利用できます。 特定のソリューションをデプロイするには、必要なすべてのテンプレートをリンクするマスター テンプレートを作成します。 次の画像は、入れ子になった 3 つのテンプレートを含む親テンプレートを利用して 3 層のソリューションをデプロイする方法を示しています。

![入れ子になったテンプレートの層](./media/template-deployment-overview/nested-tiers-template.png)

層ごとに異なるライフサイクルを希望する場合は、3 層を個別のリソース グループにデプロイできます。 リソースは、他のリソース グループ内のリソースにリンクされることもあります。

![テンプレートの層](./media/template-deployment-overview/tier-templates.png)

入れ子になったテンプレートについては、「[Azure Resource Manager でのリンクされたテンプレートの使用](resource-group-linked-templates.md)」を参照してください。

## <a name="next-steps"></a>次の手順

* テンプレート ファイルのプロパティについては、「[Azure Resource Manager テンプレートの構造と構文の詳細](resource-group-authoring-templates.md)」をご覧ください。
* テンプレートのデプロイを始めるには、「[Visual Studio Code を使って Azure Resource Manager テンプレートを作成する](resource-manager-tools-vs-code.md)」をご覧ください。
* 管理機能など、Resource Manager サービスの概要については、「[Azure Resource Manager の概要](resource-group-overview.md)」をご覧ください。

