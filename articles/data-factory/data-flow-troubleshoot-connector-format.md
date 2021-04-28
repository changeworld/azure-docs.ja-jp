---
title: マッピング データ フローにおけるコネクタと形式に関する問題をトラブルシューティングする
description: Azure Data Factory でのコネクタと形式に関するデータ フローの問題のトラブルシューティングを行う方法について説明します。
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 04/20/2021
ms.openlocfilehash: 1991436e2cc890b5c6a339f79e42536ced2fbd36
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739478"
---
# <a name="troubleshoot-connector-and-format-issues-in-mapping-data-flows-in-azure-data-factory"></a>Azure Data Factory でのマッピング データ フローにおけるコネクタと形式に関する問題をトラブルシューティングする


この記事では、Azure Data Factory (ADF) でのマッピング データ フローのためのコネクタと形式に関するトラブルシューティング方法について説明します。


## <a name="cosmos-db--json"></a>Cosmos DB および JSON

### <a name="support-customized-schemas-in-the-source"></a>ソースでのカスタマイズされたスキーマをサポートする

#### <a name="symptoms"></a>現象
ADF データ フローを使用して Cosmos DB または JSON から他のデータ ストアにデータを移動または転送する場合に、ソース データの一部の列が失われる場合がある。 

#### <a name="cause"></a>原因 
スキーマ フリー コネクタの場合 (各行の列番号、列名、および列データ型は、他の行と比較したとき異なる場合があります)、既定では、ADF はサンプル行 (上位 100 行や 1000 行のデータなど) を使用してスキーマを推論し、推論された結果はデータを読み取るスキーマとして使用されます。 そのため、サンプル行に登場しない余分な列がデータ ストアに含まれている場合、これらの余分な列のデータは、読み取られたり、移動したり、シンク データ ストアに転送されることはありません。

#### <a name="recommendation"></a>推奨
既定の動作を上書きし、追加のフィールドを取り込むために、ADF ではソース スキーマをカスタマイズするためのオプションが提供されています。 データを読み取るためのデータ フロー ソース プロジェクションで、スキーマ推論結果において欠落する可能性のある追加/欠落列を指定できます。また、次のオプションのいずれかを適用して、カスタマイズされたスキーマを設定できます。 通常の場合、**オプション 1** を使用することをお勧めします。

- **オプション 1**: 複雑なスキーマを持つ数百万行を含む 1 つの大きなファイル、テーブル、またはコンテナーである可能性がある元のソース データと比較して、読み取り対象のすべての列を含む数行の行を含む一時テーブル/コンテナーを作成し、次の操作に進むことができます。 

    1. データ フロー ソースの **[デバッグ設定]** を使用して、 **[Import projection]\(プロジェクションのインポート\)** で、完全なスキーマを取得するためにサンプルのファイル/テーブルを使用します。 次の図の手順に従うことができます。<br/>

        ![ソース スキーマをカスタマイズする最初のオプションの最初の部分を示すスクリーンショット。](./media/data-flow-troubleshoot-connector-format/customize-schema-option-1-1.png)<br/>
         1. データ フロー キャンバスで **[デバッグ設定]** を選択します。
         1. ポップアップ ウィンドウで、 **[cosmosSource]** タブの下にある **[サンプル テーブル]** を選択し、 **[テーブル]** ブロックにテーブルの名前を入力します。
         1. **[Save]\(保存\)** を選択して設定を保存します。
         1. **[Import projection]\(プロジェクションのインポート\)** を選択します。<br/>  
    
    1. 残りのデータ移動/変換用にソース データセットを使用するように、 **[デバッグ設定]** を戻します。 次の図の手順に進むことができます。<br/>

        ![ソース スキーマをカスタマイズする最初のオプションの 2 番目の部分を示すスクリーンショット。](./media/data-flow-troubleshoot-connector-format/customize-schema-option-1-2.png) <br/>   
         1. データ フロー キャンバスで **[デバッグ設定]** を選択します。
         1. ポップアップ ウィンドウで、 **[cosmosSource]** タブの下にある **[ソース データセット]** を選択します。
         1. **[Save]\(保存\)** を選択して設定を保存します。<br/>
    
    その後、ADF データ フロー ランタイムでは、カスタマイズされたスキーマを優先して使用し、元のデータ ストアからデータを読み取ります。 <br/>

- **オプション 2**: ソース データのスキーマと DSL 言語を使い慣れている場合は、データ フローのソース スクリプトを手動で更新して、データを読み取るための追加/欠落列を追加することができます。 例を次の図に示します。 

    ![ソース スキーマをカスタマイズする 2 番目のオプションを示すスクリーンショット。](./media/data-flow-troubleshoot-connector-format/customize-schema-option-2.png)

## <a name="next-steps"></a>次のステップ
トラブルシューティングの詳細について、次のリソースを参照してください。

*  [Azure Data Factory でマッピング データ フローをトラブルシューティングする](data-flow-troubleshoot-guide.md)
*  [Data Factory ブログ](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory の機能のリクエスト](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure のビデオ](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Data Factory の Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory に関する Twitter 情報](https://twitter.com/hashtag/DataFactory)