<properties linkid="manage-services-sql-databases-premium" urlDisplayName="Premium SQL データベース" pageTitle="Azure SQL データベース Premium へのサインアップ" metaKeywords="" description="SQL データベース Premium のプレビューにサインアップし、Premium データベース クォータを要求してから、Azure SQL データベースで Premium にデータベースをアップグレードする方法を説明します。" metaCanonical="" services="cloud-services" documentationCenter="" title="Azure SQL データベース Premium のプレビューへのサインアップ" authors="karaman" solutions="" manager="" editor="tysonn" />





# Azure SQL データベース Premium のプレビューへのサインアップ
このチュートリアルでは、SQL データベース Premium のプレビューに参加するために必要な手順を説明します。

Azure SQL データベースでは、新しいサービスの限定プレビュー版である SQL データベース Premium がリリースされています。Premium データベースによって提供される予約済みリソースを利用すると、クラウド アプリケーションのパフォーマンスを予測しやすくなります。

[このトピックに記載されている機能は、プレビュー版でのみ利用できます。このトピックはプレリリース資料であり、今後のリリースで変更されることがあります。]]

##目次##

* [手順 1. SQL データベース Premium のプレビューへサインアップする](#SignUp)
* [手順 2. Premium データベース クォータを要求する](#Quota)
* [手順 3. Premium データベースを作成する](#Upgrade)

<h2><a id="SignUp"></a>手順 1. SQL データベース Premium のプレビューへサインアップする</h2>
この機能を利用するための最初の手順は、SQL データベース プレミアムのプレビューのサブスクリプションにサインアップすることです。

1. Microsoft アカウントを使用して、[Azure プレビュー機能のページ](http://account.windowsazure.com/PreviewFeatures)にサインインします。

	**注** - アカウント ポータルにアクセスできるのは、Azure アカウント管理者だけです。サブスクリプションのアカウント管理者でない場合は、アカウント管理者に依頼して、サブスクリプションのサインアップ処理を完了してください。Azure のアカウントとサブスクリプションの詳細については、「[購入オプション](http://account.windowsazure.com/PreviewFeatures)」を参照してください。
 
	![Image1](./media/sql-database-premium-sign-up/AccountSignup-Figure1.png)

2. プレビュー機能リストで **[SQL データベース Premium]** 項目を探し、その項目に関連付けられている **[今すぐ試す]** ボタンをクリックします。

	![Image2](./media/sql-database-premium-sign-up/AccountSignupButton-Figure2.png)


3. プレビューにサインアップするサブスクリプションを選択します。

	![Image3](./media/sql-database-premium-sign-up/Subscription-Figure3.png)

	プレビューできるのは、アクティブな有料 Azure サブスクリプションだけです。複数のサブスクリプションにサインアップできますが、各サブスクリプションにサインアップできるのは 1 度だけです。

	プレビューを目的としたサブスクリプションへのサインアップでは追加料金は発生しませんが、サブスクリプションがアクティブ化され、Premium クォータが割り当てられた後は、Premium データベースの作成やアップグレードを行うと、「[SQL データベースの料金詳細](http://www.windowsazure.com/ja-jp/pricing/details/sql-database/)」ページに記載されている課金の対象となります。

	サインアップ要求の現在の状態は、プレビュー機能リストに反映されます。

	![Image4](./media/sql-database-premium-sign-up/Status-Figure4.png)

4. 要求は、現在の容量と需要に基づいて承認されます。サブスクリプションがアクティブ化されるまでの待機期間は最大で 2 日です。この期間は、需要が大きいほど、またはパブリック プレビュー容量が使用されているほど長くなります。

5. サブスクリプションがアクティブ化され、プレビュー可能になると、電子メールの通知を受信します。


<h2><a id="Quota"></a>手順 2. Premium データベース クォータを要求する</h2>
サブスクリプションがアクティブ化され、プレビュー可能になったら、Premium データベースを作成する各サーバー用の Premium データベース クォータを要求する必要があります。容量は限られているため、Premium データベースを作成するサーバーのクォータだけを要求し、必要でない保留された要求は取り消してください。


1.	Microsoft アカウントを使用して、[Azure 管理ポータル](https://manage.windowsazure.com)にサインインします。

	**注** - サブスクリプションにサインアップし、プレビュー可能になったら、サブスクリプションのアカウント管理者、サービス管理者、共同管理者はクォータを要求できます。

2.	**[SQL データベース]** 拡張機能の **[サーバー]** リストに移動します。
3.	プレミアム データベース クォータを要求するサーバーを選択します。
4.	上部のナビゲーション バーにある稲妻のアイコンをクリックして、選択したサーバーの **[クイック スタート]** に移動します。
5.	**[プレミアム データベース]** セクションで **[プレミアム データベースのクォータの要求]** をクリックします。

	![Image6](./media/sql-database-premium-sign-up/RequestQuota-Figure6.png)


	要求を送信した後の、クォータが割り当てられるまでの待機期間は最大で 5 日です。この期間は、需要が大きいほど、またはパブリック プレビュー容量が使用されているほど長くなります。
	
	プレミアム データベース クォータの要求に関する追加の注意事項:
	
	- 無料評価版サブスクリプションのお客様は、クォータを使用できません。
	- 最初はクォータに制限があり、要求は、現在の需要と使用可能な容量に基づいて許可されます。
	- Microsoft は、15 日後に未使用のクォータを回収する場合があります。
	- クォータ要求は、サブスクリプションの各論理サーバーについて 1 つだけ送信できます。
	- 最初のクォータは、論理サーバーごとに 1 つのデータベースに制限されています。
	- データベース クォータの要求は無料ですが、Premium Edition データベースを作成したり、既存の Web Edition または Business Edition のデータベースを Premium にアップグレードしたりすると、データベースのコストが増加します。
6.	クォータ要求の状態は、サーバーの **[クイック スタート]** ページで確認できます。

	![Image7](./media/sql-database-premium-sign-up/PendingApproval-Figure7.png)
	

7.	Premium データベース クォータ要求が許可され、クォータが使用可能になると、電子メールの通知を受信します。
8.	許可された後は、サーバーの **[クイック スタート]** または **[ダッシュボード]** タブで、サーバーの残りの Premium データベース クォータを確認できます。

	![Image8](./media/sql-database-premium-sign-up/QuotaApproved-Figure8.png)

<h2><a id="Upgrade"></a>手順 3. Premium データベースを作成する</h2>


クォータが割り当てられたら、新しい Premium Edition データベースを作成したり、Web Edition または Business Edition のデータベースを Premium にアップグレードして、予約済み容量と予測しやすいパフォーマンスを活用できます。

![Image9](./media/sql-database-premium-sign-up/SpecifyDBSettings-Figure9.png)


![Image10](./media/sql-database-premium-sign-up/PremiumDBSettings-Figure10.png)


詳細については、「[Premium データベースの管理](http://go.microsoft.com/fwlink/p/?LinkID=311927)」を参照してください。

	
	
**注:** データベースの Premium 状態または予約サイズを変更できるのは、24 時間に 1 度だけです。
<h2><a id="NextSteps"></a>次のステップ</h2>
Premium データベースのその他の情報については、次のページを参照してください。

* [プレミアム データベースを管理しています](http://go.microsoft.com/fwlink/p/?LinkID=311927)
* [SQL データベース プレミアムのガイダンス](http://go.microsoft.com/fwlink/p/?LinkId=313650)


















