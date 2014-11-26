<properties title="Use the sample datasets in Azure Machine Learning Studio" pageTitle="Use the sample datasets in Machine Learning Studio | Azure" description="Use the sample datasets in Azure Machine Learning Studio" metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye" />

# Azure Machine Learning Studio におけるサンプル データセットの使用

ML Studio で使用できるいくつかのサンプル データセットを紹介します。これらはパブリック ドメインで公開されている、標準の機械学習データセットです。

これらのデータセットを実験で使用することで、さまざまな分析向け機械学習モデルを ML Studio で試すことができます。

-   標準データセットのリストを ML Studio で表示するには、**[データセット]** タブをクリックします。各データセットについて、データセットの名前、提供者、簡単な説明などの情報が表示されます。

    データセットを並べ替えるには、任意の列ヘッダーをクリックします。たとえば **[提供者]** をクリックすると、Microsoft Corporation が提供したすべてのサンプル データセットをまとめることができます。自分やその他のユーザーがワークスペースでインポートしたデータセットを表示する場合も、この方法が簡単です。

-   実験でデータセットを使用するには、実験キャンバスの左側にあるモジュール パレットの **[保存されたデータセット]** セクションを展開するか、パレットの上にある検索ボックスに名前を入力して、目的のデータセットを検索します。キャンバスにドラッグすると、データセットが実験に追加されます。

## サンプル データセット

ML Studio の機械学習機能を検討する際に役立つデータセットの追加情報を以下の表にまとめました。各データセットについて以下の情報が記載されています。

-   データセットの名前と出典
-   データセットの用途と、そのデータセットが使用されている機械学習研究の例
-   データセットに含まれている重要な列や、その他の有用な属性の概要

いくつかの説明については、出典 (多くは [UC Irvine Machine Learning Repository][UC Irvine Machine Learning Repository]) で提供されているドキュメントを引用しています。その他、サンプルに対して追加で実施された調査や、サンプルに追加された変更について記載した説明もあります。

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">データセットの名前</th>
<th align="left">データセットの説明</th>
<th align="left">関連の研究</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">米国国勢調査局提供の、成人収入に関する二項分類データセット</td>
<td align="left"><p>調整後の所得指数が 100 を超える就労成人男性 (16 歳以上) を対象とした、米国国勢調査局のデータベース (1994 年) のサブセットです。</p>
<p><strong>用途:</strong> 人口統計データを使用して対象の人々を分類し、ある個人が 1 年間に 50,000 ドル以上の年収を得られるかどうかを予測します。</p></td>
<td align="left"><p>Kohavi, R.、Becker, B. (1996 年)。UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml" class="uri">http://archive.ics.uci.edu/ml</a>]。カリフォルニア大学、情報・コンピューター サイエンス学部 (カリフォルニア州アーバイン)</p></td>
</tr>
<tr class="even">
<td align="left"><p>自動車価格データ (生データ)</p></td>
<td align="left"><p>メーカー/モデル別にまとめた自動車の情報です。価格、シリンダー数、燃費、保険リスク スコアなどの情報が含まれます。</p>
<p>リスク スコアはまず車体価格に関連付けられた後、アクチュアリー (保険数理士) の間で<legacyitalic>シンボリング</legacyitalic>と呼ばれるプロセスによって、実際のリスクに対して調整されます。+3 は自動車のリスクが高く、-3 は非常に高い安全性が見込まれることを示しています。</p>
<p><strong>用途:</strong> 回帰または多変量分類を使用して、機能別のリスク スコアを予測します。</p></td>
<td align="left"><p>Schlimmer, J.C. (1987 年)。UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml" class="uri">http://archive.ics.uci.edu/ml</a>]。カリフォルニア大学、情報・コンピューター サイエンス学部 (カリフォルニア州アーバイン)</p></td>
</tr>
<tr class="odd">
<td align="left"><p>献血データ</p></td>
<td align="left"><p>台湾新竹市にある輸血サービス センターの献血者データベースから提供されたデータのサブセットです。</p>
<p>献血データには、最後の献血からの経過月数、頻度、献血の合計回数、最後の献血からの経過期間、献血量が含まれます。</p>
<p><strong>用途:</strong> 目標は、2007 年 3 月に献血者が献血を実施したかどうかの分類に基づいて予測を実行することです。1 は対象期間に献血をした人、0 はしなかった人を示しています。</p></td>
<td align="left"><list class="bullet"> <listitem>
<p>Yeh, I.C. (2008 年)。UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml" class="uri">http://archive.ics.uci.edu/ml</a>]。カリフォルニア大学、情報・コンピューター サイエンス学部 (カリフォルニア州アーバイン)</p>
</listitem> <listitem>
<p>Yeh, I-Cheng、Yang, King-Jang、Ting, Tao-Ming、「Knowledge discovery on RFM model using Bernoulli sequence」、Expert Systems with Applications、2008 年、[<a href="http://dx.doi.org/10.1016/j.eswa.2008.07.018" class="uri">http://dx.doi.org/10.1016/j.eswa.2008.07.018</a>]</p>
</listitem> </list></td>
</tr>
<tr class="even">
<td align="left"><p>乳がんデータ</p></td>
<td align="left"><p>機械学習に関する文献で頻繁に登場する腫瘍学研究所が提供した、3 つのがん関連データセットの 1 つです。診断情報と、研究所が分析した 300 個の細胞組織サンプルの特徴が組み合わせられています。</p>
<p><strong>用途:</strong> がんの種類を 9 つの属性 (線形、カテゴリなど) に基づいて分類します。</p></td>
<td align="left"><p>Wohlberg, W.H.、Street, W.N.、Mangasarian, O.L. (1995 年)。UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml" class="uri">http://archive.ics.uci.edu/ml</a>]。カリフォルニア大学、情報・コンピューター サイエンス学部 (カリフォルニア州アーバイン)</p></td>
</tr>
<tr class="odd">
<td align="left"><p>エネルギー効率回帰データ</p></td>
<td align="left"><p>12 種類の建物形状に基づいてシミュレーションされたエネルギー プロファイルのコレクションです。それぞれの建物は、ガラス部分、ガラス部分の配置、方向など、8 種類の特徴によって区別されています。</p>
<p><strong>用途:</strong> 回帰または分類を使用し、2 つの実数値のうち 1 つを応答としてエネルギー効率を評価します。マルチクラス分類の場合、応答変数が最も近い整数に丸められます。</p></td>
<td align="left"><p>Xifara, A.、Tsanas, A. (2012 年)。UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml" class="uri">http://archive.ics.uci.edu/ml</a>]。カリフォルニア大学、情報・コンピューター サイエンス学部 (カリフォルニア州アーバイン)</p></td>
</tr>
<tr class="even">
<td align="left"><p>定刻フライト実績 (生データ)</p></td>
<td align="left"><p>2011 年 10 月の米国における航空機のフライトの発着記録です。</p>
<p><strong>用途:</strong> フライトの遅延を予測します。</p></td>
<td align="left"><p>米国運輸省 [<a href="http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&amp;DB_Short_Name=On-Time" class="uri">http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&amp;DB_Short_Name=On-Time</a>]</p></td>
</tr>
<tr class="odd">
<td align="left"><p>森林火災データ</p></td>
<td align="left"><p>ポルトガル北東地域を対象とする、不快指数や風速などの気象データと森林火災の記録が含まれます。</p>
<p><strong>用途:</strong> 森林火災の被害地域を予測する回帰を実行するという、難しい作業となります。</p></td>
<td align="left"><list class="bullet"> <listitem>
<p>Cortez, P.、Morais, A. (2008 年)。UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml" class="uri">http://archive.ics.uci.edu/ml</a>]。カリフォルニア大学、情報・コンピューター サイエンス学部 (カリフォルニア州アーバイン)</p>
</listitem> <listitem>
<p>[Cortez and Morais、2007 年] P. Cortez、A. Morais. 「A Data Mining Approach to Predict Forest Fires using Meteorological Data」 In J. Neves、M. F. Santos、J. Machado Eds.「New Trends in Artificial Intelligence」、 Proceedings of the 13th EPIA 2007 - Portuguese Conference on Artificial Intelligence、 December、Guimarães、Portugal、pp. 512-523、2007 年。APPIA、ISBN-13 978-989-95618-0-9。公開元: [<a href="http://www.dsi.uminho.pt/~pcortez/fires.pdf" class="uri">http://www.dsi.uminho.pt/~pcortez/fires.pdf</a>]</p>
</listitem> </list></td>
</tr>
<tr class="even">
<td align="left"><p>あやめの 2 クラス データ</p></td>
<td align="left"><p>パターン認識の文献でおそらく最も多く使用されているデータベースです。3 種類のあやめの品種について 50 例の花弁を測定した、比較的小さなデータセットです。</p>
<p><strong>用途:</strong> あやめの品種を測定値から予測します。 <!-- I believe the following doesn't apply anymore, but I'm not sure so I'll remove it for now. One class is linearly separable from the other two; but the latter are not linearly separable from each other. --></p></td>
<td align="left"><p>Fisher, R.A. (1988 年)。UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml" class="uri">http://archive.ics.uci.edu/ml</a>]。カリフォルニア大学、情報・コンピューター サイエンス学部 (カリフォルニア州アーバイン)</p></td>
</tr>
<tr class="odd">
<td align="left"><p>さまざまな自動車の燃費データ</p></td>
<td align="left"><p>カーネギーメロン大学 StatLib 研究所が提供したデータセットを多少変更したものです。このデータセットは 1983 年に米国統計学会で使用された実績があります。</p>
<p>さまざまな車体の燃料消費量 (ガロンあたりのマイル数) と、シリンダー数、エンジン排気量、馬力、総重量、加速度などの情報が記載されています。</p>
<p><strong>用途:</strong> 3 つの多値離散属性と 5 つの連続属性に基づいて燃費効率を予測します。</p></td>
<td align="left"><p>カーネギーメロン大学 StatLib (1993 年)。UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml" class="uri">http://archive.ics.uci.edu/ml</a>]。カリフォルニア大学、情報・コンピューター サイエンス学部 (カリフォルニア州アーバイン)</p></td>
</tr>
<tr class="even">
<td align="left"><p>ピマ族の糖尿病に関する二項分類データセット</p></td>
<td align="left"><p>国立糖尿病・消化器・腎疾病研究所のデータベースから提供されたデータのサブセットです。データセットはフィルタリングされ、ピマ族の女性患者だけが対象とされています。データには、血糖値やインスリン値などの医療データと、生活習慣要因が含まれています。</p>
<p><strong>用途:</strong> 対象者が糖尿病にかかっているかどうかを予測します (二項分類)。</p></td>
<td align="left"><p>Sigillito, V. (1990 年)。UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml" class="uri">http://archive.ics.uci.edu/ml</a>]。カリフォルニア大学、情報・コンピューター サイエンス学部 (カリフォルニア州アーバイン)</p></td>
</tr>
<tr class="odd">
<td align="left"><p>レストラン顧客データ</p></td>
<td align="left"><p>人口統計データや嗜好など、顧客に関するメタデータのセットです。</p>
<p><strong>用途:</strong> このデータセットを、レストランに関する別のデータセット 2 つと組み合わせることで、レコメンダー システムの調整とテストを実施します。</p></td>
<td align="left"><p>Bache, K.、Lichman, M. (2013 年)。UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml" class="uri">http://archive.ics.uci.edu/ml</a>]。カリフォルニア大学、情報・コンピューター サイエンス学部 (カリフォルニア州アーバイン)</p></td>
</tr>
<tr class="even">
<td align="left"><p>レストラン特徴データ</p></td>
<td align="left"><p>料理の種類、食事スタイル、場所など、レストランとその特徴に関するメタデータのセットです。</p>
<p><strong>用途:</strong> このデータセットを、レストランに関する別のデータセット 2 つと組み合わせることで、レコメンダー システムの調整とテストを実施します。</p></td>
<td align="left"><p>Bache, K.、Lichman, M. (2013 年)。UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml" class="uri">http://archive.ics.uci.edu/ml</a>]。カリフォルニア大学、情報・コンピューター サイエンス学部 (カリフォルニア州アーバイン)</p></td>
</tr>
<tr class="odd">
<td align="left"><p>レストランの評価</p></td>
<td align="left"><p>ユーザーによるレストランの評価 (0 ～ 2) が含まれます。</p>
<p><strong>用途:</strong> このデータセットを、レストランに関する別のデータセット 2 つと組み合わせることで、レコメンダー システムの調整とテストを実施します。</p></td>
<td align="left"><p>Bache, K.、Lichman, M. (2013 年)。UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml" class="uri">http://archive.ics.uci.edu/ml</a>]。カリフォルニア大学、情報・コンピューター サイエンス学部 (カリフォルニア州アーバイン)</p></td>
</tr>
<tr class="even">
<td align="left"><p>鉄鋼のアニール処理に関するマルチクラス データセット</p></td>
<td align="left"><p>このデータセットには、鉄鋼のアニール処理試験に関する一連の記録が含まれています。幅、厚み、種類 (コイル、シートなど) など、処理後のさまざまな物理的属性も含まれます。</p>
<p><strong>用途:</strong> 2 つの数値クラス属性 (硬度と強度) のいずれかを予測します。属性間の相関関係を分析することも可能です。</p>
<p>鉄鋼のグレードは、SAE その他の機関が定義した標準に準拠します。特定の &quot;グレード&quot;(クラス変数) を検索して、必要な値を把握する必要があります。</p></td>
<td align="left"><p>Sterling, D.、Buntine, W. (年度なし)。UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml" class="uri">http://archive.ics.uci.edu/ml</a>]。カリフォルニア大学、情報・コンピューター サイエンス学部 (カリフォルニア州アーバイン)</p>
<p>鋼種に関する参考資料:</p>
<p>[<a href="http://www.outokumpu.com/SiteCollectionDocuments/Outokumpu-steel-grades-properties-global-standards.pdf">http://www.outokumpu.com/SiteCollectionDocuments/Outokumpu-steel-grades-properties-global-standards.pdf&gt;]</p></td>
</tr>
<tr class="odd">
<td align="left"><p>望遠鏡データ</p></td>
<td align="left"><p>高エネルギー ガンマ線バーストとバックグラウンド ノイズをモンテカルロ法でシミュレーションした記録です。</p>
<p>シミュレーションの目的は大気地上チェレンコフ ガンマ望遠鏡の精度向上です。期待する信号 (チェレンコフ放射) と上層大気の宇宙線から発生するバックグラウンド ノイズ (ハドロン放射) を、統計的手法によって区別します。</p>
<p>データを前処理することによって、長軸がカメラの中心を向く伸長クラスターが作成されています。この楕円のさまざまな特性は通常 Hillas パラメーターと呼ばれ、これらが画像のパラメーターに表れることによって区別が可能になります。</p>
<p><strong>用途:</strong> 放射の画像が信号とバックグラウンド ノイズのどちらを表しているかを予測します。</p>
<p><strong>注:</strong>バックグラウンド イベントを信号に分類することは、シグナル イベントをバックグラウンドに分類することよりも大きな悪影響を及ぼすため、シンプルな分類の正確度は、このデータの場合意味を持ちません。異なる分類器の比較には ROC グラフを使用する必要があります。バックグラウンド イベントを信号として許容する場合の確度は、次のしきい値のいずれを下回る必要があります: 0.01、0.02、0.05、0.1、0.2。</p>
<p>また、バックグラウンド イベントの数 (ハドロン放射の場合 h) は過小評価されますが、実際の測定では、h またはノイズ クラスがイベントの大部分を占めています。</p></td>
<td align="left"><p>Bock, R.K. (1995 年)。UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml" class="uri">http://archive.ics.uci.edu/ml</a>]。カリフォルニア大学、情報・コンピューター サイエンス学部 (カリフォルニア州アーバイン)</p></td>
</tr>
</tbody>
</table>

頻繁に使用するデータセットの種類の一覧は以下のとおりです。

| データ形式                                | 説明                                                                                                                                                                                                           |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| コンマ区切り値 (CSV)                      | あらゆるデータ プラットフォームに対応する、広く普及した交換形式です。ML Studio はこのデータを読み込み、組み込まれている列対応の型推測機能によって、各列のサンプリング データに基づいてメタデータを適用します。 |
| Attribute Relationship File Format (ARFF) | WEKA が定義する機械学習データ形式です。組み込まれているメタデータ (標準型、数値型、文字列型に対応) は、ML Studio のデータ テーブルに直接変換されます。                                                         |
| プレーン テキスト                         | プレーン テキストは、ダウンストリームの前処理モジュールを使用して読み込み、列に分割することができます。サポートされている主なプレーン テキスト形式は JSON です。                                               |
| DotNetTable                               | ML Studio のモジュール間で渡されるプライマリ コンテナー (データセット**) のシリアル化されたバージョンです。                                                                                                    |

  [UC Irvine Machine Learning Repository]: http://archive.ics.uci.edu/ml/ "UC Irvine Machine Learning Repository"
