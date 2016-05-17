<properties
   pageTitle="アマゾン ウェブ サービスで認証を構成する | Microsoft Azure"
   description="この記事では、AWS リソースを管理する Azure Automation の Runbook 用に AWS 資格情報を作成し検証する方法について説明します。"
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="jwhit"
   editor="tysonn"
   keywords="AWS の認証, AWS の構成"/>
<tags
   ms.service="automation"
   ms.workload="tbd"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.date="05/10/2016"
   ms.author="magoedte"/>

# アマゾン ウェブ サービスによる Runbook の認証
アマゾン ウェブ サービス (AWS) のリソースに関する一般的なタスクを自動化する処理は、Azure の Automation Runbook で実現することができます。Automation Runbook を使用すれば、Azure のリソースの場合と同様に、AWS でも多くのタスクを自動化することができます。必要となるのは次の 2 点だけです。

* AWS サブスクリプションと一連の資格情報。具体的には、AWS アクセス キーと秘密キーです。詳細については、記事「[Using AWS Credentials (AWS 資格情報の使用)](http://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html)」を参照してください。
* Azure サブスクリプションと Automation アカウント。Azure Automation アカウントのセットアップの詳細については、[Azure 実行アカウントの構成](../automation/automation-sec-configure-azure-runas-account.md)に関する記事を参照してください。  

AWS で認証するには、一連の AWS 資格情報を指定して、Azure Automation から実行される Runbook を認証する必要があります。Automation アカウントが作成済みで、それを AWS による認証に使用する場合は、次のセクションの手順に従ってください。アカウントを、AWS リソースをターゲットとする Runbook 専用に使用する場合は、最初に新しい [Automation 実行アカウント](../automation/automation-sec-configure-azure-runas-account)を作成してから (サービス プリンシパルを作成するオプションは省略)、次の手順に従います。

## Automation アカウントの構成
Azure Automation が AWS と通信する場合は、まず、AWS 資格情報を取得し、それを資産として Azure Automation に格納する必要があります。AWS ドキュメント「[Managing Access Keys for your AWS Account (AWS アカウントのアクセス キーを管理する)](http://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html)」に記載の以下の手順を実行して、アクセス キーを作成し、**アクセス キー ID** と**シークレット アクセス キー**をコピーします (必要に応じて、キー ファイルをダウンロードし、どこか安全な場所に保存します)。

AWS セキュリティ キーを作成し、コピーしたら、Azure Automation アカウントで資格情報資産を作成し、それらを安全に格納し、Runbook で参照する必要があります。記事「[Azure Automation での資格情報資産](../automation/automation-certificates.md/###To create a new credential with the Azure portal)」のセクション「**To create a new credential (新しい資格情報を作成するには)**」に記載の手順に従い、次の情報を入力します。

1. **[名前]** ボックスに「**AWScred**」と入力するか、または、命名規則に従って適切な値を入力します。  
2. **[ユーザー名]** ボックスに**アクセス ID** を入力し、**[パスワード]** および **[パスワードの確認]** ボックスに**シークレット アクセス キー**を入力します。   

## 次のステップ

- AWS のタスクを自動化する Runbook を作成する方法については、記事「[Automating deployment of a VM in Amazon Web Services (Amazon Web Services での VM のデプロイを自動化する)](../automation/automation-scenario-aws-deployment.md)」を参照してください。

<!---HONumber=AcomDC_0511_2016-->