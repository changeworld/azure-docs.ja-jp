---
title: Illumina Platinum Genomes
titleSuffix: Azure Open Datasets
description: Azure Open Datasets で Illumina Platinum Genomes データセットを使用する方法について説明します。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 0a1b9d5a41a0dcc45bea0456c2c3714b787a5af8
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2021
ms.locfileid: "114284245"
---
# <a name="illumina-platinum-genomes"></a>Illumina Platinum Genomes

全ゲノム シーケンシングにより、世界中の研究者がヒト ゲノムをより完全かつ正確に解析できるようになりました。 これには、ベンチマークとして使用するゲノム セットで呼び出される、信頼性の高いバリアントの包括的なゲノム全体のカタログが必要です。 Illumina は 3 世代の家系の中から 17 人の個人の深い全ゲノム シーケンス データを生成しました。 Illumina は、現在利用可能な範囲のアルゴリズムを使用して各ゲノムのバリアントを呼び出しました。

データの詳細については、公式の [Illumina サイト](https://www.illumina.com/platinumgenomes.html)を参照してください。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="data-source"></a>データ ソース

このデータセットは、ftp://ussd-ftp.illumina.com/ をミラーリングしたものです

## <a name="data-volumes-and-update-frequency"></a>データ量と更新の頻度

このデータセットには、約 2 GB のデータが含まれており、毎日更新されます。

## <a name="storage-location"></a>保存先

このデータセットは米国西部 2 および米国中西部 Azure リージョンに保存されています。 アフィニティのため、米国西部 2 または米国中西部にコンピューティング リソースを割り当てることをお勧めします。

## <a name="data-access"></a>データ アクセス

米国西部 2: "https://datasetplatinumgenomes.blob.core.windows.net/dataset"

米国中西部: "https://datasetplatinumgenomes-secondary.blob.core.windows.net/dataset"

[SAS トークン](../storage/common/storage-sas-overview.md): sv=2019-02-02&se=2050-01-01T08%3A00%3A00Z&si=prod&sr=c&sig=FFfZ0QaDcnEPQmWsshtpoYOjbzd4jtwIWeK%2Fc4i9MqM%3D

## <a name="use-terms"></a>使用条件

データは制限なく使用できます。 詳細情報と引用の詳細については、[公式の Illumina サイト](https://www.illumina.com/platinumgenomes.html)を参照してください。

## <a name="contact"></a>Contact

このデータセットに関する質問またはフィードバックについては、platinumgenomes@illumina.com にお問い合わせください。

## <a name="data-access"></a>データ アクセス

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=genomics-platinum-genomes -->

> [!TIP]
> **[代わりにデータセットをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=genomics-platinum-genomes)** 。

## <a name="getting-the-illumina-platinum-genomes-from-azure-open-datasets-and-doing-initial-analysis"></a>Azure Open Datasets からの Illumina Platinum Genomes の取得と初期分析の実行 

Jupyter Notebook、GATK、Picard を使用して、次の操作を行います。

1. VariantFiltration を使用して遺伝子型に注釈を付ける
2. 特定のバリアントを選択する
3. 関連するバリアントをフィルター処理する - 呼び出しなし、または特定のリージョン
4. 一致分析を実行する
5. 最終的な VCF ファイルをテーブルに変換する 

**依存関係:**

このノートブックには、次のライブラリが必要です。

- Azure Storage `pip install azure-storage-blob`

- numpy `pip install numpy`

- Genome Analysis Toolkit (GATK) (*ユーザーは、Broad Institute の Web ページから、次のノートブックと同じコンピューティング環境に GATK をダウンロードする必要があります: https://github.com/broadinstitute/gatk/releases* )

**重要な情報: このノートブックでは、Python 3.6 カーネルが使用されています**

## <a name="getting-the-genomics-data-from-azure-open-datasets"></a>Azure Open Datasets から Genomics データを取得する

いくつかの公開ゲノミクス データが Azure Open Dataset として[こちら](https://azure.microsoft.com/services/open-datasets/catalog/)にアップロードされています。 このオープン データセットにリンクされている Blob service を作成します。 `Illumina Platinum Genomes` データセット用のデータを Azure Open Datasets から呼び出す手順の例を以下に示します。

### <a name="downloading-the-specific-illumina-platinum-genomes"></a>特定の "Illumina Platinum Genomes" のダウンロード

```python
import os
import uuid
import sys
from azure.storage.blob import BlockBlobService, PublicAccess

blob_service_client = BlockBlobService(account_name='datasetplatinumgenomes', sas_token='sv=2019-02-02&se=2050-01-01T08%3A00%3A00Z&si=prod&sr=c&sig=FFfZ0QaDcnEPQmWsshtpoYOjbzd4jtwIWeK%2Fc4i9MqM%3D')     
blob_service_client.get_blob_to_path('dataset/2017-1.0/hg38/small_variants/NA12877', 'NA12877.vcf.gz', './NA12877.vcf.gz')
```

### <a name="1-annotate-genotypes-using-variantfiltration"></a>1. VariantFiltration を使用して遺伝子型に注釈を付ける

**重要な注意: GATK がシステムで実行されていることを確認してください。**

ヘテロ接合遺伝子型をフィルター処理する場合は、VariantFiltration の `--genotype-filter-expression isHet == 1` オプションを使用します。 ツールの注釈値を指定し、`--genotype-filter-name` オプションを使用してヘテロ接合遺伝子型にラベルを付けることができます。 ここでは、このパラメーターの値を `isHetFilter` に設定します。 最初の例では、Illimina Platinum Genomes から `NA12877.vcf.gz` を使用しましたが、ユーザーは他のデータセット `Platinum Genomes` から任意の vcf ファイルを使用できます。

```python
run gatk VariantFiltration -V NA12877.vcf.gz -O outputannot.vcf --genotype-filter-expression "isHet == 1" --genotype-filter-name "isHetFilter"
```

### <a name="2-select-specific-variants"></a>2. 特定のバリアントを選択する

VCF ファイルからバリアントのサブセットを選択します。 このツールを使用すると、特定の分析を容易にするために、さまざまな条件に基づいてバリアントのサブセットを選択できます。 このような分析の例としては、事例と対照群の比較、特定の要件を満たすバリアントまたは非バリアントの loci の抽出、予期しない結果のトラブルシューティングなどがあります。

より大きな呼び出しセットからバリアントのサブセットを選択するには、さまざまなオプションがあります。

完全なサンプル名またはパターン一致に基づいて、呼び出しセットから 1 つ以上のサンプルを抽出します。
注釈値にしきい値を設定する包含の条件を指定します。**たとえば、"DP > 1000" (カバレッジの深さが 1000x より大きい)、"AF < 0.25" (アレル頻度が 0.25 未満のサイト) など**。 これらの条件は "JEXL 式" として記述されます。これについては、JEXL 式の使用に関する記事を参照してください。
他の特定の呼び出しセットにも存在するバリアントを含めるか除外するために、一致または不一致のトラックを提供します。
種類 (INDEL のみなど)、メンデル違反の証拠、フィルター状態、アレル性などの条件に基づいてバリアントを選択します。特定の注釈の元の値を記録するためのオプションもいくつかあります。これは、新しい呼び出しセットのサブセット化、アレルのトリミングなどのときに再計算されます。

入力: サブセットを選択できる VCF 形式のバリアント呼び出しセット。

出力: バリアントの選択されたサブセットが含まれる新しい VCF ファイル。

```python
run gatk SelectVariants -R Homo_sapiens_assembly38.fasta -V outputannot.vcf --select-type-to-include SNP --select-type-to-include INDEL -O selective.vcf
```

### <a name="3-transform-filtered-genotypes-to-no-call"></a>3. フィルター処理された遺伝子型を呼び出しなしに変換する 

--set-filtered-gt-to-nocall を使用して SelectVariants を実行すると、null 遺伝子型呼び出しでフラグが設定された遺伝子型がさらに変換されます。 

ダウンストリーム ツールでは FORMAT レベルのフィルター フィールドが解析されないので、この変換が必要です。

**"呼び出しなし"** を使用してバリアントをフィルター処理する方法

```python
run gatk SelectVariants -V outputannot.vcf --set-filtered-gt-to-nocall -O outputnocall.vcf
```

### <a name="4-check-the-concordance-of-vcf-file-with-ground-truth"></a>4. Ground Truth を使用して VCF ファイルの一致を調べる

真の VCF に対して入力 VCF のサイト レベルの一致を評価します。
このツールは、2 つのバリアント呼び出しセットを相互に評価し、6 列の概要メトリック テーブルを生成します。 

**この関数では次のことが行われます。**

1. SNP と INDEL の呼び出しを階層化する
2. 真陽性、擬陽性、擬陰性の呼び出しを報告する
3. 感度と精度を計算する

このツールでは、--truth VCF のすべてのレコードで真のバリアントが渡されるものと仮定します。 -eval VCF の場合、ツールではフィルター処理されていない呼び出し引き渡しのみが使用されます。

必要に応じて、次のバリアント レコードの VCF を生成するようにツールを設定し、各バリアントの一致状態で注釈を付けることができます。

真陽性と擬陰性 (つまり、truth VCF のすべてのバリアント): 感度の計算に役立ちます

真陽性と擬陽性 (つまり、eval VCF のすべてのバリアント): 成果物の機械学習分類子のトレーニング データ セットを取得する場合に便利です

**これらの出力 VCF を VariantsToTable に渡して、R または Python での統計分析用の TSV ファイルを生成できます。**

```python
 run gatk Concordance -R Homo_sapiens_assembly38.fasta -eval outputannot.vcf --truth outputnocall.vcf  --summary summary.tsv 
```

### <a name="5-variantstotable"></a>5. VariantsToTable

VCF ファイルからタブ区切りテーブルにフィールドを抽出します。 このツールは、VCF ファイル内の各バリアントの指定されたフィールドを、タブ区切りテーブルに抽出します。これは、VCF より簡単に使用できる場合があります。 既定では、ツールにより VCF ファイル内の PASS または (フィルター処理されていない) バリアントのみが抽出されます。 --show-filtered フラグを追加することで、フィルター処理されたバリアントを出力に含めることができます。 このツールでは、INFO (つまり、サイト レベル) フィールドと FORMAT (つまり、サンプル レベル) フィールドの両方を抽出できます。

INFO/サイト レベルのフィールド:

INFO フィールドを抽出するには、`-F` 引数を使用します。各フィールドは出力ファイル内の 1 つの列を占めます。 フィールドは、標準の VCF 列 (CHROM、ID、QUAL など) でも、INFO フィールド内の任意の注釈名 (AC、AF など) でもかまいません。 このツールでは、次のフィールドもサポートされています。

EVENTLENGTH (イベントの長さ)、TRANSITION (1 は両アレル遷移 (SNP)、0 は両アレル トランスバージョン (SNP)、-1 は INDEL と複対立遺伝子)、HET (ヘテロ遺伝子型の数)、HOM-REF (同型参照遺伝型の数)、HOM-VAR (同型バリアント遺伝型の数)、NO-CALL (呼び出しなし遺伝子型の数)、TYPE (バリアントの種類、NO_VARIATION、SNP、MNP、INDEL、SYMBOLIC、MIXED)、VAR (非参照遺伝子型の数)、NSAMPLES (サンプルの数)、NCALLED (呼び出されたサンプルの数)、MULTI-ALLELIC (これはバリアント複対立遺伝子か? true/false)

FORMAT/サンプル レベル フィールド:

FORMAT/サンプル レベル フィールドを抽出するには、`-GF` 引数を使用します。 このツールでは、サンプルごとに "SAMPLE_NAME.FORMAT_FIELD_NAME" という名前の新しい列が作成されます (例: NA12877.GQ、NA12878.GQ)。

次の内容を入力します。

テーブルに変換する VCF ファイル

Output:

VCF ファイル内の要求されたフィールドの値を含むタブ区切りファイル。

```python
run gatk VariantsToTable -V NA12877.vcf.gz -F CHROM -F POS -F TYPE -F AC -F AD -F AF -GF DP -GF AD -O outputtable.table
```

## <a name="references"></a>関連項目

1. VariantFiltration: https://gatk.broadinstitute.org/hc/en-us/articles/360036827111-VariantFiltration 
2. バリアントの選択: https://gatk.broadinstitute.org/hc/en-us/articles/360037052272-SelectVariants
3. 一致： https://gatk.broadinstitute.org/hc/en-us/articles/360041851651-Concordance
4. テーブルへのバリアント: https://gatk.broadinstitute.org/hc/en-us/articles/360036882811-VariantsToTable 
5. Illumina Platinum Genomes: https://www.illumina.com/platinumgenomes.html 

<!-- nbend -->

---

## <a name="next-steps"></a>次のステップ

[Open Datasets カタログ](dataset-catalog.md)の残りのデータセットを表示します。