---
title: "Azure Data Factory を使用した Azure Data Lake Store へのデータの読み込み | Microsoft Docs"
description: "Azure Data Factory を使用して Azure Data Lake Store にデータをコピーします"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.date: 01/17/2018
ms.author: jingwang
ms.openlocfilehash: 3f73cd65b0ceb3148ce8ceb83d7b4e1be1280077
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/18/2018
---
# <a name="load-data-into-azure-data-lake-store-using-azure-data-factory"></a>Azure Data Factory を使用した Azure Data Lake Store へのデータの読み込み

[Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) は、ビッグ データの分析ワークロードに対応するエンタープライズ規模のハイパースケール リポジトリです。 Azure Data Lake を使用すると、運用分析や調査分析を目的として任意のサイズ、種類、および取り込み速度のデータを 1 か所でキャプチャすることができます。

Azure Data Factory は、完全に管理されたクラウド ベースのデータ統合サービスであり、このサービスを使用して既存のシステムのデータを Data Lake に入力することで、貴重な時間を節約しながらソリューションの構築を行うことができます。 Azure Data Factory を使用して Azure Data Lake Store にデータを読み込む主な利点を次に示します。

* **簡単にセットアップ**: 直感的なウィザードが示す 5 つの手順に従うだけです。スクリプトは必要ありません。
* **豊富なデータ ストアのサポート**: オンプレミスとクラウド ベースのデータ ストアの豊富なセットに対するサポートが組み込まれています。詳しくは、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。
* **セキュリティとコンプライアンスへの準拠**: データは HTTPS または ExpressRoute で転送され、グローバル サービスの存在により、データが地理的な境界を越えることはありません。
* **高パフォーマンス**: 最大 1 GBps の速度で Azure Data Lake Store にデータを読み込みます。 詳細については、[コピー アクティビティのパフォーマンス](copy-activity-performance.md)に関する記事を参照してください。

この記事では、Data Factory のデータのコピー ツールを使用して **Amazon S3 から Azure Data Lake Store にデータを読み込む**方法を示します。 その他の種類のデータ ストアからデータをコピーする場合も、同様の手順で実行できます。

> [!NOTE]
>  Azure Data Lake Store との間のデータのコピーに関する Data Factory の機能の一般的な情報については、「[Azure Data Factory を使用して Azure Data Lake Store をコピー先またはコピー元としてデータをコピーする](connector-azure-data-lake-store.md)」を参照してください。
>
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、「[Copy Activity in V1 (V1 でのコピー アクティビティ)](v1/data-factory-data-movement-activities.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。
* **Azure Data Lake Store**。 Data Lake Store アカウントがない場合、ストレージ アカウントの作成手順については、「[Data Lake Store アカウントの作成](../data-lake-store/data-lake-store-get-started-portal.md#create-an-azure-data-lake-store-account)」をご覧ください。
* **Amazon S3**。 この記事では、Amazon S3 からデータをコピーする方法を示します。 同様の手順に従うことによって、その他のデータ ストアも使用できます。

## <a name="create-a-data-factory"></a>Data Factory を作成する。

1. 左側のメニューで **[新規]** をクリックし、**[データ + 分析]**、**[Data Factory]** の順にクリックします。 
   
   ![New->DataFactory](./media/load-data-into-azure-data-lake-store/new-azure-data-factory-menu.png)
2. **[新しいデータ ファクトリ]** ページで、次のスクリーンショットに示されているように値を指定します。 
      
     ![[新しいデータ ファクトリ] ページ](./media/load-data-into-azure-data-lake-store//new-azure-data-factory.png)
 
   * **名前。** データ ファクトリのグローバルな一意の名前を入力します。 次のエラーが発生した場合は、データ ファクトリの名前を変更して (yournameADFTutorialDataFactory など) 作成し直してください。 Data Factory アーティファクトの名前付け規則については、[Data Factory の名前付け規則](naming-rules.md)に関する記事を参照してください。
  
            `Data factory name "LoadADLSDemo" is not available`

    * **サブスクリプション。** データ ファクトリを作成する Azure **サブスクリプション**を選択します。 
    * **リソース グループ。** ドロップダウン リストから既存のリソース グループを選択するか、**[新規作成]** オプションを選択し、リソース グループの名前を入力します。 リソース グループの詳細については、 [リソース グループを使用した Azure のリソースの管理](../azure-resource-manager/resource-group-overview.md)に関するページを参照してください。  
    * **バージョン。** **[V2 (プレビュー)]** を選択します。
    * **場所。** データ ファクトリの場所を選択します。 サポートされている場所のみがドロップダウン リストに表示されます。 データ ファクトリで使用するデータ ストア (Azure Data Lake Store、Azure Storage、Azure SQL Database など) は他の場所/リージョンに配置できます。

3. **Create** をクリックしてください。
4. 作成が完了した後にデータ ファクトリに移動すると、図に示されているような **[Data Factory]** ページが表示されます。 **[作成と監視]** タイルをクリックして、別のタブでデータ統合アプリケーションを起動します。 
   
   ![データ ファクトリのホーム ページ](./media/load-data-into-azure-data-lake-store/data-factory-home-page.png)

## <a name="load-data-into-azure-data-lake-store"></a>Azure Data Lake Store へのデータの読み込み

1. 開始ページで **[データのコピー]** タイルをクリックして、データのコピー ツールを起動します。 

   ![データのコピー ツールのタイル](./media/load-data-into-azure-data-lake-store/copy-data-tool-tile.png)
2. データのコピー ツールの **[プロパティ]** ページで、**[タスク名]** に「**CopyFromAmazonS3ToADLS**」と指定し、**[次へ]** をクリックします。 

    ![データのコピー ツール- プロパティ ページ](./media/load-data-into-azure-data-lake-store/copy-data-tool-properties-page.png)
3. **[ソース データ ストア]** ページで **[Amazon S3]** を選択し、**[次へ]** をクリックします。

    ![[ソース データ ストア] ページ](./media/load-data-into-azure-data-lake-store/source-data-store-page.png)
4. **[Amazon S3 接続の指定]** ページで、次の手順を実行します。 
    1. **[アクセス キー ID]** を指定します。
    2. **[シークレット アクセス キー]** を指定します。
    3. **[次へ]** をクリックします。 

        ![Amazon S3 アカウントの指定](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account.png)
5. **[Choose the input file or folder]\(入力ファイルまたはフォルダーの選択\)** ページで、コピーするフォルダーまたはファイルに移動します。**[選択]** を選択肢てクリックし、**[次へ]** をクリックします。 

    ![入力ファイルまたはフォルダーの選択](./media/load-data-into-azure-data-lake-store/choose-input-folder.png)

6. **[配布先データ ストア]** ページで **[Azure Data Lake Store]** を選択し、**[次へ]** をクリックします。 

    ![[配布先データ ストア] ページ](./media/load-data-into-azure-data-lake-store/destination-data-storage-page.png)

7. このページでは、**[Copy files recursively]\(ファイルを再帰的にコピー\)** オプションと **[バイナリ コピー]** (ファイルをそのままコピー) オプションをオンにして、コピーの動作を選択します。 **[次へ]** をクリックします。

    ![出力フォルダーの指定](./media/load-data-into-azure-data-lake-store/specify-binary-copy.png)

8. **[Data Lake Store 接続の指定]** ページで、次の手順を実行します。 

    1. **[Data Lake Store アカウント名]** で、Data Lake Store を選択します。
    2. **[テナント]**、**[サービス プリンシパル ID]**、および**[サービス プリンシパル キー]** を含むサービス プリンシパル情報を指定します。
    3. **[次へ]** をクリックします。 

    > [!IMPORTANT]
    > このチュートリアルでは、**サービス プリンシパル**を使用して、Data Lake Store を認証します。 [こちら](connector-azure-data-lake-store.md#using-service-principal-authentication)の指示に従って、Azure Data Lake Store でサービス プリンシパルに適切なアクセス許可を付与してください。

    ![Azure Data Lake Store アカウントの指定](./media/load-data-into-azure-data-lake-store/specify-adls.png)

9. **[Choose the output file or folder]\(出力ファイルまたはフォルダーの選択\)** ページで、「**copyfroms3**」を指定し、**[次へ]** をクリックします。 

    ![出力フォルダーの指定](./media/load-data-into-azure-data-lake-store/specify-adls-path.png)


10. **[設定]** ページで **[次へ]** をクリックします。 

    ![[設定] ページ](./media/load-data-into-azure-data-lake-store/copy-settings.png)
11. **[概要]** ページで設定を確認し、**[次へ]** をクリックします。

    ![概要ページ](./media/load-data-into-azure-data-lake-store/copy-summary.png)
12. **[Deployment]\(デプロイ\)** ページで、**[監視]** をクリックしてパイプライン (タスク) を監視します。

    ![[Deployment]\(デプロイ\) ページ](./media/load-data-into-azure-data-lake-store/deployment-page.png)
13. 左側の **[監視]** タブが自動的に選択されたことがわかります。 **[アクション]** 列で、アクティビティの実行の詳細を表示するリンクとパイプラインを再実行するリンクを確認します。 

    ![パイプラインの実行を監視する](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)
14. パイプラインの実行に関連付けられているアクティビティの実行を表示するために、**[アクション]** 列の **[View Activity Runs]\(アクティビティの実行の表示\)** リンクをクリックします。 パイプライン内のアクティビティ (コピー アクティビティ) は 1 つだけなので、エントリは 1 つのみです。 [Pipeline Runs]\(パイプラインの実行\) ビューに戻るには、上部の **[パイプライン]** リンクをクリックします。 **[最新の情報に更新]** をクリックして、リストを更新します。 

    ![アクティビティの実行を監視する](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

15. さらに、アクティビティ監視ビューの **[アクション]** の下の **[詳細]** リンクをクリックすることで、各コピー アクティビティの実行状況の詳細を監視できます。 ソースからシンクにコピーされたデータ ボリューム、スループット、対応する期間に処理されるステップ、および使用される構成などの情報が表示されます。

    ![アクティビティの実行状況の詳細の監視](./media/load-data-into-azure-data-lake-store/monitor-activity-run-details.png)

16. データが Azure Data Lake Store にコピーされたことを確認します。 

    ![Data Lake Store の出力の確認](./media/load-data-into-azure-data-lake-store/adls-copy-result.png)

## <a name="next-steps"></a>次の手順

次の資料に進んで、Azure Data Lake Store のサポートを確認します。 

> [!div class="nextstepaction"]
>[Azure Data Lake Store コネクタ](connector-azure-data-lake-store.md)