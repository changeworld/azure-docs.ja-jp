---
title: Azure Machine Learning Studio を仮想ネットワークで利用可能にする
titleSuffix: Azure Machine Learning
description: Azure Machine Learning スタジオを構成して、仮想ネットワーク内に格納されているデータにアクセスする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: how-to
ms.reviewer: larryfr
ms.author: jhirono
author: jhirono
ms.date: 11/10/2021
ms.custom: contperf-fy20q4, tracking-python, security
ms.openlocfilehash: b978955c375ff30f677a395ea549276b960a80d9
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132293100"
---
# <a name="use-azure-machine-learning-studio-in-an-azure-virtual-network"></a>Azure 仮想ネットワークで Azure Machine Learning Studio を使用する

この記事では、仮想ネットワークで Azure Machine Learning Studio を使用する方法について説明します。 Studio には、AutoML、デザイナー、データのラベル付けなどの機能が含まれています。 

仮想ネットワークでは、Studio の機能の一部が既定で無効になっています。 これらの機能を再度有効にするには、Studio で使用する予定のストレージ アカウントのマネージド ID を有効にする必要があります。 

仮想ネットワークでは、次の操作が既定で無効になっています。

* スタジオでのデータのプレビュー
* デザイナーでのデータの視覚化
* デザイナーでのモデルのデプロイ
* AutoML 実験の送信
* ラベル付けプロジェクトの開始

スタジオでは、仮想ネットワーク内の次のデータストアの種類からのデータの読み取りがサポートされています。

* Azure Storage アカウント (BLOB とファイル)
* Azure Data Lake Storage Gen1
* Azure Data Lake Storage Gen2
* Azure SQL データベース

この記事では、次のことについて説明します。

> [!div class="checklist"]
> - 仮想ネットワーク内に格納されているデータへのアクセス権を Studio に付与します。
> - 仮想ネットワーク内のリソースから Studio にアクセスします。
> - Studio によるストレージのセキュリティへの影響について理解します。

> [!TIP]
> この記事は、Azure Machine Learning ワークフローのセキュリティ保護に関するシリーズの一部です。 このシリーズの他の記事は次のとおりです。
>
> * [Virtual Network の概要](how-to-network-security-overview.md)
> * [ワークスペース リソースをセキュリティで保護する](how-to-secure-workspace-vnet.md)
> * [トレーニング環境をセキュリティで保護する](how-to-secure-training-vnet.md)
> * [推論環境をセキュリティで保護する](how-to-secure-inferencing-vnet.md)
> * [カスタム DNS を使用する](how-to-custom-dns.md)
> * [ファイアウォールを使用する](how-to-access-azureml-behind-firewall.md)


## <a name="prerequisites"></a>前提条件

+ [ネットワーク セキュリティの概要](how-to-network-security-overview.md)に関するページを参照して、一般的な仮想ネットワークのシナリオとアーキテクチャについて理解してください。

+ 使用する仮想ネットワークとサブネットが既に存在すること。

+ [プライベート エンドポイントを持つ既存の Azure Machine Learning ワークスペース](how-to-secure-workspace-vnet.md#secure-the-workspace-with-private-endpoint)。

+ [仮想ネットワークに追加された Azure ストレージ アカウント](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts) (既存)。

## <a name="limitations"></a>制限事項

### <a name="azure-storage-account"></a>Azure Storage アカウント

既定のファイル ストアでは、AutoML 実験を送信するために必要な `azureml-filestore` フォルダーが自動的に作成されないという既知の問題があります。 この問題は、ワークスペースの作成時に、ユーザーが既存のファイル ストアを既定のファイル ストアとして設定した場合に発生します。

この問題を回避するには、2つのオプションがあります。 1) ワークスペースの作成時に自動的に作成される既定のファイル ストアを使用します。 2) 独自のファイル ストアを使用するには、ワークスペースの作成時にそのファイル ストアが VNet の外部にあることを確認します。 ワークスペースが作成されたら、仮想ネットワークにストレージ アカウントを追加します。

この問題を解決するには、仮想ネットワークから ファイル ストア アカウントを削除してから、仮想ネットワークに追加し直します。

### <a name="designer-sample-pipeline"></a>デザイナーのサンプル パイプライン

ユーザーがデザイナー ホームページでサンプル パイプラインを実行できないという既知の問題があります。 このサンプル パイプラインで使用されるサンプル データセットは、Azure Global データセットであり、すべての仮想ネットワーク環境を満たすことはできません。

この問題を解決するには、パブリック ワークスペースを使用してサンプル パイプラインを実行し、デザイナーの使用方法を理解したうえで、サンプル データセットを、仮想ネットワーク内のワークスペースの実際のデータセットで置き換えます。

## <a name="datastore-azure-storage-account"></a>データストア: Azure Storage アカウント

次の手順を使用して、Azure Blob および File ストレージの格納データへのアクセスを有効にします。

> [!TIP]
> ワークスペースの既定のストレージ アカウントの場合、最初の手順は必要ありません。 その他の手順は、既定のストレージ アカウントを含め、VNet の背後にありワークスペースで使用されている "*すべて*" のストレージ アカウントに必要です。

1. **ストレージ アカウントがワークスペースの "*既定*" のストレージである場合は、この手順をスキップします**。 既定でない場合は、**Azure Storage アカウントの 'ストレージ BLOB データ閲覧者' ロールをワークスペース マネージド ID に付与し**、BLOB ストレージからデータを読み取れるようにします。

    詳細については、[BLOB データ閲覧者](../role-based-access-control/built-in-roles.md#storage-blob-data-reader)組み込みロールに関するページを参照してください。

1. **ストレージ プライベート エンドポイントの '閲覧者' ロールをワークスペース マネージド ID に付与します**。 ストレージ サービスに __プライベート エンドポイント__ を使用している場合は、プライベート エンドポイントへの **閲覧者** アクセス権をワークスペースのマネージド ID に付与します。 Azure AD のワークスペースのマネージド ID は、Azure Machine Learning ワークスペースと同じ名前です。

    > [!TIP]
    > ストレージ アカウントは、複数のプライベート エンドポイントを持つことができます。 たとえば、1 つのストレージ アカウントが、BLOB、ファイル、dfs (Azure Data Lake Storage Gen2) 用に個別のプライベート エンドポイントを持つことができます。 これらのすべてのエンドポイントにこのマネージド ID を追加します。

    詳細については、[閲覧者](../role-based-access-control/built-in-roles.md#reader)組み込みロールに関するページを参照してください。

   <a id='enable-managed-identity'></a>
1. **既定のストレージ アカウントでマネージド ID 認証を有効にします**。 各 Azure Machine Learning ワークスペースには、既定の BLOB ストレージ アカウントと既定のファイル ストア アカウントの 2 つの既定のストレージ アカウントがあり、これらはワークスペースの作成時に定義されます。 **データストア** 管理ページで新しい既定値を設定することもできます。

    ![既定のデータストアがある場所を示すスクリーンショット](./media/how-to-enable-studio-virtual-network/default-datastores.png)

    次の表に、ワークスペースの既定のストレージ アカウントにマネージド ID 認証を使用する理由を示します。

    |ストレージ アカウント  | Notes  |
    |---------|---------|
    |ワークスペースの既定の BLOB ストレージ| デザイナーからのモデル アセットが格納されます。 デザイナーでモデルをデプロイするには、このストレージ アカウントでマネージド ID 認証を有効にします。 <br> <br> マネージド ID を使用するように構成されている既定以外のデータストアを使用するデザイナー パイプラインは、視覚化して実行できます。 ただし、既定のデータストアでマネージド ID を有効にせずにトレーニング済みのモデルをデプロイしようとすると、他のデータストアの使用に関係なく、デプロイは失敗します。|
    |ワークスペースの既定のファイル ストア| AutoML 実験アセットが格納されます。 AutoML 実験を送信するには、このストレージ アカウントでマネージド ID 認証を有効にします。 |

1. **マネージド ID 認証を使用するようにデータストアを構成します**。 [サービス エンドポイント](how-to-secure-workspace-vnet.md?tabs=se#secure-azure-storage-accounts)または[プライベート エンドポイント](how-to-secure-workspace-vnet.md?tabs=pe#secure-azure-storage-accounts)のいずれかが設定されている仮想ネットワークに Azure ストレージ アカウントを追加した後、[マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) 認証を使用するようにデータストアを構成する必要があります。 これにより、Studio でストレージ アカウント内のデータにアクセスできるようになります。

    Azure Machine Learning では、[データストア](concept-data.md#datastores)を使用してストレージ アカウントに接続します。 新しいデータストアを作成するときは、次の手順を使用して、マネージド ID 認証を使用するようにデータストアを構成します。

    1. スタジオで、 __[データストア]__ を選択します。

    1. 既存のデータストアを更新する場合は、そのデータストアを選択して __[資格情報の更新]__ を選択します。

        新しいデータストアを作成する場合は、 __[+ 新しいデータストア]__ を選択します。

    1. データストアの設定で、 __[Azure Machine Learning Studio でのデータのプレビューとプロファイルにワークスペースのマネージド ID を使用する]__ で __[はい]__ を選択します。

        ![ワークスペースのマネージド ID を有効にする方法を示すスクリーンショット](./media/how-to-enable-studio-virtual-network/enable-managed-identity.png)

    この手順により、Azure RBAC を使用して、ワークスペースのマネージド ID が新しいストレージ サービスに __閲覧者__ として追加されます。 __閲覧者__ アクセス権では、ワークスペースはリソースを表示できますが、変更を加えることはできません。

## <a name="datastore-azure-data-lake-storage-gen1"></a>データストア: Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 をデータストアとして使用する場合は、POSIX スタイルのアクセス制御リストのみを使用できます。 他のセキュリティ プリンシパルと同様に、ワークスペースのマネージド ID にリソースへのアクセス権を割り当てることができます。 詳細については、「[Azure Data Lake Storage Gen1 のアクセス制御](../data-lake-store/data-lake-store-access-control.md)」を参照してください。

## <a name="datastore-azure-data-lake-storage-gen2"></a>データストア: Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 をデータストアとして使用する場合、仮想ネットワーク内のデータ アクセスの制御には、Azure RBAC と POSIX スタイルのアクセス制御リスト (ACL) の両方を使用できます。

**Azure RBAC を使用するには**、この記事の「[データストア: Azure Storage アカウント](#datastore-azure-storage-account)」セクションの手順に従います。 Data Lake Storage Gen2 は Azure Storage をベースにしているため、Azure RBAC を使用する手順は同じです。

**ACL を使用する場合** は、他のセキュリティ プリンシパルと同様に、ワークスペースのマネージド ID にアクセス権を割り当てることができます。 詳細については、「[ファイルとディレクトリのアクセス制御リスト](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)」を参照してください。

## <a name="datastore-azure-sql-database"></a>データストア: Azure SQL Database

マネージド ID を使用して Azure SQL Database 内の格納データにアクセスするには、そのマネージド ID にマップされる SQL 包含ユーザーを作成する必要があります。 外部プロバイダーからユーザーを作成する方法の詳細については、「[Azure AD ID にマップされる包含ユーザーを作成する](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)」を参照してください。

SQL 包含ユーザーを作成したら、これに対してアクセス許可を付与するため、[GRANT T-SQL コマンド](/sql/t-sql/statements/grant-object-permissions-transact-sql)を使用します。

## <a name="intermediate-component-output"></a>中間コンポーネントの出力

Azure Machine Learning デザイナーの中間コンポーネント出力を使用する場合、デザイナーの任意のコンポーネントについて出力場所を指定できます。 これは、セキュリティ、ログ、または監査の目的で、中間データセットを別の場所に格納する場合に使用します。 出力を指定するには、次の手順を実行します。

1. 出力を指定するコンポーネントを選択します。
1. 右側に表示される [コンポーネント設定] ウィンドウで、 **[出力設定]** を選択します。
1. 各コンポーネントの出力に使用するデータストアを指定します。

仮想ネットワーク内の中間ストレージ アカウントにアクセスできることを確認してください。 そうでない場合、パイプラインは失敗します。

また、出力データを視覚化するために、中間ストレージ アカウントの[マネージド ID 認証を有効にします](#enable-managed-identity)。
## <a name="access-the-studio-from-a-resource-inside-the-vnet"></a>VNet 内のリソースから Studio にアクセスする

仮想ネットワーク内のリソース (コンピューティング インスタンスや仮想マシンなど) からスタジオにアクセスする場合は、仮想ネットワークからスタジオへの送信トラフィックを許可する必要があります。 

たとえば、ネットワーク セキュリティ グループ (NSG) を使用して送信トラフィックを制限している場合は、__AzureFrontDoor.Frontend__ の __サービス タグ__ 宛先に規則を追加します。

## <a name="firewall-settings"></a>ファイアウォールの設定

Azure ストレージ アカウントなど、一部のストレージ サービスには、その特定のサービス インスタンスのパブリック エンドポイントに適用されるファイアウォール設定があります。 通常、この設定を使用すると、パブリック インターネットの特定の IP アドレスからのアクセスを許可または禁止できます。 Azure Machine Learning スタジオを使用している場合、__これはサポートされません__。 Azure Machine Learning SDK または CLI を使用している場合にサポートされます。

> [!TIP]
> Azure Machine Learning スタジオは、Azure Firewall サービスを使用している場合にサポートされます。 詳細については、[ファイアウォールの内側でのワークスペースの使用](how-to-access-azureml-behind-firewall.md)に関する記事を参照してください。
## <a name="next-steps"></a>次のステップ

この記事は、Azure Machine Learning ワークフローのセキュリティ保護に関するシリーズの一部です。 このシリーズの他の記事は次のとおりです。

* [Virtual Network の概要](how-to-network-security-overview.md)
* [ワークスペース リソースをセキュリティで保護する](how-to-secure-workspace-vnet.md)
* [トレーニング環境をセキュリティで保護する](how-to-secure-training-vnet.md)
* [推論環境をセキュリティで保護する](how-to-secure-inferencing-vnet.md)
* [カスタム DNS を使用する](how-to-custom-dns.md)
* [ファイアウォールを使用する](how-to-access-azureml-behind-firewall.md)
