<properties
    pageTitle="Automation Runbook で Azure VM アラートを修復する |Microsoft Azure"
    description="この記事では、Azure Automation Runbook で Azure Virtual Machine アラートを統合し、問題を自動修復する方法について説明します"
    services="automation"
    documentationCenter=""
    authors="csand-msft"
    manager="stevenka"
    editor="tysonn" />    
<tags
    ms.service="automation"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="04/11/2016"
    ms.author="csand;magoedte" />

# Azure Automation ソリューション - Azure VM アラートを修復する

Azure Automation および Azure Virtual Machines 向けに、Automation Runbook を実行するように仮想マシン (VM) アラートを構成できる新機能がリリースされました。この新しい機能では、VM の再起動や停止など、VM アラートに応じて標準の修復を自動的に実行できます。

これまでは、アラートがトリガーするたびに Runbook を実行するように、VM アラート ルールの作成中に Runbook に [Automation Webhook を指定](https://azure.microsoft.com/blog/using-azure-automation-to-take-actions-on-azure-alerts/)できていました。ただし、これには Runbook を作成し、Runbook 用の Webhook を作成してから、アラート ルールの作成中に Webhook をコピーして貼り付ける必要がありました。この新しいリリースでは、アラート ルールの作成中に Runbook を一覧から直接選択でき、また Runbook を実行する Automation アカウントを選択するかアカウントを簡単に作成できるようになったため、プロセスがはるかに簡単なりました。

この記事では、Azure VM アラートを設定して、アラートがトリガーするたびに実行されるように Automation Runbook を構成することがいかに簡単かを示します。シナリオ例では、メモリ リークがある VM 上のアプリケーションのためにメモリ使用量がしきい値を超えたときの VM の再起動や、CPU ユーザー時間が過去 1 時間で 1% を下回っていて使用されていないときの VM の停止を扱います。また、Automation アカウントのサービス プリンシパルの自動作成によって Azure アラート修復での Runbook の使用を簡素化させる方法についても説明します。

## VM のアラートを作成する

しきい値が満たされたら Runbook を起動するようにアラートを構成するには、次の手順を実行します。

>[AZURE.NOTE] このリリースでは、V2 仮想マシンのみをサポートします。クラシック VM のサポートについてはまもなく追加される予定です。

1. Azure ポータルにログインし、**[仮想マシン]** をクリックします。  
2. いずれかの仮想マシンを選択します。仮想マシンのダッシュボード ブレードが表示され、**[設定]** ブレードが右側に表示されます。  
3. [監視] セクションで、**[設定]** ブレードから **[アラート ルール]** を選択します。
4. **[アラート ルール]** ブレードで、**[アラートの追加]** をクリックします。

**[アラート ルールの追加]** ブレードが開きます。ここで、アラートの条件を構成して、電子メールを他のユーザーに送信する、Webhook を使用してアラートを別のシステムに転送する、応答の試行時に Automation Runbook を実行して問題を修正するなどといったオプションの 1 つまたはすべてを選択できます。

## Runbook を構成する

VM アラートしきい値に達したときに実行されるように Runbook を構成するには、**[Automation Runbook]** を選択します。 **[Configure runbook (Runbook の構成)]** ブレードで、実行する Runbook と、Runbook を実行する Automation アカウントを選択できます。

![Automation Runbook を構成して新しい Automation アカウントを作成する](media/automation-azure-vm-alert-integration/ConfigureRunbookNewAccount.png)

>[AZURE.NOTE] このリリースでは、サービスが提供する Restart VM、Stop VM、Remove VM (VM の消去) の 3 つの Runbook から選択できます。他の Runbook やいずれかの独自の Runbook を選択する機能は、今後のリリースで提供されます。

![Runbook の選択肢](media/automation-azure-vm-alert-integration/RunbooksToChoose.png)

3 つの利用可能な Runbook からいずれかを選択すると、**[Automation アカウント]** ボックスが表示され、Runbook を実行する Automation アカウントを選択できます。Runbook は、Azure サブスクリプションにある [Automation アカウント](automation-security-overview.md)のコンテキストで実行する必要があります。既に作成した Automation アカウントを選択するか、新しい Automation アカウントを作成することができます。

提供されている Runbook は、サービス プリンシパルを使用して Azure を認証します。既存の Automation アカウントのいずれかで Runbook を実行する場合は、サービス プリンシパルが自動的に作成されます。新しい Automation アカウントを作成する場合は、アカウントとサービス プリンシパルが自動的に作成されます。いずれの場合も、**AzureRunAsCertificate** という証明書資産と **AzureRunAsConnection** という接続資産の 2 つの資産が、Automation アカウントで作成されます。Runbook は、VM に対する管理アクションを実行するために、**AzureRunAsConnection** を使用して Azure を認証します。

>[AZURE.NOTE] サービス プリンシパルは、サブスクリプションのスコープで作成され、共同作成者ロールに割り当てられます。このロールは、アカウントに対し、Automation Runbook を実行して Azure VM を管理する権限を設定するために必要です。Automation アカウントとサービス プリンシパルの作成は、1 回限りのイベントです。アカウントは、1 度作成すると、他の Azure VM アラートでの Runbook の実行にも私用できます。

**[OK]** をクリックすると、アラートが構成されます。新しい Automation アカウントを作成するオプションを選択した場合は、アカウントがサービス プリンシパルと共に作成されます。この処理の完了には数秒かかります。

![構成中の Runbook](media/automation-azure-vm-alert-integration/RunbookBeingConfigured.png)

構成が完了すると、**[アラート ルールの追加]** ブレードに Runbook の名前が表示されます。

![構成済みの Runbook](media/automation-azure-vm-alert-integration/RunbookConfigured.png)

**[アラート ルールの追加]** ブレードで **[OK]** をクリックすると、アラート ルールが作成され、仮想マシンが実行状態の場合アクティブ化されます。

### Runbook を有効または無効にする

アラートで Runbook を構成した場合、Runbook はその構成を削除せず無効にできます。これにより、アラートの実行を保持したまま、一部のアラート ルールをテストし、その後 Runbook を再度有効にすることができます。

## 概要

Azure VM でアラートを構成する場合、アラートがトリガー時に修復アクションを自動で実行するように Automation Runbook を簡単に構成できるようになりました。このリリースでは、アラート シナリオに応じて VM の再起動、停止、または削除を行う Runbook を選択できます。この新機能は、アラートのトリガー時に自動的に実行されるアクション (通知、トラブルシューティング、修復) を制御できる、便利なシナリオの始まりにすぎません。

## 次のステップ

- グラフィカルな Runbook の使用を開始するには、「[初めてのグラフィカルな Runbook](automation-first-runbook-graphical.md)」を参照してください。
- PowerShell Workflow Runbook の使用を開始するには、「[最初の PowerShell Workflow Runbook](automation-first-runbook-textual.md)」を参照してください。
- Runbook の種類とそれらの利点や制限事項の詳細については、「[Azure Automation の Runbook の種類](automation-runbook-types.md)」を参照してください。

<!---HONumber=AcomDC_0420_2016-->