<properties
	pageTitle="ローカル ファイルのデータを Machine Learning Studio にインポートする | Microsoft Azure"
	description="ローカル ファイルのトレーニング データを Azure Machine Learning Studio にインポートする方法。"
	keywords="データのインポート、データ形式、データ型、データ ソース、トレーニング データ"
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


# ローカル ファイルのトレーニング データを Azure Machine Learning Studio にインポートする

[AZURE.INCLUDE [import-data-into-aml-studio-selector](../../includes/machine-learning-import-data-into-aml-studio.md)]


独自のデータを Machine Learning Studio で使用するために、事前にローカル ハード ドライブからデータ ファイルをアップロードして、データセット モジュールをワークスペースで作成できます。


## ローカル ファイルからデータをインポートする

次の手順に従って、ローカルのハード ドライブからデータをインポートできます。

1. Machine Learning Studio ウィンドウの下部で、**[+新規]** をクリックします。
2. **[データセット]** と **[FROM LOCAL FILE]** を選択します。
3. **[Upload a new dataset]** ダイアログ ボックスで、アップロードするファイルを参照します。
4. 名前を入力し、データ型を指定したら、必要に応じて説明を入力します。データの特徴を記録しておくと、後でデータを使用する際に参照できるため、説明を入力しておくことをお勧めします。
5. チェックボックス **[This is the new version of an existing dataset]** をオンにしておくことにより、新しいデータで既存のデータセットを更新できます。このチェックボックスをクリックし、既存のデータセット名を入力するだけです。

アップロードする際に、ファイルがアップロードされていることを示すメッセージが表示されます。アップロード時間は、データのサイズと、サービスへの接続速度に依存します。ファイルのアップロードに時間がかかる場合、待機している間に Machine Learning Studio で他の作業も実行できます。ただし、ブラウザーを閉じるとデータのアップロードに失敗します。

データがアップロードされると、データセット モジュールに格納され、ワークスペース内のすべての実験で使用できるようになります。実験を編集するときは、モジュール パレットの **[保存されたデータセット]** 一覧の **[マイ データセット]** 一覧に、作成済みのデータセットが表示されます。データセットを別の分析と機械学習に使用する場合は、データセットを実験キャンバスにドラッグ アンド ドロップします。

<!---HONumber=AcomDC_0921_2016-->