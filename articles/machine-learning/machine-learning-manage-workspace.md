<properties 
	pageTitle="Machine Learning ワークスペースの管理 | Microsoft Azure" 
	description="Azure Machine Learning ワークスペースへのアクセスを管理し、ML API Web サービスをデプロイおよび管理します" 
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
	ms.date="04/29/2015" 
	ms.author="garye"/>


# Azure Machine Learning ワークスペースの管理 
Microsoft Azure 管理ポータルを使用すると、Machine Learning ワークスペースで次の操作を管理できます。

- ワークスペースの使用方法を監視する
- アクセスを許可または拒否するようにワークスペースを構成する
- ワークスペースで作成した Web サービスを管理する
- ワークスペースを削除する

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

また、[ダッシュボード] タブには、ワークスペースの使用状況の概要とワークスペース情報の要約が表示されます。

> [AZURE.TIP]Azure Machine Learning Studio の **[WEB サービス]** タブで、Machine Learning Web サービスを追加、更新、削除できます。

ワークスペースを管理するには

1.	Azure サブスクリプションに関連付けられているアカウントを使用して、Microsoft Azure アカウントにサインインします。
2.	Microsoft Azure サービス パネルの [[Microsoft Azure 管理ポータル]](https://manage.windowsazure.com/) で、**[MACHINE LEARNING]** をクリックします。
3.	管理するワークスペースをクリックします。

ワークスペースのページには次の 3 つのタブがあります。

- **[ダッシュボード]** - ワークスペースの使用状況と情報を表示します
- **[構成]** - ワークスペースへのアクセスを管理します
- **[WEB サービス]** - このワークスペースから公開されている Web サービスを管理できます

  
## ワークスペースの使用方法を監視するには

**[ダッシュボード]** タブをクリックします。

ダッシュボードには、ワークスペースの全体的な使用状況と、ワークスペース情報の概要が表示されます。

- **[コンピューティング]** グラフは、ワークスペースで使用されているコンピューティング リソースを表示します。ビューを変更して相対値または絶対値を表示したり、グラフに表示される期間を変更したりできます。
- **[使用状況の概要]** は、ワークスペースで使用されている Azure ストレージを表示します。注: Azure Machine Learning のパブリック プレビュー中はストレージ監視を使用できません。
- **[概要]** は、ワークスペース情報や役に立つリンクを提供します。

> [AZURE.NOTE]**[OPEN IN STUDIO]** リンクは、現在サインイ中の Microsoft アカウントを使用して Machine Learning Studio を開きます。Azure ポータルへのサインインに使用する Microsoft アカウントでワークスペースを作成する場合は、ワークスペースを開く権限が自動的には付与されません。ワークスペースを開くには、ワークスペースの所有者として定義された Microsoft アカウントにサインインするか、所有者からワークスペースへの参加の招待を受け取る必要があります。


## ユーザーのアクセスを許可または一時停止するには ##

**[構成]** タブをクリックします。

[構成] タブで次の操作を実行できます。
 
- [拒否] をクリックして、Machine Learning ワークスペースへのアクセスを中断します。ユーザーは、Machine Learning Studio でワークスペースを開くことができなくなります。アクセスを復元するには、[許可] をクリックします。
- 他の Microsoft アカウントを指定して、ワークスペースの所有者を変更します。 

Machine Learning Studio のワークスペースにアクセスできるユーザーを管理するには、**[OPEN IN STUDIO]** をクリックします(**OPEN IN STUDIO** については上記をご覧ください)。Machine Learning Studio でワークスペースが開きます。ここで、**[設定]** タブの **[ユーザー]** をクリックします。**[INVITE MORE USERS]** をクリックしてユーザーにワークスペースへのアクセス権を付与するか、ユーザーを選択して **[削除]** をクリックします。


## このワークスペースの Web サービスを管理するには

**[Web サービス]** タブをクリックします。

このワークスペースから発行された Web サービスの一覧が表示されます。Web サービスを管理するには、一覧で名前をクリックして Web サービス ページを開きます。

Web サービスには、1 つ以上のエンドポイントが定義されている場合があります。

- "既定" のエンドポイントの他に追加のエンドポイントを定義できます。エンドポイントを追加するには、ページの下部にある **[エンドポイントの追加]** をクリックします。

- エンドポイントを削除するには ("既定" のエンドポイントを削除することはできません)、エンドポイント行の名前以外の任意の場所をクリックし、ページの下部にある **[エンドポイントの削除]** をクリックします。Web サービスからエンドポイントが削除されます。
 
    > [AZURE.NOTE]Web サービス エンドポイントの使用中にエンドポイントが削除された場合は、アプリケーションが次回サービスにアクセスを試みるとエラーが表示されます。

Web サービス エンドポイントの名前をクリックして開きます。使用状況グラフに、Web サービス エンドポイントで使用されているコンピューティングと予測のリソースが表示されます。ビューを変更して相対値または絶対値を表示したり、グラフに表示される期間を変更したりできます。

このページには、Web サービスの REST API を使用してエンドポイントにアクセスする必要がある情報も表示されます。詳細については、「[How to consume an Azure Machine Learning web service (Azure Machine Learning Web サービスを使用する方法)][consume]」をご覧ください。

このページから Azure Marketplace に Web サービスを発行することもできます。詳細については、「[Azure Marketplace への Azure Machine Learning Web サービスの発行][marketplace]」をご覧ください。

[consume]: machine-learning-consume-web-services.md
[marketplace]: machine-learning-publish-web-service-to-azure-marketplace.md

 

<!---HONumber=July15_HO1-->