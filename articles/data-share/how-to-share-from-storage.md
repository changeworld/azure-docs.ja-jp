---
title: Azure Blob Storage と Azure Data Lake Storage からデータを共有および受信する
description: Azure Blob Storage と Azure Data Lake Storage からデータを共有および受信する方法について説明します
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 12/16/2020
ms.openlocfilehash: 9dfc8be54fc55842440e376916b2eb9bb04a4610
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2020
ms.locfileid: "97617087"
---
# <a name="share-and-receive-data-from-azure-blob-storage-and-azure-data-lake-storage"></a>Azure Blob Storage と Azure Data Lake Storage からデータを共有および受信する

[!INCLUDE[appliesto-storage](includes/appliesto-storage.md)]

Azure Data Share によって、ストレージ アカウントからのスナップショットベースの共有がサポートされています。 この記事では、次のソースからデータを共有および受信する方法について説明します。Azure Blob Storage、Azure Data Lake Storage Gen1、Azure Data Lake Storage Gen2。

Azure Data Share では、Azure Data Lake Gen1 および Azure Data Lake Gen2 からのファイル、フォルダー、ファイル システムの共有がサポートされています。 また、Azure Blob Storage からの BLOB、フォルダー、コンテナーの共有もサポートされています。 現時点では、ブロック BLOB のみがサポートされています。 これらのソースから共有されているデータは、Azure Data Lake Gen2 または Azure Blob Storage で受信することができます。

スナップショット ベースの共有でファイル システム、コンテナー、またはフォルダーが共有されている場合、データ コンシューマーは、共有データの完全なコピーを作成するか、増分スナップショット機能を利用して新規または更新されたファイルのみをコピーするかを選択できます。 増分スナップショットは、ファイルの最終更新時刻に基づいています。 同じ名前の既存のファイルは、スナップショット中に上書きされます。 ソースから削除されたファイルは、ターゲットで削除されません。 ソースにある空のサブフォルダーは、ターゲットにコピーされません。 

## <a name="share-data"></a>データの共有

### <a name="prerequisites-to-share-data"></a>データを共有するための前提条件

* Azure サブスクリプション:Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
* 受信者の Azure ログイン用メール アドレス (メール エイリアスは無効です)。
* Data Share リソースの作成用とは異なる Azure サブスクリプションにソース Azure データ ストアが存在する場合、Azure データ ストアがあるサブスクリプションで [Microsoft.DataShare リソースプロバイダー](concepts-roles-permissions.md#resource-provider-registration)を登録してください。 

### <a name="prerequisites-for-source-storage-account"></a>ソース ストレージ アカウントの前提条件

* Azure Storage アカウント: まだお持ちでない場合は、[Azure Storage アカウント](../storage/common/storage-account-create.md)を作成できます。
* ストレージ アカウントに書き込む権限。これは、*Microsoft.Storage/storageAccounts/write* に含まれています。 この権限は、投稿者ロール内に存在します。
* ストレージ アカウントにロールの割り当てを追加する権限。これは、*Microsoft.Authorization/role assignments/write* に含まれています。 この権限は、所有者ロール内に存在します。 

### <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

[Azure portal](https://portal.azure.com/) にサインインします。

### <a name="create-a-data-share-account"></a>Data Share アカウントを作成する

Azure リソース グループに Azure Data Share リソースを作成します。

1. ポータルの左上隅にあるメニュー ボタンを選択し、 **[リソースの作成]** (+) を選択します。

1. *Data Share* を検索します。

1. Data Share を選択し、 **[作成]** を選択します。

1. Azure Data Share リソースの基本的な詳細に、次の情報を入力します。 

     **設定** | **推奨値** | **フィールドの説明**
    |---|---|---|
    | サブスクリプション | 該当するサブスクリプション | Data Share アカウントに使う Azure サブスクリプションを選択します。|
    | リソース グループ | *test-resource-group* | 既存のリソース グループを使用するか、新しいリソース グループを作成します。 |
    | 場所 | *米国東部 2* | Data Share アカウントのリージョンを選択します。
    | 名前 | *datashareaccount* | Data Share アカウントの名前を指定します。 |
    | | |

1. **[Review + create]\(レビュー + 作成\)** を選択し、 **[作成]** を選択して Data Share アカウントをプロビジョニングします。 通常、新しい Data Share アカウントのプロビジョニングにかかる時間は約 2 分以下です。 

1. デプロイが完了したら、 **[リソースに移動]** を選択します。

### <a name="create-a-share"></a>共有を作成する

1. Data Share の [概要] ページに移動します。

    ![データを共有する](./media/share-receive-data.png "データを共有する") 

1. **[Start sharing your data]\(データの共有の開始する\)** を選択します。

1. **［作成］** を選択します   

1. 共有の詳細を入力します。 名前、共有の種類、共有の内容の説明、利用規約 (省略可能) を指定します。 

    ![EnterShareDetails](./media/enter-share-details.png "共有の詳細を入力する") 

1. **[続行]** をクリックします。

1. 共有にデータセットを追加するには、 **[データセットの追加]** を選択します。 

    ![共有にデータセットを追加する](./media/datasets.png "データセット")

1. 追加するデータセットの種類を選択します。 前の手順で選択した共有の種類 (スナップショットまたはインプレース) によって異なる種類のデータセット一覧が表示されます。 

    ![AddDatasets](./media/add-datasets.png "データセットを追加する")    

1. 共有するオブジェクトに移動し、[Add Datasets]\(データセットの追加\) を選択します。 

    ![SelectDatasets](./media/select-datasets.png "データセットを選択する")    

1. [Recipients]\(受信者\) タブで、[+ Add Recipient]\(+ 受信者の追加\) を選択して、データ コンシューマーのメール アドレスを入力します。 

    ![AddRecipients](./media/add-recipient.png "受信者の追加") 

1. **[続行]** をクリックします。

1. スナップショット共有タイプを選択した場合は、データの更新をデータ コンシューマーに提供するスナップショット スケジュールを構成できます。 

    ![EnableSnapshots](./media/enable-snapshots.png "スナップショットを有効化する") 

1. 開始時刻と繰り返しの間隔を選択します。 

1. **[続行]** をクリックします。

1. [Review + Create]\(レビュー + 作成\) タブで、パッケージの内容、設定、受信者、および同期の設定を確認します。 **［作成］** を選択します

ご自分の Azure データ共有が作成され、データ共有の受信者が招待を受け取れる状態になりました。 

## <a name="receive-data"></a>データの受信

### <a name="prerequisites-to-receive-data"></a>データを受信するための前提条件
データ共有の招待を受け入れる前に、次に一覧表示されている多くの Azure リソースをプロビジョニングする必要があります。 

データ共有の招待を受け入れる前に、すべての前提条件を満たしていることを確認します。 

* Azure サブスクリプション:Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
* Data Share の招待:" **<yourdataprovider@domain.com>** からの Azure Data Share の招待" という件名の Microsoft Azure からの招待。
* Data Share リソースの作成先となる Azure サブスクリプションとターゲット Azure データ ストアがある Azure サブスクリプションで [Microsoft.DataShare リソースプロバイダー](concepts-roles-permissions.md#resource-provider-registration)を登録します。

### <a name="prerequisites-for-target-storage-account"></a>ターゲット ストレージ アカウントの前提条件

* Azure Storage アカウント: [Azure Storage アカウント](../storage/common/storage-account-create.md)をまだお持ちでない場合は、作成できます。 
* ストレージ アカウントに書き込む権限。これは、*Microsoft.Storage/storageAccounts/write* に含まれています。 この権限は、投稿者ロール内に存在します。 
* ストレージ アカウントにロールの割り当てを追加する権限。これは、*Microsoft.Authorization/role assignments/write* に含まれています。 この権限は、所有者ロール内に存在します。  

### <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

[Azure portal](https://portal.azure.com/) にサインインします。

### <a name="open-invitation"></a>招待を開く

1. 招待は、メールから開くことができるほか、Azure portal から直接開くこともできます。 

   招待をメールから開くには、受信トレイでデータ プロバイダーからの招待を確認します。 招待は Microsoft Azure からで、件名は " **<yourdataprovider@domain.com> からの Azure Data Share の招待**" になっています。 **[招待を表示]** をクリックして、Azure で招待を確認します。 

   Azure portal から直接招待を開くには、Azure portal で **[データ共有への招待]** を検索します。 Data Share の招待が一覧表示されます。

   ![招待の一覧](./media/invitations.png "招待の一覧") 

1. 表示する共有を選択します。 

### <a name="accept-invitation"></a>招待を受け入れる
1. **[使用条件]** を含む、すべてのフィールドを確認してください。 使用条件に同意する場合は、同意を示すボックスをオンにする必要があります。 

   ![使用条件](./media/terms-of-use.png "使用条件") 

1. *[Target Data Share Account]\(ターゲット データ共有アカウント\)* の下で、ご自分のデータ共有のデプロイ先となるサブスクリプションとリソース グループを選択します。 

   **[Data Share Account]\(データ共有アカウント\)** フィールドには、既存のデータ共有アカウントがない場合には、 **[新規作成]** を選択します。 それ以外の場合は、自分のデータ共有を受け入れる既存のデータ共有アカウントを選択します。 

   **[Received Share Name]\(受信した共有名\)** フィールドは、データの指定で指定された既定のままにしておくことも、受信した共有のために新しい名前を指定することもできます。 

   利用規約に同意し、受信した共有を管理するための Data Share アカウントを指定したら、 **[Accept and configure]\(受け入れと構成\)** を選択します。 共有サブスクリプションが作成されます。 

   ![受け入れオプション](./media/accept-options.png "受け入れオプション") 

   これにより、Data Share アカウントに、受信した共有が表示されます。 

   招待を受け入れたくない場合は、*[拒否]* を選択します。 

### <a name="configure-received-share"></a>受信した共有を構成する
データを受信する場所を構成するには、次の手順に従います。

1. **[データセット]** タブを選択します。ターゲット先を割り当てるデータセットの横にあるチェックボックスをオンにします。 **[+ ターゲットへのマップ]** を選択し、ターゲット データ ストアを選択します。 

   ![ターゲットへのマップ](./media/dataset-map-target.png "ターゲットへのマップ") 

1. データを配置するターゲット データ ストアを選択します。 ターゲット データ ストア内にある、同じパスと名前を持つデータ ファイルがすべて上書きされます。 

   ![ターゲット ストレージ アカウント](./media/map-target.png "ターゲット ストレージ") 

1. スナップショットベースの共有の場合、データに定期的な更新を提供するためのスナップショット スケジュールがデータ プロバイダーによって作成されていれば、 **[スナップショット スケジュール]** タブを選択して、スナップショット スケジュールを有効にすることもできます。スナップショット スケジュールの横にあるチェック ボックスをオンにし、 **[+ Enable]\(+ 有効\)** を選択します。

   ![スナップショット スケジュールを有効にする](./media/enable-snapshot-schedule.png "スナップショット スケジュールを有効にする")

### <a name="trigger-a-snapshot"></a>スナップショットをトリガーする
これらの手順は、スナップショットベースの共有にのみ適用されます。

1. スナップショットをトリガーするには、 **[詳細]** タブ、 **[スナップショットのトリガー]** の順に選択します。 これで、データの完全なスナップショットまたは増分スナップショットをトリガーすることができます。 データ プロバイダーから初めてデータを受信する場合は、完全なコピーを選択します。 

   ![スナップショットのトリガー](./media/trigger-snapshot.png "スナップショットのトリガー") 

1. 最終実行状態が "*成功*" の場合、ターゲット データ ストアに移動して、受信したデータを表示します。 **[データセット]** を選択して、ターゲット パスのリンクをクリックしてください。 

   ![コンシューマー データセット](./media/consumer-datasets.png "コンシューマー データセットのマッピング") 

### <a name="view-history"></a>履歴を表示する
この手順は、スナップショットベースの共有にのみ適用されます。 スナップショットの履歴を表示するには、 **[履歴]** タブを選択します。ここには、過去 30 日間に生成されたすべてのスナップショットの履歴があります。 

## <a name="next-steps"></a>次の手順
Azure Data Share サービスを使用してストレージ アカウントからデータを共有および受信する方法について学習しました。 他のデータ ソースからの共有の詳細については、[サポートされているデータ ストア](supported-data-stores.md)に関するページに進んでください。