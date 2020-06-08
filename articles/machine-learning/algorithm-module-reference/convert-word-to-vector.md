---
title: 単語からベクトルへの変換
titleSuffix: Azure Machine Learning
description: 提供された 3 つの Word2Vec モデルを使用して、テキストのコーパスからボキャブラリとそれに対応する単語の埋め込みを抽出する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/19/2020
ms.openlocfilehash: e0e796b75690bcacc6be8ef29b8b490c7faa40af
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2020
ms.locfileid: "83853654"
---
# <a name="convert-word-to-vector"></a>単語からベクトルへの変換

この記事では、Azure Machine Learning デザイナー (プレビュー) で **Convert word to Vector** モジュールを使用して、入力として指定したテキストのコーパスにさまざまな Word2Vec モデル (Word2Vec、FastText、Glove 事前トレーニング済みモデル) を適用し、単語の埋め込みを使用してボキャブラリを生成する方法について説明します。

このモジュールでは Gensim ライブラリを使用します。 Gensim の詳細については、[公式 Web サイト](https://radimrehurek.com/gensim/apiref.html)を参照してください。ここにはチュートリアルとアルゴリズムの説明があります。

### <a name="more-about-convert-word-to-vector"></a>Convert word to Vector の詳細

一般に、単語からベクトルへの変換 (単語のベクトル化) は、自然言語処理プロセスです。このプロセスでは、言語モデルまたは技法を使用して単語をベクトル空間にマップします。つまり、各単語を実数のベクトルで表現します。それと同時に、同様の意味を持つ単語が同様の表現を持つことを可能にします。

単語の埋め込みは、テキスト分類、感情分析などの NLP ダウンストリーム タスクの初期入力として使用できます。

このモジュールでは、さまざまな単語の埋め込みテクノロジの中から広く使用されている 3 つの方法を実装します。これには、2 つのオンライン トレーニング モデル (Word2Vec と FastText) と 1 つの事前トレーニング済みモデル (glove-wiki-gigaword-100) が含まれます。 オンライン トレーニング モデルは自分の入力データでトレーニングされているのに対し、事前トレーニング済みモデルは、オフラインで、通常、約 1,000 億個の単語が含まれるより大規模なテキスト コーパス (Wikipedia、Google News など) でトレーニングされています。単語の埋め込みは単語のベクトル化中は一定です。 事前トレーニング済みの単語モデルにより、トレーニング時間の短縮、単語ベクトルのエンコードの向上、全体的なパフォーマンスの向上などの利点がもたらされます。

+ Word2Vec は、浅いニューラルネットワークを使用して単語の埋め込みを学習するための最も一般的な手法の 1 つです。理論は次の論文で説明されています。この論文は PDF でダウンロードできます。[Efficient Estimation of Word Representations in Vector Space, Mikolov, Tomas, et al](https://arxiv.org/pdf/1301.3781.pdf).このモジュールでの実装は、[Word2Vec 用の gensim ライブラリ](https://radimrehurek.com/gensim/models/word2vec.html)に基づいています。

+ FastText 理論について説明している次の論文を PDF でダウンロードできます:[Enriching Word Vectors with Subword Information, Bojanowski, Piotr, et al](https://arxiv.org/pdf/1607.04606.pdf).このモジュールでの実装は、[FastText 用の gensim ライブラリ](https://radimrehurek.com/gensim/models/fasttext.html)に基づいています。

+ Glove 事前トレーニング済みモデル (glove-wiki-gigaword-100) は、Wikipedia のテキスト コーパスに基づく事前トレーニング済みのベクトルのコレクションです。これには、56 億個のトークンと 40 万個の小文字化したボキャブラリが含まれています。この PDF は次で入手できます。[GloVe:Global Vectors for Word Representation](https://nlp.stanford.edu/pubs/glove.pdf).

## <a name="how-to-configure-convert-word-to-vector"></a>Convert word to Vector の構成方法

このモジュールには、テキスト列を含むデータセットが必要です。前処理されたテキストの方が適しています。

1. **Convert word to Vector** モジュールをパイプラインに追加します。

2. モジュールの入力として、1 つまたは複数のテキスト列を含むデータセットを指定します。

3. **[Target column]\(ターゲット列\)** で、処理するテキストが含まれている列を 1 つだけ選択します。

    一般に、このモジュールではテキストからボキャブラリが作成されるため、列ごとに内容が異なっており、それが異なるボキャブラリの内容につながります。そのため、モジュールでは 1 つのターゲット列のみを受け入れます。

4. **Word2Vec 戦略**については、`GloVe pretrained English Model`、`Gensim Word2Vec`、`Gensim FastText` から選択します。

5. **Word2Vec 戦略**が `Gensim Word2Vec` または `Gensim FastText` の場合:

    + **Word2Vec トレーニング アルゴリズム**。 `Skip_gram` と `CBOW` から選択します。 違いは、元の[論文](https://arxiv.org/pdf/1301.3781.pdf)で説明されています。

        既定のメソッドは `Skip_gram` です。

    + **単語の埋め込みの長さ**。 単語ベクトルの次元を指定します。 gensim の `size` パラメーターに対応します。

        既定の embedding_size は 100 です。

    + **コンテキスト ウィンドウのサイズ**。 予測される単語と現在の単語の間の最大距離を指定します。 gensim の `window` パラメーターに対応します。

        既定のウィンドウ サイズは 5 です。

    + **エポックの数**。 コーパス上のエポック (イテレーション) の数を指定します。 gensim の `iter` パラメーターに対応します。

        既定のエポック数は 5 です。

6. **ボキャブラリの最大サイズ**には、生成されたボキャブラリに含まれる単語の最大数を指定します。

    一意の単語がこの値よりも多い場合は、頻度の低いものが取り除かれます。

    ボキャブラリの既定のサイズは 1 万です。

7. **最小ワード カウント**には、最小ワード カウントを指定します。これにより、この値よりも頻度が低いすべての単語がモジュールで無視されるようになります。

    既定値は 5 です。

8. パイプラインを送信します。

## <a name="examples"></a>例

モジュールには次の 1 つの出力があります。

+ **埋め込みを含むボキャブラリ**:生成されたボキャブラリと、各単語の埋め込みを格納します。1 つの次元が 1 つの列を占有します。

### <a name="result-examples"></a>結果の例

**Convert word to Vector** モジュールがどのように機能するかを示すため、次の例では、Azure Machine Learning (プレビュー) で提供される前処理された Wikipedia SP 500 データセットに、既定の設定でこのモジュールを適用しています。

#### <a name="source-dataset"></a>ソース データセット

データセットには、カテゴリ列と、Wikipedia からフェッチされたフルテキストが含まれています。 代表的な例のほんの一部を次の表に示します。

|text|
|----------|
|nasdaq 100 component s p 500 component foundation founder location city apple campus 1 infinite loop street infinite loop cupertino california cupertino california location country united states...|
|br nasdaq 100 nasdaq 100 component br s p 500 s p 500 component industry computer software foundation br founder charles geschke br john warnock location adobe systems...|
|s p 500 s p 500 component industry automotive industry automotive predecessor general motors corporation 1908 2009 successor...|
|s p 500 s p 500 component industry conglomerate company conglomerate foundation founder location city fairfield connecticut fairfield connecticut location country usa area...|
|br s p 500 s p 500 component foundation 1903 founder william s harley br arthur davidson harley davidson founder arthur davidson br walter davidson br william a davidson location...|

#### <a name="output-vocabulary-with-embeddings"></a>埋め込みを含むボキャブラリの出力

次の表には、入力として Wikipedia SP 500 データセットを使用するこのモジュールの出力が含まれています。 左端の列はボキャブラリを示し、その埋め込みベクトルは同じ行の残りの列の値によって表されます。

|ボキャブラリ|埋め込み次元 0|埋め込み次元 1|埋め込み次元 2|埋め込み次元 3|埋め込み次元 4|埋め込み次元 5|...|埋め込み次元 99|
|-------------|-------------|-------------|-------------|-------------|-------------|-------------|-------------|-------------|
|nasdaq|-0.375865|0.609234|0.812797|-0.002236|0.319071|-0.591986|...|0.364276
|コンポーネント|0.081302|0.40001|0.121803|0.108181|0.043651|-0.091452|...|0.636587
|s|-0.34355|-0.037092|-0.012167|0.151542|0.601019|0.084501|...|0.149419
|p|-0.133407|0.073244|0.170396|0.326706|0.213463|-0.700355|...|0.530901
foundation|-0.166819|0.10883|-0.07933|-0.073753|0.262137|0.045725|...|0.27487
founder|-0.297408|0.493067|0.316709|-0.031651|0.455416|-0.284208|...|0.22798
location|-0.375213|0.461229|0.310698|0.213465|0.200092|0.314288|...|0.14228
city|-0.460828|0.505516|-0.074294|-0.00639|0.116545|0.494368|...|-0.2403
apple|0.05779|0.672657|0.597267|-0.898889|0.099901|0.11833|...|0.4636
campus|-0.281835|0.29312|0.106966|-0.031385|0.100777|-0.061452|...|0.05978
infinite|-0.263074|0.245753|0.07058|-0.164666|0.162857|-0.027345|...|-0.0525
loop|-0.391421|0.52366|0.141503|-0.105423|0.084503|-0.018424|...|-0.0521

この例では、**Word2Vec 戦略**として既定の `Gensim Word2Vec` を使用し、**トレーニング アルゴリズム**を `Skip-gram`、**単語の埋め込みの長さ**を 100 にしたため、100 の埋め込み列があります。

## <a name="technical-notes"></a>テクニカル ノート

このセクションには、ヒント、よく寄せられる質問への回答が含まれています。

+ オンライン トレーニング モデルと事前トレーニング済みモデルの違い

    この **Convert word to Vector モジュール**では、3 つの異なる戦略、2 つのオンライン トレーニング モデル、および 1 つの事前トレーニング済みモデルを提供しました。 オンライン トレーニング モデルが、トレーニング データとして入力データセットを使用し、トレーニング中にボキャブラリと単語ベクトルを生成するのに対し、事前トレーニング済みモデルは、Wikipedia や Twitter テキストなどのより大規模なテキスト コーパスによって既にトレーニングされています。したがって、事前トレーニング済みモデルは、実際には (単語、埋め込み) ペアのコレクションです。  

    Glove 事前トレーニング済みモデルが単語ベクトル化戦略として選択されている場合は、入力データセットからボキャブラリを要約し、事前トレーニング済みのモデルから各単語の埋め込みベクトルを生成します。オンライン トレーニングは行われません。事前トレーニング済みモデルを使用すると、トレーニング時間が短縮され、特に入力データセットのサイズが比較的小さい場合にパフォーマンスが向上します。

+ 埋め込みサイズ

    一般に、高いパフォーマンスを実現するために、単語の埋め込みの長さは数百 (100、200、300 など) に設定されています。これは、埋め込みサイズが小さいと、ベクトル空間が小さくなり、単語埋め込みの衝突が発生する可能性があるからです。  

    事前トレーニング済みモデルでは、単語埋め込みの長さが固定されています。この実装では、glove-wiki-gigaword-100 の埋め込みサイズは 100 です。


## <a name="next-steps"></a>次のステップ

Azure Machine Learning で[使用できる一連のモジュール](module-reference.md)を参照してください。 

デザイナー (プレビュー) モジュール固有のエラーの一覧については、[Machine Learning のエラー コード](designer-error-codes.md)を参照してください。
