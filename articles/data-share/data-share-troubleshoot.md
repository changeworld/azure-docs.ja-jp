---
title: Azure Data Share のトラブルシューティング
description: Azure Data Share でデータ共有を作成または受信するときに発生する招待の問題やエラーをトラブルシューティングする方法について説明します。
services: data-share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: troubleshooting
ms.date: 09/10/2021
ms.openlocfilehash: da746d14d891c875c9bed49c94e8b408d956e29e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124811246"
---
# <a name="troubleshoot-common-problems-in-azure-data-share"></a>Azure Data Share での一般的な問題をトラブルシューティングする 

この記事では、Azure Data Share での一般的な問題をトラブルシューティングする方法について説明します。 

## <a name="azure-data-share-invitations"></a>Azure Data Share の招待 

場合によっては、新しいユーザーがメールの招待状で **[招待を承諾]** を選択すると、空の招待一覧が表示されることがあります。 この問題には、次のいずれかの原因が考えられます。

* **Azure Data Share サービスが、Azure テナント内の Azure サブスクリプションのリソース プロバイダーとして登録されていない。** この問題は、Azure テナントに Data Share リソースがない場合に発生します。 

    Azure Data Share リソースを作成すると、Azure サブスクリプションにリソース プロバイダーが自動的に登録されます。 次の手順に従って、Data Share サービスを手動で登録できます。 これらの手順を実行するには、Azure サブスクリプションの[共同作成者ロール](../role-based-access-control/built-in-roles.md#contributor)が必要です。 

    1. Azure portal で **[サブスクリプション]** に移動します。
    1. Azure Data Share リソースの作成に使用するサブスクリプションを選択します。
    1. **[リソース プロバイダー]** を選択します。
    1. 「**Microsoft.DataShare**」を検索します。
    1. **[登録]** を選択します。

* **Azure サインイン用のメール アドレスではなく、メール エイリアスに招待が送信されている。** Azure Data Share サービスを既に登録している、または Azure テナントに Data Share リソースを作成しているのに招待が表示されない場合は、メール エイリアスが受信者としてリストされている可能性があります。 データ プロバイダーに連絡して、招待がメール エイリアスではなく、Azure サインイン用のメール アドレスに送信されるようにしてください。

* **招待が既に承諾されている。** メールに記載されているリンクを使用すると、Azure portal の **[Data Share Invitations]\(Data Share の招待\)** ページに移動します。 このページには、保留中の招待のみが表示されます。 承諾済みの招待は、このページに表示されません。 受信した共有を表示して、ターゲットの Azure Data Explorer クラスター設定を構成するには、招待を承諾するために使用した Data Share リソースに移動します。

* **あなたはテナントのゲスト ユーザーです。** テナントのゲスト ユーザーであれば、招待を表示する前に、テナントのメール アドレスを確認する必要があります。 検証が完了すると、12 か月間有効になります。 

## <a name="creating-and-receiving-shares"></a>共有の作成と受信

新しい共有を作成したり、データセットを追加したり、データセットをマップしたりするときに、次のエラーが表示されることがあります。

* データセットを追加できませんでした。
* データセットをマップできませんでした。
* Data Share リソース x に y へのアクセス許可を付与できません。
* x に対する適切なアクセス許可がありません。
* 選択された 1 つ以上のリソースに対する Azure Data Share アカウントのアクセス許可を追加できませんでした。

Azure データ ストアに対する十分なアクセス許可がない場合、これらのエラーのいずれかが表示されることがあります。 詳細については、「[ロールと要件](concepts-roles-permissions.md)」を参照してください。 

Azure データ ストアとの間でデータを共有または受信するには、書き込みアクセス許可が必要です。 通常、このアクセス許可は共同作成者ロールの一部です。 

初めて Azure データ ストアからデータを共有または受信する場合は、*Microsoft.Authorization/role assignments/write* アクセス許可も必要です。 通常、このアクセス許可は所有者ロールの一部です。 自分で Azure データ ストア リソースを作成したとしても、必ずしもそのリソースの所有者になるわけではありません。 

適切なアクセス許可がある場合は、Azure Data Share サービスによって、データ ストアにアクセスするためのデータ共有リソースの管理 ID が自動的に付与されます。 このプロセスには数分かかることがあります。 この遅延のためにエラーが発生した場合は、数分後にもう一度やり直してください。

SQL ベースの共有には、追加のアクセス許可が必要です。 前提条件の詳細については、[SQL ソースからの共有](how-to-share-from-sql.md)に関する記事を参照してください。

## <a name="snapshots"></a>スナップショット
スナップショットは、さまざまな理由で失敗する場合があります。 スナップショットの開始時刻、次に各データ セットの状態を選択して、詳細なエラー メッセージを開きます。 

スナップショットは、通常、次の理由で失敗します。

* Data Share に、ソース データ ストアからの読み取り、またはターゲット データ ストアへの書き込みアクセス許可がない。 詳細については、「[ロールと要件](concepts-roles-permissions.md)」を参照してください。 スナップショットを初めて使用する場合、Data Share リソースが Azure データ ストアにアクセスするのに数分かかることがあります。 しばらくしてからもう一度試してください。
* ソース データ ストアまたはターゲット データ ストアへの Data Share の接続がファイアウォールによってブロックされている。
* 共有データセット、ソース データ ストア、またはターゲット データ ストアが削除された。

ストレージ アカウントの場合、スナップショットの実行中にソースでファイルが更新されているために、スナップショットが失敗することがあります。 その結果、0 バイトのファイルがターゲットに表示されることがあります。 ソースで更新が完了すると、スナップショットは成功します。

SQL ソースの場合、スナップショットは次のような理由で失敗することがあります。

* Data Share のアクセス許可を付与するソース SQL スクリプトまたはターゲット SQL スクリプトが実行されていない。 または、Azure SQL Database または Azure Synapse Analytics (旧称 Azure SQL Data Warehouse) の場合は、Azure Active Directory 認証ではなく、SQL 認証を使用してスクリプトが実行されている。 Data Share アカウントに SQL データベースへの適切なアクセス許可が付与されている場合は、次のクエリを実行して確認できます。 ソース SQL データベースの場合、クエリ結果には、Data Share アカウントに *db_datareader* ロールがあることが示されます。 ターゲット SQL データベースの場合、クエリ結果には、Data Share アカウントに *db_datareader* ロール、*db_datawriter* ロール、*db_dlladmin* ロールがあることが示されます。

    ```sql
        SELECT DP1.name AS DatabaseRoleName,
        isnull (DP2.name, 'No members') AS DatabaseUserName
        FROM sys.database_role_members AS DRM
        RIGHT OUTER JOIN sys.database_principals AS DP1
        ON DRM.role_principal_id = DP1.principal_id
        LEFT OUTER JOIN sys.database_principals AS DP2
        ON DRM.member_principal_id = DP2.principal_id
        WHERE DP1.type = 'R'
        ORDER BY DP1.name; 
     ``` 

* ソース データ ストアまたはターゲット SQL データ ストアが一時停止されている。
* スナップショット プロセスまたはターゲット データ ストアで SQL データ型がサポートされていない。 詳細については、[SQL ソースからの共有](how-to-share-from-sql.md#supported-data-types)に関する記事を参照してください。
* ソース データ ストアまたはターゲット SQL データ ストアが他のプロセスによってロックされている。 Azure Data Share では、これらのデータ ストアをロックしません。 ただし、これらのデータ ストアの既存のロックによって、スナップショットが失敗する可能性があります。
* ターゲット SQL テーブルが外部キー制約により参照されています。 スナップショット中、ターゲット テーブルが、ソース データ内のテーブルと同じ名前を持っている場合、Azure Data Share ではそのテーブルを削除して、新しいテーブルを作成します。 ターゲット SQL テーブルが外部キー制約により参照されている場合は、テーブルを削除できません。
* ターゲット CSV ファイルは生成されるが、Excel でそのデータを読み取れない。 ソース SQL テーブルに英語以外の文字が含まれるデータが含まれている場合に、この問題が発生することがあります。 Excel で、 **[データの取得]** タブを選択し、CSV ファイルを選択します。 ファイルの Origin として **65001:Unicode (UTF-8)** を選択してから、データを読み込みます。

## <a name="update-snapshot-schedule"></a>スナップショット スケジュールを更新する
送信された共有のスナップショット スケジュールがデータ プロバイダーによって更新された後、データ コンシューマーによって前のスナップショット スケジュールが無効化され、受信された共有の更新済みスナップショット スケジュールが有効化される必要があります。 スナップショット スケジュールは協定世界時で保存され、UI はコンピューターの現地時刻で表示されます。 夏時間への自動調整は行われません。  

## <a name="in-place-sharing"></a>インプレース共有
Azure Data Explorer クラスターでのデータベース マッピングは、次の理由で失敗するおそれがあります。

* ユーザーに Azure Data Explorer への "*書き込み*" アクセス許可が付与されていない。 通常、このアクセス許可は共同作成者ロールの一部です。 
* ソースまたはターゲットの Azure Data Explorer クラスターが一時停止している。
* ソースの Azure Data Explorer クラスターが EngineV2、ターゲットの Azure Data Explorer クラスターが EngineV3、またはその反対である。 異なるエンジン バージョンの Azure Data Explorer クラスター間での共有はサポートされていません。

## <a name="next-steps"></a>次のステップ

データの共有を始める方法については、[データの共有](share-your-data.md)に関するチュートリアルをご覧ください。 

データを受信する方法については、[データの受け入れと受信](subscribe-to-data-share.md)に関するチュートリアルに進んでください。