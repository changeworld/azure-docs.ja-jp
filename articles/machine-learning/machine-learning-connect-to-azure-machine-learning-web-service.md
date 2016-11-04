---
title: Machine Learning Web サービスを発行する | Microsoft Docs
description: C# または Python を使用して、Azure Machine Learning Web サービスに承認キーを利用して接続します。
services: machine-learning
documentationcenter: ''
author: garyericson
manager: jhubbard
editor: cgronlun

ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: garye

---
# Azure Machine Learning Web サービスに接続する
開発者が体験する Azure Machine Learning は、入力データから予測をリアルタイムまたはバッチ モードで作成する Web サービス API です。Azure Machine Learning Studio を使用して予測を作成し、Azure Machine Learning Web サービスをデプロイします。

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Machine Learning Studio を使用して Machine Learning Web サービスを作成してデプロイする方法の詳細については、次を参照してください。

* Machine Learning Studio で実験を作成する方法については、[初めての実験を作成する方法のチュートリアル](machine-learning-create-experiment.md)をご覧ください。
* Web サービスをデプロイする方法の詳細については、「[Azure Machine Learning Web サービスをデプロイする](machine-learning-publish-a-machine-learning-web-service.md)」をご覧ください。
* Machine Learning 全般の詳細については、[Machine Learning ドキュメント センター](https://azure.microsoft.com/documentation/services/machine-learning/)をご覧ください。

## Azure Machine Learning Web サービス
Azure Machine Learning Web サービスを使用して、外部のアプリケーションが Machine Learning のワークフローのスコア付けモデルとリアルタイムで通信します。Machine Learning Web サービスの呼び出しは、予測結果を外部のアプリケーションに返します。Machine Learning Web サービスの呼び出しを実行するために、予測のデプロイ時に作成される API キーを渡します。Machine Learning Web サービスは、Web プログラミング プロジェクトでよく選択されるアーキテクチャの REST に基づいています。

Azure Machine Learning には、2 種類のサービスがあります。

* 要求応答サービス (RRS) – 待ち時間が短く拡張性の高い、Machine Learning Studio から作成およびデプロイされるステートレスなモデルへのインターフェイスを提供するサービス。
* バッチ実行サービス (BES) – データ レコードのバッチをスコア付けする非同期のサービス。

Machine Learning Web サービスの詳細については、「[Azure Machine Learning Web サービスをデプロイする](machine-learning-publish-a-machine-learning-web-service.md)」をご覧ください。

## Azure Machine Learning の承認キーを取得する
実験をデプロイすると、Web サービスの API キーが生成されます。キーの取得元は、実験を新しい Web サービスとしてデプロイしたか、従来の Web サービスとしてデプロイしたかによって決まります。

## 従来の Web サービス
 Machine Learning Studio または Azure ポータルからキーを取得できます。

### Machine Learning Studio
1. Machine Learning Studio で、左側の **[Web サービス]** をクリックします。
2. Web サービスをクリックします。**[ダッシュボード]** タブに **[API キー]** があります。

### Azure ポータル
1. 左側の **[Machine Learning]** をクリックします。
2. ワークスペースをクリックします。
3. **[Web サービス]** をクリックします。
4. Web サービスをクリックします。
5. エンドポイントをクリックします。[API キー] が右下にあります。

## 新しい Web サービス
新しい Machine Learning Web サービスの API キーを取得するには、[Microsoft Azure Machine Learning Web サービス](https://services.azureml.net/quickstart) ポータルにログインする必要があります。

1. Azure Machine Learning Web サービス ポータルで、上部メニューの **[WEB サービス]** をクリックします。
2. キーを取得する Web サービスをクリックします。
3. 上部のメニューで **[Consume(使用)]** をクリックします。
4. **主キー**をコピーして保存します。

## <a id="connect"></a>Machine Learning Web サービスに接続する
HTTP 要求と応答をサポートする任意のプログラミング言語を使用して、Machine Learning Web サービスに接続することができます。Machine Learning Web サービス ヘルプ ページから、C#、Python、および R の例を表示できます。

**Machine Learning API ヘルプ** Machine Learning API ヘルプは、Web サービスをデプロイするときに作成されます。「[Azure Machine Learning チュートリアル - Web サービスをデプロイする](machine-learning-walkthrough-5-publish-web-service.md)」を参照してください。Machine Learning API ヘルプには、予測 Web サービスの詳細が含まれます。

**従来の Web サービスの Machine Learning API ヘルプを表示するには** Machine Learning Studio の場合:

1. **[Web サービス]** をクリックします。
2. Web サービスをクリックします。
3. **[API ヘルプ ページ]** - **[要求/応答]** または **[バッチの実行]** をクリックします。

**新しい Web サービスの Machine Learning API ヘルプを表示するには** Azure Machine Learning Web サービス ポータルの場合:

1. 上部のメニューで **[Web サービス]** をクリックします。
2. キーを取得する Web サービスをクリックします。

**[Consume (使用)]** をクリックして、[要求/応答] および [バッチの実行] サービスの URI と、C#、R、および Python のサンプル コードを取得します。

**Swagger API** をクリックして、指定した URI から呼び出される API の Swagger ベースのドキュメントを取得します。

### C# のサンプル
Machine Learning Web サービスに接続するには、ScoreData を渡す **HttpClient** を使用します。ScoreData には、ScoreData を表す数値機能の n 次元ベクトルである FeatureVector が含まれています。Machine Learning サービスの認証には、API キーを使用します。

Machine Learning Web サービスに接続するには、**Microsoft.AspNet.WebApi.Client** Nuget パッケージをインストールする必要があります。

**Microsoft.AspNet.WebApi.Client Nuget in Visual Studio をインストールする**

1. 「UCI からデータセットをダウンロード: 成人向け 2 クラス データセット」Web サービスを発行します。
2. **[ツール]** > **[Nuget パッケージ マネージャー]** > **[Package Manager Console]** をクリックします。
3. **[Install-Package Microsoft.AspNet.WebApi.Client]** を選択します。

**サンプル コードを実行するには**

1. Machine Learning サンプル コレクションに含まれる「サンプル 1: UCI からデータセットをダウンロード: 成人向け 2 クラス データセット」実験を発行します。
2. Web サービスからのキーを持つ apiKey を割り当てます。前述の「**Azure Machine Learning の承認キーを取得する**」をご覧ください。
3. 要求の URI を含む serviceUri を割り当てます。

### Python サンプル
Machine Learning Web サービスに接続するには、ScoreData を渡す **urllib2** ライブラリを使用します。ScoreData には、ScoreData を表す数値機能の n 次元ベクトルである FeatureVector が含まれています。Machine Learning サービスの認証には、API キーを使用します。

**サンプル コードを実行するには**

1. Machine Learning サンプル コレクションに含まれる「サンプル 1: UCI からデータセットをダウンロード: 成人向け 2 クラス データセット」実験をデプロイします。
2. Web サービスからのキーを持つ apiKey を割り当てます。前述の「**Azure Machine Learning の承認キーを取得する**」をご覧ください。
3. 要求の URI を含む serviceUri を割り当てます。

<!---HONumber=AcomDC_0914_2016-->