---
title: Web アプリ テンプレートによる Studio Web サービスの使用 - Azure Machine Learning Studio | Microsoft Docs
description: Azure Marketplace の Web アプリ テンプレートを利用して、Azure Machine Learning で予測 Web サービスを使用します。
keywords: Web サービス,運用,REST API,Machine Learning
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: e0d71683-61b9-4675-8df5-09ddc2f0d92d
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.openlocfilehash: 6a6da1af937338637cd875abe4e9d539b2e2b6e2
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53273886"
---
# <a name="consume-an-azure-machine-learning-studio-web-service-by-using-a-web-app-template"></a>Web アプリ テンプレートによる Azure Machine Learning Studio Web サービスの使用

次のものを使って予測モデルを開発し、Azure Web サービスとしてデプロイできます。
- Azure Machine Learning Studio。
- R や Python などのツール。 

その後、運用化されたモデルに REST API を使ってアクセスできます。

REST API を使用して Web サービスにアクセスする方法は、いろいろあります。 たとえば、Web サービスをデプロイしたときに生成されたサンプル コードを使って、C#、R、または Python でアプリケーションを記述できます  (サンプル コードは、[Machine Learning Web サービス ポータル](https://services.azureml.net/quickstart)または Machine Learning Studio の Web サービス ダッシュボードで入手できます)。または同時に作成された Microsoft Excel ブックのサンプルを使用することもできます。

ただし、Web サービスにアクセスする最も速くて簡単な方法は、[Azure Marketplace](https://azure.microsoft.com/marketplace/web-applications/all/) で入手できる Web アプリ テンプレートを利用する方法です。



## <a name="azure-machine-learning-web-app-templates"></a>Azure Machine Learning Web アプリ テンプレート
Azure Marketplace で入手できる Web アプリ テンプレートを使用すると、Web サービスの入力データと予想される結果を認識するカスタム Web アプリを構築できます。 必要な操作は、Web アプリに Web サービスおよびデータへのアクセスを許可することだけで、後の処理はテンプレートによって行われます。

使用できるテンプレートは、次の 2 つです。

* [Azure ML Request-Response Service Web App Template](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/)
* [Azure ML Batch Execution Service Web App Template](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/)

各テンプレートは、Web サービスの API URI とキーを使って、ASP.NET アプリケーションのサンプルを作成します。 その後、テンプレートはアプリケーションを Web サイトとして Azure にデプロイします。 

Request-Response Service (RRS) テンプレートは、1 つの結果を取得するために Web サービスに 1 行のデータを送信できる Web アプリを作成します。 Batch Execution Service (BES) テンプレートは、複数の結果を取得するために多くの行のデータを送信できる Web アプリを作成します。

これらのテンプレートは、コードを記述せずに使用できます。 API キーと URI を指定するだけで、テンプレートによりアプリケーションが自動的に構築されます。

Web サービスの API キーと要求 URI を取得する手順は次のとおりです。

1. [Web サービス ポータル](https://services.azureml.net/quickstart)で、上部の **[Web サービス]** を選びます。 または、クラシック Web サービスの場合は、**[Classic Web Services]\(クラシック Web サービス\)** を選びます。
2. アクセスする Web サービスを選びます。
3. クラシック Web サービスの場合は、アクセスするエンドポイントを選びます。
4. 上部の **[Consume]\(使用\)** を選びます。
5. プライマリ キーまたはセカンダリ キーをコピーし、保存します。
6. RRS テンプレートを作成している場合は、**[要求 - 応答]** の URI をコピーして保存します。 BES テンプレートを作成している場合は、**[Batch Requests]\(バッチ要求\)** の URI をコピーして保存します。


## <a name="how-to-use-the-request-response-service-template"></a>要求応答サービス テンプレートの使用方法
次の図に示すように、RRS Web アプリ テンプレートを使用する手順に従います。

![RRS Web テンプレートを使用する手順][image1]


<!--    ![API Key][image3] -->

<!-- This value will look like this:
   
        https://ussouthcentral.services.azureml.net/workspaces/<workspace-id>/services/<service-id>/execute?api-version=2.0&details=true
   
    ![Request URI][image4] -->

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. **[新規]** を選び、**[Azure ML Request-Response Service Web App]\(Azure ML 要求応答サービス Web アプリ\)** を探して選んで、**[作成]** を選びます。 
3. **[作成]** ウィンドウで次のようにします。
   
   * Web アプリケーションに一意の名前を付けます。 Web アプリの URL は、この名前の後に **.azurewebsites.net** を付けたものです。 例: **http://carprediction.azurewebsites.net**
   * 実行している Web サービスの Azure サブスクリプションとサービスを選択します。
   * **作成**を選択します。
     
   ![Web アプリの作成][image5]

4. Azure による Web アプリのデプロイが完了したら、Azure の Web アプリ設定ページで **[URL]** を選ぶか、Web ブラウザーで URL を入力します。 たとえば、「**http://carprediction.azurewebsites.net**」と入力します。
5. 初めて Web アプリを実行すると、**API ポスト URL** と **API キー**の指定を求められます。 前に保存した値 (それぞれ、要求 URI と API キー) を入力します。 **[Submit]\(送信\)** をクリックします。
     
   ![ポスト URI と API キーの入力][image6]

6. **Web アプリの構成** ページに、現在の Web サービス設定が表示されます。 ここで、Web アプリが使う設定を変更できます。
   
   > [!NOTE]
   > ここでの設定の変更は、この Web アプリだけに適用されます。 Web サービスの既定の設定は変更されません。 たとえば、ここで **[説明]** のテキストを変更しても、Machine Learning Studio の Web サービス ダッシュボードに表示される説明は変更されません。
   > 
   > 
   
    設定が終わったら、**[変更を保存]** を選んで、**[ホーム ページに移動]** を選びます。

7. Web サービスに送信する値をホーム ページで入力できます。 入力を完了して **[送信]** を選ぶと、結果が返されます。

**[構成]** ページに戻るには、Web アプリの **setting.aspx** ページに移動します。 たとえば、**http://carprediction.azurewebsites.net/setting.aspx** に移動します。 再び、API キーの入力を求められます。 ページにアクセスして設定を更新するには、API キーを入力する必要があります。

他の Web アプリと同様に、Azure ポータルでこの Web アプリの停止、再開、または削除を行うことができます。 実行中は、ホーム Web アドレスに移動して、新しい値を入力することができます。

## <a name="how-to-use-the-batch-execution-service-template"></a>Batch Execution Service テンプレートの使用方法
RRS テンプレートと同じ方法で、BES Web アプリ テンプレートを使うことができます。 違いは、作成した Web アプリを使うと、複数行のデータを送信し、複数の結果を受信できることです。

バッチ実行 Web サービスの入力値は、Azure Storage から取得することも、ローカル ファイルから取得することもできます。 結果は、Azure Storage コンテナーに格納されます。 そのため、Web アプリから返される結果を保持するには、Azure Storage コンテナーが必要です。 また、入力データを準備する必要もあります。

![BES Web テンプレートを使用する手順][image2]

1. RRS テンプレートと同じ手順で、BES Web アプリを作成します。 ただし、この場合は、[[Azure ML Batch Execution Service Web App Template]\(Azure ML バッチ実行サービス Web アプリ テンプレート\)](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/) に移動し、Azure Marketplace で BES テンプレートを開きます。 **[Web アプリの作成]** を選びます。

2. 結果の格納先を指定するには、Web アプリのホーム ページで格納先コンテナーの情報を入力します。 Web アプリが入力値を取得する場所として、ローカル ファイルまたは Azure ストレージ コンテナーも指定します。
   **[Submit]\(送信\)** をクリックします。
   
   ![ストレージ情報][image7]

ページが表示され、ジョブの状態が示されます。 ジョブが完了すると、Azure Blob Storage 内の結果の場所が表示されます。 結果をローカル ファイルにダウンロードするためのオプションも表示されます。

## <a name="for-more-information"></a>BLOB の詳細
関連情報:

* Machine Learning Studio で Machine Learning の実験を作成する方法については、「[Azure Machine Learning Studio での初めての実験の作成](create-experiment.md)」をご覧ください。
* Machine Learning の実験を Web サービスとしてデプロイする方法については、「[Azure Machine Learning Web サービスをデプロイする](publish-a-machine-learning-web-service.md)」をご覧ください。
* Web サービスにアクセスするための別の方法については、「[Azure Machine Learning Web サービスを使用する方法](consume-web-services.md)」をご覧ください。

[image1]: media/consume-web-service-with-web-app-template/rrs-web-template-flow.png
[image2]: media/consume-web-service-with-web-app-template/bes-web-template-flow.png
[image3]: media/consume-web-service-with-web-app-template/api-key.png
[image4]: media/consume-web-service-with-web-app-template/post-uri.png
[image5]: media/consume-web-service-with-web-app-template/create-web-app.png
[image6]: media/consume-web-service-with-web-app-template/web-service-info.png
[image7]: media/consume-web-service-with-web-app-template/storage.png
