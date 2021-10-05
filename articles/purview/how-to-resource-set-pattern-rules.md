---
title: リソース セットのパターン ルールを作成する方法
description: リソース セットのパターン ルールを作成して、資産がリソース セットにどのようにグループ化されるかを上書きする方法について説明します
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: c2d6f90bf9a3e3b3f7c03db1ab0a4fbf258703eb
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129218871"
---
# <a name="create-resource-set-pattern-rules"></a>リソース セットのパターン ルールの作成

大規模なデータ処理システムでは、通常、1 つのテーブルが複数のファイルとしてストレージに格納されます。 Azure Purview では、この概念がリソース セットを使用して表されます。 リソース セットは、データ カタログにおいて、ストレージ内の多数の資産を表す単一のオブジェクトです。 詳細については、「[リソース セットについて](concept-resource-sets.md)」を参照してください。

Azure Purview では、ストレージ アカウントをスキャンするときに、一連の定義済みパターンを使用して、資産のグループがリソース セットであるかどうかを判断します。 場合によっては、Azure Purview のリソース セットのグループ化がデータ資産を正確に反映しないことがあります。 リソース セットのパターン ルールを使用すると、Azure Purview がリソース セットとしてグループ化される資産を検出する方法や、カタログ内でのそれらの表示方法をカスタマイズまたはオーバーライドできます。

現在、パターン規則は次のソースの種類でサポートされています。
- Azure Data Lake Storage Gen2
- Azure Blob Storage
- Azure Files
- Amazon S3

リソース セットのパターン ルールを作成するには、高度なリソース セット機能セットを有効にする必要があります。 詳細については、[高度なリソース セットの概要](concept-resource-sets.md#advanced-resource-sets)に関するセクションを参照してください。

## <a name="how-to-create-a-resource-set-pattern-rule"></a>リソース セットのパターン ルールを作成する方法

リソース セットのパターン ルールを新しく作成するには、次の手順に従います。

1. データ マップに移動します。 [Source management]\(ソース管理\) 見出しの下にあるメニューの **[パターン ルール]** を選択します。 新しいルール セットを作成するには、 **[+ 新規]** を選択します。

   :::image type="content" source="media/how-to-resource-set-pattern-rules/create-new-scoped-resource-set-rule.png" alt-text="新しいリソース セットのパターン ルールの作成" border="true":::

1. リソース セットのパターン ルールのスコープを入力します。 ストレージ アカウントの種類と、ルール セットを作成するストレージ アカウントの名前を選択します。 各ルールのセットは、 **[フォルダー パス]** フィールドで指定されたフォルダー パス スコープに対して相対的に適用されます。

   :::image type="content" source="media/how-to-resource-set-pattern-rules/create-new-scoped-resource-set-scope.png" alt-text="リソース セットのパターン ルールの構成を作成する" border="true":::

1. 構成スコープのルールを入力するには、 **[+ 新しいルール]** を選択します。

1. ルールを作成するには、次のフィールドを入力します。

   1. **ルールの名前:** 構成ルールの名前。 このフィールドは、ルールが適用される資産には影響しません。

   1. **修飾名:** 資産を構成ルールと照合するために、テキスト、動的置換関数、および静的置換関数を組み合わせて使用する修飾パス。 このパスは、構成ルールのスコープを基準としたものです。 修飾名を指定する方法の詳細については、以下の[構文](#syntax)に関するセクションを参照してください。

   1. **表示名:** 資産の表示名。 このフィールドは省略可能です。 プレーン テキストと静的置換関数を使用して、資産をカタログに表示する方法をカスタマイズします。 詳細な手順については、以下の[構文](#syntax)に関するセクションを参照してください。

   1. **リソース セットとしてグループ化しない:** 有効な場合、一致したリソースはリソース セットにグループ化されません。

      :::image type="content" source="media/how-to-resource-set-pattern-rules/scoped-resource-set-rule-example.png" alt-text="新しい構成ルールを作成する。" border="true":::

1. **[追加]** を選択してルールを保存します。

> [!NOTE]
> パターン ルールが作成されると、すべての新しいスキャンでインジェスト中にルールが適用されます。 データ カタログ内の既存の資産は、バックグラウンド プロセスによって更新されます。これには最大で数時間かかる場合があります。 

## <a name="pattern-rule-syntax"></a><a name="syntax"></a> パターン ルールの構文

リソース セットのパターン ルールを作成する場合、次の構文を使用して、ルールが適用される資産を指定します。

### <a name="dynamic-replacers-single-brackets"></a>動的置換関数 (単一かっこ)

単一かっこは、パターン ルールでは **動的置換関数** として使用されます。 `{<replacerName:<replacerType>}` の形式を使用して、修飾名に動的置換関数を指定します。 一致した場合、動的置換関数は、資産がリソース セットとして表される必要があることを示すグループ化条件として使用されます。 資産がリソース セットにグループ化されている場合は、リソース セットの修飾パスには、置換関数が指定された `{replacerName}` が含まれます。

たとえば、`folder1/file-1.csv` および `folder2/file-2.csv` という 2 つの資産がルール `{folder:string}/file-{NUM:int}.csv` と一致した場合は、リソース セットは 1 つのエンティティ `{folder}/file-{NUM}.csv` になります。

#### <a name="special-case-dynamic-replacers-when-not-grouping-into-resource-set"></a>特殊なケース: リソース セットにグループ化しない場合の動的置換関数

パターン ルールに対して *[リソース セットとしてグループ化しない]* が有効になっている場合、置換関数名は省略可能なフィールドです。 `{:<replacerType>}` が有効な構文です。 たとえば、`file-{:int}.csv` は `file-1.csv` と `file-2.csv` について正常に一致すると、リソース セットの代わりに 2 つの異なる資産が作成されます。

### <a name="static-replacers-double-brackets"></a>静的置換関数 (二重かっこ)

二重かっこは、パターン ルールの修飾名で、**静的置換関数** として使用されます。 `{{<replacerName>:<replacerType>}}` の形式を使用して、修飾名に静的置換関数を指定します。 一致した場合、一意の静的置換関数値の各セットによって、異なるリソース セット グループが作成されます。

たとえば、`folder1/file-1.csv` および `folder2/file-2.csv` という 2 つの資産がルール `{{folder:string}}/file-{NUM:int}.csv` と一致した場合は、2 つのリソース セットが `folder1/file-{NUM}.csv` および `folder2/file-{NUM}.csv` で作成されます。

静的置換関数を使用すると、パターン ルールに一致する資産の表示名を指定できます。 ルールの表示名に `{{<replacerName>}}` を使用すると、資産名の一致する値が使用されます。

### <a name="available-replacement-types"></a>利用可能な置換の型

静的および動的な置換関数に使用できる利用可能な型を以下に示します。

| Type | 構造体 |
| ---- | --------- |
| string | スペースなどの区切り記号を含む 1 つ以上の Unicode 文字の文字列。 |
| INT | 1 以上の 0 ～ 9 の ASCII 文字の文字列で、先頭に 0 を配置できる (0001 など)。 |
| guid | [RFC 4122](https://tools.ietf.org/html/rfc4122) で定義されている UUID の 32 または 8-4-4-4-12 の文字列表現の文字列。 |
| date | 任意の区切り記号を含む 6 または 8 文字の 0 - 9 の ASCII 文字の文字列: [RFC 3339](https://tools.ietf.org/html/rfc3339) で指定されている yyyymmdd、yyyy-mm-dd、yymmdd、yy-mm-dd。 |
| time | 任意の区切り記号を含む 4 または 6 文字の 0 - 9 の ASCII 文字の文字列: [RFC 3339](https://tools.ietf.org/html/rfc3339) で指定されている HHmm、HH:mm、HHmmss、HH:mm:ss。 |
| timestamp | 任意の区切り記号を含む 12 または 14 文字の 0 - 9 の ASCII 文字の文字列:[RFC 3339](https://tools.ietf.org/html/rfc3339) で指定されている yyyy-mm-ddTHH:mm、yyyymmddhhmm、yyyy-mm-ddTHH:mm:ss、yyyymmddHHmmss。 |
| boolean | ‘true’ または ‘false’ を含めることができる (大文字と小文字は区別されない)。 |
| number | 任意のドット ‘.’ に続いて、先頭に 0 を配置できる 0 以上の 0 - 9 の ASCII 文字の文字列 (0001 など) と、末尾に 0 を配置できる 1 以上の 0 - 9 の ASCII 文字の文字列 (.100 など) |
| hex | セット 0 ～ 1 および A ～ F の 1 つ以上の ASCII 文字の文字列で、値の先頭には 0 を配置できる |
| locale | [RFC 5646](https://tools.ietf.org/html/rfc5646) で指定された構文に一致する文字列。 |

## <a name="order-of-resource-set-pattern-rules-getting-applied"></a>リソース セットのパターン ルールが適用される順序

パターン ルールを適用する操作の順序を以下に示します。

1. 資産が 2 つのルールと一致する場合は、より具体的なスコープが優先されます。 たとえば、スコープ `container/folder` のルールは、スコープ `container` のルールより前に適用されます。

1. 特定のスコープ内のルールの順序。 これは UX 内で編集可能です。

1. 資産が指定したルールのどれとも一致しない場合は、既定のリソース セット ヒューリスティックが適用されます。

## <a name="examples"></a>例

### <a name="example-1"></a>例 1

フル ロードとデルタ ロードに対する SAP データの抽出

#### <a name="inputs"></a>入力

ファイル :

- `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/13/saptable_customer_20200101_20200102_01.txt`
- `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/13/saptable_customer_20200101_20200102_02.txt`
- `https://myazureblob.blob.core.windows.net/bar/customer/delta/2020/01/15/saptable_customer_20200101_20200102_01.txt`
- `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/17/saptable_customer_20200101_20200102_01.txt`
- `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/17/saptable_customer_20200101_20200102_02.txt`

#### <a name="pattern-rule"></a>パターン ルール

**スコープ:** `https://myazureblob.blob.core.windows.net/bar/`

**表示名:** '外部の顧客'

**修飾名:** `customer/{extract:string}/{year:int}/{month:int}/{day:int}/saptable_customer_{date_from:date}_{date_to:time}_{sequence:int}.txt`

**リソース セット:** true

#### <a name="output"></a>出力

1 つのリソース セット資産

**表示名:** 外部の顧客

**修飾名:** `https://myazureblob.blob.core.windows.net/bar/customer/{extract}/{year}/{month}/{day}/saptable_customer_{date_from}_{date_to}_{sequence}.txt`

### <a name="example-2"></a>例 2

AVRO 形式の IoT データ

#### <a name="inputs"></a>入力

ファイル :

- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/02-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

#### <a name="pattern-rules"></a>パターン ルール

**スコープ:** `https://myazureblob.blob.core.windows.net/bar/`

規則 1

**表示名:** 'machine-89'

**修飾名:** `raw/machinename-89/{date:date}/{time:time}-{id:int}.avro`

**リソース セット:** true

規則 2

**表示名:** 'machine-90'

**修飾名:** `raw/machinename-90/{date:date}/{time:time}-{id:int}.avro`

**リソース セット:** true

#### <a name="outputs"></a>出力

2 つのリソース セット

リソース セット 1

**表示名:** machine-89

**修飾名:** `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/{date}/{time}-{id}.avro`

リソース セット 2

**表示名:** machine-90

**修飾名:** `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/{date}/{time}-{id}.avro`

### <a name="example-3"></a>例 3

AVRO 形式の IoT データ

#### <a name="inputs"></a>入力

ファイル :

- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`
- `https://myazureblob.blob.core.windows.netbar/raw/machinename-89/02-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

#### <a name="pattern-rule"></a>パターン ルール

**スコープ:** `https://myazureblob.blob.core.windows.net/bar/`

**表示名:** 'Machine-{{machineid}}'

**修飾名:** `raw/machinename-{{machineid:int}}/{date:date}/{time:time}-{id:int}.avro`

**リソース セット:** true

#### <a name="outputs"></a>出力

リソース セット 1

**表示名:** machine-89

**修飾名:** `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/{date}/{time}-{id}.avro`

リソース セット 2

**表示名:** machine-90

**修飾名:** `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/{date}/{time}-{id}.avro`

### <a name="example-4"></a>例 4

リソース セットにグループ化しない

#### <a name="inputs"></a>入力

ファイル :

- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/02-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

#### <a name="pattern-rule"></a>パターン ルール

**スコープ:** `https://myazureblob.blob.core.windows.net/bar/`

**表示名:** `Machine-{{machineid}}`

**修飾名名:** `raw/machinename-{{machineid:int}}/{{:date}}/{{:time}}-{{:int}}.avro`

**リソース セット:** false

#### <a name="outputs"></a>出力

4 つの個別資産

資産 1

**表示名:** machine-89

**修飾名:** `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`

資産 2

**表示名:** machine-89

**修飾名:** `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`

資産 3

**表示名:** machine-89

**修飾名:** `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/02-01-2020/22:33:22-001.avro`

資産 4

**表示名:** machine-90

**修飾名:** `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

## <a name="next-steps"></a>次のステップ

[Azure Data Lake Gen2 ストレージ アカウントの登録とスキャン](register-scan-adls-gen2.md)から始めます。