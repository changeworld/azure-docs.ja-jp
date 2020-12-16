---
title: Synapse で一般的なタスクを実行するために必要なロールについて理解する
description: この記事では、特定のタスクを実行するために必要な組み込みの Synapse RBAC ロールについて説明します
author: billgib
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/1/2020
ms.author: billgib
ms.reviewer: jrasnick
ms.openlocfilehash: aadc8e817eb2b5de856ac73cfd010b48d0531bfc
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/02/2020
ms.locfileid: "96523297"
---
# <a name="understand-the-roles-required-to-perform-common-tasks-in-synapse"></a>Synapse で一般的なタスクを実行するために必要なロールについて理解する

この記事では、Synapse Studio で作業を行うために必要な Synapse RBAC (ロールベースのアクセス制御) または Azure RBAC ロールについて説明します。  

## <a name="synapse-studio-access-control-and-workflow-summary"></a>Synapse Studio のアクセス制御とワークフローの概要 

### <a name="accessing-synapse-studio-and-viewing-its-content"></a>Synapse Studio へのアクセスとそのコンテンツの表示

- Synapse RBAC ロールが割り当てられているか、ワークスペースに対する所有者、共同作成者、または閲覧者の Azure ロールを持っている場合は、Synapse Studio を開いてワークスペースの詳細を確認し、Azure リソース (SQL プール、Spark プール、または統合ランタイム) を一覧表示できます。

### <a name="resource-management"></a>リソース管理

- ワークスペースの Azure 所有者または共同作成者である場合は、SQL プール、Apache Spark プール、統合ランタイムを作成できます。
- ワークスペースまたはそのリソースの Azure 所有者または共同作成者である場合は、専用の SQL プールを一時停止またはスケーリングしたり、Spark プールまたは統合ランタイムを構成したりすることができます。

### <a name="viewing-and-editing-code-artifacts"></a>コード成果物の表示と編集

- Synapse Studio にアクセスすると、SQL スクリプト、ノートブック、Spark ジョブ、リンクされたサービス、パイプライン、データフロー、トリガー、資格情報などの新しいコード成果物を作成できます。  (追加のアクセス許可があれば、これらの成果物を公開または保存できます。)  
- Synapse 成果物ユーザー、Synapse 成果物発行元、Synapse 共同作成者、または Synapse 管理者は、既に発行されているコード成果物を一覧表示すること、開くこと、および編集することができます。

### <a name="executing-your-code"></a>コードの実行

- SQL プールに定義された必要な SQL アクセス許可を持っている場合は、SQL プールで SQL スクリプトを実行できます。  
- ワークスペースまたは特定の Apache Spark プールに対する Synapse コンピューティング オペレーターのアクセス許可を持っている場合は、ノートブックと Spark ジョブを実行できます。  
- ワークスペースまたは特定の統合ランタイムに対するコンピューティング オペレーターのアクセス許可と、適切な資格情報のアクセス許可があれば、パイプラインを実行できます。

### <a name="monitoring-and-managing-execution"></a>実行の監視と管理
- Synapse ユーザーである場合は、Apache Spark プールで実行中のノートブックとジョブの状態を確認できます。
- ワークスペースまたは特定の Spark プールまたはパイプラインの Synapse コンピューティング オペレーターである場合は、ログを確認したり、実行中のジョブとパイプラインを取り消したりすることができます。  

### <a name="publishing-and-saving-your-code"></a>コードの発行と保存

- Synapse 成果物発行元、Synapse 共同作成者、または Synapse 管理者である場合は、新しいまたは更新されたコード成果物をサービスに発行できます。 
- ワークスペースが Git 対応で自分に Git アクセス許可がある場合は、Git リポジトリの作業ブランチにコード成果物をコミットできます。 Git 対応の場合、発行はコラボレーション ブランチからのもののみが許可されます。
- コード成果物への変更を発行またはコミットせずに Synapse Studio を終了すると、それらの変更は失われます。


## <a name="tasks-and-required-roles"></a>タスクと必要なロール

次の表は、一般的なタスクと、各タスクに必要な Synapse RBAC または Azure RBAC ロールを示しています。  

>[!Note]
>- Synapse 管理者は、必要なアクセス許可を提供する唯一のロールでない限り、各タスクに示されていません。  Synapse 管理者は、他の Synapse RBAC ロールによって可能になるすべてのタスクを実行できます。</br>
>- あらゆるスコープのすべての Synapse RBAC ロールによって、ワークスペースでの Synapse ユーザーのアクセス許可が提供されます
>- 表に示されているすべての Synapse RBAC のアクセス許可/アクションには、プレフィックスとして Microsoft/Synapse/workspaces/... が付いています </br>


タスク (実行する操作...) |ロール (必要なのは...)|Synapse RBAC アクセス許可/アクション
--|--|--
|ワークスペースで Synapse Studio を開く|Synapse ユーザー、または|読み取り
| |ワークスペースの Azure 所有者、共同作成者、または閲覧者|なし
|SQL プール、Apache Spark プール、統合ランタイムを一覧表示し、それらの構成の詳細にアクセスする|Synapse ユーザー、または|読み取り|
||ワークスペースの Azure 所有者、共同作成者、または閲覧者|なし
|リンクされたサービス、資格情報、マネージド プライベート エンドポイントを一覧表示する|Synapse ユーザー|読み取り
**SQL プール**||
専用 SQL プールまたはサーバーレス SQL プールを作成する|ワークスペースの Azure 所有者または共同作成者|なし
専用 SQL プールを管理 (一時停止、スケーリング、または削除) する|SQL プールまたはワークスペースの Azure 所有者または共同作成者|なし
SQL スクリプトを作成する</br>|Synapse ユーザー、または </br>ワークスペースの Azure 所有者または共同作成者、 </br>"*SQL スクリプトを実行するには追加の SQL アクセス許可が必要*"。|
発行された SQL スクリプトを一覧表示し、開く| Synapse 成果物ユーザー、成果物発行元、Synapse 共同作成者|artifacts/read
サーバーレス SQL プールで SQL スクリプトを実行する|プールに対する SQL アクセス許可 (Synapse 管理者に対して自動的に付与される)|なし
専用 SQL プールで SQL スクリプトを実行する|プールに対する SQL アクセス許可が必要|なし
新しいか、更新または削除された SQL スクリプトを発行する|Synapse 成果物発行元、Synapse 共同作成者|sqlScripts/write、delete
SQL スクリプトへの変更を Git リポジトリにコミットする|リポジトリに対する Git アクセス許可が必要|
ワークスペースに Active Directory 管理者を割り当てる (Azure Portal のワークスペースのプロパティを使用)|ワークスペースの Azure 所有者または共同作成者 |
**Apache Spark プール**||
Apache Spark プールを作成する|ワークスペースの Azure 所有者または共同作成者|
Apache Spark アプリケーションを監視する| Synapse ユーザー|読み取り
ノートブックとジョブの実行のログを表示する |Synapse コンピューティング オペレーター|
Apache Spark プールで実行中のノートブックまたは Spark ジョブをキャンセルする|Apache Spark プールに対する Synapse コンピューティング オペレーター|bigDataPools/useCompute
ノートブックまたはジョブ定義を作成する|ワークスペースの Synapse ユーザーまたは Azure 所有者、共同作成者、閲覧者</br> "*実行、発行、または保存を行うには追加のアクセス許可が必要*"|読み取り
発行済みノートブックまたはジョブの定義を一覧表示し、開く (保存済み出力の確認を含む)|ワークスペースでの Synapse 成果物ユーザー、Synapse 成果物発行元、Synapse 共同作成者|artifacts/read
ノートブックを実行し、その出力を確認する|Synapse Apache Spark 管理者、選択した Apache Spark プールに対する Synapse コンピューティング オペレーター|bigDataPools/useCompute 
ノートブックまたはジョブ定義 (出力を含む) を削除またはサービスに発行する|ワークスペースの成果物発行元、Synapse Apache Spark 管理者|notebooks/write、delete
ノートブックまたはジョブ定義に対する変更を Git 作業ブランチにコミットする|Git のアクセス許可|なし
**パイプライン、統合ランタイム、データフロー、データセット、およびトリガー**||
統合ランタイムを作成、更新、または削除する|ワークスペースの Azure 所有者または共同作成者|
Integration Runtime 状態を監視する|Synapse ユーザー|read、pipelines/viewOutputs
パイプラインの実行を確認する|Synapse 成果物発行元、Synapse 共同作成者|read、pipelines/viewOutputs 
パイプラインを作成する |Synapse ユーザー </br>[*_検討中 + WorkspaceSystemIdentity に対する Synapse 資格情報ユーザー_*]</br>"発行または保存するには追加のアクセス許可が必要"|read、credentials/UseSecret/action
データフロー、データセット、またはトリガーを作成する |Synapse ユーザー</br>"*発行または保存するには追加のアクセス許可が必要*"|読み取り
発行されたパイプラインを一覧表示し、開く |Synapse 成果物ユーザー | artifacts/read
データセット データのプレビュー|WorkspaceSystemIdentity に対する Synapse ユーザー + Synapse 資格情報ユーザー| 
既定の統合ランタイムを使用してパイプラインをデバッグする|WorkspaceSystemIdentity に対する Synapse ユーザー + Synapse 資格情報ユーザーの資格情報|read、 </br>credentials/useSecret
トリガーを作成する (今すぐトリガーを含む)|WorkspaceSystemIdentity に対する Synapse ユーザー + Synapse 資格情報ユーザー|read、credentials/useSecret/action
データのコピー ツールを使用してデータをコピーする|ワークスペース システム ID に対する Synapse ユーザー + Synapse 資格情報ユーザー|read、credentials/useSecret/action
データの取り込み (スケジュールを使用)|ワークスペース システム ID に対する Synapse 作成者 + Synapse 資格情報ユーザー|read、credentials/useSecret/action
新しいか、更新または削除されたパイプライン、データフロー、トリガーをサービスに発行する|ワークスペースでの Synapse 成果物発行元|pipelines/write、delete</br>dataflows write、delete</br>triggers/write、delete
新しい、更新または削除されたデータフロー、データセット、またはトリガーをサービスに発行する|ワークスペースでの成果物発行元|triggers/write、delete
パイプライン、データフロー、データセット、トリガーに対する変更を Git リポジトリに保存 (コミット) する |Git のアクセス許可|なし 
**リンクされたサービス**||
リンクされたサービスを作成する (資格情報の割り当てを含む)|Synapse ユーザー</br>"*実行、発行、または保存を行うには追加のアクセス許可が必要*"|読み取り
発行済みのリンクされたサービスを一覧表示し、開く|Synapse 成果物ユーザー|linkedServices/write、delete  
資格情報によって保護されているリンクされたサービスで接続をテストする|Synapse ユーザーと Synapse 資格情報ユーザー|credentials/useSecret/action|
リンクされたサービスを発行する|Synapse 成果物発行元|linkedServices/write、delete
リンクされたサービスの定義を Git リポジトリに保存 (コミット) する|Git のアクセス許可|なし
**アクセス管理**||
任意のスコープで Synapse RBAC ロールの割り当てを確認する|Synapse ユーザー|読み取り
ユーザー、グループ、サービス プリンシパルに対する Synapse RBAC ロールの割り当てと割り当て削除| ワークスペースまたは特定のワークスペース項目スコープでの Synapse 管理者|roleAssignments/write、delete
コード成果物への Synapse RBAC アクセスを作成または削除する|ワークスペース スコープの Synapse 管理者|roleAssignments/write、delete   

>[!Note]
>別のテナントのゲスト ユーザーは、割り当てられているロールに関係なく、ロールの割り当てを確認、追加、または変更できません。 

## <a name="next-steps"></a>次のステップ

[Synapse RBAC ロールの割り当てを確認する方法](./how-to-review-synapse-rbac-role-assignments.md)について学習する

[Synapse RBAC ロールの割り当てを管理する方法](./how-to-manage-synapse-rbac-role-assignments.md)について学習する。 