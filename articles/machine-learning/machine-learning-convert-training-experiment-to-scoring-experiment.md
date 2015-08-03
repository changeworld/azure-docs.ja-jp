<properties 
	pageTitle="Machine Learning のトレーニング実験からスコア付け実験に変換する | Microsoft Azure" 
	description="予測分析モデルのトレーニングに使用されるMachine Learning のトレーニング実験を、Web サービスとして発行できるスコア付け実験に変換する方法。" 
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
	ms.date="07/10/2015" 
	ms.author="garye"/>

#Machine Learning のトレーニング実験からスコア付け実験に変換する

Azure Machine Learning では、予測分析ソリューションをビルド、テスト、および配置できます。

予測分析モデルをトレーニングするために、*トレーニング実験*で作成および反復処理を実行し、新しいデータのスコア付けに使用する準備ができたら、スコア付け実験を準備して効率化する必要があります。次に、この*スコア付け実験*を Azure Web サービスとして発行し、ユーザーがデータをモデルに送信して、モデルの予測を受信できるようにします。

スコア付け実験に変換することで、トレーニングされたモデルをスコア付けの Web サービスとして公開できるようになります。Web サービスのユーザーがモデルに入力データを送信し、モデルは予測結果を返します。したがって、スコア付け実験を変換した場合は、モデルが他のユーザーに使用されることを想定しておいてください。

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

トレーニング実験をスコア付け実験に変換するプロセスには、次の 3 つの手順が含まれます。

1.	トレーニング済みの機械学習モデルを保存し、機械学習のアルゴリズムと[モデルのトレーニング][train-model] モジュールを、保存したトレーニング済みのモデルに置き換えます。
2.	スコア付けに必要なモジュールのみに実験を絞り込みます。トレーニング実験には、トレーニングに必要な多数のモジュールが含まれますが、モデルがトレーニングされてスコア付けに使用できるようになると、それらのモジュールは必要なくなります。
3.	実験の中で Web サービスのユーザーからのデータをどこで受け入れ、どのデータを返すか定義します。

##[スコア付け実験の作成] ボタン

実験のキャンバスの下部にある **[スコア付け実験の作成]** ボタンは、トレーニング実験をスコア付け実験に変換する 3 つの手順をユーザーに代わって実行します。

1.	トレーニング済みのモデルを、(実験キャンバスの左側にある) モジュール パレットの **[トレーニング済みモデル]** セクションのモジュールとして保存し、機械学習アルゴリズムと[モデルのトレーニング][train-model] モジュールを保存されたトレーニング済みのモデルで置き換えます。 
2.	これは明らかに不要なモジュールを削除します。この例では、[分割][split]、2 番目の[モデルのスコア付け][score-model]、および[モデルの評価][evaluate-model]モジュールなどが該当します。
3.	Web サービスの入力モジュールと出力モジュールを作成し、実験の既定の場所に追加します。

たとえば、次の実験では、サンプルの国勢調査のデータを使用した 2 クラスのブースト デシジョン ツリー モデルをトレーニングします。

![トレーニング実験][figure1]

この実験のモジュールは、基本的に 4 つの異なる機能を実行します。

![モジュール関数][figure2]

このトレーニング実験をスコア付け実験に変換するときにこれらのモジュールの一部が不要になったり、別の目的を持つようになったりします。

- **データ**- このサンプル データセット内のデータは、スコア付け中には使用されません。Web サービスのユーザーは、スコア付けするデータを指定します。ただし、データ型など、このデータセットからのメタデータは、トレーニング済みのモデルで使用されます。このため、このメタデータを提供できるように、スコア付け実験のデータセットを保持する必要があります。

- **準備** -スコア付けのために送信されるデータによっては、これらのモジュールが受信データの処理に必要な場合と不必要な場合があります。

	たとえば、この例では、サンプルのデータセットの値が欠落しており、モデルのトレーニングに必要ない列が含まれています。このため、[見つからないデータのクリーンアップ][clean-missing-data] モジュールが見つからない値の処理に含まれ、[プロジェクトの列][project-columns]モジュールが、これらの余分な列をデータ フローから除外するために含まれます。Web サービスを通じてスコアリングのために送信されるデータに不足している値がないことがわかっている場合は、[見つからないデータのクリーンアップ][clean-missing-data] モジュールを削除できます。ただし、[プロジェクトの列][project-columns]モジュールはスコア付けされる機能セットの定義に役立つため、このモジュールは残す必要があります。

- **トレーニング** - モデルが正常にトレーニングされたら、1 つのトレーニング済みモデルのモジュールとして保存します。次に、これらの各モジュールを、保存されているトレーニング済みのモデルに置き換えます。

- **スコア** - この例では、データ ストリームをテスト データとトレーニング データのセットに分割するために、分割モジュールが使用されます。スコア付け実験では、これは必要ないため削除できます。同様に、2 番目の[モデルのスコア付け][score-model]モジュールと[モデルの評価][evaluate-model]モジュールは、テスト データの結果を比較するために使用されるため、これらのモジュールもスコア付け実験では必要ありません。ただし、残りの[モデルのスコア付け][score-model]モジュールは、Web サービスを通じてスコア結果を返すために必要です。

**[スコア付け実験の作成]** をクリックした後で、この例がどのようになるのかを次に示します。

![変換済みスコア付け実験][figure3]

これは、実験を Web サービスとして発行するための準備として充分である場合があります。ただし、実験に合わせて追加の作業を実行する可能性があります。

###入力と出力のモジュールを調整する

トレーニング実験では、トレーニング データのセットを使用して、機械学習アルゴリズムが必要とする形式でデータを取得するいくつかの処理を実行しました。Web サービスを通じて受信することを想定しているデータで、この処理が不要な場合は、**Web サービスの入力モジュール**を実験の別のノードに移動できます。

たとえば、既定では、上の図で示すように、**スコア付け実験の作成**が **Web サービスの入力**モジュールをデータ フローの上部に置きます。ただし、入力データがこの処理を必要としない場合、**Web サービスの入力**を、データ処理モジュールの先に手動で配置できます。

![Web サービス入力の移動][figure4]

Web サービスを通じて提供される入力データが、これで処理なしでモデルのスコア付けモジュールに直接渡されるようになりました。

同様に、既定では**スコア付け実験の作成**によって、Web サービスの出力モジュールがデータ フローの下部に置かれます。この例では、Web サービスはユーザーに完全な入力データのベクトル、およびスコア付けの結果を含む[モデルのスコア付け][score-model]モジュールの出力を返します。

ただし、入力データのベクトル全体ではなく、スコア付けの結果のみなど、別のものが返されるようにする場合は、[プロジェクトの列][project-columns]モジュールを挿入して、スコア付けの結果以外のすべての列を除外することができます。次に、**Web サービスの出力**モジュールを[プロジェクトの列][project-columns]モジュールの出力に移動します。

![Web サービス出力の移動][figure5]

###データ処理モジュールを追加または削除する

スコア付け中に必要ないことがわかっているモジュールが、他にも実験に存在する場合は、それらを削除できます。たとえば、**Web サービスの入力**モジュールをデータ処理モジュールの後の地点に移動したため、[見つからないデータのクリーンアップ][clean-missing-data] モジュールをスコア付け実験から削除できます。

これで、スコア付け実験が次のようになります。

![その他のモジュールの取り外し][figure6]

###オプションの Web サービス パラメーターを追加する

場合によっては、Web サービスのユーザーがサービスにアクセスしたときに、モジュールの動作を変更できるようにすることもあります。*Web サービスのパラメーター*を使えば変更できます。

一般的な例として、発行された Web サービスのユーザーが、Web サービスにアクセスしたときに別のデータ ソースを指定できるように[リーダー][reader] モジュールを設定する場合があります。または、別の宛先を指定できるように[ライター][writer] モジュールを構成します。

Web サービスのパラメーターを定義して、1 つまたは複数のモジュール パラメーターに関連付け、必須かオプションかを指定することができます。Web サービスのユーザーは、サービスにアクセスするときにこれらのパラメーターの値を指定することができ、モジュールの操作はそれに応じて変更されます。

Web サービスのパラメーターの詳細については、「[Azure Machine Learning の Web サービスのパラメーターを使用する][webserviceparameters]」をご覧ください。

[webserviceparameters]: machine-learning-web-service-parameters.md


##スコア付け実験を Web サービスとして公開する

スコア付け実験の準備を十分に実行したので、Azure Web サービスとして発行できます。Web サービスを使用してユーザーはデータをモデルに送信でき、モデルは予測を返します。

完全な発行プロセスの詳細については、「[Azure Machine Learning Web サービスを発行する][publish]」をご覧ください。

[publish]: machine-learning-publish-a-machine-learning-web-service.md


<!-- Images -->
[figure1]: ./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure1.png
[figure2]: ./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure2.png
[figure3]: ./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure3.png
[figure4]: ./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure4.png
[figure5]: ./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure5.png
[figure6]: ./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure6.png


<!-- Module References -->
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[project-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[writer]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/
 

<!---HONumber=July15_HO4-->