---
title: スタジオでのネットワーク データ アクセス
titleSuffix: Azure Machine Learning
description: ワークスペースまたはストレージが仮想ネットワーク内に存在する場合の Azure Machine Learning スタジオでのデータ アクセスについて説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: conceptual
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 11/08/2021
ms.openlocfilehash: c30b88361b4b617b092995cb8ddc0138ffcc8240
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132706594"
---
# <a name="network-data-access-with-azure-machine-learning-studio"></a>Azure Machine Learning スタジオでのネットワーク データ アクセス

データ アクセスは複雑であり、そこにはさまざまな要素が含まれていることに気付くことが大切です。 たとえば、Azure Machine Learning スタジオからのデータ アクセスは、SDK を使用したデータ アクセスとは異なります。 ローカル開発環境で SDK を使用している場合、クラウドのデータに直接アクセスすることになります。 スタジオを使用する場合は、必ずしもクライアントから直接データ ストアにアクセスするとは限りません。 スタジオでのデータ アクセスには、ユーザーに代わってワークスペースが利用されます。

> [!IMPORTANT]
> この記事の情報は、Azure Machine Learning ソリューションに必要なインフラストラクチャを作成する Azure 管理者を対象としています。

> [!TIP]
> VNet に置かれた次の種類のデータストアに対してスタジオがサポートするのはデータの読み取りのみです。
>
> * Azure Storage アカウント (BLOB とファイル)
> * Azure Data Lake Storage Gen1
> * Azure Data Lake Storage Gen2
> * Azure SQL データベース

## <a name="data-access"></a>データ アクセス

一般に、スタジオからのデータ アクセスには、次のチェックが伴います。

1. だれがアクセスしているか
    - ストレージの種類に応じてさまざまな種類の認証があります。 たとえば、アカウント キー、トークン、サービス プリンシパル、マネージド ID、ユーザー ID が挙げられます。
    - ユーザー ID を使用して認証を行う場合、ストレージにアクセスしようとしているのが "*どの*" ユーザーであるかを知ることが大切です。
2. アクセス許可はあるか
    - 資格情報は正しいでしょうか。 だとすれば、ストレージに対する必要なアクセス許可が、サービス プリンシパルやマネージド ID などに割り当てられているでしょうか。 アクセス許可の付与には、Azure ロールベースのアクセス制御 (Azure RBAC) が使用されます。
    - ストレージ アカウントの[閲覧者](../role-based-access-control/built-in-roles.md#reader)は、ストレージのメタデータを読み取ることができます。
    - [ストレージ BLOB データ閲覧者](../role-based-access-control/built-in-roles.md#storage-blob-data-reader)は、BLOB コンテナー内のデータを読み取ることができます。
    - [共同作成者](../role-based-access-control/built-in-roles.md#contributor)には、ストレージ アカウントへの書き込みアクセスが許可されます。
    - ストレージの種類によっては、さらに他のロールも必要になる場合があります。
3. どこからアクセスしているか
    - ユーザー: クライアントの IP アドレスは、VNet またはサブネットの範囲内であるか。
    - ワークスペース: ワークスペースはパブリックであるか、または VNet またはサブネット内にプライベート エンドポイントはあるか。
    - ストレージ: ストレージでパブリック アクセスが許可されているか。または、サービス エンドポイントまたはプライベート エンドポイントを通じてアクセスが制限されているか。
4. 何の操作が実行されているか。
    - データ ストアやデータセットに対する CRUD (作成、読み取り、更新、削除) 操作は、Azure Machine Learning によって処理されます。
    - データ アクセス呼び出し (プレビュー、スキーマなど) は、下層のストレージに渡されるため、特別なアクセス許可が必要となります。
5. その操作がどこで実行されているか。Azure サブスクリプション内のコンピューティング リソースか、それとも Microsoft サブスクリプションでホストされているリソースか。
    - データセットやデータストア サービスに対するすべての呼び出し ("プロファイルの生成" オプションを除く) では、__Microsoft サブスクリプション__ でホストされたリソースを使用して操作が実行されます。
    - "プロファイルの生成" オプションを含め、データセットに対するジョブは、__ご使用のサブスクリプション__ 内のコンピューティング リソースで実行され、そこからデータにアクセスします。 したがって、ストレージへのアクセス許可は、ジョブを送信するユーザーの ID にではなく、コンピューティング ID に必要となります。

次の図は、データ アクセス呼び出しの大まかな流れを示しています。 この例では、Machine Learning ワークスペースからユーザーが、コンピューティング リソースを使用せずにデータ アクセス呼び出しを実行しようとしています。

:::image type="content" source="./media/concept-network-data-access/data-access-flow.svg" alt-text="データにアクセスする際のロジック フローの図":::

## <a name="azure-storage-account"></a>Azure Storage アカウント

Azure Machine Learning スタジオから Azure ストレージ アカウントを使用する場合、そのストレージ アカウントの次の Azure RBAC ロールにワークスペースのマネージド ID を追加する必要があります。

* [BLOB データ閲覧者](../role-based-access-control/built-in-roles.md#storage-blob-data-reader)
* ストレージ アカウントから VNet への接続にプライベート エンドポイントが使用されている場合、ストレージ アカウントのプライベート エンドポイントに対する[閲覧者](../role-based-access-control/built-in-roles.md#reader)ロールをマネージド ID に付与する必要があります。

詳細については、「[Azure 仮想ネットワークで Azure Machine Learning スタジオを使用する](how-to-enable-studio-virtual-network.md)」を参照してください。

ワークスペースから VNet 内の Azure ストレージ アカウントを使用する際の制限については、以降のセクションを参照してください。
### <a name="using-an-existing-storage-account"></a>既存のストレージ アカウントを使用する

ワークスペースの作成時に、既存のストレージ アカウントを __既定のストレージ__ として使用した場合、ファイル ストアの `azureml-filestore` フォルダーが自動的には作成されません。 [AutoML](concept-automated-ml.md) の実験を送信する際には、このフォルダーが必要となります。

この問題を回避するには、ワークスペースの作成時に、自分の代わりに既定のストレージを作成することを Azure Machine Learning に許可するか、または、既存のストレージ アカウントの場所が VNet 内 __ではない__ ことを確認してください。 Azure ストレージ アカウントでのネットワークの詳細については、[仮想ネットワークに対する Azure ストレージ アカウントの構成](../storage/common/storage-network-security.md)に関するページを参照してください。

### <a name="azure-storage-firewall"></a>Azure Storage ファイアウォール

Azure ストレージ アカウントが仮想ネットワークの内側にある場合、ストレージのファイアウォールを使用して普通に、インターネットでの直接接続をクライアントに許可できます。 一方、スタジオを使用する場合、ストレージ アカウントに接続するのはクライアントではありません。要求を送信するのは、Azure Machine Learning service です。 このサービスの IP アドレスは文書に記載されておらず、また頻繁に変更されます。 __ストレージのファイアウォールを有効にしても、スタジオから VNet 構成のストレージ アカウントにアクセスできるようにはなりません__。

## <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 をデータストアとして使用する場合は、POSIX スタイルのアクセス制御リストのみを使用できます。 他のセキュリティ プリンシパルと同様に、ワークスペースのマネージド ID にリソースへのアクセス権を割り当てることができます。 詳細については、「[Azure Data Lake Storage Gen1 のアクセス制御](../data-lake-store/data-lake-store-access-control.md)」を参照してください。

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 をデータストアとして使用する場合、仮想ネットワーク内のデータ アクセスの制御には、Azure RBAC と POSIX スタイルのアクセス制御リスト (ACL) の両方を使用できます。

**Azure RBAC を使用する場合** は、「Azure 仮想ネットワークで Azure Machine Learning Studio を使用する」の記事の「[データストア: Azure Storage アカウント](how-to-enable-studio-virtual-network.md#datastore-azure-storage-account)」セクションの手順に従ってください。 Data Lake Storage Gen2 は Azure Storage をベースにしているため、Azure RBAC を使用する手順は同じです。

**ACL を使用する場合** は、他のセキュリティ プリンシパルと同様に、ワークスペース マネージド ID にアクセス権を割り当てることができます。 詳細については、「[ファイルとディレクトリのアクセス制御リスト](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)」を参照してください。

## <a name="azure-sql-database"></a>Azure SQL データベース

マネージド ID を使用して Azure SQL Database 内の格納データにアクセスするには、そのマネージド ID にマップされる SQL 包含ユーザーを作成する必要があります。 外部プロバイダーからユーザーを作成する方法の詳細については、「[Azure AD ID にマップされる包含ユーザーを作成する](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)」を参照してください。

SQL 包含ユーザーを作成したら、これに対してアクセス許可を付与するため、[GRANT T-SQL コマンド](/sql/t-sql/statements/grant-object-permissions-transact-sql)を使用します。

### <a name="deny-public-network-access"></a>パブリック ネットワーク アクセスの拒否

Azure SQL Database では、__パブリック ネットワーク アクセスの拒否__ によって、データベースへのパブリック アクセスをブロックできます。 このオプションを有効にした場合、SQL Database へのアクセスは __サポートされません__。 Azure Machine Learning スタジオで SQL Database を使用する場合、データ アクセスは常に SQL Database のパブリック エンドポイントを介して行われます。

## <a name="next-steps"></a>次のステップ

ネットワーク内でのスタジオの使用については、「[Azure 仮想ネットワークで Azure Machine Learning スタジオを使用する](how-to-enable-studio-virtual-network.md)」を参照してください。