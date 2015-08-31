<properties
	pageTitle="Machine Learning Web サービスの Excel アドイン | Microsoft Azure"
	description="コードを作成することがなく Excel から直接 Azure Machine Learning Web サービスにアクセスする方法。"
	services="machine-learning"
	documentationCenter=""
	authors="tedway"
	manager="paulettm"
	editor="cgronlun"
    tags=""/>

<tags
	ms.service="machine-learning"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
	ms.workload="data-services"
	ms.date="08/10/2015"
	ms.author="tedway;garye" />

# Azure Machine Learning Web サービスの Excel アドイン

Excel を使用すると、コードを作成することなく、直接 Web サービスを簡単に呼び出すことができます。

## 既存の Web サービスを使用する手順

1. サンプルの Excel ファイルをここから、または Web サービスの **[ダッシュボード]** タブから開きます。
2. 対象の Web サービスをクリックして選択します。この例では、「Titanic Survivor Predictor (Excel Add-in Sample) [Score]」を選択します。

    ![Web サービスの選択][01]

3. この操作により、**[予測]** セクションが表示されます。Excel のセルを選択し、**[サンプル データを使用]** をクリックします。必要に応じて Excel のデータを編集します。

	![[予測] セクション][02]

4. データを強調表示して、入力データ範囲のアイコンをクリックします。
5. **[出力]** で、データを出力するセル番号を入力します。
6. **[予測]** をクリックします。

## 新しい Web サービスを追加する手順

1. Web サービスを発行 (方法は[こちらのページ](machine-learning-walkthrough-5-publish-web-service.md)をご覧ください) または既存の Web サービスを検索します。
2. Web サービスを作成したら、Machine Learning Studio の左側の **[Web サービス]** セクションをクリックして、使用する Web サービスを選択します。
3. Web サービスの **[ダッシュボード]** タブで、Web サービス用の API キーをコピーします。
4. Excel で、**[Web サービス]** セクションに移動します (**[予測]** セクションにいる場合は、戻る矢印をクリックして Web サービスの一覧に移動します)。

	![[Web サービス] セクション][03]

5. **[Web サービスの追加]** をクリックします。
6. **API キー**というラベルが付いたテキスト ボックスにキーを貼り付けます。
7. Web サービスの **[ダッシュボード]** タブで、**API のヘルプ ページ**へのリンクをクリックします。
8. **[要求]** の下にある要求 URI をコピーして、**URL** というラベルのテキスト ボックスに貼り付けます。
9. **[追加]** をクリックします。

	![URL と API キー][04]

10.	Web サービスを使用するには、前述の「既存の Web サービスを使用する手順」に従います。

## ブックを共有する

ブックを保存すると、追加した Web サービス用の API キーも保存されます。このため、信頼できる個人とのみブックを共有することをお勧めします。


[01]: ./media/machine-learning-excel-add-in-for-web-services/image1.png
[02]: ./media/machine-learning-excel-add-in-for-web-services/image2.png
[03]: ./media/machine-learning-excel-add-in-for-web-services/image3.png
[04]: ./media/machine-learning-excel-add-in-for-web-services/image4.png

<!---HONumber=August15_HO8-->