<properties
   pageTitle="Azure Marketplace の仮想マシン イメージの管理 | Microsoft Azure"
   description="最初に発行した後に Azure Marketplace の仮想マシン イメージを管理する方法についての詳細なガイドです。"
   services="Azure Marketplace"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="Azure"
   ms.workload="na"
   ms.date="06/01/2016"
   ms.author="hascipio;"/>

# Azure Marketplace の仮想マシン プランの作成後ガイド

この記事では、Azure Marketplace の発行済みの仮想マシン プランを更新する方法について説明します。また、既存のプランに 1 つ以上の新しい SKU を追加するプロセスと、Azure Marketplace から発行済みの仮想マシン プランまたは SKU を削除するプロセスについて説明します。

**Azure Marketplace で SKU を発行した後に、以下の詳細を更新することはできません。**

- **SKU ID**
- **発行者 ID**
- **プラン ID**
- **料金の変更**
- **課金モデルの変更**
- **課金リージョンの削除**


## 1\.SKU の技術的な詳細を更新する方法

VM イメージを更新し、プランを再発行するには、次の手順に従います。

1. [発行ポータル](https://publish.windowsazure.com)にログインします。
2. **[Virtual Machines]** タブに移動し、プランを選択します。
3. 左側のメニューで、**[VM イメージ]** タブをクリックします。
4. [VM イメージ] タブの **[SKUs (SKU)]** セクションで、更新する SKU を探します。その後、SKU のバージョン番号を更新します。新しいバージョンは、X.Y.Z の形式である必要があります (X、Y、Z は整数)。バージョンは、増分変更のみでなければなりません。
5. **[OS VHD URL]** ボックスに、オペレーティング システム VHD に対して作成した Shared Access Signature URI を更新し、変更を保存します。
6. 変更を行った後、**[発行]** タブに移動し、**[PUSH TO STAGING (ステージングにプッシュ)]** をクリックします。ステージング環境でプランをテストする方法に関する詳細なガイダンスについては、こちらの[リンク](marketplace-publishing-vm-image-test-in-staging.md)を参照してください。
7. ステージング環境でプランをテストしたら、発行ポータルの **[発行]** タブに移動し、**[REQUEST APPROVAL TO PUSH TO PRODUCTION (本番にプッシュするための承認を要求)]** をクリックして、Azure Marketplace でプランを再発行します。

    ![図](media/marketplace-publishing-vm-image-post-publishing/img01.png)

## 2\.プランまたは SKU の非技術的な詳細を更新する方法

Azure Marketplace で発行済みのプランまたは SKU の非技術的な (マーケティング、法的情報、サポート、カテゴリの) 詳細を更新できます。

### 2\.1 プランの説明とロゴの更新

プランの詳細を更新し、プランを再発行するには、次の手順に従います。

1. [発行ポータル](https://publish.windowsazure.com)にログインします。
2. **[Virtual Machines]** タブに移動し、プランを選択します。
3. 左側のメニューで、**[マーケティング]** タブをクリックします。
4. **[ENGLISH (US) (英語 (米国))]** をクリックします。
5. 左側のメニューで、**[詳細]** タブをクリックします。**[詳細]** タブの *[説明]* セクションで、プランのタイトル、プランの概要、プランの長い概要を更新し、変更を保存できます。

    >[AZURE.NOTE] SKU の詳細を更新する際は、次の点に注意してください。**プランと SKU の説明に、同じテキストを入力しないでください。SKU のタイトルとプランの長い概要に、同じテキストを入力しないでください。SKU のタイトルとプランの概要に、同じテキストを入力しないでください。**

    ![図](media/marketplace-publishing-vm-image-post-publishing/img02.png)

6. **[詳細]** タブの *[LOGOS (ロゴ)]* セクションで、ロゴを更新できます。ただし、ロゴが [Azure Marketplace のガイドライン](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content) (「手順 1: Marketplace のマーケティング コンテンツを指定する」の「詳細」の「Azure Marketplace のロゴのガイドライン」) に従っていることを確認してください。

    >[AZURE.NOTE] Hero アイコンはオプションです。Hero アイコンをアップロードしなくてもかまいません。ただし、Hero アイコンをアップロード後に発行ポータルから削除するプロビジョニングがありません。削除する場合は、[Hero アイコンのガイドライン](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content) (「手順 1: Marketplace のマーケティング コンテンツを指定する」の「詳細」の「Hero ロゴ バナーに関する追加のガイドライン」) に従う必要があります。

7. 変更を行った後、**[発行]** タブに移動し、**[PUSH TO STAGING (ステージングにプッシュ)]** をクリックします。ステージング環境でプランをテストする方法に関する詳細なガイダンスについては、こちらの[リンク](marketplace-publishing-vm-image-test-in-staging.md)を参照してください。
8. ステージング環境でプランをテストしたら、発行ポータルの **[発行]** タブに移動し、**[REQUEST APPROVAL TO PUSH TO PRODUCTION (本番にプッシュするための承認を要求)]** をクリックして、Azure Marketplace でプランを再発行します。

    ![図](media/marketplace-publishing-vm-image-post-publishing/img02.1.png)

### 2\.2.SKU の説明の更新

SKU の詳細を更新し、プランを再発行するには、次の手順に従います。

1. [発行ポータル](https://publish.windowsazure.com)にログインします。
2. **[Virtual Machines]** タブに移動し、プランを選択します。
3. 左側のメニューで、**[マーケティング]** タブをクリックします。
4. **[ENGLISH (US) (英語 (米国))]** をクリックします。
5. 左側のメニューで、**[プラン]** タブをクリックします。**[プラン]** タブの *[SKUs (SKU)]* セクションで、SKU のタイトル、SKU の概要、および SKU の説明の詳細を更新し、変更を保存できます。

    >[AZURE.NOTE] SKU の詳細を更新する際は、次の点に注意してください。**プランと SKU の説明に、同じテキストを入力しないでください。SKU のタイトルとプランの長い概要に、同じテキストを入力しないでください。SKU のタイトルとプランの概要に、同じテキストを入力しないでください。**

6. 変更を行った後、**[発行]** タブに移動し、**[PUSH TO STAGING (ステージングにプッシュ)]** をクリックします。ステージング環境でプランをテストする方法に関する詳細なガイダンスについては、こちらのリンクを参照してください。
7. ステージング環境でプランをテストしたら、発行ポータルの **[発行]** タブに移動し、**[REQUEST APPROVAL TO PUSH TO PRODUCTION (本番にプッシュするための承認を要求)]** をクリックして、Azure Marketplace でプランを再発行します。

    ![図](media/marketplace-publishing-vm-image-post-publishing/img02.2.png)

### 2\.3 既存のリンクの変更または新しいリンクの追加

既存のリンクを変更するか、新しいリンクを追加し、プランを再発行するには、次の手順に従います。

1. [発行ポータル](https://publish.windowsazure.com)にログインします。
2. **[Virtual Machines]** タブに移動し、プランを選択します。
3. 左側のメニューで、**[マーケティング]** タブをクリックします。
4. **[ENGLISH (US) (英語 (米国))]** をクリックします。
5. 左側のメニューで、**[リンク]** タブをクリックします。
6. 新しいリンクを追加する場合は、*[リンク]* セクションで、**[ADD LINK (リンクの追加)]** をクリックします。*[Add Link (リンクの追加)]* ダイアログ ボックスが開きます。このダイアログ ボックスでは、リンクのタイトル フィールドと URL フィールドを追加し、変更を保存できます。お客様の役に立つ情報が含まれる任意のリンクを入力できます。
7. 既存のリンクを更新または削除する場合は、適切なリンクを選択し、[編集] または [削除] を適宜クリックします。

    >[AZURE.NOTE] このセクションで入力したリンクは、本番要求の処理中に検証されるため、適切に動作することを確認してください。

8. 変更を行った後、**[発行]** タブに移動し、**[PUSH TO STAGING (ステージングにプッシュ)]** をクリックします。ステージング環境でプランをテストする方法に関する詳細なガイダンスについては、こちらの[リンク](marketplace-publishing-vm-image-test-in-staging.md)を参照してください。
9. ステージング環境でプランをテストしたら、発行ポータルの **[発行]** タブに移動し、**[REQUEST APPROVAL TO PUSH TO PRODUCTION (本番にプッシュするための承認を要求)]** をクリックして、Azure Marketplace でプランを再発行します。

    ![図](media/marketplace-publishing-vm-image-post-publishing/img02.3.png)

    ![図](media/marketplace-publishing-vm-image-post-publishing/img02.3-2.png)

### 2\.4 既存のサンプル イメージの変更または新しいサンプル イメージの追加

既存のサンプル イメージを変更するか、新しいサンプル イメージを追加し、プランを再発行するには、次の手順に従います。

>[AZURE.NOTE] [https://portal.azure.com](https://portal.azure.com) に表示されるサンプル イメージは 1 つだけです。

1. [発行ポータル](https://publish.windowsazure.com)にログインします。
2. **[Virtual Machines]** タブに移動し、プランを選択します。
3. 左側のメニューで、**[マーケティング]** タブをクリックします。
4. **[ENGLISH (US) (英語 (米国))]** をクリックします。
5. 左側のメニューで、**[SAMPLE IMAGES (サンプル イメージ)]** タブをクリックします。
6. 新しいサンプル イメージを追加する場合は、*[Sample Images (サンプル イメージ)]* セクションで、**[UPLOAD A NEW IMAGE (新しいイメージのアップロード)]** をクリックし、変更を保存します。

    >[AZURE.NOTE] サンプル イメージを含めるステップは、省略可能です。

7. 既存のサンプル イメージを更新または削除する場合は、適切なサンプル イメージを見つけ、**[REPLACE IMAGE (イメージの置換)]** または [削除] ボタンを適宜クリックします。

8. 変更を行った後、**[発行]** タブに移動し、**[PUSH TO STAGING (ステージングにプッシュ)]** をクリックします。ステージング環境でプランをテストする方法に関する詳細なガイダンスについては、こちらの[リンク](marketplace-publishing-vm-image-test-in-staging.md)を参照してください。
9. ステージング環境でプランをテストしたら、発行ポータルの **[発行]** タブに移動し、**[REQUEST APPROVAL TO PUSH TO PRODUCTION (本番にプッシュするための承認を要求)]** をクリックして、Azure Marketplace でプランを再発行します。

    ![図](media/marketplace-publishing-vm-image-post-publishing/img02.4.png)

### 2\.5 法的情報コンテンツの更新

法的情報コンテンツを更新し、プランを再発行するには、次の手順に従います。

1. [発行ポータル](https://publish.windowsazure.com)にログインします。
2. **[Virtual Machines]** タブに移動し、プランを選択します。
3. 左側のメニューで、**[マーケティング]** タブをクリックします。
4. **[ENGLISH (US) (英語 (米国))]** をクリックします。
5. 左側のメニューで、**[LEGAL (法的情報)]** タブをクリックします。*[Legal (法的情報)]* セクションで、ポリシーまたは使用条件を更新できます。*[使用条件]* ボックスにポリシーまたは使用条件を入力するか、貼り付け、変更を保存します。
6. 使用条項の文字制限は、1,000,000 文字です。
7. 変更を行った後、**[発行]** タブに移動し、**[PUSH TO STAGING (ステージングにプッシュ)]** をクリックします。ステージング環境でプランをテストする方法に関する詳細なガイダンスについては、こちらの[リンク](marketplace-publishing-vm-image-test-in-staging.md)を参照してください。
8. ステージング環境でプランをテストしたら、発行ポータルの **[発行]** タブに移動し、**[REQUEST APPROVAL TO PUSH TO PRODUCTION (本番にプッシュするための承認を要求)]** をクリックして、Azure Marketplace でプランを再発行します。

    ![図](media/marketplace-publishing-vm-image-post-publishing/img02.5.png)

### 2\.6 サポート情報の更新

サポート情報を更新し、プランを再発行するには、次の手順に従います。

1. [発行ポータル](https://publish.windowsazure.com)にログインします。
2. **[Virtual Machines]** タブに移動し、プランを選択します。
3. 左側のメニューで、**[サポート]** タブをクリックします。
4. **[サポート]** タブの *[Engineering Contact (エンジニアリング連絡先)]* セクションで、連絡先の詳細を更新できます。これらの詳細は、パートナーとマイクロソフト間の内部コミュニケーションにのみに使用されます。
5. **[サポート]** タブの *[Customer Support (カスタマー サポート)]* セクションで、**名前、電子メール、電話番号**、**サポート URL** などのサポート連絡先の詳細を更新できます。これらの詳細は、パートナーとマイクロソフト間の内部コミュニケーションにのみに使用されます。

    >[AZURE.NOTE] 電子メールによるサポートのみを提供する場合は、**[Customer Support (カスタマー サポート)]** セクションでダミーの電話番号を入力します。そうすると、指定した電子メールが代わりに使用されます。

6. 変更を行った後、**[発行]** タブに移動し、**[PUSH TO STAGING (ステージングにプッシュ)]** をクリックします。ステージング環境でプランをテストする方法に関する詳細なガイダンスについては、こちらの[リンク](marketplace-publishing-vm-image-test-in-staging.md)を参照してください。
7. ステージング環境でプランをテストしたら、発行ポータルの **[発行]** タブに移動し、**[REQUEST APPROVAL TO PUSH TO PRODUCTION (本番にプッシュするための承認を要求)]** をクリックして、Azure Marketplace でプランを再発行します。

    ![図](media/marketplace-publishing-vm-image-post-publishing/img02.7.png)

### 2\.7 カテゴリの更新

プランの [カテゴリ] セクションを更新し、プランを再発行するには、次の手順に従います。

1. [発行ポータル](https://publish.windowsazure.com)にログインします。
2. **[Virtual Machines]** タブに移動し、プランを選択します。
3. 左側のメニューで、**[カテゴリ]** タブをクリックします。
4. *[カテゴリ]* セクションで、プランのカテゴリを更新し、変更を保存できます。Azure Marketplace ギャラリーのカテゴリを最大 5 つ選択できます。
5. 変更を行った後、**[発行]** タブに移動し、**[PUSH TO STAGING (ステージングにプッシュ)]** をクリックします。ステージング環境でプランをテストする方法に関する詳細なガイダンスについては、こちらの[リンク](marketplace-publishing-vm-image-test-in-staging.md)を参照してください。
6. ステージング環境でプランをテストしたら、発行ポータルの **[発行]** タブに移動し、**[REQUEST APPROVAL TO PUSH TO PRODUCTION (本番にプッシュするための承認を要求)]** をクリックして、Azure Marketplace でプランを再発行します。

    ![図](media/marketplace-publishing-vm-image-post-publishing/img02.7.png)

## 3\.発行済みのプランに新しい SKU を追加する方法

発行済みのプランに新しい SKU を追加するには、次の手順に従います。

1. [発行ポータル](https://publish.windowsazure.com)にログインします。
2. **[Virtual Machines]** タブに移動し、プランを選択します。
3. 左側のメニューで、**[SKUs (SKU)]** タブをクリックします。その後、**[ADD A SKU (SKU の追加)]** をクリックします。新しいダイアログ ボックスが開きます。SKU ID を小文字で入力します。持ち込み課金モデル (BYOL) の新しい SKU を発行する場合は、BYOL 課金モデルのチェック ボックスをオンにします。それ以外の場合は、BYOL のチェック ボックスをオフにします。その後、ダイアログ ボックスのチェック マークをクリックして、新しい SKU を作成します。新しい SKU に BYOL 課金モデルを選択しなかった場合は、新しい SKU の課金モデルは時間単位に自動的に設定されます。時間単位課金モデルで 30 日間の無料試用版を有効にする場合は、[Is a free trial available? (無料試用版の利用)] の [One Month (1 か月)] オプションをクリックします。それ以外の場合は、[NO TRIAL (試用版なし)] を選択します (注: [Is a free trial available? (無料試用版の利用)] オプションは、新しい SKU を作成するときにダイアログ ボックスで BYOL を選択しなかった場合にのみ表示されます)。

    >[AZURE.IMPORTANT] [Hide this SKU from the Marketplace because it should always be bought via a solution template (この SKU は、常にソリューション テンプレートを介して購入する必要があるため、Marketplace に表示しない)] オプションは、Azure Marketplace でソリューション テンプレート プランの発行が承認されている場合にのみ、[はい] とマークします。それ以外の場合は、このオプションを常に [いいえ] としてマークします。

4. 左側にあるメニューから、**[VM イメージ]** タブをクリックし、作成した新しい SKU を確認します。
5. 新しい SKU を設定する場合、ガイダンスについては、こちらの[リンク](marketplace-publishing-vm-image-creation.md#5-obtain-certification-for-your-vm-image)の手順 5. を参照してください。
6. 新しい SKU のマーケティング資料を追加するには、こちらの[リンク](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content)の「手順 1: Marketplace のマーケティング コンテンツを指定する」の「詳細」の 2. ～ 5. を参照してください。
7. 新しい SKU の価格情報を追加するには、セクション 2.1 を参照してください。こちらの[リンク](marketplace-publishing-push-to-staging.md#step-2-set-your-prices)の VM 料金を設定します。
8. 変更を行った後、**[発行]** タブに移動し、**[PUSH TO STAGING (ステージングにプッシュ)]** をクリックします。ステージング環境でプランをテストする方法に関する詳細なガイダンスについては、こちらの[リンク](marketplace-publishing-vm-image-test-in-staging.md)を参照してください。
9. ステージング環境でプランをテストしたら、発行ポータルの **[発行]** タブに移動し、**[REQUEST APPROVAL TO PUSH TO PRODUCTION (本番にプッシュするための承認を要求)]** をクリックして、Azure Marketplace でプランを再発行します。

    ![図](media/marketplace-publishing-vm-image-post-publishing/img03.1-1.png)

    ![図](media/marketplace-publishing-vm-image-post-publishing/img03.1-2.png)

## 4\.Azure Marketplace から発行済みのプランまたは SKU を削除する方法

発行済みのプランの削除を要求する場合は、さまざまな注意事項があります。Azure Marketplace から発行済みのプランまたは SKU を削除するには、次の手順に従って、サポート チームからガイダンスを入手してください。

1.	こちらの[リンク](https://support.microsoft.com/ja-JP/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&locale=ja-JP&supportregion=ja-JP&pesid=15635&ccsid=635993707583706681)を使用して、サポート チケットを作成します。
2.	[Problem type (問題の種類)] で **[Managing offers (プランの管理)]** を選択し、[Category (カテゴリ)] で **[Modifying an offer and/or SKU already in production (既に運用中のプランまたは SKU の変更)]** を選択します。
3.	要求を送信します。

サポート チームがプランまたは SKU の削除手順を説明します。

>[AZURE.NOTE] ドラフト状態の (つまり、ステージングまたは運用中ではない) プランは、**[履歴]** タブの **[ドラフトの破棄]** をクリックすることで、いつでも削除できます。

## 関連項目
- [Getting Started: How to publish an offer to the Azure Marketplace (概要: Azure Marketplace へのプランの発行方法)](marketplace-publishing-getting-started.md)
- [Understanding seller insights reporting (Seller Insights レポートを理解する)](marketplace-publishing-report-seller-insights.md)
- [Understanding Payout reporting (支払いレポートを理解する)](marketplace-publishing-report-payout.md)
- [How to change your Cloud Solution Provider reseller incentive (Cloud Solution Provider のリセラー インセンティブを変更する方法)](marketplace-publishing-csp-incentive.md)
- [Troubleshooting Common Publishing Problems in the Marketplace (Marketplace での発行に関してよくある問題のトラブルシューティング)](marketplace-publishing-support-common-issues.md)
- [Get support as a publisher (発行元としてサポートを受ける)](marketplace-publishing-get-publisher-support.md)
- [Creating a VM Image on-premise (オンプレミスでの VM イメージの作成)](marketplace-publishing-vm-image-creation-on-premise.md)
- [Windows を実行する仮想マシンを Azure プレビュー ポータルで作成する](../virtual-machines/virtual-machines-windows-hero-tutorial.md)

<!---HONumber=AcomDC_0608_2016-->