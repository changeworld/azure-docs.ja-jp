---
title: 'Enter Data Manually (データの手動入力): コンポーネント リファレンス'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning で Enter Data Manually (データの手動入力) コンポーネントを使用し、値を入力することによって、小さなデータセットを作成する方法について説明します。 データセットには、複数の列を含めることができます。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 836b43a26f89856f2a3c122c5f5ab1f81d0680db
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566120"
---
# <a name="enter-data-manually-component"></a>Enter Data Manually (データの手動入力) コンポーネント

この記事では Azure Machine Learning デザイナーのコンポーネントについて説明します。

値を入力して小さなデータセットを作成するには、**Enter Data Manually (データの手動入力)** コンポーネントを使用します。 データセットには、複数の列を含めることができます。
  
このコンポーネントは、次のような用途に使用できます。  
  
- テスト用に少数の値のセットを生成する。  
- ラベルの簡単なリストを作成する。  
- データセットに挿入する列名のリストを入力する。

## <a name="create-a-dataset"></a>データセットを作成する 
  
1. [Enter Data Manually (データの手動入力)](./enter-data-manually.md) コンポーネントをパイプラインに追加します。 このコンポーネントは、Azure Machine Learning の **[Data Input and Output]\(データの入力と出力\)** カテゴリにあります。 
  
1. **[DataFormat]** で、次のいずれかのオプションを選択します。 指定したデータの解析方法は、これらのオプションによって決まります。 それぞれの形式で要件が大きく異なるため、関連するトピックに必ず目を通してください。  
  
   - **ARFF**: Weka で使用される属性関連ファイル形式。   
   - **CSV**: コンマ区切りの値形式。 詳細については、「[Convert to CSV (CSV への変換)](./convert-to-csv.md)」を参照してください。    
   - **SVMLight**: Vowpal Wabbit などの機械学習フレームワークで使用される形式。    
   - **TSV**: タブ区切りの値形式。

   形式を選択したにもかかわらず、その形式の仕様を満たしたデータを入力しなかった場合、実行時エラーが発生します。
  
1. **[Data]\(データ\)** ボックス内をクリックして、データの入力を開始します。 次の形式には、特別な注意が必要です。  
  
   - **CSV**: 複数の列を作成するには、コンマ区切りテキストを貼り付けるか、フィールド間にコンマを使用して複数の列を入力します。
  
     **HasHeader** オプションをオンにすると、値の先頭行を列見出しとして使用できます。  
  
     このオプションをオフにした場合は、Col1、Col2... という列名が使用されます。 列名は、後から [[メタデータの編集]](./edit-metadata.md) を使用して追加したり変更したりできます。  
  
   - **TSV**: 複数の列を作成するには、タブ区切りテキストを貼り付けるか、フィールド間にタブを使用して複数の列を入力します。  
  
     **HasHeader** オプションをオンにすると、値の先頭行を列見出しとして使用できます。  
  
     このオプションをオフにした場合は、Col1、Col2... という列名が使用されます。 列名は、後から [[メタデータの編集]](./edit-metadata.md) を使用して追加したり変更したりできます。  
  
   - **ARFF**:既存の ARFF 形式のファイルを貼り付けます。 値を直接入力する場合は、データの先頭にヘッダー (省略可能) と必須の属性フィールドを追加してください。 

     たとえば、単純なリストに次のヘッダーと属性行を追加できます。 この場合、列見出しは `SampleText` となります。 文字列型はサポートされていません。
    
     ```text
     % Title: SampleText.ARFF  
     % Source: Enter Data component  
     @ATTRIBUTE SampleText NUMERIC  
     @DATA  
     \<type first data row here>  
     ```

   - **SVMLight**:SVMLight 形式を使用して値を入力するか貼り付けます。  
  
     たとえば次のサンプルは、SVMLight 形式の献血データセットの最初の 2 行を表したものです。  
  
     ```text  
     # features are [Recency], [Frequency], [Monetary], [Time]  
     1 1:2 2:50 3:12500 4:98   
     1 1:0 2:13 3:3250 4:28   
     ```  
  
     [Enter Data Manually (データの手動入力)](./enter-data-manually.md) コンポーネントを実行すると、これらの行が列とインデックス値のデータセットに変換されます。その例を次に示します。  
  
     |Col1|Col2|Col3|Col4|ラベル|  
     |-|-|-|-|-|  
     |0.00016|0.004|0.999961|0.00784|1|  
     |0|0.004|0.999955|0.008615|1|  
  
1. それぞれの行の後に Enter キーを選択して改行します。      
     
   複数回 Enter キーを選択して空の後続行を複数追加した場合、空の行は削除またはトリミングされます。  
  
   欠損値を含んだ行を作成した場合、それらの行は、後からいつでもフィルターで除去できます。  
  
1. 出力ポートを他のコンポーネントに接続し、パイプラインを実行します。  
  
   データセットを表示するには、コンポーネントを右クリックして **[可視化]** を選択します。

## <a name="next-steps"></a>次の手順

Azure Machine Learning で[使用できる一連のコンポーネント](component-reference.md)をご覧ください。 