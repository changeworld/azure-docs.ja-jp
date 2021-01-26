---
title: リソース セットについて
description: この記事では、リソース セットの概要と、Azure Purview でそれらが作成される仕組みについて説明します。
author: yaronyg
ms.author: yarong
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/19/2020
ms.openlocfilehash: 55efa9443fd59b66a7677c9c460e473715f201df
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2020
ms.locfileid: "96550730"
---
# <a name="understanding-resource-sets"></a>リソース セットについて

この記事は、Azure Purview がリソース セットを使用してデータ資産を論理リソースにマップする方法を理解するのに役立ちます。

## <a name="background-info"></a>背景情報

大規模なデータ処理システムでは、通常、1 つのテーブルが複数のファイルとしてディスクに格納されます。 Azure Purview では、この概念がリソース セットを使用して表されます。 リソース セットは、カタログにおいて、ストレージ内の多数の資産を表す単一のオブジェクトです。

たとえば、Spark クラスターが ADLS Gen2 データ ソースに DataFrame を永続化したとします。 Spark では、テーブルは 1 つの論理リソースのように見えますが、ディスク上には Parquet ファイルが多数存在し、それぞれが DataFrame のコンテンツ全体のパーティションを表す可能性があります。 IoT データと Web ログ データには同じ課題があります。 たとえば、ログフ ァイルを 1 秒間に数回出力するセンサーがあるとします。 この 1 つのセンサーから数千のログ ファイルが取得されるのに時間はかかりません。

大量のデータ資産を 1 つの論理リソースにマッピングするという課題に対処するため、Azure Purview ではリソース セットを使用します。

## <a name="how-azure-purview-detects-resource-sets"></a>Azure Purview がリソース セットを検出する方法

Azure Purview は、Azure BLOB、ADLS Gen1、ADLS Gen2 でのみ、リソース セットの検出をサポートしています。

Azure Purview は、自動リソース セット検出機能を使用して、リソース セットを自動的に検出します。 この機能は、スキャンによって取り込まれたすべてのデータを調べ、定義済みのパターンのセットと比較します。

たとえば、URL が `https://myaccount.blob.core.windows.net/mycontainer/machinesets/23/foo.parquet` のデータ ソースをスキャンするとします。 Azure Purview はパス セグメントを参照し、それらが組み込みのパターンに一致するかどうかを判断します。 GUID、数値、日付形式、ローカライズ コード (en-us など) などのパターンが組み込まれています。 この場合、数値パターンが *23* と一致します。 Azure Purview では、このファイルが `https://myaccount.blob.core.windows.net/mycontainer/machinesets/{N}/foo.parquet` という名前のリソース セットの一部であると見なされます。

また、`https://myaccount.blob.core.windows.net/mycontainer/weblogs/en_au/23.json` のような URL の場合、Azure Purview はローカライズ パターンと数値パターンの両方を一致させて、`https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` という名前のリソース セットを生成します。

この戦略を使用すると、Azure Purview は次のリソースを同じリソース セット `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` にマップします。

- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/1004.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/234.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/de_Ch/23434.json`

> [!Note]
> Azure Data Lake Storage Gen2 の一般提供が開始されました。 今すぐ使用を開始することをお勧めします。 詳細については、[製品に関するページ](https://azure.microsoft.com/en-us/services/storage/data-lake-storage/)を参照してください。

## <a name="file-types-that-azure-purview-will-not-detect-as-resource-sets"></a>Azure Purview がリソース セットとして検出しないファイルの種類

Purview は、Word、Excel、PDF など、ほとんどの種類のドキュメント ファイルを、意図的にリソース セットとして分類しようとしません。 一般的なパーティション分割されたファイル形式である CSV は例外です。

## <a name="how-azure-purview-scans-resource-sets"></a>Azure Purview がリソース セットをスキャンする方法

リソース セットの一部であると思われるリソースを検出すると、Azure Purview はフル スキャンからサンプル スキャンに切り替えます。 サンプル スキャンでは、リソース セットに含まれていると見なされるファイルのサブセットのみが開かれます。 開くファイルごとに、そのスキーマが使用され、分類子が実行されます。 その後 Azure Purview は、開いているリソースの中で最も新しいリソースを見つけ、そのリソースのスキーマと分類を、カタログでのリソース セット全体のエントリで使用します。

## <a name="what-azure-purview-stores-about-resource-sets"></a>Azure Purview でリソース セットに関して格納される情報

Azure Purview では、単一のスキーマと分類に加えて、リソース セットに関する次の情報が格納されます。

- ディープ スキャンを行った最新のパーティション リソースのデータ。
- リソース セットを構成するパーティション リソースに関して集めた情報。
- 検出されたパーティション リソースの数を示すパーティション数。
- ディープ スキャンを行ったサンプル セットで見つかった一意のスキーマの数を示すスキーマ数。 この値は、1 から 5 の間の数値か、5、5 以上の値です。
- 複数のパーティションの種類がリソース セットに含まれている場合のパーティションの種類のリスト。 たとえば、IoT センサーは XML ファイルと JSON ファイルの両方を出力する場合がありますが、どちらも論理的には同じリソース セットに含まれます。

## <a name="built-in-resource-set-patterns"></a>組み込みのリソース セット パターン

Azure Purview では、次のリソース セット パターンがサポートされています。 これらのパターンは、ディレクトリ内の名前として、またはファイル名の一部として表示されます。

| パターン名 | Display name | 説明 |
|--------------|--------------|-------------|
| GUID         | {GUID}       | [RFC 4122](https://tools.ietf.org/html/rfc4122) で定義されている、グローバル一意識別子です。 |
| Number       | {N}          | 1 桁以上の数字です。 |
| 日付/時刻形式 | {N}     | Azure Purview は、さまざまな種類の日付/時刻形式をサポートしていますが、すべてが一連の {N} に短縮されます。 |
| 4ByteHex     | {HEX}        | 4 桁の 16 進数です。 |
| ローカライズ | {LOC}        | [BCP 47](https://tools.ietf.org/html/bcp47) で定義されている言語タグです。 Azure Purview では、ハイフン (-) またはアンダースコア (_) を含むタグがサポートされています。 たとえば、en_ca や en-ca です。 |

## <a name="issues-with-resource-sets"></a>リソース セットに関する問題

リソース セットは、ほとんどの場合、適切に機能しますが、Azure Purview で次のような問題が発生する可能性があります。

- 資産をリソース セットとして誤ってマークする
- 資産を間違ったリソース セットに配置する
- 資産をリソース セットではないとして誤ってマークする

## <a name="next-steps"></a>次のステップ

Data Catalog の使用を開始するには、「[クイックスタート: Azure Purview アカウントの作成](create-catalog-portal.md)」を参照してください。
