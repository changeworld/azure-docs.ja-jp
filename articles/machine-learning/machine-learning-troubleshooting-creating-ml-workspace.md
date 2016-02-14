<properties
	pageTitle="トラブルシューティング: Machine Learning ワークスペースの作成と接続 | Microsoft Azure"
	description="Azure Machine Learning ワークスペースの作成と接続に関する一般的な問題の解決策"
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
	ms.date="02/03/2016"
	ms.author="garye"/>


# トラブルシューティング ガイド: Machine Learning ワークスペースの作成と接続

このガイドでは、Azure Machine Learning ワークスペースの設定に伴い、よく生じることのある問題の解決策を説明します。

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## ワークスペースの所有者

新しい Azure Machine Learning ワークスペースを作成する場合、[ワークスペースの所有者] フィールドに入力する ID は、有効な Microsoft アカウント (以前の Windows Live ID) である必要があります。たとえば john-contoso@live.com や john-contoso@hotmail.com のようなアカウントです。会社の電子メール アカウントなどの Microsoft アカウント以外のアカウントを指定することはできません。無料の Microsoft アカウントを作成するには、[www.live.com](http://www.live.com) にアクセスします。

Azure クラシック ポータルにサインインするために使用するアカウントでワークスペースを作成する場合、所有者としてそのアカウントを指定しない限り、ワークスペースを*開く*権限は自動的には与えられないことに注意してください。Machine Learning Studio でワークスペースを開くには、ワークスペースの所有者として定義された Microsoft アカウントにサインインするか、所有者からワークスペースへの参加の招待を受け取る必要があります。ただし、Azure クラシック ポータルからワークスペースを*管理*でき、所有者を変更したり、アクセスを構成したりすることができます。

ワークスペースの管理方法の詳細については、「[Azure Machine Learning ワークスペースの管理]」をご覧ください。

[Azure Machine Learning ワークスペースの管理]: machine-learning-manage-workspace.md

## 許可されたリージョン

現在、Machine Learning は一部のリージョンでのみ利用できます。サブスクリプションに対象のリージョンが含まれていない場合は、"許可されたリージョンにサブスクリプションがありません" というエラー メッセージが表示されることがあります。

サブスクリプションへのリージョンの追加を要求するには、Azure クラシック ポータルの **[Microsoft サポートに問い合わせる]** を選択し、問題の種類として **[課金]** を選択し、プロンプトに従って要求を送信します。

![Microsoft サポートに問い合わせる][screen1]

## ストレージ アカウント

Machine Learning サービスでは、データを格納するためのストレージ アカウントが必要です。既存のストレージ アカウントを使用するか、新しい Machine Learning ワークスペースの作成時に新しいストレージ アカウントを作成することができます (新しいストレージ アカウントを作成するためのクォータがある場合)。

<!-- These instructions no longer work, but I'm not sure what to replace them with
To see if you can create a new storage account, in the Classic Portal, go to **Settings** and then click **Usage**.
-->

![ワークスペースの作成][screen2]

新しい Machine Learning ワークスペースを作成した後、ワークスペースの所有者として指定した Microsoft アカウントを使用して Machine Learning Studio にサインインできます。エラー メッセージ「ワークスペースが見つかりません」(次のスクリーン ショットに類似) が発生する場合は、次の手順に従ってブラウザーの Cookie を削除します。

![Workspace not found][screen3]

**ブラウザーの Cookie を削除するには**

Internet Explorer を使用している場合は、右上隅の **[ツール]** をクリックして、**[インターネット オプション]** を選択します。

![インターネット オプション][screen4]

**[全般]** タブで、**[削除]** をクリックします。

![[全般] タブ][screen5]

**[閲覧の履歴の削除]** ダイアログ ボックスで、**[クッキーと Web サイト データ]** が選択されていることを確認し、**[削除]** をクリックします。

![Cookie の削除][screen6]

Cookie が削除されたら、ブラウザーを再起動し、[Microsoft Azure Machine Learning](https://studio.azureml.net) ページに移動します。ユーザー名とパスワードの入力が求められたら、ワークスペースの所有者として指定したアカウントと同じ Microsoft アカウントを入力します。

目的は、可能な限り円滑でシームレスに Machine Learning を体験していただくことです。お客様の操作の円滑化をお手伝いできるよう、コメントや問題がありましたら、[Azure Machine Learning フォーラム](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning)に投稿してください。

[screen1]: media/machine-learning-troubleshooting-creating-ml-workspace/screen1.png
[screen2]: media/machine-learning-troubleshooting-creating-ml-workspace/screen2.png
[screen3]: media/machine-learning-troubleshooting-creating-ml-workspace/screen3.png
[screen4]: media/machine-learning-troubleshooting-creating-ml-workspace/screen4.png
[screen5]: media/machine-learning-troubleshooting-creating-ml-workspace/screen5.png
[screen6]: media/machine-learning-troubleshooting-creating-ml-workspace/screen6.png

<!---HONumber=AcomDC_0204_2016-->