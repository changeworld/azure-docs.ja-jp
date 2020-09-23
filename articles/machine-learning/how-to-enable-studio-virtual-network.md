---
title: Azure Machine Learning Studio を仮想ネットワークで利用可能にする
titleSuffix: Azure Machine Learning
description: Azure Machine Learning Studio を使用して、仮想ネットワーク内に格納されているデータにアクセスします。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 07/16/2020
ms.custom: contperfq4, tracking-python
ms.openlocfilehash: 5dce7cde3c46fbcf3f764819f730f42cace4a74c
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90897541"
---
# <a name="use-azure-machine-learning-studio-in-an-azure-virtual-network"></a>Azure 仮想ネットワークで Azure Machine Learning Studio を使用する

この記事では、仮想ネットワークで Azure Machine Learning Studio を使用する方法について説明します。 学習内容は次のとおりです。

> [!div class="checklist"]
> - 仮想ネットワーク内のリソースから Studio にアクセスします。
> - 仮想ネットワーク内に格納されているデータへのアクセス権を Studio に付与します。
> - Studio によるストレージのセキュリティへの影響について確認します。

この記事は、Azure Machine Learning ワークフローをセキュリティで保護する手順を説明する全 5 パートからなるシリーズのパート 5 です。 まずは[パート 1:VNet の概要](how-to-network-security-overview.md)に関するページを読んで、アーキテクチャ全体を理解することを強くお勧めします。 

このシリーズの他の記事は次のとおりです。

[1.VNet の概要](how-to-network-security-overview.md) > [2.ワークスペースをセキュリティで保護する](how-to-secure-workspace-vnet.md) > [3.トレーニング環境をセキュリティで保護する](how-to-secure-training-vnet.md) > [4.推論環境をセキュリティで保護する](how-to-secure-inferencing-vnet.md) > [5.Studio の機能を有効にする](how-to-enable-studio-virtual-network.md)


> [!IMPORTANT]
> ほとんどの Studio では仮想ネットワークに格納されているデータを操作しますが、統合されたノートブックでは__異なります__。 統合されたノートブックでは、仮想ネットワーク内のストレージの使用はサポートされていません。 代わりに、コンピューティング インスタンスから Jupyter Notebook を使用できます。 詳細については、「[コンピューティング インスタンス ノートブック内のデータにアクセスする]()」のセクションを参照してください。


## <a name="prerequisites"></a>前提条件

+ [ネットワーク セキュリティの概要](how-to-network-security-overview.md)に関するページを参照して、一般的な仮想ネットワークのシナリオと全体的な仮想ネットワーク アーキテクチャについて理解してください。

+ 使用する仮想ネットワークとサブネットが既に存在すること。

+ [Private Link が有効になっている Azure Machine Learning ワークスペース](how-to-secure-workspace-vnet.md#secure-the-workspace-with-private-endpoint) (既存)。

+ [仮想ネットワークに追加された Azure ストレージ アカウント](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts) (既存)。

## <a name="access-the-studio-from-a-resource-inside-the-vnet"></a>VNet 内のリソースから Studio にアクセスする

仮想ネットワーク内のリソース (コンピューティング インスタンスや仮想マシンなど) からスタジオにアクセスする場合は、仮想ネットワークからスタジオへの送信トラフィックを許可する必要があります。 

たとえば、ネットワーク セキュリティ グループ (NSG) を使用して送信トラフィックを制限している場合は、__AzureFrontDoor.Frontend__ の__サービス タグ__宛先に規則を追加します。

## <a name="access-data-using-the-studio"></a>Studio を使用したデータへのアクセス

データが仮想ネットワークに格納されている場合、データへのアクセス権を Studio に付与するには、[マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) を使用するようにストレージ アカウントを構成する必要があります。


マネージド ID を有効にしないと、次のエラー `Error: Unable to profile this dataset. This might be because your data is stored behind a virtual network or your data does not support profile.` が発生し、さらに次の操作が無効になります。

* スタジオでのデータのプレビュー
* デザイナーでのデータの視覚化
* AutoML 実験の送信
* ラベル付けプロジェクトの開始

スタジオでは、仮想ネットワーク内の次のデータストアの種類からのデータの読み取りがサポートされています。

* Azure BLOB
* Azure Data Lake Storage Gen1
* Azure Data Lake Storage Gen2
* Azure SQL データベース


### <a name="configure-datastores-to-use-managed-identity"></a>マネージド ID を使用するようにデータストアを構成する

Azure Machine Learning では、[データストア](concept-data.md#datastores)を使用してストレージ アカウントに接続します。 マネージド ID を使用するようにデータストアを構成するには、次の手順に従います。 

1. スタジオで、 __[データストア]__ を選択します。

1. 新しいデータストアを作成する場合は、 __[+ 新しいデータストア]__ を選択します。

    既存のデータストアを更新する場合は、そのデータストアを選択して __[資格情報の更新]__ を選択します。

1. データストアの設定で、 __[Azure Machine Learning service がワークスペース マネージド ID を使用してストレージにアクセスするのを許可する]__ で __[はい]__ を選択します。


この手順により、Azure リソースベース アクセス制御 (RBAC) を使用して、ワークスペースのマネージド ID がストレージ サービスに__閲覧者__として追加されます。 __閲覧者__のアクセス権を指定することにより、ワークスペースはファイアウォールの設定を取得し、データが仮想ネットワークから離れないようにすることができます。

> [!NOTE]
> これらの変更は、有効になるまでに最大 10 分かかる場合があります。

## <a name="technical-notes-for-managed-identity"></a>マネージド ID のテクニカル ノート

ストレージ サービスへのアクセス時にマネージド ID を使用すると、いくつかのセキュリティの考慮事項に影響します。 これらの考慮事項は、アクセスしているストレージ アカウントの種類によって異なります。 このセクションでは、ストレージ アカウントの種類ごとの変化について説明します。

### <a name="azure-blob-storage"></a>Azure BLOB ストレージ

__Azure Blob ストレージ__では、ワークスペース マネージド ID は [BLOB データ閲覧者](../role-based-access-control/built-in-roles.md#storage-blob-data-reader)としても追加されるため、BLOB ストレージからデータを読み取ることができます。

### <a name="azure-data-lake-storage-gen2-access-control"></a>Azure Data Lake Storage Gen2 のアクセスの制御

仮想ネットワーク内のデータ アクセスの制御には、RBAC と POSIX スタイルのアクセス制御リスト (ACL) の両方を使用できます。

RBAC を使用するには、ワークスペース マネージド ID を [BLOB データ閲覧者](../role-based-access-control/built-in-roles.md#storage-blob-data-reader)のロールに追加します。 詳細については、[ロールベースのアクセス制御](../storage/blobs/data-lake-storage-access-control.md#role-based-access-control)に関するページを参照してください。

ACL を使用する場合は、他のセキュリティ原則の場合と同様に、ワークスペース マネージド ID にアクセス権を割り当てることができます。 詳細については、「[ファイルとディレクトリのアクセス制御リスト](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)」を参照してください。

### <a name="azure-data-lake-storage-gen1-access-control"></a>Azure Data Lake Storage Gen1 のアクセスの制御

Azure Data Lake Storage Gen1 では、POSIX スタイルのアクセス制御リストのみをサポートしています。 他のセキュリティ原則の場合と同様に、ワークスペース マネージド ID にリソースに対するアクセス権を割り当てることができます。 詳細については、「[Azure Data Lake Storage Gen1 のアクセス制御](../data-lake-store/data-lake-store-access-control.md)」を参照してください。

### <a name="azure-sql-database-contained-user"></a>Azure SQL Database 包含ユーザー

Azure SQL Database の格納データにマネージド ID を使用してアクセスするには、そのマネージド ID にマップする SQL 包含ユーザーを作成する必要があります。 外部プロバイダーからユーザーを作成する方法の詳細については、「[Azure AD ID にマップされる包含ユーザーを作成する](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)」を参照してください。

SQL 包含ユーザーを作成したら、これに対してアクセス許可を付与するため、[GRANT T-SQL コマンド](https://docs.microsoft.com/sql/t-sql/statements/grant-object-permissions-transact-sql)を使用します。

### <a name="azure-machine-learning-designer-default-datastore"></a>Azure Machine Learning デザイナーの既定のデータストア

既定では、デザイナーはワークスペースにアタッチされているストレージ アカウントを使用して出力を格納します。 ただし、アクセスできる任意のデータストアに出力を格納するように指定することもできます。 お使いの環境で仮想ネットワークが使用されている場合は、これらの制御を使用して、データの安全とアクセス可能な状態を維持できます。

パイプラインの新しい既定のストレージを設定するには:

1. パイプライン ドラフトで、パイプラインのタイトルの近くにある**設定の歯車アイコン**を選択します。
1. **[Select default datastore]\(既定のデータストアの選択\)** を選択します。
1. 新しいデータストアを指定します。

また、既定のデータストアをモジュールごとにオーバーライドすることもできます。 これにより、個々のモジュールのストレージの場所を制御できます。

1. 出力を指定するモジュールを選択します。
1. **[Output settings]\(出力設定\)** セクションを展開します。
1. **[Override default output settings]\(既定の出力設定のオーバーライド\)** を選択します。
1. **[Set output settings]\(出力設定の設定\)** を選択します。
1. 新しいデータストアを指定します。

## <a name="next-steps"></a>次のステップ

この記事は、全 4 パートからなる仮想ネットワーク シリーズのオプションの部分です。 仮想ネットワークをセキュリティで保護する方法については、記事の残りの部分を参照してください。

* [パート 1: 仮想ネットワークの概要](how-to-network-security-overview.md)
* [パート 2: ワークスペース リソースをセキュリティで保護する](how-to-secure-workspace-vnet.md)
* [パート 3: トレーニング環境をセキュリティで保護する](how-to-secure-training-vnet.md)
* [パート 4: 推論環境をセキュリティで保護する](how-to-secure-inferencing-vnet.md)