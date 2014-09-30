<properties title="Troubleshooting Guide: Creating and connecting to an Azure Machine Learning workspace" pageTitle="Troubleshooting Guide: Creating and connecting to an Azure Machine Learning workspace | Azure" description="Solutions for common issues in creating and connecting to an Azure Machine Learning workspace " metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

# トラブルシューティング ガイド: Azure Machine Learning ワークスペースの作成と接続

このガイドでは、Azure ML ワークスペースの設定に伴い、よく生じることのある問題の解決策を説明します。

## ワークスペースの所有者

新しい Azure ML ワークスペースを作成する場合、"ワークスペースの所有者" フィールドに入力する ID は、有効な Microsoft アカウント (以前の Windows Live ID) である必要があります。たとえば、<john-contoso@live.com> または <john-contoso@hotmail.com> のようなアカウントです。会社の電子メール アカウントなどの Microsoft アカウント以外のアカウントを指定することはできません。無料の Microsoft アカウントを作成するには、[www.live.com][] にアクセスします。

## 許可されたリージョン

Azure ML は、現在、米国中南部でパブリック プレビューが行われています。サブスクリプションに米国中南部が含まれていない場合は、"許可されたリージョンにサブスクリプションがありません。許可されたリージョン: 米国中南部。" というエラーが表示されます。

これを解決するには、Azure ポータルから [Microsoft サポートに問い合わせる] (以下に表示) を選択し、**[サポートの種類]** として **[課金]** を選択して、このリージョンをサブスクリプションに追加します。Azure ML では、時間の経過と共に複数のリージョンが追加されます。

![Contact Microsoft support][]

## ストレージ アカウント

Azure ML サービスでは、データを格納するためのストレージ アカウントが必要です。米国中南部の既存のストレージ アカウントを使用することもできれば、新しい ML ワークスペースの作成時に新しいストレージ アカウントを作成することもできます (新しいストレージ アカウントを作成するためのクォータがある場合)。新しいストレージ アカウントを作成できるかどうかを確認するには、Azure ポータルで、**[設定]**、**[使用状況]** の順に選択します。

![Create workspace][]

新しい ML ワークスペースを作成した後、ワークスペースの所有者として指定した Microsoft アカウントを使用して ML Studio にサインインできます。以下のスクリーンショットのように、"ワークスペースが見つかりません" というエラーが表示された場合は、次の手順で問題を修正してください。

![Workspace not found][]

1.  ブラウザーの Cookie をフラッシュします。

    Internet Explorer を使用している場合は、右上隅の **[ツール]** をクリックして、**[インターネット オプション]** を選択します。

    ![Internet options][]

    **[全般]** タブで、**[削除]** をクリックします。

    ![General tab][]

    **[閲覧の履歴の削除]** ダイアログで、**[クッキーと Web サイト データ]** が選択されていることを確認し、**[削除]** をクリックします。

    ![Delete cookies][]

2.  Cookie がフラッシュされた後、ブラウザーを再起動して、[][]<https://studio.azureml.net></a> へ移動します。ユーザー名とパスワードの入力が求められたら、ワークスペースの所有者として指定したアカウントと同じ Microsoft アカウントを入力します。

目的は、可能な限り円滑でシームレスに Azure ML を体験していただくことです。お客様の操作の円滑化をお手伝いできるよう、コメントや問題がありましたら、下記または [Azure ML フォーラム][]に投稿してください。

  [www.live.com]: http://www.live.com
  [Contact Microsoft support]: ./media/machine-learning-troubleshooting-creating-ml-workspace/screen1.png
  [Create workspace]: ./media/machine-learning-troubleshooting-creating-ml-workspace/screen2.png
  [Workspace not found]: ./media/machine-learning-troubleshooting-creating-ml-workspace/screen3.png
  [Internet options]: ./media/machine-learning-troubleshooting-creating-ml-workspace/screen4.png
  [General tab]: ./media/machine-learning-troubleshooting-creating-ml-workspace/screen5.png
  [Delete cookies]: ./media/machine-learning-troubleshooting-creating-ml-workspace/screen6.png
  []: https://studio.azureml.net
  [Azure ML フォーラム]: http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=MachineLearning
