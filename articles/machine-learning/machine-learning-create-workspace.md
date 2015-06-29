<properties 
	pageTitle="Machine Learning ワークスペースの作成 | Microsoft Azure" 
	description="Azure Machine Learning Studio のワークスペースの作成方法" 
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
	ms.date="04/07/2015" 
	ms.author="garye"/>


# Azure Machine Learning ワークスペースの作成 

Azure Machine Learning Studio を使用するには、機械学習ワークスペースが必要です。このワークスペースには、実験を管理および公開するのに必要なツールが用意されています。

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## ワークスペースを作成するには

1. Microsoft Azure アカウントにサインインします。
2. Microsoft Azure サービス パネルで、**[MACHINE LEARNING]** をクリックします。

    ![Machine Learning サービス][1]

3. ウィンドウの下部にある **[+新規]** をクリックします。
4. **[Data Services]**、**[MACHINE LEARNING]**、**[簡易作成]** の順にクリックします。

	![新しいワークスペースの簡易作成][3]

5. **[ワークスペース名]** にワークスペースを入力し、**[ワークスペースの所有者]** を指定します。ワークスペースの所有者は有効な Microsoft アカウントである必要があります (例: name@outlook.com)。

    > [AZURE.NOTE]後からワークスペースに招待することで、作業中の実験を自分以外のユーザーと共有することができます。これは、Machine Learning Studio の **[設定]** ページから実行でき、必要な情報は各ユーザーの Microsoft アカウントまたは組織アカウントだけです。

6. **[場所]** に Azure の場所を指定し、**[ストレージ アカウント]** に既存の Azure のストレージ アカウントを入力するか、**[新しいストレージ アカウントの作成]** を選択して新たに作成します。
7. **[ML ワークスペースを作成]** をクリックします。

Machine Learning ワークスペースが作成されると、**機械学習**のページの一覧に表示されます。

ワークスペースの管理方法の詳細については、「[Azure Machine Learning ワークスペースの管理]」をご覧ください。ワークスペースの作成で問題が発生した場合は、「[Troubleshooting guide: Create and connect to an Machine Learning workspace (トラブルシューティング ガイド: Machine Learning ワークスペースの作成と接続)]」をご覧ください。

[Azure Machine Learning ワークスペースの管理]: machine-learning-manage-workspace.md
[Troubleshooting guide: Create and connect to an Machine Learning workspace (トラブルシューティング ガイド: Machine Learning ワークスペースの作成と接続)]: machine-learning-troubleshooting-creating-ml-workspace.md
 
<!-- ![List of Machine Learning workspaces][2] -->

<!--Anchors-->
[To create a workspace]: #createworkspace

<!--Image references-->
[1]: media/machine-learning-create-workspace/cw1.png
[2]: media/machine-learning-create-workspace/cw2.png
[3]: media/machine-learning-create-workspace/cw3.png



<!--Link references-->

<!---HONumber=58_postMigration-->