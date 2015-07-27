<properties 
	pageTitle="Machine Learning アルゴリズムの選択方法 | Microsoft Azure" 
	description="クラスタリング、分類、または回帰の実験で教師あり学習と教師なし学習用の Azure Machine Learning のアルゴリズムを選択する方法" 
	services="machine-learning"
	documentationCenter="" 
	authors="garyericson" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/01/2015" 
	ms.author="bradsev;garye" />


# Azure Machine Learning のクラスタリング、分類、回帰用アルゴリズムを選択する方法

このトピックでは、Machine Learning のアプローチのいくつかの基本的な側面について説明します。具体的には、特定の種類のデータを分析するのに適切な Machine Learning アルゴリズムを選択する方法、提起された質問に回答する方法、指定されたタスクを達成する方法、または意思決定のために条件を提供する方法について説明します。

> [AZURE.TIP][Microsoft Azure Machine Learning のアルゴリズム チート シート](machine-learning-algorithm-cheat-sheet.md)は、この記事に付属する便利なリファレンスです。

Machine Learning を使用して分析を行う場合、しばしば次の 2 つの質問に直面します。

* 利用可能なデータを使用して目標を達成するためにはどのような種類の分析が必要か? 
* この分析を行うのに最適なアルゴリズムまたはモデルは何か?

次の 3 種類の Machine Learning 分析について説明し、そのユース ケースを比較します。

* **クラスタリング**
* **分類** 
* **回帰** 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


<a name="anchor-1"></a>
## データから学習する Machine Learning のアルゴリズム

Machine Learning は、データから学習するように設計されていて、事前に定義された特定のモデルにデータに対するテストを課すことはしない、という種類のアルゴリズムを研究する学問分野です。考え方としては、データセットに含まれるパターンを帰納的に調査する手法によって知識を得ようとするものであり、まずデータセット全体に当てはまる適切なモデルを推測してからそれを経験的に試験していく*仮説演繹法*は使いません。

データから学習とも考えられる Machine Learning の種類には、*教師あり学習*と*教師なし学習*という 2 種類があります。

<a name="anchor-2"></a>
## 教師あり学習  

教師あり学習では、ターゲット変数が明確に定義されていることと、その変数の値が十分に多く与えられていることが必要です。

教師あり学習は、入力するトレーニング インスタンスに対する正しい出力結果 (またはターゲット変数) がわかっている場合に行われる機械学習の種類です。Machine Learning アルゴリズムをトレーニングする目的は、入力を既知の出力値にマップするモデル (つまり、ルールまたは関数) を見つけることです。これは、アルゴリズム エージェントが入力を出力に正しくマッピングしているかどうかをそのエージェントに教えることのできる教師がいることに似ています。学習プロセスが完了し、実行可能なモデルが得られたら、新しい入力データにそのモデルを適用することによって、予想される出力を予測することができます (この場合、トレーニング用のデータセットとは異なり、ターゲット値は事前にはわかりません)。

モデルの種類は、ターゲット変数の性質によって決まります。

![Diagram of supervised learning: Model from labeled data and use it to predict outcomes from new data.](./media/machine-learning-algorithm-choice/supervised-learning-using-known-data-to-model-solution.png)

教師あり学習を利用する分析には、大きく分けて、*分類*と*回帰*という 2 つのカテゴリがあります。分類問題の場合は、教師あり学習になるのがごく一般的です。私たちが作成した分類システムについてコンピューターに学習させることが目的だからです。通常、応答は少数の既知の値 (ラベル) になります。たとえば、'true' または 'false' や 'high'、'medium'、または 'low' です。分類アルゴリズムは、順序応答値ではなく、名目値に適用されます。数字の認識は分類の学習の一般的な例です。さらに一般的に言うと、分類の学習は、分類の判別が便利で簡単なあらゆる問題に適しています。

教師あり学習では、調査する変数を説明変数 (予測変数ともいう) と従属変数 (応答変数ともいう) という 2 つのグループに分けることができます。分析の目的は、*回帰分析*で行われるのと同様に、説明変数と従属変数の関係を定義することです。従属変数の値は、データセットの十分に広範囲の部分について既知である必要があります。回帰では、応答変数または出力変数は、特定の自動車のガロンあたりのマイル数や、人の年齢など、連続する数値をとります。

さらに、教師あり学習は、ニューラル ネットワークやデシジョン ツリーをトレーニングする手法としても最も一般的です。

> これらの手法はどちらも、あらかじめ決められた分類によって提供される情報に大きく依存します。ニューラル ネットワークの場合、分類は、ネットワークの誤差を特定し、その誤差を最小限に抑えるようにネットワークを調整するために使用されます。デシジョン ツリーの場合、分類は、分類パズルを解くために使用できる情報を最も多く提供する属性を判別するために使用されます。これらの例はどちらも、あらかじめ決められた分類として提供される情報に依存しているという意味で "教師" を成長させます。

>  隠れマルコフ モデルおよびベイジアン ネットワークを使用する音声認識も、通常どおりに特定の入力に対する誤差を最小限に押さえるようにパラメーターを調整するために "教師" 要素に依存します。[「[Machine Learning, Part II: Supervised and Unsupervised Learning](http://www.aihorizon.com/essays/generalai/supervised_unsupervised_machine_learning.htm)」、[AI Horizon](http://www.aihorizon.com/)]


<a name="anchor-3"></a>
##教師なし学習

Machine Learning において、教師なし学習の問題は、ラベルの付いていないデータでパターンまたは隠れた構造があるかどうかを検索することです。トレーニングするデータセットについて "正しい結果" はモデルに提供されません。学習者に提供される例にはラベルが付いていないため、可能性のある解決策を評価するためのフィードバック (誤差と報酬) は存在しません。目標は、私たちがそのタスクの達成方法をコンピューターに明示的に教えなくてもコンピューターに何かの方法を学び取ってもらうことです。教師なし学習の場合、すべての変数が同じ方法で処理されます。説明変数と従属変数の区別はありません。ただし、データ整理のような汎用性の高い目標や、クラスターの検出のようなもう少し具体的な目標など、達成すべき目標がまだあります。

Azure Machine Learning では、教師あり学習と教師なし学習の両方を、**クラスタリング**、**分類**、**回帰**を使用して実行できます。

   ![screenshot_of_experiment](./media/machine-learning-algorithm-choice/help2.png)

<a name="anchor-4"></a>
##クラスタリング
クラスタリングは、教師なし学習の一例です。この種類の学習の目的は、トレーニング データ内の類似点を見つけ、データセットをこれらの類似点で区分したサブセットに分割することです。これらのデータ ドリブンの手順によって発見される最重要なクラスターは、私たちの直感的な分類と一致しているだろうという期待は、(必ずではありませんが) 多くの場合、満たされます。

クラスタリング アルゴリズムによってこれらのクラスターに適切な名前が割り当てられることはありませんが、クラスターを生成し、それらのクラスターを使用して新しい例を最も適切なクラスターに分類することにより、新しい例に期待される類似点を予想できます。このデータドリブンのアプローチは、十分なデータを使用できる場合に適切に機能します。たとえば、Amazon.com でお勧めの書籍を表示するために使用されるようなソーシャル情報のフィルタリング アルゴリズムは、顧客から類似したグループを検出し、新しいユーザーをこれらのグループに割り当てることにより、お勧め情報を提供するという目的を果たします。

Azure Machine Learning で使用可能なクラスタリング アルゴリズムは、[K-Means クラスタリング][k-means-clustering]です。

![K-Means clustering algorithm exeperiment: Screenshot](./media/machine-learning-algorithm-choice/k-means-clustering-algorithm-menu.png)

K-Means は、既知のクラスタリングの問題を解決する最も簡単な教師なしのクラスタリング アルゴリズムの 1 つです。K-Means アルゴリズムによるデータのクラスタリングでは、分散が等しい N 個のグループにサンプルを分け、"慣性" または "クラスター内の平方和" 条件を最小にしようとします。このアルゴリズムでは、クラスターの数を指定する必要があります。K-means は、多数のサンプルにも拡大して対応できる手法であり、これまで、多くの異なる学問分野のさまざまな適用例で使用されてきました。

[K-Means クラスタリング][k-means-clustering] アルゴリズムのモジュールからは未トレーニングの [K-Means クラスタリング モデル][train-clustering-model]が返され、そのモデルを トレーニング クラスタリング モデル モジュールに渡せばトレーニングできます。

![screenshot_of_experiment](./media/machine-learning-algorithm-choice/k4.png)

この図は、K-Means クラスタリングを使用するときに構成するオプションを示しています。K-Means 手法では、D 次元のデータ ポイントのセットから指定された数のクラスターを検索します。この手法では、Lloyd のアルゴリズムを使用し、*K 重心の初期セット*から始めて、重心の位置を繰り返し絞り込みます。このアルゴリズムは、重心が安定したとき、または*指定された数のイテレーション*が完了したときに終了します。このモジュールでは、K × D の配列を N 個のデータ点から見つかった K 個のクラスターを定義する最終的な重心で初期化します。また、このアルゴリズムでは、各データ ポイントから K 個のクラスターの 1 つまでを割り当てた長さ N のベクトルも使用します。検出する特定の数のクラスター (K) を指定した場合、モジュールによって K クラスターのために最初の K 個のデータ ポイントが割り当てられます。


また、このモジュールでは、初期クラスター構成を定義する初期ポイントの受け入れまたは生成も可能です。*メトリック*では、データ ポイントと重心の間の距離を測定するために使用する方式を定義します。各データ ポイントは、そのデータ ポイントに最も近い重心を持つクラスターには割り当てられます。既定では、*ユークリッド メトリック*が使用されます。別のメトリックとして、*コサイン メトリック*を指定することもできます。K-Means 手法では、データセットに対してローカルに最適なクラスター構成のみしか見つからない可能性があることに注意してください。また、異なる初期構成を指定すると、異なる (そしておそらく、さらに優れた) 構成が見つかることがあります。

<a name="anchor-5"></a>
##分類 
分類分析では、サンプルを複数のクラスに分割します。また、トレーニング済みのデータとして、事前にラベルを付けた一連のデータが使用されます。この手法は、データ インスタンスがどのグループに属すかを予測するために使用されます。Azure Machine Learning では、以下の分類アルゴリズムを使用できます。

![screenshot_of_experiment](./media/machine-learning-algorithm-choice/help3.png)

*2 クラス アルゴリズム*は二項応答変数 (yes または no、0 または 1、true または false など) に使用し、*多クラス アルゴリズム*はインスタンスを 3 つ以上のクラスに分類する任意の名義応答変数に使用します。

### 分類アルゴリズムを選択するためのガイドライン
アルゴリズムのこの長いリストを見ると、次の一連の質問が提起されます。

* これら多くの分類モデルのうち特定のデータセットで使用するのに最適なものはどうすればわかるか? 
* いずれかの分類モデルが "自然な" 選択であるというケースがあるか? 
* それを選択する原理は何か?

推奨される方法の 1 つは、いくつかの異なる分類モデルを、各アルゴリズムに異なるパラメーターを設定して試験的に実行した後、クロス検証を使用して最適なものを 1 つ選択することです。

> [AZURE.TIP] [Azure Machine Learning Studio](https://studio.azureml.net/) を使用すると、同じデータに対して複数のアルゴリズムを同時に実行し、結果を比較することができます。ここでは、[Azure Machine Learning ギャラリー](http://gallery.azureml.net/)にある[多クラス分類モデルの比較の文字認識](http://gallery.azureml.net/Details/a635502fc98b402a890efe21cec65b92)の例を使用します。

この検討の出発点となる一般的なガイドラインを次に示します。使用するアルゴリズムを選択する際は、次の点について検討してください。[概要は「[Choosing a Machine Learning Classifier](http://blog.echen.me/2011/04/27/choosing-a-machine-learning-classifier/)」(Edwin Chen) を参照]

**トレーニング データの大きさはどの程度か?** トレーニング セットが小さい場合に、教師ありで分類モデルをトレーニングするには、Machine Learning の原則に忠実に高バイアス/低分散 (ナイーブ ベイズなど) を使用する必要があります。これらの分類モデルは、kNN などの低バイアス/高分散の分類モデルよりも過剰適合が発生しにくいため、メリットがあります。ただし、トレーニング セットが大きくなると、低バイアス/高分散の分類モデルがより適切になります。これは漸近誤差が小さくなるためです。このような場合、高バイアスの分類モデルは正確なモデルを提供するほど強力ではありません。このような状況にはナイーブ ベイズが適していることを示す理論上および経験上の結果が出ています。しかし、適切なデータと優れた機能を使用すると、通常は、適切なアルゴリズムを使用する場合よりも利点が多いことに注意してください。また、非常に大規模なデータセットがある場合、分類のパフォーマンスは、使用するアルゴリズムの影響をそれほど受けない可能性があります。その場合は、アルゴリズムは、拡張性、速度、使いやすさなどに基づいて選択することをお勧めします。

**段階的にトレーニングする必要があるか、それともバッチ モードでトレーニングする必要があるか?** 大量のデータがある場合またはデータが頻繁に更新される場合は、更新に適したベイジアン アルゴリズムを使用できます。トレーニング データをバッチ モードで処理する場合は、ニューラル ネットワークと SVM の両方が適切に機能します。

**データはカテゴリ別に分類されるか、数値を表しているか、またはその両方の組み合わせか?** ベイジアンは、カテゴリ別のデータまたは二項データに対して最も適切に機能します。デシジョン ツリーは、数値を予測できません。

**自分または対象ユーザーが分類モデルのしくみを理解する必要があるか?** ベイジアンまたはデシジョン ツリーはより簡単に説明できます。ニュートラル ネットワークと SVM によるデータ分類のしくみを確認または説明することはますます難しくなっています。

**どれほど速く分類を生成する必要があるか?** デシジョン ツリーは、ツリーが複雑な場合に処理速度が低下することがあります。一方、SVM による分類は処理が速くなります。これは、データが "直線" のどちら側にあるかを判別するだけだからです。

**問題が提示している複雑さ、または必要とする複雑さはどの程度か?** ニューラル ネットワークおよび SVM は複雑な非線形の分類を処理できます。

### 分類アルゴリズムの長所と短所
これらの各分類アルゴリズムには、いくつかの長所と短所があります。

<a name="anchor-5a"></a> **ロジスティック回帰の長所と短所:** ロジスティック回帰分析は、ある結果になる可能性を、その結果にならない可能性で割った比率として計算する結果の確率 (オッズ) に基づいています。[「[Logistic Regression and Linear Discriminant Analyses in Evaluating Factors Associated with Asthma Prevalence among 10- to 12-Years-Old Children: Divergence and Similarity of the Two Statistical Methods](http://www.hindawi.com/journals/ijpedi/2009/952042/)」、George Antonogeorgos 他 (International Journal of Pediatrics、2009 年) Article ID 952042]
 
ロジスティック モデルは、パラメトリックであるため、応答変数に対する各予測変数の影響について洞察が得られるという利点があります。


自然な確率論的解釈を利用できる (その点でデシジョン ツリーや SVM とは異なる) ため、簡単にモデルを更新して新しいデータを取り入れることができます。モデルを正規化する方法は多数あるため、ナイーブ ベイズとは異なり、相互に関連付ける特徴についてそれほど心配する必要はありません。ロジスティック回帰が役立つのは、次の場合です。

* 分類のしきい値を調整するための確率論的フレームワークが必要な場合
* 追加のトレーニング データをすばやく取り入れる場合  

ロジスティック回帰は、高次元データの場合に、デシジョン ツリーより適切に機能します。たとえば、テキストの分類で、10 万個のドキュメントに 50 万個の別個の単語 (特徴) が入っているとします。この場合は、線形の超平面の学習のような単純なルールが適しています。デシジョン ツリーは自由度が多すぎて、過剰適合を起こしやすいためです。テキスト データに対してデシジョン ツリーを使用して特徴を選択することは可能ですが、取得する特徴のサブセット数がはるかに少ない場合は、テキスト分類の貴重な情報の多くが失われます。高次元のデータに対して学習モデルを使用すると、分散に基づく誤差は簡単に大きくなります。その場合は、バイアスが高いほど、単純なモデルを使用することをお勧めします。

ロジスティック回帰の 1 つの欠点は、1 つの予測変数で大部分の応答変数の説明ができる場合に、その変数の係数が非常に大きくなり、安定性に欠けることです。

<a name="anchor-5b"></a> **ディシジョン ツリーの長所と短所:** [デシジョン ツリー](http://research.microsoft.com/pubs/155552/decisionForests_MSR_TR_2011_114.pdf)は、解釈と説明が容易です。

> 特徴の相互作用を簡単に処理することができ、パラメトリックではないため、外れ値や、データが直線的に分離可能かどうかについて心配する必要がありません (たとえば、デシジョン ツリーでは、ある特徴 x の下限にクラス A があり、特徴 x の中程度の位置にクラス B があり、特徴 x の上限に再びクラス A があるようなケースを簡単に処理できます)。1 つの欠点として、デシジョン ツリーはオンライン学習をサポートしないため、新しい例が提供されたときにツリーを再構築する必要があります。別の欠点として過剰適合を起こしやすいことが挙げられますが、ランダム フォレスト (またはブーストしたツリー) のようなアンサンブル手法が用意されています。さらに、ランダム フォレストは、分類における数多くの問題に対して多くの場合に勝ちを収める手法でもあります (通常は SVM より若干勝ると考えられます)。高速で、拡張性が高いため、SVM のように大量のパラメーターをチューニングするという心配がありません。[「[Choosing a Machine Learning Classifier](http://blog.echen.me/2011/04/27/choosing-a-machine-learning-classifier/)」、Edwin Chen]

デシジョン ツリーでは、*サポート*、*信頼度*、*リフト*などのメトリックと共に、規則として出力が生成されます。


<a name="anchor-5c"></a> **SVM の長所と短所:** SVM （Support Vector Machine） は、高次元空間の場合に効果を発揮します。次元の数がサンプル数より多いようなケースにも有効です。ただし、特徴の数がサンプル数より大幅に多い場合、この手法はパフォーマンスが低下する傾向があります。また、決定関数でトレーニング ポイントのサブセット (サポート ベクトルと呼ばれる) を使用するため、メモリの使用効率が高くなっています。さらに、汎用性が非常に高いため、決定関数には、一般的な関数でもカスタム関数でも、さまざまなカーネル関数を指定できます。カーネル関数は、低次元のトレーニング サンプルの次元を高める変換に使用され、線形の可分性問題に役立ちます。

ただし、SVM は確率の推定を直接には提供しておらず、負荷の大きい 5 フォールドのクロス検証を使用して計算されます。

>高精度で、過剰適合についてしっかりした理論的な保証があり、適切なカーネルを使用できるため、基本特徴空間でデータが直線的に分離可能でない場合でも処理できます。非常に高次元の空間がノルムであるテキスト分類の問題で、特によく使用される手法です。[「[Choosing a Machine Learning Classifier](http://blog.echen.me/2011/04/27/choosing-a-machine-learning-classifier/)」、Edwin Chen]

フォレストとは異なり、SVM は二項分類モデルに由来します。しかし、ここ最近、複数のクラスを処理できるように手が加えられてきました。"1 つと残り" のようなトレーニングを使用して、複数クラスの分類モデルを作成するために使用できます (最適化の程度は劣ることがあります)。SVM は 2 クラスの出力 (つまり、種類が 2 つのカテゴリ出力変数) しか処理できないため、N 個のクラスがある場合は、N 個の SVM で学習します (SVM 1 で "Output==1" と "Output != 1" を学習し、SVM 2 で "Output==2" と "Output != 2" を学習し、…、SVM N で "Output==N" と "Output != N" を学習します)。その後、新しい入力に対する出力を予測するには、各 SVM で予測を行い、正の領域に最も遠くまで入り込んでいる予測値を見つけるだけです。[「[Support Vector Machines](http://www.astro.caltech.edu/~george/aybi199/AMooreTutorials/svm.ppt)」、Andrew W. Moore (カーネギーメロン大学、2001 年)]

<a name="anchor-5d"></a> **ナイーブ ベイズの長所と短所:** [ナイーブ ベイズ (NB)](http://www.aaai.org/Papers/FLAIRS/2004/Flairs04-097.pdf) 分類モデルは、分類問題でよく使用される選択肢です。この分類モデルでは、特徴はそれぞれ独立していると仮定します (これが "ナイーブ" という手法の名称の由来です)。

> NB 条件の独立性という前提条件が実際に保持されれば、ナイーブ ベイズ分類モデルは、ロジスティック回帰のように識別性の高いモデルよりも速く収束します。そのため、必要なトレーニング データは少なくなります。もし NB 分類モデルの前提条件が保持されなくても、多くの場合、実際には NB 分類モデルは十分に機能します。NB の主な短所は、それぞれの特徴間の相互作用について学習できないことです (たとえば、ブラッド ピットが出演する映画とトム クルーズが出演する映画はどちらも好きだが、この 2 人が共演する映画は嫌いだというケースを学習できません)。[「[Choosing a Machine Learning Classifier](http://blog.echen.me/2011/04/27/choosing-a-machine-learning-classifier/)」、Edwin Chen]


<a name="anchor-5e"></a> **:** "一対全" は、複数クラスの分類の問題を、複数の二項分類の問題のセットにする方法です。この方法には、クラスごとに 1 つの分類モデルのトレーニングが含まれます。このトレーニングでは、そのクラスのサンプルは陽性で、それ以外のすべてのサンプルが陰性です。この方法には、クラス ラベルだけではなく、意思決定の実際の値に対する信頼スコアを生成する基本の分類モデルが必要になります。1 つのサンプルに対して複数のクラスが予測された場合は、離散的なクラス ラベルだけでは結果があいまいになる可能性があります。[[多クラス分類](http://en.wikipedia.org/wiki/Multiclass_classification) (Wikipedia 2006)]


<a name="anchor-6"></a>
##回帰
 
回帰分析では、過去の推定に基づいて新しい値を予測します。従属変数に対する新しい値が、1 つ以上の測定された属性値に基づいて計算されます。Azure Machine Learning で使用できる回帰アルゴリズムは次のとおりです。

![screenshot_of_experiment](./media/machine-learning-algorithm-choice/help4.png)

ユース ケースと入手可能なデータに応じて、複数の中から 1 つのアルゴリズムを選択します。この後、いくつかの回帰アルゴリズムとその主要なユース ケースについて説明します。

<a name="anchor-6b"></a> **[ベイジアン線形回帰][bayesian-linear-regression]** ベイジアン線形回帰は、ベイジアン推論のコンテキスト内で統計分析が実行される線形回帰の方法です。回帰モデルの誤差が正規分布していて、特定の形式の事前分布を想定できる場合は、モデルのパラメーターの事後確率分布に対して明示的な結果を利用できます。[[線形回帰](http://en.wikipedia.org/wiki/Bayesian_linear_regression) ([Wikipedia](http://en.wikipedia.org))]

<a name="anchor-6f"></a> **[ブーストしたデシジョン ツリー回帰][boosted-decision-tree-regression]** ブーストしたデシジョン ツリー回帰では、予測変数と応答変数の間の関係を計算します。回帰ツリーの構造は、分類ツリーに似ています。終端ノードが予測関数 (モデル) の値です。予測値は、終端ノードの値に限定されます。デシジョン ツリーを使用することの利点としては、たとえば、次のような点があります。

* 判断の規則を解釈しやすいモデルです。 
* ノンパラメトリックであるため、さまざまな数値データ レイヤーまたはカテゴリ別データ レイヤーを簡単に使用できます。単峰型のトレーニング データは必要ありません。
* トレーニング データ内に外れ値があっても、信頼性が低下しません。 
* いったん規則を作成すると、分類をすばやく実行できます。 

ただし、デシジョン ツリーの使用にはいくつか欠点があります。

* トレーニング データが過剰適合される傾向があるため、データ セット全体に適用したときの結果が不正確になる。
* トレーニング データ内の応答変数の最小値と最大値の範囲を超えた予測をできない。


<a name="anchor-6g"></a> **[デシジョン フォレスト回帰][decision-forest-regression]** デシジョン フォレストは、分類 (カテゴリ変数) または回帰 (連続的な変数) の応用分野で使用できます。回帰フォレストは、従属変数に独立した入力を指定する非線形回帰で使用できます。また、入力と出力の両方を多次元にできます。回帰フォレストは、分類フォレストほど多くは使用されません。主な違いは、入力データに関連付けられたデシジョン フォレストの出力ラベル (およびトレーニング ラベル) を連続値にする必要があることです。つまり、目標関数をそれに合わせて調整する必要があります。回帰フォレストは、効率性や柔軟性など、分類フォレストの利点の多くを共有しています。

<a name="anchor-6a"></a> **[線形回帰][linear-regression]** 線形回帰は、スカラー従属変数 Y と 1 つ以上の説明変数 X の間の関係をモデリングするために広く使用されます。通常は、予想、予測、または換算に適用されます。Y および X の値の観察されたデータセットに予測モデルに適合させるために使用できます。線形回帰は Y の基になる構造が X 変数の線形の組み合わせであることを前提としています。この場合、付随する Y 値のない X 値に対して線形回帰モデルを適用すると、その Y 値を予測することがきます。線形回帰モデルは通常、最小二乗法のアプローチを使用してモデルを適合させますが、最もよく適合するものを測定する他のオプションも用意されています。[[線形回帰](http://en.wikipedia.org/wiki/Bayesian_linear_regression) ([Wikipedia](http://en.wikipedia.org))]

<a name="anchor-6c"></a> **[ニューラル ネットワーク回帰][neural-network-regression]** ニューラル ネットワークは、ノンパラメトリック回帰に便利な統計ツールです。ノンパラメトリック回帰で対処する問題は、変数 Y に対する一連の説明変数 X1; : : : ;Xp に適合するモデルを探すことであり、X と Y の間の関係が単純な線形関係よりも複雑であるケースに対応できます。[「[A Framework for Nonparametric Regression Using Neural Networks](http://ftp.isds.duke.edu/WorkingPapers/00-32.pdf)」、Herbert K. H.Lee (ISDS、デューク大学)]

<a name="anchor-6d"></a> **[順序回帰][ordinal-regression]** 順序回帰は、順序従属変数をモデル化または予測するために使用する種類の回帰分析です。順序従属変数の場合、値にランク付けすることができますが、カテゴリ間の実際の距離は不明です。異なる値間の相対的な順序のみが意味を持ちます。ラベルまたはターゲット値は自然な順序またはランク付けであるため、順序ターゲットとして任意の数値列を使用できます。数値の自然な順序を使用して順位付けを行います。たとえば、病気は "ごく軽症" から "非常に重症" までで段階評価されたり、アンケートの回答者は "非常にそう思う" から "まったくそう思わない" までの中から回答を選択したり、学生は A から F までで格付けされたりします。ただし、基本的には、順序回帰は、*比例オッズ* モデルに基づくロジスティック回帰を拡張したものです。


<a name="anchor-6e"></a> **[ポワソン回帰][poisson-regression]** ポワソン回帰は、カウント データのモデリングによく使用されます。ポワソン回帰では、応答変数がポワソン分布に従っていることを前提とします。ポワソン分布に従うデータは本質的に整数値 (不連続で正の値) であり、カウント データとして意味のあるものです。ポワソン回帰にトレーニング データセットを与えると、指定された入力に対するパラメーターの対数尤度を最大化する最適値を見つけ出そうとします。パラメーターの尤度は、トレーニング データがこれらのパラメーターの分布からサンプリングされた確率です。ポワソン回帰は、たとえば次のような用途に役立ちます。

* 飛行機のフライトに関連付けられる風邪の数のモデリング 
* イベントまたはプロモーションに関連する通話の数を見積もる 
* 分割表を作成する

## 参照

Machine Learning Studio で使用可能な Machine Learning アルゴリズムの種類の一覧については、「[Machine Learning Studio: アルゴリズムとモジュールのヘルプ](https://msdn.microsoft.com/library/azure/dn905974.aspx)」の[モデルの初期化](https://msdn.microsoft.com/library/azure/0c67013c-bfbc-428b-87f3-f552d8dd41f6/)に関するページを参照してください。

すべての種類の Machine Learning アルゴリズムの詳細については、次のリファレンスを参照してください。リファレンスの多くは、この記事の作成に使用されています。

* 「[Choosing a Machine Learning Classifier](http://blog.echen.me/2011/04/27/choosing-a-machine-learning-classifier/)」、Edwin Chen

* 「[Decision Forests for Classification, Regression, Density Estimation, Manifold Learning and Semi-Supervised Learning](http://research.microsoft.com/pubs/155552/decisionForests_MSR_TR_2011_114.pdf)」、A. Criminisi1、J. Shotton2、および E. Konukoglu (Microsoft Research、2011 年) 技術レポート TR-2011-114

* 「[A Framework for Nonparametric Regression Using Neural Networks](http://ftp.isds.duke.edu/WorkingPapers/00-32.pdf)」、Herbert K. H.Lee (ISDS、デューク大学)

* 『Handbook of Computational Statistics』の「Concepts and Methods」、James E. Gentle、Wolfgang Karl Härdle、Yuichi Mori 編集 (Springer-Verlag Berlin Heidelberg New York、2004 年)

* 「[Logistic Regression and Linear Discriminant Analyses in Evaluating Factors Associated with Asthma Prevalence among 10- to 12-Years-Old Children: Divergence and Similarity of the Two Statistical Methods](http://www.hindawi.com/journals/ijpedi/2009/952042/)」、George Antonogeorgos、Demosthenes B. Panagiotakos、Kostas N. Priftis、Anastasia Tzonou (International Journal of Pediatrics、2009 年) Article ID 952042

* 「[The Optimality of Naive Bayes](http://www.aaai.org/Papers/FLAIRS/2004/Flairs04-097.pdf)」(ニューブランズウィック大学、2004年)、Harry Zhang

* 「[Support Vector Machines](http://www.astro.caltech.edu/~george/aybi199/AMooreTutorials/svm.ppt)」、Andrew W. Moore (カーネギー メロン大学、2001 年)

* 「[Machine Learning, Part II: Supervised and Unsupervised Learning](http://www.aihorizon.com/essays/generalai/supervised_unsupervised_machine_learning.htm)」、[AI Horizon](http://www.aihorizon.com/)

* 「[What are the advantages of logistic regression over decision trees?](http://www.quora.com/What-are-the-advantages-of-logistic-regression-over-decision-trees)」 ([Quora](http://www.quora.com/))

* 「[What is the difference between supervised learning and unsupervised learning?](http://stackoverflow.com/questions/1832076/what-is-the-difference-between-supervised-learning-and-unsupervised-learning)」 ([Stackoverflow](http://stackoverflow.com/))

* 「[When to choose which machine learning classifier?](http://stackoverflow.com/questions/2595176/when-to-choose-which-machine-learning-classifier)」 ([Stackoverflow](http://stackoverflow.com/))

* [Wikipedia](http://en.wikipedia.org):
	* [ベイジアン線形回帰](http://en.wikipedia.org/wiki/Bayesian_linear_regression)
	* [線形回帰](http://en.wikipedia.org/wiki/Linear_regression)
	* [多クラス分類](http://en.wikipedia.org/wiki/Multiclass_classification)
	* [教師なし学習](http://en.wikipedia.org/wiki/Unsupervised_learning)

その他のリファレンス

* [Microsoft Azure Machine Learning のアルゴリズム チート シート](machine-learning-algorithm-cheat-sheet.md) (Microsoft)

* [Choosing the right estimator](http://scikit-learn.org/stable/tutorial/machine_learning_map/) ([scikit-learn](http://scikit-learn.org/stable/index.html) 社)


<!-- Module References -->
[k-means-clustering]: https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/
[train-clustering-model]: https://msdn.microsoft.com/library/azure/bb43c744-f7fa-41d0-ae67-74ae75da3ffd/
[bayesian-linear-regression]: https://msdn.microsoft.com/library/azure/ee12de50-2b34-4145-aec0-23e0485da308/
[boosted-decision-tree-regression]: https://msdn.microsoft.com/library/azure/0207d252-6c41-4c77-84c3-73bdf1ac5960/
[decision-forest-regression]: https://msdn.microsoft.com/library/azure/562988b2-e740-4e3a-8131-358391bad755/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[neural-network-regression]: https://msdn.microsoft.com/library/azure/d7ee222c-669f-4200-a576-a761a9c1a928/
[ordinal-regression]: https://msdn.microsoft.com/library/azure/ffb557f8-dc7f-44bd-8fd0-b25666dd23f1/
[poisson-regression]: https://msdn.microsoft.com/library/azure/80e21b9d-3827-40d8-b733-b53148becbc2/

 

<!---HONumber=July15_HO3-->