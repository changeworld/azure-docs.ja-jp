<properties 
	pageTitle="機械学習アルゴリズム チート シート | Microsoft Azure" 
	description="印刷可能な機械学習アルゴリズム チート シートは、Azure Machine Learning Studio で予測モデルに適したアルゴリズムを選択するのに役立ちます。"
	services="machine-learning"
	documentationCenter="" 
	authors="brohrer" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/18/2015" 
	ms.author="brohrer;garye" />


# Microsoft Azure Machine Learning Studio の機械学習アルゴリズム チート シート

**Microsoft Azure Machine Learning のアルゴリズム チート シート**を使用すると、Microsoft Azure Machine Learning のアルゴリズム ライブラリから、予測分析ソリューションに適した機械学習アルゴリズムを選択できます。このチート シートで、データの性質や対応している問題について質問に答えていくと、適切なアルゴリズムが提案されます。

[Azure Machine Learning Studio](https://studio.azureml.net/) には、予測分析ソリューションの構築に使用できる多くの機械学習アルゴリズムが用意されています。これらのアルゴリズムは、一般的な機械学習のカテゴリ (***回帰***、***分類***、***クラスタリング***、***異常検出***) に分類され、それぞれが別の種類の機械学習の問題に対応するように設計されています。



## 機械学習アルゴリズム チート シートのダウンロード

機械学習アルゴリズム チート シートをダウンロードし、ソリューションに適した機械学習アルゴリズムの選択方法を確認するのに役立てます。チート シートをタブロイド サイズ (11 x 17 インチ) で印刷し、手元に置いておくことができます。

**チート シートをダウンロードする: [Microsoft Azure Machine Learning Algorithm Cheat Sheet](http://download.microsoft.com/download/A/6/1/A613E11E-8F9C-424A-B99D-65344785C288/microsoft-machine-learning-algorithm-cheat-sheet-v2.pdf)**

![Machine Learning Algorithm cheat sheet: Learn how to choose a Machine Learning algorithm.][cheat-sheet]

[cheat-sheet]: ./media/machine-learning-algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet-microsoft-azure.png



<!-- This is now covered in the first footnote below
[Azure Machine Learning Studio](https://studio.azureml.net/) gives you the flexibility to experiment - 
try one algorithm, and if you're not satisfied with the results, try another.
Here's an example from the [Azure Machine Learning Gallery](http://gallery.azureml.net) of an experiment that tries several algorithms against the same data and compares the results:
[Compare Multi-class Classifiers: Letter recognition](http://gallery.azureml.net/Details/a635502fc98b402a890efe21cec65b92).
-->

## アルゴリズムに関するその他のヘルプ 

* さまざまな種類の機械学習アルゴリズムとその使用方法の詳細については、「[Azure Machine Learning のアルゴリズムを選択する方法](machine-learning-algorithm-choice.md)」をご覧ください。
* Machine Learning Studio で使用可能な機械学習アルゴリズムのカテゴリ別の一覧については、Machine Learning Studio アルゴリズムとモジュールのヘルプの[モデルの初期化][initialize-model]に関するページを参照してください。
* Machine Learning Studio で使用可能なすべてのアルゴリズムとモジュールの一覧については、Machine Learning Studio アルゴリズムとモジュールのヘルプの「[Machine Learning Studio モジュールの一覧 (アルファベット順)](https://msdn.microsoft.com/library/azure/dn906033.aspx)」を参照してください。

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## 機械学習アルゴリズム チート シートの注意事項と用語の定義

* このアルゴリズム チート シートに示した提案は経験則です。変化する場合や著しく異なる場合があります。これは、開始点を提案するものです。データ上の複数のアルゴリズム間で競合が発生しても心配しないでください。それぞれのアルゴリズムの原則を理解し、データが生成されたシステムを理解することに代わるものはありません。 

* すべての機械学習アルゴリズムには、独自のスタイルや*帰納的バイアス*があります。特定の問題に対しては、複数のアルゴリズムが適切な場合や、1 つのアルゴリズムが他のアルゴリズムよりも適している場合があります。しかし、事前にどれが最適かを知ることができるとは限りません。このような場合は、複数のアルゴリズムがチート シートに一緒に記載されています。1 つのアルゴリズムを試してみて、結果に満足できない場合は、他のアルゴリズムを試してみるのが適切な方策でしょう。同じデータに対して複数のアルゴリズムを試した[Azure Machine Learning ギャラリー](http://gallery.azureml.net/)の実験例と、その結果の比較については、「[Compare Multi-class Classifiers: Letter recognition (多クラス分類モデルの比較: 文字認識)](http://gallery.azureml.net/Details/a635502fc98b402a890efe21cec65b92)」をご覧ください。

* 機械学習には、3 つの主なカテゴリ (**教師あり学習**、**教師なし学習**、**強化学習**) があります。

  * **教師あり学習**では、各データ ポイントに、カテゴリや関心のある値がラベル付けまたは関連付けられています。カテゴリのラベルには、たとえば '猫' または '犬' のいずれかの画像を割り当てています。値のラベルの例は、中古車に関連付けられている販売価格です。教師あり学習の目的は、このような多くのラベルの付いた例を学習し、将来のデータ ポイントを予測して、たとえば、新しい写真の動物を正しく識別したり、他の中古車に正しい販売価格を割り当てることができるようになることです。これは、人気のある便利な機械学習の種類です。Azure Machine Learning のモジュールは、[K 平均法クラスタリング][k-means-clustering]を除いて、すべて教師あり学習アルゴリズムです。

  * **教師なし学習**では、データ ポイントにラベルが関連付けられていません。代わりに、教師なし学習アルゴリズムの目的は、いくつかの方法でデータを整理したり、その構造を記述することです。これは、K 平均法と同様にクラスターにグループ化したり、複雑なデータを簡単に表示できるようにさまざまな表示方法を検索することを意味します。

  * **強化学習**では、アルゴリズムが各データ ポイントに応答してアクションを選択します。これはロボット工学の一般的な手法です。ある時点での一連のセンサーの読み取りがデータ ポイントになり、アルゴリズムがロボットの次の動作を選択します。モノのインターネット アプリケーションにも自然に適合します。学習アルゴリズムでは、短時間で報酬信号を受信し、その決定がいかに優れていたかを示します。アルゴリズムはこれに基に、最大の報酬を実現するために、戦略を変更します。現時点で、Azure ML には強化学習アルゴリズム モジュールはありません。

* **ベイズ法**は、統計的に独立したデータ ポイントを仮定します。これは、1 つのデータ ポイントでモデル化されていない変動性は、他と相関関係がないことを意味するため、予測できません。たとえば、記録されているデータが、次の地下鉄が到着するまでの分単位の時間である場合は、1 日の間に行われた 2 つの測定値は統計的に独立しています。ただし、1 分間に行われた 2 つの測定値は統計的に独立していません。一方の値は他の値の高い予測です。

* **ブースト デシジョン ツリー回帰**は、特徴間の特徴の重複や相互作用を活用します。つまり、特定のデータ ポイントの 1 つの特徴の値は、ある程度別の値の予測であるということです。たとえば、毎日の最高 / 最低気温データで、その日の最低気温がわかれば、最高気温を合理的に推測できます。2 つの特徴に含まれる情報は、少し冗長です。

* データを 3 つ以上のカテゴリに分類するには、本質的に多クラス分類モデルを使用するか、2 つのクラス分類モデルのセットを**アンサンブル**に組み合わせます。アンサンブルの手法では、各クラスに 2 つのクラス分類モデルがあり、各クラスはデータを 2 つのカテゴリ ("このクラス" と "これ以外のクラス") に分類します。 これらの分類モデルは、データ ポイントの適切な割り当てに投票します。これは、[一対全多クラス][one-vs-all-multiclass]の背後にある運用原理です。

* ロジスティック回帰やベイズ ポイント マシンなどのいくつかのメソッドは、**線形クラス境界**、つまり、クラス間の境界は直線に近いこと (または、一般的に超平面) を前提としています。多くの場合、これは、分離を試みるまでわからないデータの特性ですが、通常は事前に視覚化することで学習できるものです。クラス境界が非常に不規則に見える場合は、デシジョン ツリー、デシジョン ジャングル、サポート ベクター マシン、ニューラル ネットワークを使用します。

* ニューラル ネットワークは、各カテゴリに**ダミー変数**を作成し、カテゴリが適用される場合はこれを 1 に設定し、適用されない場合は 0 に設定することで、カテゴリ変数と一緒に使用できます。


<!-- This is how you can add a link to the image in HTML. Don't know how to do this in markdown.
<a href="http://download.microsoft.com/download/A/6/1/A613E11E-8F9C-424A-B99D-65344785C288/microsoft-machine-learning-algorithm-cheat-sheet.pdf">
<img src="C:\Users\garye\azure-content-pr\articles\media\machine-learning-algorithm-cheat-sheet\cheat-sheet-small.png">
</a>
-->

<!-- Module References -->
[initialize-model]: https://msdn.microsoft.com/library/azure/0c67013c-bfbc-428b-87f3-f552d8dd41f6/
[k-means-clustering]: https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/
[one-vs-all-multiclass]: https://msdn.microsoft.com/library/azure/7191efae-b4b1-4d03-a6f8-7205f87be664/
 

<!---HONumber=July15_HO2-->