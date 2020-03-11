---
title: Azure Databricks による変換
description: Azure Data Factory で Databricks ノートブックを使用することにより、ソリューション テンプレートを使用してデータを変換する方法について説明します。
services: data-factory
ms.author: abnarain
author: nabhishek
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/10/2018
ms.openlocfilehash: 322f5306949b266958ded908e981ed530e8245c8
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2020
ms.locfileid: "78227866"
---
# <a name="transformation-with-azure-databricks"></a>Azure Databricks による変換

このチュートリアルでは、Data Factory の **検証**、**コピー**、および**ノートブック** アクティビティが含まれる、エンド ツー エンドのパイプラインを作成します。

-   **検証**アクティビティでは、コピーおよび分析ジョブをトリガーする前に、ソース データセットをダウンストリームで使用できる状態にするために使用されます。

-   **コピー** アクティビティでは、ソース ファイル/データセットがシンク ストレージにコピーされます。 Spark でデータセットを直接使用できるように、シンク ストレージは Databricks ノートブックに DBFS としてマウントされています。

-   **Databricks ノートブック** アクティビティでは、データセットを変換する Databricks ノートブックがトリガーされて、処理済みのフォルダー/SQL DW に追加されます。

このテンプレートを簡潔にするため、テンプレートではスケジュールされたトリガーは作成されません。 必要に応じて追加できます。

![1](media/solution-template-Databricks-notebook/Databricks-tutorial-image01.png)

## <a name="prerequisites"></a>前提条件

1. **BLOB ストレージ アカウント**と、**シンク** として使用される `sinkdata` という名前のコンテナーを作成します。 **ストレージ アカウントの名前**、**コンテナーの名前**、および**アクセス キー**を記録しておきます。後で、テンプレートにおいてこれらを参照します。

2. **Azure Databricks ワークスペース**があることを確認します。または、新しく作成します。

3. **変換のノートブックをインポート**します。 
    1. Azure Databricks では、次のスクリーンショットを参考にして、 **[変換]** ノートブックを Databricks ワークスペースにインポートします。 以下と同じ場所でなくてもかまいませんが、後でわかるように選択したパスを覚えておいてください。
   
       ![2](media/solution-template-Databricks-notebook/Databricks-tutorial-image02.png)    
    
    1. [**URL** からインポートする] を選択して、テキストボックスに以下の URL を入力します。
    
       * `https://adflabstaging1.blob.core.windows.net/share/Transformations.html`
        
       ![3](media/solution-template-Databricks-notebook/Databricks-tutorial-image03.png)    

4. それでは、 **[変換]** ノートブックを実際のストレージ接続情報で更新しましょう。 上記のインポートされたノートブックで、(以下のコード スニペットに示すように) **コマンド 5** に移動して、`<storage name>` および `<access key>` を実際のストレージ接続情報に置き換えます。 このアカウントが先ほど作成したものと同じストレージ アカウントであり、`sinkdata` コンテナーが含まれることを確認します。

    ```python
    # Supply storageName and accessKey values  
    storageName = "<storage name>"  
    accessKey = "<access key>"  

    try:  
      dbutils.fs.mount(  
        source = "wasbs://sinkdata\@"+storageName+".blob.core.windows.net/",  
        mount_point = "/mnt/Data Factorydata",  
        extra_configs = {"fs.azure.account.key."+storageName+".blob.core.windows.net": accessKey})  

    except Exception as e:  
      # The error message has a long stack track. This code tries to print just the relevant line indicating what failed.

    import re
    result = re.findall(r"\^\s\*Caused by:\s*\S+:\s\*(.*)\$", e.message, flags=re.MULTILINE)
    if result:
      print result[-1] \# Print only the relevant error message
    else:  
      print e \# Otherwise print the whole stack trace.  
    ```

5.  Data Factory で Databricks にアクセスするための **Databricks アクセス トークン**を生成します。 後で Databricks のリンクされたサービスを作成するときに使用するので、**アクセス トークンを保存します**。トークンは、"dapi32db32cbb4w6eee18b7d87e45exxxxxx" のような値です。

    ![4](media/solution-template-Databricks-notebook/Databricks-tutorial-image04.png)

    ![5](media/solution-template-Databricks-notebook/Databricks-tutorial-image05.png)

## <a name="how-to-use-this-template"></a>このテンプレートの使用方法

1.  **[Transformation with Azure Databricks]\(Azure Databricks による変換\)** テンプレートに移動します。 次の接続用に新しいリンクされたサービスを作成します。 
    
    ![接続の設定](media/solution-template-Databricks-notebook/connections-preview.png)

    1.  **ソース BLOB 接続** - ソース データへのアクセス用です。 
        
        このサンプルのソース ファイルが含まれるパブリック BLOB ストレージを使用できます。 構成については、次のスクリーンショットを参考にしてください。 以下の **[SAS URL]** を使用して、ソース ストレージに接続します (読み取り専用アクセス)。 
        * `https://storagewithdata.blob.core.windows.net/data?sv=2018-03-28&si=read%20and%20list&sr=c&sig=PuyyS6%2FKdB2JxcZN0kPlmHSBlD8uIKyzhBWmWzznkBw%3D`

        ![6](media/solution-template-Databricks-notebook/Databricks-tutorial-image06.png)

    1.  **コピー先 BLOB 接続** – データのコピー先用です。 
        
        シンクのリンクされたサービスでは、**前提条件** 1 で作成されたストレージを選択します。

        ![7](media/solution-template-Databricks-notebook/Databricks-tutorial-image07.png)

    1.  **Azure Databricks** – Databricks クラスターへの接続用です。

        **前提条件** 2.c で生成したアクセス キーを使用して、Databricks のリンクされたサービスを作成します。 "*対話型クラスター*" がある場合は、それを選択してもかまいません。 (この例では、 *[New job cluster]\(新しいジョブ クラスター\)* オプションを使用します。)

        ![8](media/solution-template-Databricks-notebook/Databricks-tutorial-image08.png)

1. **[Use this template]\(このテンプレートを使用する\)** を選択すると、以下に示すようにパイプラインが作成されていることがわかります。
    
    ![パイプラインを作成する](media/solution-template-Databricks-notebook/new-pipeline.png)   

## <a name="pipeline-introduction-and-configuration"></a>パイプラインの概要と構成

作成された新しいパイプラインでは、ほとんどの設定が自動的に既定値で構成されています。 構成を確認し、必要に応じてご自身の設定に合うように更新してください。 詳細については、以下の手順と参考用のスクリーンショットを確認してください。

1.  検証アクティビティの **[Availability flag]\(可用性フラグ\)** は、ソースの可用性チェックを実行するために作成されます。 前の手順で作成した *SourceAvailabilityDataset* が、データセットとして選択されます。

    ![12](media/solution-template-Databricks-notebook/Databricks-tutorial-image12.png)

1.  コピー アクティビティの **[file-to-blob]\(file-to-blob\)** は、ソースからシンクにデータセットをコピーするために作成されます。 コピー アクティビティのソースとシンクの構成については、以下のスクリーンショットを参照してください。

    ![13](media/solution-template-Databricks-notebook/Databricks-tutorial-image13.png)

    ![14](media/solution-template-Databricks-notebook/Databricks-tutorial-image14.png)

1.  ノートブック アクティビティの **[変換]** が作成され、前の手順で作成されたリンクされたサービスが選択されます。
    ![16](media/solution-template-Databricks-notebook/Databricks-tutorial-image16.png)

     1. **[設定]** タブを選択します。"*ノートブックのパス*" については、テンプレートによって既定でパスが定義されます。 必要に応じて、**前提条件 2** でアップロードした正しいノートブック パスを参照して選択します。 

         ![17](media/solution-template-Databricks-notebook/databricks-tutorial-image17.png)
    
     1. スクリーンショットに示されているように、作成された "*基本パラメータ―* " を確認します。 それらは、Data Factory から Databricks ノートブックに渡されます。 

         ![基本パラメーター](media/solution-template-Databricks-notebook/base-parameters.png)

1.  **[パイプライン パラメーター]** は次のように定義されます。

    ![15](media/solution-template-Databricks-notebook/Databricks-tutorial-image15.png)

1. データセットを設定します。
    1.  ソース データが使用できることを確認するために、**SourceAvailabilityDataset** が作成されます。

        ![9](media/solution-template-Databricks-notebook/Databricks-tutorial-image09.png)

    1.  **SourceFilesDataset** - ソース データのコピー用です。

        ![10](media/solution-template-Databricks-notebook/Databricks-tutorial-image10.png)

    1.  **DestinationFilesDataset** – シンク/コピー先の場所へのコピー用です。

        1.  リンクされたサービス - 前の手順で作成した *sinkBlob_LS*。

        2.  ファイル パス - *sinkdata/staged_sink*。

            ![11](media/solution-template-Databricks-notebook/Databricks-tutorial-image11.png)


1.  **[デバッグ]** を選択してパイプラインを実行します。 より詳細な Spark のログについては、Databricks のログへのリンクを検索できます。

    ![18](media/solution-template-Databricks-notebook/Databricks-tutorial-image18.png)

    ストレージ エクスプローラーを使用してデータ ファイルを確認することもできます。 (Data Factory のパイプラインの実行と関連付けるため、この例では Data Factory からのパイプライン実行 ID を出力フォルダーに追加しています。 このようにすると、各実行で生成されたファイルに戻って追跡できます。)

    ![19](media/solution-template-Databricks-notebook/Databricks-tutorial-image19.png)

## <a name="next-steps"></a>次のステップ

- [Azure Data Factory の概要](introduction.md)
