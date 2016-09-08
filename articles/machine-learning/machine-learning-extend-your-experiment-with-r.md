<properties
	pageTitle="R を使用した実験の拡張 | Microsoft Azure"
	description="R スクリプトの実行モジュール使用して、R 言語によって Azure Machine Learning Studio 機能を拡張する方法。"
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
	ms.date="08/19/2016"
	ms.author="garye" />


#R を使用した実験の拡張

[R スクリプトの実行][execute-r-script]モジュール使用して、R 言語によって ML Studio 機能を拡張できます。

このモジュールは、複数の入力データセットを受け取り、1 つのデータセットを出力として生成します。[R スクリプトの実行][execute-r-script]モジュールの **R Script** パラメーターに、R スクリプトを入力できます。

モジュールの各入力ポートにアクセスするには、次のようなコードを使用します。

    dataset1 <- maml.mapInputPort(1)

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##現在インストールされているすべてのパッケージを一覧表示する

インストールされているパッケージの一覧を変更できます。現在インストールされているパッケージと各パッケージの説明が含まれた完全な一覧を取得するには、[R スクリプトの実行][execute-r-script]モジュールに次のコードを入力します。

    out <- data.frame(installed.packages(,,,fields="Description"))
	maml.mapOutputPort("out")

これにより、[R スクリプトの実行][execute-r-script]モジュールの出力ポートにパッケージの一覧が送信されます。パッケージの一覧を表示するには、[CSV に変換][convert-to-csv]などの変換モジュールを、[R スクリプトの実行][execute-r-script]モジュールの左側の出力に接続し、実験を実行します。次に、変換モジュールの出力をクリックし、**[ダウンロード]** を選択します。

![](./media/machine-learning-extend-your-experiment-with-r/download-package-list.png)


##パッケージをインポートする

[R スクリプトの実行][execute-r-script]モジュールと圧縮されたパッケージのアーカイブで次のコマンドを使用して、ステージングされた ML Studio リポジトリから、まだインストールされていないパッケージをインポートすることもできます。

    install.packages("src/my_favorite_package.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("my_favorite_package", lib.loc = ".", logical.return = TRUE, verbose = TRUE)

ここで、`my_favorite_package.zip` にはパッケージの zip ファイルが含まれています。


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/

<!---HONumber=AcomDC_0824_2016-->