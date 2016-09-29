<properties
	pageTitle="Web アプリ テンプレートによる Azure Machine Learning Web サービスの使用 | Microsoft Azure"
	description="Azure Marketplace の Web アプリ テンプレートを利用して、Azure Machine Learning で予測 Web サービスを使用します。"
	keywords="Web サービス,運用,REST API,Machine Learning"
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
	ms.date="07/06/2016"
	ms.author="garye;raymondl"/>

# Web アプリケーション テンプレートによる Azure Machine Learning Web サービスの使用

>[AZURE.NOTE] このトピックでは、従来の Web サービスに適用できる手法について説明します。

予測モデルを開発し、Machine Learning Studio を使用するか、R または Python などのツールを使用してそれを Azure Web サービスとしてデプロイしたら、REST API を使用して運用可能モデルにアクセスできます。

REST API を使用して Web サービスにアクセスする方法は、いろいろあります。たとえば、Web サービスをデプロイしたときに生成されたサンプル コード (Machine Learning Studio の Web サービス ダッシュボードにある API ヘルプ ページで利用可能) を使用して、C#、R、または Python でアプリケーションを記述できます。また、作成されたサンプル Microsoft Excel ブック (これも Studio の Web サービス ダッシュボードで利用可能) を使用することもできます。

しかし、Web サービスにアクセスするための最もすばやくて簡単な方法は、[Azure Web アプリケーション Marketplace](https://azure.microsoft.com/marketplace/web-applications/all/)で入手できる Web アプリ テンプレートを利用する方法です。

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Azure Machine Learning Web アプリ テンプレート

Azure Marketplace で入手できる Web アプリ テンプレートを使用すると、Web サービスの入力データと予想される結果を認識するカスタム Web アプリを構築できます。必要な操作は、Web アプリに Web サービスおよびデータへのアクセスを許可することだけで、後の処理はテンプレートによって行われます。

使用できるテンプレートは、次の 2 つです。

- [Azure ML Request-Response Service Web App Template](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/)
- [Azure ML Batch Execution Service Web App Template](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/)

各テンプレートは、Web サービスの API URI とキーを使用してサンプル ASP.NET アプリケーションを作成し、Web サイトとして Azure にデプロイします。Request-Response Service (RRS) テンプレートは、1 つの結果を取得するために Web サービスに 1 行のデータを送信できる Web アプリケーションを作成します。Batch Execution Service (BES) テンプレートは、複数の結果を取得するために多くの行のデータを送信できる Web アプリケーションを作成します。

これらのテンプレートは、コードを記述せずに使用できます。API URI とキーだけを指定すれば、テンプレートによってアプリケーションが構築されます。

## Request-Response Service (RRS) テンプレートの使用方法

Web サービスをデプロイした後は、次の図のように、以下の手順に従って RRS Web アプリケーション テンプレートを使用します。

![RRS Web テンプレートを使用する手順][image1]

1. Machine Learning Studio で **[Web サービス]** タブを開き、アクセスする Web サービスを開きます。**[API キー]** の下に表示されるキーをコピーして保存します。

	![API キー][image3]

2. **REQUEST/RESPONSE** API のヘルプ ページを開きます。ヘルプ ページの上部にある **[要求]** の下で、**要求 URI** 値をコピーして保存します。この値は、次のようなものです。

		https://ussouthcentral.services.azureml.net/workspaces/<workspace-id>/services/<service-id>/execute?api-version=2.0&details=true

	![要求 URI][image4]

3. [Azure ポータル](https://portal.azure.com)に移動して**ログイン**し、**[新規]** をクリックします。**[Azure ML Request-Response Service Web App]** を探して選択し、**[作成]** をクリックします。

	- Web アプリケーションに一意の名前を付けます。Web アプリの URL は、この名前の後に `.azurewebsites.net.` を付けたものです。たとえば、`http://carprediction.azurewebsites.net.` のようになります。

	- 実行している Web サービスの Azure サブスクリプションとサービスを選択します。

	- **[作成]** をクリックします。

	![Web アプリの作成][image5]

4. Azure による Web アプリのデプロイが完了したら、Azure で Web アプリ設定ページの **[URL]** をクリックするか、Web ブラウザーで URL を入力します。たとえば、`http://carprediction.azurewebsites.net.` のように指定します。

5. 初めて Web アプリを実行すると、**API ポスト URL** と **API キー**を指定するように求められます。前に保存しておいた値を入力します。
	- **API ポスト URL** には、API ヘルプ ページの**要求 URI**
	- **API キー**には Web サービス ダッシュボードの **API キー**

	**[Submit]** をクリックします。

	![ポスト URI と API キーの入力][image6]

6. **Web アプリの構成**ページに、現在の Web サービス設定が表示されます。ここで、Web アプリで使用される設定を変更できます。

	> [AZURE.NOTE] ここでの設定の変更は、この Web アプリだけに適用されます。Web サービスの既定の設定は変更されません。たとえば、ここで**説明**を変更した場合、Machine Learning Studio の Web サービス ダッシュボードに表示される説明は変更されません。

	設定し終わったら、**[変更を保存]** をクリックし、**[ホーム ページに移動]** をクリックします。

7. ホーム ページでは、Web サービスに送信する値を入力できます。**[送信]** をクリックすると、結果が返されます。

**構成**ページに戻るには、Web アプリの `setting.aspx` ページに移動します。たとえば、`http://carprediction.azurewebsites.net/setting.aspx.` ページに移動します。もう一度 API キーを入力するように求められます。ページにアクセスして設定を更新するには、キーの入力が必要です。

他の Web アプリと同様に、Azure ポータルでこの Web アプリの停止、再開、または削除を行うことができます。実行中は、ホーム Web アドレスに移動して、新しい値を入力することができます。

## Batch Execution Service (BES) テンプレートの使用方法

BES Web アプリ テンプレートは、RRS テンプレートと同じように使用できます。ただし、作成される Web アプリで複数行のデータを送信し、複数の結果を受け取ることができる点が異なります。

バッチ実行 Web サービスからの結果は、Azure ストレージ コンテナーに格納されます。入力値は、Azure Storage から取得することも、ローカル ファイルから取得することもできます。そのため、Web アプリから返される結果を保持するための Azure ストレージ コンテナーが必要であり、入力データも準備する必要があります。

![BES Web テンプレートを使用する手順][image2]

1. RRS テンプレートと同じ手順で、BES Web アプリを作成します。ただし、以下の点が異なります。
	- Web サービスの**バッチ実行** API ヘルプ ページから**要求 URI** を取得します。
	- [Azure ML Batch Execution Service Web App Template](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/) に移動して、Azure Marketplace で BES テンプレートを開き、**[Web アプリを作成]** をクリックします。

2. 結果の格納先を指定するには、Web アプリのホーム ページで格納先コンテナーの情報を入力します。Web アプリが入力値を取得する場所として、ローカル ファイルまたは Azure ストレージ コンテナーも指定します。**[Submit]** をクリックします。

	![ストレージ情報][image7]

ページが表示され、ジョブの状態が示されます。ジョブが完了すると、Azure BLOB ストレージ内の結果の場所が表示されます。結果をローカル ファイルにダウンロードするためのオプションも表示されます。

## BLOB の詳細

詳細については、以下のページをご覧ください。

- Machine Learning Studio で Machine Learning の実験を作成する方法については、「[Azure Machine Learning Studio で初めての実験を作成する](machine-learning-create-experiment.md)」を参照してください。

- Machine Learning の実験を Web サービスとしてデプロイする方法については、「[Azure Machine Learning Web サービスをデプロイする](machine-learning-publish-a-machine-learning-web-service.md)」を参照してください。

- Web サービスにアクセスするための別の方法については、「[Azure Machine Learning Web サービスを使用する方法](machine-learning-consume-web-services.md)」を参照してください。


[image1]: media\machine-learning-consume-web-service-with-web-app-template\rrs-web-template-flow.png
[image2]: media\machine-learning-consume-web-service-with-web-app-template\bes-web-template-flow.png
[image3]: media\machine-learning-consume-web-service-with-web-app-template\api-key.png
[image4]: media\machine-learning-consume-web-service-with-web-app-template\post-uri.png
[image5]: media\machine-learning-consume-web-service-with-web-app-template\create-web-app.png
[image6]: media\machine-learning-consume-web-service-with-web-app-template\web-service-info.png
[image7]: media\machine-learning-consume-web-service-with-web-app-template\storage.png

<!---HONumber=AcomDC_0914_2016-->