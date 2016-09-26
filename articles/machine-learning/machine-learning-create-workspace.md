<properties
	pageTitle="Machine Learning ワークスペースの作成 | Microsoft Azure"
	description="Azure Machine Learning Studio のワークスペースの作成方法"
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
	ms.date="08/16/2016"
	ms.author="garye;bradsev;ahgyger"/>


# Azure Machine Learning ワークスペースの作成と共有

このメニューは、Cortana Analytics Process (CAP) によって使用されるさまざまなデータ サイエンス環境の設定方法を説明するトピックにリンクしています。

[AZURE.INCLUDE [data-science-environment-setup](../../includes/cap-setup-environments.md)]

Azure Machine Learning Studio を使用するには、Machine Learning ワークスペースが必要です。このワークスペースには、実験を管理および公開するのに必要なツールが用意されています。

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## ワークスペースを作成するには

1. [Microsoft Azure クラシック ポータル]にサインインします。

> [AZURE.NOTE] サインインするには、Azure サブスクリプションの管理者である必要があります。Machine Learning ワークスペースの所有者であっても、[Microsoft Azure クラシック ポータル]へのアクセス権は付与されません。詳細については、「[Azure サブスクリプションとワークスペース所有者の特権](#subscriptionvsworkspace)」を参照してください。

2. Microsoft Azure サービス パネルで、**[MACHINE LEARNING]** をクリックします。

    ![Machine Learning サービス][1]

3. ウィンドウの下部にある **[+新規]** をクリックします。
4. **[Data Services]**、**[MACHINE LEARNING]**、**[簡易作成]** の順にクリックします。

	![新しいワークスペースの簡易作成][3]

5. ワークスペースの **[ワークスペース名]** を入力します。
6. **[場所]** に Azure の場所を指定し、**[ストレージ アカウント]** に既存の Azure のストレージ アカウントを入力するか、**[新しいストレージ アカウントの作成]** を選択して新たに作成します。
7. **[ML ワークスペースを作成]** をクリックします。

Machine Learning ワークスペースが作成されると、**機械学習**のページの一覧に表示されます。

## Azure Machine Learning ワークスペースの共有

Machine Learning ワークスペースを作成した後は、ユーザーをワークスペースに招待し、ワークスペースとそのすべての実験へのアクセス権を共有することができます。次の 2 つのユーザー ロールがサポートされます。

- **ユーザー** - ワークスペース ユーザーは、ワークスペース内のデータセット、実験、Web サービスの作成、表示、変更を行うことができます。
- **所有者** - 所有者は、ユーザーの招待、削除、ワークスペースへのアクセス権の付与、ユーザーが実行できる操作の登録を行うことができます。また、所有者には Notebook へのアクセス権もあります。

### ワークスペースを共有するには
1. [Machine Learning Studio] にサインインします
2. Machine Learning Studio パネルの **[設定]** をクリックします
3. **[ユーザー]** をクリックします
4. **[その他のユーザーの招待]** をクリックします

    ![その他のユーザーを招待する][4]

5. 1 つ以上の電子メール アドレスを入力します。ユーザーには、有効な Microsoft アカウント (name@outlook.com など) または (Azure Active Directory の) 組織アカウントのみが必要です。
6. チェック マークのボタンをクリックします。

追加した各ユーザーには、共有ワークスペースにログインする手順が記載された電子メールが送信されます。

ワークスペースの管理方法の詳細については、「[Azure Machine Learning ワークスペースの管理]」をご覧ください。ワークスペースの作成で問題が発生した場合は、「[Troubleshooting guide: Create and connect to an Machine Learning workspace (トラブルシューティング ガイド: Machine Learning ワークスペースの作成と接続)]」をご覧ください。

## <a name="subscriptionvsworkspace"></a>Azure サブスクリプションとワークスペース所有者の特権

次の表は、Azure サブスクリプション管理者とワークスペース所有者の違いを説明した一覧です。

| アクション | Azure サブスクリプション管理者 | ワークスペースの所有者 |
| --------------			|:------------------------:| :----------------:|
| [Microsoft Azure クラシック ポータル]にアクセスする| はい | なし |
| 新しいワークスペースを作成する | はい | なし |
| ワークスペースを削除する | はい | なし |
| Web サービスにエンドポイントを追加する | はい | なし |
| Web サービスからエンドポイントを削除する | はい | なし |
| Web サービスの同時実行を変更する | はい | なし |
| [Machine Learning Studio] にアクセスする | なし * | はい |


> [AZURE.NOTE] * Azure サブスクリプション管理者は、自分がワークスペース所有者として作成するワークスペースに自動的に追加されます。ただし、単に Azure サブスクリプション管理者であるというだけでは、そのサブスクリプションのワークスペースへのアクセスは許可されません。

<!-- ![List of Machine Learning workspaces][2] -->

<!--Anchors-->
[To create a workspace]: #createworkspace

<!--Image references-->
[1]: media/machine-learning-create-workspace/cw1.png
[2]: media/machine-learning-create-workspace/cw2.png
[3]: media/machine-learning-create-workspace/cw4.png
[4]: media/machine-learning-create-workspace/cw5.png


<!--Link references-->
[Azure Machine Learning ワークスペースの管理]: machine-learning-manage-workspace.md
[Troubleshooting guide: Create and connect to an Machine Learning workspace (トラブルシューティング ガイド: Machine Learning ワークスペースの作成と接続)]: machine-learning-troubleshooting-creating-ml-workspace.md
[Machine Learning Studio]: https://studio.azureml.net/
[Microsoft Azure クラシック ポータル]: https://manage.windowsazure.com/

<!---HONumber=AcomDC_0914_2016-->