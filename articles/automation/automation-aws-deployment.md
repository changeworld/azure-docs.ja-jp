<properties
   pageTitle="Azure Automation とハイブリッド クラウド管理: Amazon Web サービスへの VM のデプロイを自動化する"
   description="Azure Automation とハイブリッド クラウド管理: Amazon Web サービスへの VM のデプロイを自動化する"
   services="automation"
   documentationCenter=""
   authors="tiander"
   manager="stevenka"
   editor="" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/01/2016"
   ms.author="tiandert; bwren" />

# Azure Automation とハイブリッド クラウド管理: Amazon Web サービスへの VM のデプロイを自動化する

先日私はマイクロソフトの [Cloud Roadshow](https://microsoftcloudroadshow.com/cities) イベントで Azure Automation を紹介させていただいたのですが、その際、Azure Automation が Azure のみを意図したものであるのかどうか、という点について多数の質問をいただきました。ちょうどその後デモンストレーションを行いましたので、このブログ記事の中で紹介していきたいと思います。結論から言えば、**Azure Automation は Azure だけのものではありません。オンプレミス リソースに使用できるほか、ハイブリッド クラウド環境の管理に*も*使用できます。** したがって、たとえば Amazon Web サービス (AWS) をご利用の場合も、Azure Automation を利用することができます。

## 目標

この記事では、Azure Automation を利用して、VM を AWS にプロビジョニングし、その VM に特定の名前を付ける方法について取り上げます。ちなみに AWS では、これを VM に "タグ付けする" といいます。

## 前提条件

この記事の目的上、Azure Automation アカウントのセットアップが既に済んでおり、AWS サブスクリプションも取得済みであることを前提に話を進めさせていただきます。

Azure Automation アカウントのセットアップについて詳しくは、こちらの[ドキュメント ページ](automation-configuring.md)をご覧ください。

## 準備: AWS の資格情報の保存

Azure Automation が AWS と通信できるよう、ご利用の AWS の資格情報を取得し、それを Azure Automation の資産として保存する必要があります。

AWS ポータルにログインしたら、名前の下に表示される小さな三角形をクリックし、**[Security Credentials]** をクリックします。

![AWS security credential](./media/automation-aws-deployment/73522ff9-30b5-431c-a3b6-5a33167827ab.png "AWS security credential")

**[Access Keys]** をクリックします。

![AWS Access Keys](./media/automation-aws-deployment/215d76d0-6e7f-4f55-9128-ba618d4514be.jpg "AWS Access Keys")

アクセス キーとシークレット アクセス キーをコピーします (必要に応じてキー ファイルをダウンロードして厳重に保管してください)。

![Create AWS access keys](./media/automation-aws-deployment/81ca1d36-2814-478b-858f-8346f2a28211.png "Create AWS access keys")

## Azure Automation における AWS 資産

前の手順では、ご利用の AWS の**アクセス キー ID** と**シークレット アクセス キー**をコピーして保存しました。今度はそれらを Azure Automation に保存する必要があります。

ご利用の Automation アカウントに移動して、**[アセット]**、**[資格情報]** の順に選択し、新しい資格情報を追加して、「**AWScred**」という名前を付けます。

![image](./media/automation-aws-deployment/19c3669d-5259-4566-9479-5dbebb8ac733.png "image")

説明 (省略可能) を入力し、**[ユーザー名]** フィールドに**アクセス ID** を、**[パスワード]** と表示されているフィールドに**シークレット アクセス キー**を入力します。AWS の資格情報を保存します。

## Amazon Web サービス PowerShell モジュール

VM プロビジョニング Runbook は、AWS PowerShell モジュールを利用して処理を実行します。AWS PowerShell モジュールは [PowerShell ギャラリー](http://www.powershellgallery.com/packages/AWSPowerShell/)で公開されており、**[Deploy to Azure Automation]** ボタンで簡単に Automation アカウントに追加できます。

![AWS PS Module import to AA](./media/automation-aws-deployment/daa07d22-0464-4ec2-8c85-35525f95e5e4.png "AWS PS Module import to AA")

**[Deploy to Azure Automation]** をクリックすると、Azure ログイン ページに誘導されます。そこで資格情報を入力すると、次の画面が表示されます。

![Import AWS Module wizard](./media/automation-aws-deployment/575cd4d9-2ca5-4540-869d-3919f91294af.png "Import AWS Module wizard")

新規または既存の Automation アカウントを選択します。既存のアカウントにはドロップダウン ボックスがないので、タイプ ミスをしないように注意すると共に、ご利用の Automation アカウントのリージョンとリソース グループが正しいことを確認してください以降の手順に従って必要な構成を行ったら **[作成]** をクリックします。

選択した Automation アカウントに移動し、**[アセット]**、**[モジュール]** の順に選択すると、対象の AWS モジュールと 1427 件のアクティビティが表示されます。

>[AZURE.NOTE] PowerShell モジュールを Azure Automation にインポートするには、次の 2 つの手順を実行します。
>
> 1.  モジュールをインポートする
> 2.  コマンドレットを抽出する
>
>モジュールが完全にインポートされ、コマンドレットが抽出されるまで、アクティビティは表示されません。この処理には数分かかる場合があります。

## AWS VM Runbook のプロビジョニング

以上で必要な条件はすべて整いました。今度は、VM を AWS にプロビジョニングする Runbook を作成する必要があります。また、Azure Automation で、PowerShell ワークフローの代わりにネイティブの PowerShell スクリプトを利用する方法も紹介します。

ネイティブ PowerShell と PowerShell ワークフローの違いについて詳しくは、[こちら](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)を参照してください。Runbook をグラフィカルに作成する方法については、[こちら](https://azure.microsoft.com/blog/azure-automation-graphical-and-textual-runbook-authoring/)を参照してください。

Runbook の PowerShell スクリプトは、[PowerShell ギャラリー](https://www.powershellgallery.com/packages/New-AwsVM/DisplayScript)からダウンロードできます。

スクリプトをダウンロードするには、PowerShell セッションを開いて「**Save-Script -Name New-AwsVM -Path <パス>**」と入力します。

PowerShell スクリプトを保存したら、次の手順に従って Azure Automation に Runbook として追加してください。

ご利用の **Automation アカウント**の **[Runbook]** をクリックし、**[Runbook の追加]** を選択します。**[簡易作成]** (新しい Runbook の作成) を選択して Runbook の名前を入力し、**[Runbook の種類]** で **[PowerShell]** を選択します。続けて **[作成]** をクリックします。

![Create AWS runbook](./media/automation-aws-deployment/4759ad00-f800-44ba-94be-307cf034a9df.png "Create AWS runbook")

Runbook エディターが表示されたら、PowerShell スクリプトをコピーして Runbook 作成領域に貼り付けます。

![image](./media/automation-aws-deployment/7e13addf-cae3-49a5-9d6c-28aa0b7263f4.png "image")

ダウンロードした PowerShell スクリプトを使用する際は、次の点にご注意ください。

-   Runbook には、#ToDo セクションで言及されているパラメーターの既定値が多数存在します。すべての既定値に目を通して適宜変更してください。
-   AWS の資格情報 (**AWScred**) を **[アセット]** に保存したことを確認します。
-   使用するリージョンは、ご利用の AWS サブスクリプションによって異なります。先ほどセキュリティ資格情報を確認する際にアクセスした AWS ポータルで、ご利用のアカウントの横に表示される矢印をクリックし、該当するリージョンを確認します。

![AWS region](./media/automation-aws-deployment/5a789953-7329-4f0e-9501-e2d3347a8730.png "AWS region")

-   自分の Runbook に指定すべき[リージョン](http://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html)を調べます。たとえば私のリージョンは "米国西部 (オレゴン)" で、これは "us-west-2" に該当します。

![AWS regions](./media/automation-aws-deployment/3ee8065d-0480-4c78-a2e3-6062653e9cb6.png "AWS regions")

-   PowerShell ISE を使用してイメージ名の一覧を取得できます。ISE 環境で **Get-AutomationPSCredential** を **$AwsCred = Get-Credential** に変更して対象モジュールをインポートし、AWS に対して認証を行ってください。 資格情報を求めるメッセージが表示されたら、該当する**アクセス キー ID** と**シークレット アクセス キー**をユーザー名およびパスワードとして入力します。以下のサンプルを参照してください。


		#Sample to get the AWS VM available images
		#Please provide the path where you have downloaded the AWS PowerShell module
		Import-Module AWSPowerShell
		$AWSRegion = "us-west-2"
		$AwsCred = Get-Credential
		$AwsAccessKeyId = $AwsCred.UserName
		$AwsSecretKey = $AwsCred.GetNetworkCredential().Password
		
		# Set up the environment to access AWS
		Set-AWSCredentials -AccessKey $AwsAccessKeyId -SecretKey $AwsSecretKey -StoreAs AWSProfile
		Set-DefaultAWSRegion -Region $AWSRegion
		
		Get-EC2ImageByName -ProfileName AWSProfile 


次の出力が表示されます。

![Get AWS images](./media/automation-aws-deployment/1b1b0e9f-1af0-471c-9f5e-43f4bb29f4ed.png "Get AWS images")

Runbook で **$InstanceType** として参照されている Automation 変数には、好きなイメージ名をコピーして貼り付けてください。筆者は AWS の無料使用枠を使っているため、Runbook で **t2.micro** を選択しました。

Amazon EC2 インスタンス タイプの詳細については、[こちら](https://aws.amazon.com/ec2/instance-types/)を参照してください。

## プロビジョニング AWS VM Runbook のテスト

プレフライト チェックリスト:

-   AWS に対して認証を行うための資産を作成し、**AWScred** という名前を付けました。
-   AWS PowerShell モジュールを Azure Automation にインポートしました。
-   新しい Runbook を作成し、パラメーターの値を確認して適宜更新しました。
-   Runbook の設定にある **[詳細レコードの記録]** と (必要に応じて)**[進行状況レコードの記録]** を**オン**に設定しました。

早速 **VMname** を指定して新しい Runbook を開始しましょう。

![Start New-AwsVM runbook](./media/automation-aws-deployment/31cd9d1d-208e-4e96-897f-05e37bf5ee7d.png "Start New-AwsVM runbook")

**[詳細レコードの記録]** と **[進行状況レコードの記録]** を有効にしたため、出力された **[ストリーム]** が **[すべてのログ]** に表示されます。

![Stream output](./media/automation-aws-deployment/77191c69-37fa-4598-82d8-cf5dbadfaff5.png "Stream output")

AWS でチェックしてみましょう。

![AWS console deployed VM](./media/automation-aws-deployment/c5a16bd4-6c54-40d6-9811-7d8e6c5ec74d.png "AWS console deployed VM")

問題なさそうです。

この記事では、Azure Automation を使用してハイブリッド クラウドを管理する例として、Amazon Web サービスに VM を作成し、カスタム タグを適用して名前を付ける方法を紹介しました。

これからも Azure Automation をぜひご活用ください。

Tiander

<!---HONumber=AcomDC_0204_2016-->