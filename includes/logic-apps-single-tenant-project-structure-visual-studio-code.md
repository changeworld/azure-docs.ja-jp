---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 09/13/2021
ms.openlocfilehash: 58f0c7bab2308912979d1a8dfc442f7af4d27591
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128548197"
---
Visual Studio Code では、ロジック アプリ プロジェクトには次のいずれかの種類があります。

* 拡張バンドルベース (Node.js) (既定の種類)
* NuGet パッケージベース (.NET) (既定の種類から変換できます)

これらの種類に基づき、プロジェクトにはわずかに異なるフォルダーとファイルが含まれています。 NuGet ベースのプロジェクトには、パッケージや他のライブラリ ファイルが入った .bin フォルダーが含まれています。 バンドルベースのプロジェクトには、.bin フォルダーと他のファイルは含まれていません。 シナリオによっては、カスタムの組み込み操作を開発して実行する場合などに、アプリを実行するために NuGet ベースのプロジェクトが必要です。 NuGet を使用するようにプロジェクトを変換する方法の詳細については、「[組み込みコネクタの作成を有効にする](../articles/logic-apps/create-single-tenant-workflows-visual-studio-code.md#enable-built-in-connector-authoring)」を参照してください。

既定のバンドルベースのプロジェクトの場合、プロジェクトのフォルダーとファイルの構造は次の例のようになります。

```text
MyBundleBasedLogicAppProjectName
| .vscode
| Artifacts
  || Maps 
     ||| MapName1
     ||| ...
  || Schemas
     ||| SchemaName1
     ||| ...
| WorkflowName1
  || workflow.json
  || ...
| WorkflowName2
  || workflow.json
  || ...
| workflow-designtime
| .funcignore
| connections.json
| host.json
| local.settings.json
```

プロジェクトのルート レベルでは、他の項目を含む次のファイルとフォルダーを確認できます。

| Name | フォルダーまたはファイル | Description |
|------|----------------|-------------|
| **.vscode** | Folder | Visual Studio Code 関連の設定ファイル (**extensions.json**、**launch.json**、**settings.json**、**tasks.json** ファイルなど) が含まれています。 |
| **アイテム** | Folder | 企業間 (B2B) シナリオをサポートするワークフローで定義および使用する統合アカウント成果物が含まれています。 たとえば、この構造の例には、XML 変換と検証の操作のマップとスキーマが含まれます。 |
| **<*WorkflowName*>** | フォルダー | ワークフローごとに、<*WorkflowName*> フォルダーに、ワークフローの基礎になっている JSON 定義が入った **workflow.json** ファイルが含まれています。 |
| **workflow-designtime** | Folder | 開発環境関連の設定ファイルが含まれています。 |
| **.funcignore** | ファイル | インストールされている [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md) に関連する情報が含まれています。 |
| **connections.json** | ファイル | ワークフローで使用されるマネージド接続と Azure 関数のメタデータ、エンドポイント、キーが含まれています。 <p><p>**重要**: 環境ごとに異なる接続と関数を使用するには、必ずこの **connections.json** ファイルをパラメーター化し、エンドポイントを更新してください。 |
| **host.json** | ファイル | ランタイム固有の構成設定と値が含まれます。たとえば、シングルテナント Azure Logic Apps プラットフォーム、ロジック アプリ、ワークフロー、トリガー、アクションの既定の制限などです。 ロジック アプリ プロジェクトのルート レベルでは、**host.json** メタデータ ファイルに、同じロジック アプリ内の "*すべてのワークフロー*" で実行中 (ローカルか Azure 内かを問わず) に使用される構成設定と既定値が含まれています。 <p>**注**: ロジック アプリを作成すると、Visual Studio Code コンテナーによってストレージ コンテナーにバックアップ **host.snapshot.*.json** ファイルが作成されます。 ロジック アプリを削除した場合、このバックアップ ファイルは削除されません。 同じ名前の別のロジック アプリを作成すると、別のスナップショット ファイルが作成されます。 同じロジック アプリに対して作成できるスナップショットは最大 10 件です。 この制限を超えると、次のエラーが表示されます。 <p>`Microsoft.Azure.WebJobs.Script.WebHost: Repository has more than 10 non-decryptable secrets backups (host))` <p>このエラーを解決するには、ストレージ コンテナーから追加のスナップショット ファイルを削除します。 |
| **local.settings.json** | ファイル | ローカルでの実行中にワークフローで使用されるアプリ設定、接続文字列、その他の設定が含まれています。 つまり、これらの設定と値は、ローカル開発環境でプロジェクトを実行する場合に "*のみ*" 適用されます。 Azure へのデプロイ中は、このファイルと設定は無視され、デプロイには含まれません。 <p><p>このファイルには、設定と値が、ローカル開発ツールによって `appSettings` 値として使用される "*ローカル環境変数*" として格納されます。 これらの環境変数は、実行時とデプロイ時の両方で、"*アプリ設定*" と "*パラメーター*" を使用して呼び出して参照できます。 <p><p>**重要**: **local.settings.json** ファイルにはシークレットが含まれる場合があるため、必ずこのファイルもプロジェクトのソース管理から除外してください。 |
||||