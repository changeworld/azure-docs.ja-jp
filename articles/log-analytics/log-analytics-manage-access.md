<properties
	pageTitle="Log Analytics へのアクセスを管理する | Microsoft Azure"
	description="ユーザー、アカウント、OMS ワークスペース、Azure アカウントにさまざまな管理タスクを実行し、Log Analytics へのアクセスを管理します。"
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/28/2016"
	ms.author="banders"/>

# Log Analytics へのアクセスを管理する

Log Analytics へのアクセスを管理するには、ユーザー、アカウント、OMS ワークスペース、Azure アカウントに対してさまざまな管理タスクを使用します。Operations Management Suite (OMS) で新しいワークスペースを作成するには、ワークスペース名を選択して、アカウントと関連付け、地理的な場所を選択します。ワークスペースは基本的にはアカウント情報とアカウントの単純な構成情報が含まれるコンテナーです。組織のメンバーは、複数の OMS ワークスペースを使用して、 IT インフラストラクチャの一部またはすべてから収集されるデータのさまざまなセットを管理する場合があります。

「[ログ分析を使ってみる](log-analytics-get-started.md)」では、すばやく稼働させる方法について紹介していますが、この記事では、OMS へのアクセスの管理に必要ないくつかの操作について、より詳しく説明します。

最初にすべての管理タスクを実行する必要はないかもしれませんが、後続のセクションで頻繁に使用されるタスクをすべて取り上げます。

- アカウントとユーザーの管理
- 既存のワークスペースにグループを追加する
- 必要なワークスペースの数を決定する
- 既存のワークスペースを Azure サブスクリプションへリンクする
- ワークスペースを有料データ プランにアップグレードする
- データ プランの種類を変更する
- 既存のワークスペースに Azure Active Directory の組織を追加する
- OMS Log Analytics リソースへのアクセスを制御する
- OMS ワークスペースを閉じる

## アカウントとユーザーの管理
[設定] ページの **[アカウント]** タブを使用してアカウントとユーザーを管理します。ここでは、後続セクションのタスクを実行できます。

![ユーザーの管理](./media/log-analytics-manage-access/setup-workspace-manage-users.png)

### 既存のワークスペースへのユーザーの追加

次の手順で OMS ワークスペースにユーザーやグループを追加します。ユーザーやグループは、このワークスペースに関連付けられているすべてのアラートを表示し、操作することができます。

>[AZURE.NOTE] Azure Active Directory の組織アカウントから、ユーザーやグループを追加する場合は、まず、OMS アカウントが Active Directory ドメインと関連付けられていることを確認する必要があります。[既存のワークスペースに Azure Active Directory の組織を追加する](#add-an-azure-active-directory-organization-to-an-existing-workspace)をご覧ください。

#### 既存のワークスペースにユーザーを追加するには
1. OMS で、**[設定]** タイルをクリックします。
2. **[アカウント]** タブをクリックします。
3. **[ユーザーの管理]** セクションで、追加するアカウントの種類を **[組織アカウント]** または **[Microsoft アカウント]** から選択します。
    - Microsoft アカウントを選択する場合は、Microsoft アカウントに関連付けられているユーザーのメール アドレスを入力します。
    - 組織アカウントを選択する場合は、ユーザー、グループ名、メール エイリアスのどれか一部を入力すると、ユーザーとグループの一覧が表示されます。ユーザーまたはグループを選択します。

    >[AZURE.NOTE] 最適なパフォーマンスの結果を得るには、単一の OMS アカウントに関連付けられている Active Directory グループの数を 1つは管理者用に、1 つはユーザー用の 2 つに制限します。それ以上のグループを使用すると、Log Analytics のパフォーマンスに影響を与える可能性があります。

7. 追加するユーザーかグループの種類を **[管理者]** か **[ユーザー]** から選択します。
8. **[追加]** をクリックします。

  Microsoft アカウントを追加する場合は、指定したメールに、ワークスペースへの招待が送信されます。招待の指示に従って OMS に加入すると、ユーザーは、この OMS アカウントのアラートとアカウント情報を表示できます。また、**[設定]** ページの **[アカウント]** タブでユーザー情報を表示できます。組織アカウントを追加すると、ユーザーは Log Analytics にすぐにアクセスすることができます。![招待電子メール](./media/log-analytics-manage-access/setup-workspace-invitation-email.png)

### 既存のユーザーの種類を編集する

OMS アカウントに関連付けられているユーザーのアカウント ロールを変更することができます。次のオプションがあります。

 - *管理者*: ユーザーの管理、すべてのアラートの表示と操作、サーバーの追加、削除を行うことができます

 - *ユーザー*: すべてのアラートの表示と操作、サーバーの追加、削除を行うことができます

#### アカウントを編集するには
1. OMS の **[設定]** ページで、**[アカウント]** タブから変更するユーザーのロールを選択します。
2. **[OK]** をクリックします。

## OMS ワークスペースからユーザーを削除する

次の手順で OMS ワークスペースからユーザーを削除します。この操作では、ユーザーのワークスペースは閉じられないことに注意してください。ユーザーとワークスペース間の関連付けが削除されます。ユーザーが複数のワークスペースに関連付けられている場合は、OMS にサインインすることができます。

### ワークスペースからユーザーを削除するには

1. OMS の **[設定]** ページにある **[アカウント]** タブで、削除するユーザー名の横にある [削除] をクリックします。
2. **[OK]** をクリックして、削除するユーザーを確認します。


## 既存のワークスペースにグループを追加する

1.	上の “既存のワークスペースにユーザーを追加するには” の手順 1-4 を実行します。
2.	**[ユーザー/グループの選択]** で **[グループ]** を選択します。 ![既存のワークスペースにグループを追加する](./media/log-analytics-manage-access/add-group.png)
3.	追加するグループの表示名または電子メール アドレスを入力します。
4.	一覧結果でグループを選択し、**[追加]** をクリックします。

## 必要なワークスペースの数を決定する

ワークスペースは、Azure 管理ポータル内では、Azure のリソースとして表示されます。

新しいワークスペースを作成することも、System Center Operations Manager を使用するために既に開いている既存のワークスペースへリンクすることもできます。Azure のサブスクリプション (有料) にはまだ関連付けられていません。

ワークスペースは、データが収集、集計、分析されて、OMS ポータルに表示されたレベルを表示します。複数のワークスペース選択して、さまざまな環境やシステムからデータを分離しておくことができます。各 Operations Manager 管理グループ (またそのすべてのエージェント) や個々 の VM/ エージェントはそれぞれ 1 つだけのワークスペースに接続できます。

各ワークスペースは、ワークスペースごとに複数の Microsoft アカウントと組織アカウントを関連付けることができます。また、各ユーザー アカウントは複数の OMS ワークスペースを持つことができます。

既定では、ワークスペースを作成するために使用した Microsoft アカウントや組織アカウントはワークスペースの管理者になります。管理者は、追加の Microsoft アカウントを招待したり、Azure Active Directory からユーザーを選択できます。

## 既存のワークスペースを Azure サブスクリプションへリンクする

[microsoft.com/oms](https://microsoft.com/oms) Web サイトからワークスペースを作成することができます。ただし、これらのワークスペースには、特定の制限が存あり、無料のアカウントを使用している場合に最も注意すべきなのは、データのアップロードが 500 MB/日に制限されていることです。このワークスペースを変更するには、 **既存のワークスペースを Azure サブスクリプションにリンクする**必要があります。

>[AZURE.IMPORTANT] ワークスペースをリンクするためには、リンクするワークスペースに Azure アカウントが既にアクセスしていることが必要です。つまり、Azure ポータルへのアクセスに使用するアカウントは、OMS ワークスペースへのアクセスに使用するアカウントと **同じ**アカウントである必要があります。そうでない場合は、[既存のワークスペースへのユーザーの追加](#add-a-user-to-an-existing-workspace)をご覧ください。

1.	[Azure ポータル](http://portal.azure.com)にサインインします。
2.	**Log Analytics (OMS)** を見つけ、選択します。
3.	既存ワークスペースの一覧が表示されます。**[追加]** をクリックします。 ![ワークスペースの一覧](./media/log-analytics-manage-access/manage-access-link-azure01.png)
4.	**[OMS ワークスペース]** で、**[または既存のリンク]** をクリックします。 ![既存をリンクする](./media/log-analytics-manage-access/manage-access-link-azure02.png)
5.	**[必要な設定の構成]** をクリックします。 ![必要な設定を構成する](./media/log-analytics-manage-access/manage-access-link-azure03.png)
6.	ユーザーの Azure アカウントにまだリンクされていないワークスペースの一覧が表示されます。ワークスペースを選択します。 ![ワークスペースを選択する](./media/log-analytics-manage-access/manage-access-link-azure04.png)
7.	必要に応じて、次の項目の値を変更できます。
    - [サブスクリプション]
    - リソース グループ
    - Location (場所)
    - 価格レベル ![値を変更する](./media/log-analytics-manage-access/manage-access-link-azure05.png)
8.	**[作成]** をクリックします。これでワークスペースがユーザーの Azure アカウントにリンクされました。

>[AZURE.NOTE] リンクするワークスペースが表示されない場合、OMS Web サイトで作成した OMS ワークスペースにアクセスする許可がユーザーの Azure サブスクリプションにありません。OMS Web サイトを利用し、OMS ワークスペース内からこのアカウントにアクセスする許可を与える必要があります。その方法については、「[既存のワークスペースへのユーザーの追加](#add-a-user-to-an-existing-workspace)」をご覧ください。



## ワークスペースを有料データ プランにアップグレードする

OMS のワークスペース データ プランには、**無料**、**標準**、**プレミアム**の 3 つの種類があります。*無料* プランの場合は、500 MB のデータの上限に達した可能性があります。この制限を超えるデータを収集するには、ワークスペースを「**従量課金制プラン**」にアップグレードする必要があります。いつでもプランの種類を変更できます。OMS 価格の詳細については、[価格詳細](https://www.microsoft.com/ja-JP/server-cloud/operations-management-suite/pricing.aspx)に関する記述を参照してください。

>[AZURE.IMPORTANT] ワークスペース プランは、Azure サブスクリプションに*リンク*されている場合にのみ変更できます。Azure でワークスペースを作成した場合や、*既に*ワークスペースをリンクしている場合は、このメッセージを無視してください。[OMS Web サイト](http://www.microsoft.com/oms)でワークスペースを作成した場合は、[既存のワークスペースを Azure サブスクリプションへリンクする](#link-an-existing-workspace-to-an-azure-subscription)の手順に従う必要があります。

### OMS Add On for System Center の資格を使用する

OMS Add On for System Center は OMS Log Analytics の Premium プランの資格を与えます。詳細は [OMS 価格](https://www.microsoft.com/ja-JP/server-cloud/operations-management-suite/pricing.aspx)にあります。

OMS Add On for System Center を購入した場合、Microsoft アカウント チームまたはリセラーは、Azure 購入を含むエンタープライズ契約に OMS アドオンを関連付けます。OMS アドオンにより契約上で資格が生じます。あらゆる Azure サブスクリプションでその資格を活用できます。たとえば、複数の OMS ワークスペースで OMS アドオンの資格を利用できます。

OMS ワークスペースの使用を OMS アドオンからの資格に適用するには、次の作業が必要になります。

1. OMS アドオン購入と Azure サブスクリプション使用の両方を含むエンタープライズ契約に含まれる Azure サブスクリプションに OMS ワークスペースをリンクする
2. ワークスペースの Premium プランを選択する

Azure または OMS のポータルで使用状況を確認するとき、OMS アドオン資格は表示されません。ただし、エンタープライズ ポータルで資格を確認できます。

OMS ワークスペースがリンクされている Azure サブスクリプションを変更する必要がある場合、Azure PowerShell [Move-AzureRMResource](https://msdn.microsoft.com/library/mt652516.aspx) コマンドレットを使用できます。

### エンタープライズ契約の Azure コミットメントを使用する

OMS コンポーネントのスタンドアロン価格を使用する場合、OMS のコンポーネント別に支払います。使用状況は Azure 請求書に表示されます。

エンタープライズ契約の一部として、一定量のみ Azure 使用について前払いしている場合、OMS を使用するとき、その前払いが適用されます。OMS Log Analytic に Azure コミットメント価格を使用するには、OMS ワークスペースがリンクされているサブスクリプションが Azure エンタープライズ契約に含まれている必要があります。

OMS ワークスペースがリンクされている Azure サブスクリプションを変更する必要がある場合、Azure PowerShell [Move-AzureRMResource](https://msdn.microsoft.com/library/mt652516.aspx) コマンドレットを使用できます。



### ワークスペースを有料データ プランに変更する

1.	[Azure ポータル](http://portal.azure.com)にサインインします。
2.	**Log Analytics (OMS)** を見つけ、選択します。
3.	既存ワークスペースの一覧が表示されます。ワークスペースを選択します。 ![ワークスペースの一覧](./media/log-analytics-manage-access/manage-access-change-plan01.png)
4.	**[設定]** で **[価格レベル]** をクリックします。 ![価格レベル](./media/log-analytics-manage-access/manage-access-change-plan02.png)
5.	**[価格レベル]** で、データ プランを選択し、**[選択]** をクリックします。 ![プランの選択](./media/log-analytics-manage-access/manage-access-change-plan03.png)
6.	Azure ポータルでビューを更新すると、選択したプランの**価格レベル**が更新されます。 ![価格レベルを更新する](./media/log-analytics-manage-access/manage-access-change-plan04.png)

これで "無料" のデータ上限を超えてデータを収集できます。


## 既存のワークスペースに Azure Active Directory の組織を追加する

Operational Insights (OMS) ワークスペースを Azure Active Directory ドメインに関連付けることができます。これにより、Active Directory からユーザーを OMS ワークスペースに直接追加することができます。別の Microsoft アカウントは必要ありません。

### 既存のワークスペースに Azure Active Directory の組織を追加するには

1. OMS の [設定] ページで、**[アカウント]** をクリックし、**[ワークスペース情報]** をクリックします。  
2. 組織のアカウントに関する情報を確認し、**[組織の追加]** をクリックします。 ![組織を追加する](./media/log-analytics-manage-access/manage-access-add-adorg01.png)
3. Azure Active Directory ドメインの管理者の ID 情報を入力します。その後、ワークスペースが Azure Active Directory ドメインにリンクされたことを告げる確認が表示されます。 ![リンクされているワークスペースの確認](./media/log-analytics-manage-access/manage-access-add-adorg02.png)


## OMS Log Analytics リソースへのアクセスを制御する

OMS ワークスペースへのアクセスは 2 か所で管理できます。

- OMS ポータルへのアクセスの場合、OMS ポータル内で管理され、リソースがある Azure サブスクリプションへのアクセスとは区別されます。
- PowerShell と直接の REST API アクセスの場合、Azure RBAC を利用して Azure 内で管理します。

OMS ポータルへのアクセスをユーザーに与えているが、それがリンクされている Azure サブスクリプションへのアクセスは与えていない場合、ユーザーが OMS ポータルにログインしたとき、Automation、Backup、Site Recovery ソリューションのタイルにはユーザーのデータが表示されません。

すべてのユーザーがこれらのソリューションでデータを参照できるようにするには、OMS ワークスペースにリンクされている Automation、Backup、Site Recovery コンテナーについて、**読み取り**アクセス以上の権限を与えます。


## OMS ワークスペースを閉じる

OMS ワークスペースを閉じると、ワークスペースを閉じてから 30 日以内に、ワークスペースに関連するすべてのデータが、OMS サービスから削除されます。

ユーザーが管理者で、ワークスペースに関連付けられている複数のユーザーが存在する場合は、それらのユーザーとワークスペース間の関連付けがなくなります。ユーザーが他のワークスペースに関連付けられている場合は、その他のワークスペースを使用して OMS を継続して使用できます。ただし、他のワークスペースに関連付けられていない場合は、OMS を使用するには、新しいワークスペースを作成する必要があります。

### OMS ワークスペースを閉じるには

1. OMS の **[設定]** ページにある **[アカウント]** タブで、**[ワークスペースの終了]** をクリックします。

2. ワークスペースを閉じる理由の 1 つを選ぶか、テキスト ボックスに、別の理由を入力します。

3. **[ワークスペースを閉じる]** をクリックします。

## 次のステップ

- エージェントを追加し、データを収集する方法については、「[Connect Windows computers to Log Analytics (Log Analytics に Windows コンピューターを接続する)](log-analytics-windows-agents.md)」を参照してください。
- 機能を追加し、データを収集する方法については、「[Add Log Analytics solutions from the Solutions Gallery (ソリューションギャラリーから Log Analytics ソリューションを追加する)](log-analytics-add-solutions.md)」を参照してください。
- エージェントが Log Analytics サービスと通信できるように組織がプロキシ サーバーまたはファイアウォールを使用している場合、「[Configure proxy and firewall settings in Log Analytics (Log Analytics のプロキシとファイアウォールの設定を構成する)](log-analytics-proxy-firewall.md)」を参照してください。

<!---HONumber=AcomDC_0504_2016-->