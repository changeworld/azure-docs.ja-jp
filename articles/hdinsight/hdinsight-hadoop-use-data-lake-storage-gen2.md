---
title: Azure HDInsight クラスターで Azure Data Lake Storage Gen2 を使用する
description: Azure HDInsight クラスターで Azure Data Lake Storage Gen2 を使用する方法について学習します。
services: hdinsight
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: howto
ms.date: 01/10/2019
ms.author: hrasheed
ms.openlocfilehash: 1d43c7b6dd1bdec0a2507d8ce1a3883f5ce31a39
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/23/2019
ms.locfileid: "54479395"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Azure HDInsight クラスターで Azure Data Lake Storage Gen2 を使用する

Azure Data Lake Storage (Data Lake Storage) Gen2 は、ビッグ データ分析専用の機能セットであり、Azure Blob Storage に基づいて構築されています。 Data Lake Storage Gen2 は Azure Blob Storage と Azure Data Lake Storage Gen1 の機能を集約したものです。 その結果、ファイル システム セマンティクス、ディレクトリ、ファイル レベルのセキュリティおよびスケーリングなど、Azure Data Lake Storage Gen1 に由来する機能と、Azure Blob Storage の低コストの階層型記憶域、高可用性/ディザスター リカバリー機能とを提供するサービスになりました。

## <a name="data-lake-storage-gen2-availability"></a>Data Lake Storage Gen2 の可用性

Azure Data Lake Storage Gen2 は、ほぼすべての Azure HDInsight クラスターの種類のストレージ オプションとして、既定のストレージ アカウントと追加のストレージ アカウントの両方で使用できます。 ただし、HBase は、1 つの Data Lake Storage Gen2 アカウントしか持てません。

> [!Note] 
> Data Lake Storage Gen2 を**プライマリ ストレージの種類**として選択すると、追加のストレージとして Data Lake Storage Gen1 アカウントを選択できなくなります。

## <a name="creating-an-hdinsight-cluster-with-data-lake-storage-gen2"></a>Data Lake Storage Gen2 を使用する HDInsight クラスターの作成

ストレージに Data Lake Storage Gen2 を使用する HDInsight クラスターを作成するには、次の手順に従って、正しく構成された Data Lake Storage Gen2 アカウントを作成します。

1. ユーザー割り当てマネージド ID をまだお持ちでない場合には、作成します。 「[Azure Portal を使用してユーザー割り当てマネージド ID を作成、一覧表示、削除したり、それにロールを割り当てたりする](/../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal#create-a-user-assigned-managed-identity.md)」を参照してください。

    ![ユーザー割り当てマネージド ID を作成する](./media/hdinsight-hadoop-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

1. Azure Data Lake Storage Gen2 ストレージ アカウントを作成します。 **[Hierarchical filesystem]\(階層型ファイルシステム\)** オプションが有効になっていることを確認します。 「[クイック スタート:Azure Data Lake Storage Gen2 ストレージ アカウントを作成する](/../storage/blobs/data-lake-storage-quickstart-create-account.md)」を参照してください。

    ![Azure Portal でのストレージ アカウントの作成を示したスクリーンショット](./media/hdinsight-hadoop-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)
 
1. ストレージ アカウントでマネージド ID を**ストレージ BLOB データ共同作成者 (プレビュー)** ロールに割り当てます。 「[RBAC を使用して Azure BLOB およびキューのデータへのアクセス権を管理する (プレビュー)](/../storage/common/storage-auth-aad-rbac#assign-a-role-scoped-to-the-storage-account-in-the-azure-portal.md)」をご覧ください。

    1. [Azure portal](https://portal.azure.com) のストレージ アカウントに移動します。
    1. ストレージ アカウントを選択し、**[アクセス制御 (IAM)]** を選択してアカウントのアクセス制御設定を表示します。 **[ロールの割り当て]** タブを選択して、ロールの割り当ての一覧を表示します。
    
        ![ストレージのアクセス制御設定を示すスクリーン ショット](./media/hdinsight-hadoop-data-lake-storage-gen2/portal-access-control.png)
    
    1. **[ロールの割り当ての追加]** ボタンをクリックして新しいロールを追加します。
    1. **[ロール割り当ての追加]** ウィンドウで、**[ストレージ Blob データ共同作成者 (プレビュー)]** ロールを選択します。 次に、マネージド ID とストレージ アカウントを持つサブスクリプションを選択します。 次に、以前作成したユーザー割り当てマネージド ID を検索して見つけます。 最後に、マネージド ID を選択すると、その ID が **[選択したメンバー]** の下に一覧表示されます。
    
        ![RBAC ロールの割り当て方法を示すスクリーンショット](./media/hdinsight-hadoop-data-lake-storage-gen2/add-rbac-role2.png)
    
    1. **[Save]** をクリックします。 選択したユーザー割り当て ID が、**共同作成者**ロールの下に一覧表示されています。

    1. この初期セットアップを完了すると、ポータルを通じてクラスターを作成できます。 クラスターは、ストレージ アカウントと同じ Azure リージョンに存在する必要があります。 クラスターの作成メニューの **[ストレージ]** セクションで、次のオプションを選択します。
        
        * **[プライマリ ストレージの種類]** で、**[Data Lake Storage Gen2]** をクリックします。
        * **[ストレージ アカウントの選択]** の下で、新しく作成した Data Lake Storage Gen2 ストレージ アカウントを検索して選択します。
        
            ![Azure HDInsight で Data Lake Storage Gen2 を使用するためのストレージ設定](./media/hdinsight-hadoop-data-lake-storage-gen2/primary-storage-type-adls-gen2.png)
        
        * **[ID]** の下で、正しいサブスクリプションと新しく作成されたユーザー割り当てマネージド ID を選択します。
        
            ![Azure HDInsight で Data Lake Storage Gen2 を使用するための ID 設定](./media/hdinsight-hadoop-data-lake-storage-gen2/managed-identity-cluster-creation.png)

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>HDInsight での Data Lake Storage Gen2 のアクセス制御

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Data Lake Storage Gen2 をサポートするアクセス許可の種類

Azure Data Lake Storage Gen2 では、Azure のロール ベースのアクセス制御 (RBAC) と POSIX のようなアクセス制御リスト (ACL) の両方をサポートするアクセス制御モデルが実装されています。 Data Lake Storage Gen1 では、データへのアクセスを制御するためのアクセス制御リストのみがサポートされていました。

Azure のロールベースのアクセス制御 (RBAC) では、ロールの割り当てを使用して、Azure リソースのユーザー、グループ、およびサービス プリンシパルにアクセス許可のセットを効果的に適用します。 通常、これらの Azure リソースは、最上位のリソース (例: Azure Storage アカウント) に制約されます。 Azure Storage および Azure Data Lake Storage Gen2 のため、このメカニズムは、ファイル システムのリソースに拡張されています。

 RBAC を使用したファイルのアクセス許可の詳細については、「[Azure のロールベースのアクセス制御 (RBAC)](/../storage/blobs/data-lake-storage-access-control#azure-role-based-access-control-rbac.md)」を参照してください。

ACL を使用したファイルのアクセス許可の詳細については、「[ファイルとディレクトリのアクセス制御リスト](/../storage/blobs/data-lake-storage-access-control#access-control-lists-on-files-and-directories.md)」を参照してください。


### <a name="how-do-i-control-access-to-my-data-in-gen2"></a>Gen2 で自分のデータへのアクセスを制御する方法

Data Lake Storage Gen2 で HDInsight クラスターがファイルにアクセスする機能は、マネージド ID によって制御されます。 マネージド ID とは、資格情報が Azure によって管理されている Azure AD に登録されている ID です。 Azure AD にサービス プリンシパルを登録して、証明書などの資格情報を維持する必要はありません。

Azure サービスには、システム割り当てとユーザー割り当ての 2 種類のマネージド ID があります。 Azure HDInsight では、ユーザー割り当てマネージド ID を使用して、Azure Data Lake Storage Gen2 にアクセスします。 ユーザー割り当てマネージド ID は、スタンドアロン Azure リソースとして作成されます。 作成プロセスで、使用されているサブスクリプションによって信頼されている Azure AD テナントに、Azure が ID を作成します。 作成された ID は、1 つまたは複数の Azure サービス インスタンスに割り当てることができます。 ユーザー割り当て ID のライフサイクルは、その ID が割り当てられている Azure サービス インスタンスのライフサイクルとは個別に管理されます。 マネージド ID の詳細については、「[Azure リソースのマネージド ID のしくみ](/../active-directory/managed-identities-azure-resources/overview#how-does-the-managed-identities-for-azure-resources-worka-namehow-does-it-worka.md)」を参照してください。

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-using-hive-or-other-services"></a>Hive またはその他のサービスを使用して Data Lake Storage Gen2 でデータをクエリするためのアクセス許可を Azure AD ユーザーに設定する方法

ACL で割り当て済みのプリンシパルとして Azure AD セキュリティ グループを使用します。 個々のユーザーまたはサービス プリンシパルにファイルのアクセス許可を直接割り当てないでください。 AD セキュリティ グループを使用してアクセス許可のフローを制御すると、ACL をディレクトリ構造全体に再適用することなく、ユーザーまたはサービス プリンシパルを追加および削除できます。 適切な Azure AD セキュリティ グループからユーザーまたはサービス プリンシパルを追加または削除するだけです。 ACL は継承されないため、ACL の再適用には、すべてのファイルとサブディレクトリで ACL を更新する必要があります。

## <a name="next-steps"></a>次の手順

* [Azure HDInsight クラスターで Azure Data Lake Storage Gen2 プレビューを使用する](/../storage/blobs/data-lake-storage-use-hdi-cluster.md)
* [Azure HDInsight と Data Lake Storage Gen2 プレビューとの統合 - ACL とセキュリティ更新プログラム](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Azure Data Lake Storage Gen2 プレビューの概要](/../storage/blobs/data-lake-storage-introduction.md)