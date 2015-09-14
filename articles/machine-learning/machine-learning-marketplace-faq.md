<properties 
	pageTitle="Azure Marketplace に Machine Learning Web service の公開と使用に関する FAQ | Microsoft Azure"
	description="よく寄せられる質問"
	services="machine-learning"
	documentationCenter=""
	authors="LuisCabrer"
	manager="paulettm"
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/01/2015"
	ms.author="luisca"/>

#Azure Marketplace での Machine Learning アプリケーションの公開と使用に関する FAQ

##Marketplace から使用する場合


**1.Web サービスの入力を入力した後に、なぜ次のエラー メッセージが表示されるのですか。**

**要求がバックエンドでタイムアウトかエラーになっています。チームでは現在この問題を調査中です。ご不便をおかけして申し訳ありません。(500)**

入力パラメーターが特定の Web サービスの所定の形式に準拠していない可能性があります。入力パラメーターの正しい形式およびこの Web サービスの制限事項については、対応するドキュメントのリンクを参照してください。


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

**2.[このデータセットの参照] ページに表示されている Web サービスの API リンクをコピーして、別のブラウザー ウィンドウかタブに貼り付けた場合、結果にアクセスするにはどのような資格情報が必要で、結果はどのように表示されるでしょうか。**

ユーザー名として Marketplace アカウントを使用し、パスワードとしてプライマリ アカウント キーを使用する必要があります。プライマリ アカウント キーは、**[このデータセットの参照]** ページの Web サービスの説明の下に表示されています (**[表示]** ボタンをクリック)。使用しているブラウザーによって、結果がブラウザーに表示されるか、ダウンロードできるようになります。

**3.[このデータセットの参照] ページのWeb サービスの入力を入力した後に、なぜ次のエラー メッセージが表示されるのですか。**

**要求の処理中に予期しないエラーが発生しました。もう一度実行してください。**

Marketplace の **[このデータセットの参照]** ページで Web サービスを使用するときに、Web サービスの 1 つ以上の入力パラメーターが長さの制限を超過した可能性があります。HTTP POST メソッドを使用して、より長い入力文字列でサービスを呼び出すことができます。例については、「[Azure ML で R を使用して構築され、Marketplace に発行されたサンプル Web サービス](machine-learning-r-csharp-web-service-examples.md)」をご覧ください。

**4.なぜ Microsoft Azure の管理ポータルのストアの [API エクスプローラー] タブに何も表示されないのでしょうか。**

これは、Azure 管理ポータルの Marketplace の既知の問題です。チームではこの問題を解決するために作業中です。


##Marketplace の Azure Machine Learning からの発行に関する質問

**1.ロゴ、イメージのトランザクション数が Web サービスに対して更新されないのはなぜでしょうか。**

ロゴとイメージは公開ポータルでキャッシュされ、ポータルで新しいロゴとイメージに更新されるまで最大 10 日間かかる場合があります。

**2.Marketplace で Web サービスの [詳細] タブにエラー メッセージが表示されているのはなぜですか。**

Marketplace では、サービスの詳細情報を取得するために Azure Machine Learning にアクセスする場合の既知の問題があります。チームではこの問題を解決するために作業中です。

**3.Azure Machine Learning Web サービスの R サンプル コードが Marketplace で Web サービスを使用するときに機能しないのはなぜですか。**

Azure Machine Learning Web サービスに直接接続するときと、これらの Web サービスに Marketplace を通じて接続するときとでは、認証システムが異なります。Marketplace のサービスは OData サービスであり、GET か POST メソッドで呼び出すことができます。

**4.Web サービスのサポート リンクがいくつかのプランで正しく更新されていないのはなぜですか。**

サポート リンクは、プランごとにではなく発行者ごとにグローバルです。

**5.Marketplace でバッチ入力モードを使用して Web サービスを発行するにはどうすればよいですか。**

バッチ入力モードは、現在 Marketplace Web サービスでサポートされていません。

**6.データ発行者になる方法や、発行時に発生した問題について質問がある場合は、どこに問い合わせればよいでしょうか。**

詳細については、<datamarketbd@microsoft.com> のAzure Marketplace チームにお問い合わせください。





 

<!---HONumber=September15_HO1-->