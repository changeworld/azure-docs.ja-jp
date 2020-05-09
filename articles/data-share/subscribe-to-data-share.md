---
title: チュートリアル:データの受け入れと受信 - Azure Data Share
description: チュートリアル - Azure Data Share を使用したデータの受け入れと受信
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: 4dff48f909cd3febbbb7e92dcf96070020b8f57c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/29/2020
ms.locfileid: "82145147"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share"></a>チュートリアル:Azure Data Share を使用したデータの受け入れと受信  

このチュートリアルでは、Azure Data Share を使用してデータ共有の招待を受け入れる方法を学習します。 自分が共有しているデータを受信する方法と、自分が共有しているデータのスナップショットを常に最新に保つために通常の更新間隔を有効にする方法を学習します。 

> [!div class="checklist"]
> * Azure Data Share の招待を受け入れる方法
> * Azure Data Share アカウントを作成する
> * 自分のデータの受信先を指定する
> * スケジュールされた更新のため、自分のデータ共有のサブスクリプションを作成する

## <a name="prerequisites"></a>前提条件
データ共有の招待を受け入れる前に、次に一覧表示されている多くの Azure リソースをプロビジョニングする必要があります。 

データ共有の招待を受け入れる前に、すべての前提条件を満たしていることを確認します。 

* Azure サブスクリプション:Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
* Data Share の招待:" **<yourdataprovider@domain.com>** からの Azure Data Share の招待" という件名の Microsoft Azure からの招待。
* Data Share リソースの作成先となる Azure サブスクリプションとターゲット Azure データ ストアがある Azure サブスクリプションで [Microsoft.DataShare リソースプロバイダー](concepts-roles-permissions.md#resource-provider-registration)を登録します。

### <a name="receive-data-into-a-storage-account"></a>ストレージ アカウントへのデータの受信: 

* Azure Storage アカウント: [Azure Storage アカウント](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)をまだお持ちでない場合は、作成できます。 
* ストレージ アカウントに書き込む権限。これは、*Microsoft.Storage/storageAccounts/write* に含まれています。 この権限は、投稿者ロール内に存在します。 
* ストレージ アカウントにロールの割り当てを追加する権限。これは、*Microsoft.Authorization/role assignments/write* に含まれています。 この権限は、所有者ロール内に存在します。  

### <a name="receive-data-into-a-sql-based-source"></a>SQL ベースのソースへのデータの受信:

* SQL サーバー上のデータベースに書き込む権限。これは、*Microsoft.Sql/servers/databases/write* に含まれています。 この権限は、投稿者ロール内に存在します。 
* データ共有リソースのマネージド ID が Azure SQL Database または Azure SQL Data Warehouse にアクセスするためのアクセス許可。 この操作を行うには、以下の手順を実行します。 
    1. 自分自身を SQL サーバーの Azure Active Directory 管理者として設定します。
    1. Azure Active Directory を使用して Azure SQL Database/Data Warehouse に接続します。
    1. クエリ エディター (プレビュー) を使用して次のスクリプトを実行し、Data Share のマネージド ID を "db_datareader、db_datawriter、db_ddladmin" として追加します。 SQL Server 認証ではなく Active Directory を使用して接続する必要があります。 

        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```      
        *<share_acc_name>* は、Data Share リソースの名前であることに注意してください。 Data Share リソースをまだ作成していない場合は、後でこの前提条件に戻ってくることが可能です。         

* クライアント IP SQL Server のファイアウォール アクセス。 この操作を行うには、以下の手順を実行します。 
    1. Azure portal の SQL サーバーで、 *[ファイアウォールと仮想ネットワーク]* に移動します。
    1. Azure サービスへのアクセスを許可するには、**オン** トグルをクリックします。
    1. **[+ クライアント IP の追加]** をクリックし、 **[保存]** をクリックします。 クライアントの IP アドレスは変わることがあります。 次回 Azure portal から SQL ターゲットにデータを受信するときにも、このプロセスを繰り返すことが必要になる場合もあります。 IP 範囲を追加することもできます。 


### <a name="receive-data-into-an-azure-data-explorer-cluster"></a>Azure Data Explorer クラスターへのデータの受信:  

* データ プロバイダーの Data Explorer クラスターと同じ Azure データ センター内の Azure Data Explorer クラスター: まだお持ちでない場合は、[Azure Data Explorer クラスター](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal)を作成できます。 データ プロバイダーのクラスターの Azure データ センターがわからない場合は、後でこの手順の中でクラスターを作成できます。
* Azure Data Explorer クラスターに書き込む権限。これは、*Microsoft.Kusto/clusters/write* に含まれています。 この権限は、投稿者ロール内に存在します。 
* Azure Data Explorer クラスターにロールの割り当てを追加する権限。これは、*Microsoft.Authorization/role assignments/write* に含まれています。 この権限は、所有者ロール内に存在します。 

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

[Azure portal](https://portal.azure.com/) にサインインします。

## <a name="open-invitation"></a>招待を開く

1. 招待は、メールから開くことができるほか、Azure portal から直接開くこともできます。 

   招待をメールから開くには、受信トレイでデータ プロバイダーからの招待を確認します。 招待は Microsoft Azure からで、件名は " **<yourdataprovider@domain.com> からの Azure Data Share の招待**" になっています。 **[招待を表示]** をクリックして、Azure で招待を確認します。 

   Azure portal から直接招待を開くには、Azure portal で **[データ共有への招待]** を検索します。 Data Share の招待が一覧表示されます。

   ![招待](./media/invitations.png "招待の一覧") 

1. 表示する共有を選択します。 

## <a name="accept-invitation"></a>招待を受け入れる
1. **[使用条件]** を含む、すべてのフィールドを確認してください。 使用条件に同意する場合は、同意を示すボックスをオンにする必要があります。 

   ![使用条件](./media/terms-of-use.png "使用条件") 

1. *[Target Data Share Account]\(ターゲット データ共有アカウント\)* の下で、ご自分のデータ共有のデプロイ先となるサブスクリプションとリソース グループを選択します。 

   **[Data Share Account]\(データ共有アカウント\)** フィールドには、既存のデータ共有アカウントがない場合には、 **[新規作成]** を選択します。 それ以外の場合は、自分のデータ共有を受け入れる既存のデータ共有アカウントを選択します。 

   **[Received Share Name]\(受信した共有名\)** フィールドは、データの指定で指定された既定のままにしておくことも、受信した共有のために新しい名前を指定することもできます。 

   ![ターゲット データ共有アカウント](./media/target-data-share.png "ターゲット データ共有アカウント") 

1. 利用規約に同意して共有の場所を指定したら、 *[Accept and configure]\(受け入れと構成\)* を選択します。 共有サブスクリプションが作成されます。

   スナップショットベースの共有では、次の画面で、データのコピー先のターゲット ストレージ アカウントを選択するように求められます。 

   ![受け入れオプション](./media/accept-options.png "受け入れオプション") 

   今すぐ招待を受け入れて、後でターゲット データ ストアを構成する場合は、 *[Accept and configure later]\(受け入れて後で構成する\)* を選択します。 後でストレージの構成を続行する場合、データ共有の構成を再開する詳しい手順については、[データセット マッピングの構成](how-to-configure-mapping.md)に関するページを参照してください。 

   インプレース共有の場合、データ共有の構成を再開する詳しい手順については、[データセット マッピングの構成](how-to-configure-mapping.md)に関するページを参照してください。 

   招待を受け入れたくない場合は、 *[拒否]* を選択します。 

## <a name="configure-storage"></a>記憶域を構成する
1. *[Target Storage Settings]\(ターゲット ストレージ設定\)* の下で、自分のデータの受信先となるサブスクリプション、リソース グループ、ストレージ アカウントを選択します。 

   ![ターゲット ストレージ設定](./media/target-storage-settings.png "ターゲット ストレージ") 

1. 自分のデータの定期的な更新を受信するには、スナップショットの設定が有効になっていることを確認します。 スナップショットの設定スケジュールは、ご利用のデータ プロバイダーでそれがデータ共有内に含まれている場合にのみ表示されることに注意してください。 

   ![スナップショット設定](./media/snapshot-settings.png "スナップショット設定") 

1. *[保存]* を選択します。 

> [!IMPORTANT]
> SQL ベースのデータを受信しており、そのデータを SQL ベースのソースに受信したいと考える場合、SQL Server をデータセットの宛先として構成する方法について、[データセットのマッピングを構成する](how-to-configure-mapping.md)攻略ガイドを参照してください。 

## <a name="trigger-a-snapshot"></a>スナップショットをトリガーする
これらの手順は、スナップショットベースの共有にのみ適用されます。

1. スナップショットをトリガーするには、[Received Share]\(受信した共有\)、[詳細] タブの順に選択し、 **[Trigger snapshot]\(スナップショットのトリガー\)** を選択します。 これで、データの完全なスナップショットまたは増分スナップショットをトリガーすることができます。 データ プロバイダーから初めてデータを受信する場合は、完全なコピーを選択します。 

   ![スナップショットのトリガー](./media/trigger-snapshot.png "スナップショットのトリガー") 

1. 最終実行状態が "*成功*" の場合、ターゲット データ ストアに移動して、受信したデータを表示します。 **[データセット]** を選択して、ターゲット パスのリンクをクリックしてください。 

   ![コンシューマー データセット](./media/consumer-datasets.png "コンシューマー データセットのマッピング") 

## <a name="view-history"></a>履歴を表示する
スナップショットの履歴を表示するには、[Received Share]\(受信した共有\)、[履歴] の順に移動します。 ここには、過去 60 日間に生成されたすべてのスナップショットの履歴があります。 

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、Azure Data Share の受け入れ方法と受信方法について学習しました。 Azure Data Share の概念についてさらに詳しく学習するには、[概念:Azure Data Share の用語集](terminology.md)に進んでください。