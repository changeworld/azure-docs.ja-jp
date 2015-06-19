<properties 
	pageTitle="トラブルシューティング ガイド:Azure Machine Learning ワークスペースの作成と接続 | Azure" 
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
	ms.date="08/25/2014" 
	ms.author="garye"/>


#トラブルシューティング ガイド: Azure Machine Learning ワークスペースの作成と接続

このガイドでは、Azure ML ワークスペースの設定に伴い、よく生じることのある問題の解決策を説明します。

##ワークスペースの所有者

新しい Azure ML ワークスペースを作成する場合、"ワークスペースの所有者" フィールドに入力する ID は、有効な Microsoft アカウント (以前の Windows Live ID) である必要があります。たとえば、john-contoso@live.com や john-contoso@hotmail.com のようなアカウントです。会社の電子メール アカウントなどの Microsoft アカウント以外のアカウントを指定することはできません。無料の Microsoft アカウントを作成するには、[www.live.com](http://www.live.com) にアクセスします。 

##許可されたリージョン

Azure ML は、現在、米国中南部でパブリック プレビューが行われています。サブスクリプションに米国中南部が含まれていない場合は、"許可されたリージョンにサブスクリプションがありません。許可されたリージョン: 米国中南部。" というエラーが表示されます。 

これを解決するには、Azure ポータルから [Microsoft サポートに問い合わせる] (以下に表示) を選択し、**[サポートの種類]** として **[課金]** を選択して、このリージョンをサブスクリプションに追加するよう依頼します。Azure ML では、時間の経過と共に複数のリージョンが追加されます。

![Contact Microsoft support][screen1]

##ストレージ アカウント
 
Azure ML サービスでは、データを格納するためのストレージ アカウントが必要です。米国中南部の既存のストレージ アカウントを使用することもできれば、新しい ML ワークスペースの作成時に新しいストレージ アカウントを作成することもできます (新しいストレージ アカウントを作成するためのクォータがある場合)。新しいストレージ アカウントを作成できるかどうかを確認するには、Azure ポータルで、**[設定]**、**[使用状況]** の順に選択します。

![Create workspace][screen2]

新しい ML ワークスペースを作成した後、ワークスペースの所有者として指定した Microsoft アカウントを使用して ML Studio にサインインできます。以下のスクリーンショットのように、"ワークスペースが見つかりません" というエラーが表示された場合は、次の手順で問題を修正してください。

![Workspace not found][screen3]

1. ブラウザーの Cookie をフラッシュします。

	Internet Explorer を使用している場合は、右上隅の **[ツール]** をクリックして、**[インターネット オプション]** を選択します。  

	![Internet options][screen4]

	**[全般]** タブで、**[削除]** をクリックします。

	![General tab][screen5]

	**[閲覧の履歴の削除]** ダイアログで、**[クッキーと Web サイト データ]** が選択されていることを確認し、**[削除]** をクリックします。

	![Delete cookies][screen6]

2. Cookie が削除されたら、ブラウザーを再起動して、[https://studio.azureml.net](https://studio.azureml.net) に移動します。ユーザー名とパスワードの入力が求められたら、ワークスペースの所有者として指定したアカウントと同じ Microsoft アカウントを入力します。

目的は、可能な限り円滑でシームレスに Azure ML を体験していただくことです。お客様の操作の円滑化をお手伝いできるよう、コメントや問題がありましたら、下記または [Azure ML フォーラム](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=MachineLearning)に投稿してください。 

[screen1]:./media/machine-learning-troubleshooting-creating-ml-workspace/screen1.png
[screen2]:./media/machine-learning-troubleshooting-creating-ml-workspace/screen2.png
[screen3]:./media/machine-learning-troubleshooting-creating-ml-workspace/screen3.png
[screen4]:./media/machine-learning-troubleshooting-creating-ml-workspace/screen4.png
[screen5]:./media/machine-learning-troubleshooting-creating-ml-workspace/screen5.png
[screen6]:./media/machine-learning-troubleshooting-creating-ml-workspace/screen6.png

<!--HONumber=46--> 
 