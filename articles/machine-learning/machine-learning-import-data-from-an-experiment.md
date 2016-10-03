<properties
	pageTitle="別の実験のデータを Machine Learning Studio にインポートする | Microsoft Azure"
	description="Azure Machine Learning Studio にトレーニング データを保存して別の実験で保存する方法。"
	keywords="データのインポート、データ、データ ソース、トレーニング データ"
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="garye;bradsev" />


# 別の実験のデータを Azure Machine Learning Studio にインポートする

[AZURE.INCLUDE [import-data-into-aml-studio-selector](../../includes/machine-learning-import-data-into-aml-studio.md)]


実験の中間結果を別の実験の一部として使用することが必要になる場合があります。この場合は、モジュールをデータセットとして保存します。

1. データセットとして保存するモジュールの出力をクリックします。

2. **[データセットとして保存]** をクリックします。

3. メッセージが表示されたら、名前と、データセットを簡単に識別できるような説明を入力します。

4. **[OK]** チェックマークをクリックします。

保存が完了すると、ワークスペースのどの実験でもデータセットを使用できるようになります。データセットは、モジュール パレットの **[保存されたデータセット]** リストから検索できます。

<!---HONumber=AcomDC_0921_2016-->