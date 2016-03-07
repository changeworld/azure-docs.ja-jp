<properties
   pageTitle="ロジック アプリでの Azure Machine Learning コネクタの使用 | Microsoft Azure App Service"
   description="Azure Machine Learning コネクタを作成、構成し、Azure App Service のロジック アプリで使用する方法"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="02/11/2016"
   ms.author="jehollan"/>
   
# 概要
>[AZURE.NOTE] 本記事は、ロジック アプリの 2014-12-01-preview スキーマ バージョンを対象としています。

ロジック アプリ用 Azure ML コネクタを使用すると、バッチ スコアリング (バッチ実行サービス) および再トレーニングのために Azure ML API を呼び出すことができます。これらの機能とロジック アプリのトリガーを使用すると、バッチ ジョブのスケジュールや、スケジュールされたモデルの再トレーニングのセットアップを行うことができます。

 ![][1]
 
## Azure Machine Learning コネクタの使用開始とロジック アプリへの追加
まず、Azure ML Studio で実験を作成し、Web サービスをセットアップしてデプロイします。これで、API URL と、バッチ実行のヘルプ ページに記載されている BES Post URL のキーを使用できるようになります。([Machine Learning チュートリアル](../machine-learning/machine-learning-walkthrough-5-publish-web-service.md))

コネクタを使用して BES ジョブを実行するには、ロジック アプリに Azure ML コネクタを追加します。次に、必要な情報を入力します (詳細については後述)。再トレーニングをセットアップするには、2 つ目の Azure ML コネクタを追加し、入力パラメーターを指定します (「[プログラムによる Machine Learning のモデルの再トレーニング](../machine-learning/machine-learning-retrain-models-programmatically.md)」を参照してください)。

## Azure ML バッチ実行ジョブの実行
Azure ML コネクタには、バッチ実行 (BES) ジョブを実行するためのオプションが 4 つあります。1. 入力と出力があるバッチ ジョブ: 実験に Web サービス入力モジュールと出力モジュールがあります。2. 入力と出力がないバッチ ジョブ: 実験に Web サービス入力モジュールと出力モジュールがありません (たとえば、リーダー モジュールとライター モジュールを使用します)。3. 入力のみがあるバッチ ジョブ: 実験には Web サービス入力モジュールがありますが、Web サービス出力モジュールはありません (たとえば、ライター モジュールを使用します)。4. 出力のみがあるバッチ ジョブ: 実験には Web サービス入力モジュールがありませんが、Web サービス出力モジュールはあります (たとえば、リーダー モジュールを使用します)。BES は非同期要求であり、データのサイズとモデルの複雑さによっては、完了までに時間がかかる場合があることに注意してください。ジョブが完了すると、コネクタは出力結果を返します。

### バッチ実行: 入力と出力がある場合
Studio 実験に Web サービス入力モジュールと出力モジュールがある場合、([Storage BLOB アカウントと場所に関する情報を指定する](../machine-learning/machine-learning-consume-web-services.md)) 必要があります。また、実験でセットアップされていれば、グローバル (Web サービス) パラメーターを含めることができます ([Machine Learning Web サービス パラメーター](../machine-learning/machine-learning-web-service-parameters.md))。

![][2]

グローバル パラメーターのフィールドの表示と非表示を切り替えるには、省略記号ボタンをクリックします。これで、フィールドと値のコンマ区切りリストによって 1 つ以上のグローバル (Web サービス) パラメーターを指定できます。

![][3]

BES ジョブの他のバリエーション (Web サービスの入力または出力がないジョブなど) も、コネクタを通じて使用できます。

## 再トレーニングのセットアップ

ML モデルの 1 回限りの再トレーニングまたはスケジュールされた再トレーニングをセットアップするには、再トレーニングのセットアップ アクションを使用します。コネクタから作成したバッチ実行ジョブと組み合わせて、Web サービスのモデルのトレーニングと更新の手順を完了できます。このワークフローでは、コネクタを 2 回使用します。1.最初のコネクタは、BES ジョブを実行し、モデルを再トレーニングして出力を返すために使用されます。この実行の出力には、新しいモデル (.ilearner) の URL が含まれます。また、オプションとして、実験でセットアップしてあれば、評価モジュールの出力 (csv ファイル) の URL を返すこともできます。次の手順では、評価モジュール出力内のデータを使用して、Web サービス内のモデルを置き換えるかどうかを判断できます (たとえば、精度 > 0.85 の場合)。1.2 つ目のコネクタは、再トレーニングをセットアップするために使用されます。1 つ目のコネクタからの出力のパラメーターを使用して、必要に応じて更新モデル条件をチェックし、新しいトレーニング済みモデルで Web サービスを更新します。* たとえば、新しいトレーニング済みモデルへの URL で BES ジョブの出力にアクセスするには、再トレーニング済みモデルの URL のフィールドに「`@{body('besconnector').Results.output2.FullURL}`」と入力します。この例では、トレーニング実験の Web サービス出力の名前が output2 であることを想定しています。* リソース名については、予測実験で保存されたトレーニング済みモデルの完全名を使用してください (たとえば、MyTrainedModel [トレーニング済みモデル])。* 評価結果キーのフィールドには、トレーニング実験の評価モジュールの出力として返された任意のパラメーターを入力できます (含めてあった場合)。使用可能なパラメーターの一覧を表示するには、Azure ML Studio でトレーニング実験の評価モジュールの結果を視覚化します。分類実験の場合、含まれているのは Accuracy、Precision、Recall、F-Score、AUC、Average Log Loss、Training Log Loss などです。

![][4]
 
### スケジュールされた再トレーニング
 
ロジック アプリのトリガーを使用すると、スケジュールに従って実行されるようにコネクタをセットアップできます。そうすることで、新しいデータの取得時に定期的にモデルを再トレーニングできます。BES ジョブはモデルを再トレーニングし、再トレーニング アクションは再トレーニングの完了後にモデルを更新します。
 
![][5]
 
## コネクタの出力 
 
**BES**: バッチ ジョブが正常に完了した後、コネクタの出力には、各 Web サービス出力に関する以下の情報が含まれています。
 
 ![][6]
 
Web サービス出力を含めなかった場合は、これを使用できないことに注意してください (たとえば、Studio の実験の DB への書き込みにライター モジュールを使用している場合)。

**再トレーニング**: 再トレーニングが正常に完了した後、出力には以下の情報が含まれています。

![][7]

## まとめ

ロジック アプリ用 Azure ML コネクタを使用すると、バッチ スコアリング ジョブと再トレーニング ジョブを、オンデマンドまたは定期的なスケジュールで実行することができます。2 つのアクションを組み合わせると、自動的にデータをスコア付けし、Web サービスのモデルを再トレーニング、評価、更新できます。この場合、コードを記述する必要はありません。

 <!--Image references-->
[1]: ./media/app-service-logic-connector-azureml/img1.png
[2]: ./media/app-service-logic-connector-azureml/img2.png
[3]: ./media/app-service-logic-connector-azureml/img3.png
[4]: ./media/app-service-logic-connector-azureml/img4.png
[5]: ./media/app-service-logic-connector-azureml/img5.png
[6]: ./media/app-service-logic-connector-azureml/img6.png
[7]: ./media/app-service-logic-connector-azureml/img7.png

<!---HONumber=AcomDC_0224_2016-->