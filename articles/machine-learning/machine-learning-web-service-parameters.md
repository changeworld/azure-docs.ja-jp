<properties 
	pageTitle="Azure Machine Learning Web サービスのパラメーターの使用 | Microsoft Azure" 
	description="Azure Machine Learning Web サービスを使用して、Web サービス アクセス時のモデルの動作を変更する方法です。" 
	services="machine-learning" 
	documentationCenter="" 
	authors="raymondlaghaeian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/29/2015" 
	ms.author="raymondl;garye"/>

#Azure Machine Learning Web サービスのパラメーターの使用

Azure Machine Learning Web サービスは、変更可能なパラメーターを持つモジュールを含む実験を発行すると作成されます。状況によっては、Web サービスの実行中にモジュールの動作変更が必要になる場合がありますが、*Web サービスのパラメーター*を使えば変更できます。

一般的な例として、発行された Web サービスのユーザーが、Web サービスにアクセスしたときに別のデータ ソースを指定できるように[リーダー][reader] モジュールを設定する場合があります。または、別の宛先を指定できるように[ライター][writer] モジュールを構成します。その他の例としては、[特徴ハッシュ][feature-hashing] モジュールのビット数の変更や、[フィルターに基づく特徴の選択][filter-based-feature-selection]モジュールにおける目的の特徴の数の変更があります。

Web サービスのパラメーターを定義して、1 つまたは複数のモジュール パラメーターに関連付け、必須かオプションかを指定することができます。Web サービスのユーザーは、サービスにアクセスするときにこれらのパラメーターの値を指定でき、モジュールの操作は実行時に変更されます。

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


##Web サービスのパラメーターを設定して使用する方法

Web サービスのパラメーターを定義するには、モジュールのパラメーターの横にあるアイコンをクリックして、[Set as web service parameter] を選択します。すると、新しい Web サービスのパラメーターが作成されて、モジュール パラメーターにつながります。これで、Web サービスにアクセスしたときにユーザーが Web サービスのパラメーターの値を指定できるようになり、それがモジュール パラメーターに適用されます。

Web サービスのパラメーターを 1 回定義すれば、その実験の他のモジュール パラメーターでも使用できます。1 つのモジュールのパラメーターに関連付けられている Web サービスのパラメーターを定義する場合、Web サービスのパラメーターに同じ種類の値が想定されるのであれば、その同じパラメーターを他のモジュールでも使用できます。たとえば、Web サービスのパラメーターが数値の場合、数値が想定されるモジュール パラメーターでのみ使用できます。Web サービスのパラメーターの値を設定すると、すべての関連付けられたモジュール パラメーターに適用されます。

Web サービスのパラメーターの既定値を指定するかどうかを設定できます。指定した場合、Web サービスのユーザー向けのパラメーターはオプションとなります。既定値を指定しない場合、ユーザーは Web サービスにアクセスしたときに値の入力を求められます。

Web サービスのドキュメント (Machine Learning Studio の Web サービス **ダッシュボード**にある **API ヘルプ ページ** リンクから入手可能) には、Web サービス アクセス時にプログラムで Web サービスのパラメーターを指定する方法に関する Web サービス ユーザー向けの情報が含まれます。


##例

たとえば、Azure BLOB ストレージに情報を送信する[ライター][writer] モジュールの実験があるとします。Web サービスのユーザーがサービスにアクセスしたときに BLOB ストレージへのパスを変更できるようにする "Blob path" という名前の Web サービスのパラメーターを定義しましょう。

1.	Machine Learning Studio で[ライター][writer] モジュールをクリックして選択します。実験キャンバスの右側の [プロパティ] ウィンドウにプロパティが表示されます。

2.	ストレージの種類を指定します。

    - **[Please specify data destination]** の下の [Azure Blob Storage] を選択します。
    - **[Please specify authentication type]** の下の [アカウント] を選択します。
    - Azure BLOB ストレージのアカウント情報を入力します。 
    <p />

3.	**[Path to blob beginning with container parameter]** の右にあるアイコンをクリックします。次のように表示されています。

	![Web サービスのパラメーター アイコン][icon]

    [Set as web service parameter] を選択します。

    [プロパティ] ウィンドウの下部にある **[Web Service Parameters]** の下に [Path to blob beginning with container] という名前のエントリが追加されます。これは、この[ライター][writer] モジュール パラメーターに現在関連付けられている Web サービスのパラメーターです。

4.	Web サービスのパラメーターの名前を変更するには、名前をクリックし、「Blob path」と入力して **Enter** キーを押します。
 
5.	Web サービスのパラメーターの既定値を指定するには、名前の右側にあるアイコンをクリックして、[Provide default value] を選択して値を入力し (たとえば 「container1/output1.csv」)、**Enter** キーを押します。

	![Web サービスのパラメーター][parameter]

6.	**[実行]** をクリックします。

7.	**[PUBLISH WEB SERVICE]** をクリックして Web サービスを発行します。

これで Web サービスのユーザーが Web サービス アクセス時に[ライター][writer] モジュールの新しい宛先を指定できるようになります。

##詳細

詳細については、[Machine Learning Blog (Machine Learning ブログ)](http://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx) の[「Web サービスのパラメーター (ブログの投稿)」](http://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx)をご覧ください。

Machine Learning Web サービスへのアクセスの詳細については、[「発行済みの Machine Learning Web サービスを使用する方法」](machine-learning-consume-web-services.md)をご覧ください。



<!-- Images -->
[icon]: ./media/machine-learning-web-service-parameters/icon.png
[parameter]: ./media/machine-learning-web-service-parameters/parameter.png


<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[writer]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/
 

<!---HONumber=July15_HO4-->