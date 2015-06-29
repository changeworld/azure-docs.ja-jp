<properties
   pageTitle="Azure Automation での Runbook の実行"
   description="Azure Automation で Runbook が処理される方法の詳細について説明します。"
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/28/2015"
   ms.author="bwren" />

# Azure Automation での Runbook の実行


Azure Automation で runbook を開始するときに、ジョブが作成されます。ジョブは、Runbook の単一の実行インスタンスです。各ジョブを実行する Azure Automation ワーカーが割り当てられます。ワーカーは複数の Azure アカウントで共有されるが、さまざまな Automation アカウントからのジョブは互いに分離されます。ジョブに対する要求をどのワーカーで処理するかを制御することはできません。1 つの Runbook で、複数のジョブを同時に実行することができます。Azure ポータルで Runbook の一覧を表示すると、各 Runbook に対して最後に起動されたジョブの状態が一覧表示されます。それぞれの状態を追跡するために、Runbook ごとにジョブの一覧を表示できます。ジョブのさまざまな状態の説明については、「[ジョブの状態](automation-viewing-the-status-of-a-runbook-job.md#job-statuses)」を参照してください。

![ジョブの状態](./media/automation-runbook-execution/job-statuses.png)


ジョブは、Azure サブスクリプションに接続することにより Azure リソースにアクセスします。データ センター内のリソースにパブリック クラウドからアクセスできる場合、ジョブはそれらのリソースにのみアクセスします。

## アクセス許可

Azure Automation の Runbook は、通常、Azure サブスクリプション内のリソースへのアクセスを必要とします。「[Azure Active Directory を使用した Azure への認証](http://aka.ms/runbookauthor/authentication)」では、Azure リソースで認証するために Azure Automation の[資格情報]()と Azure Active Directory を構成する方法について説明します。このトピックでは、作成する Runbook 内の Azure リソースに [Add-AzureAccount コマンドレット](http://aka.ms/runbookauthor/azurecmdlets/addazureaccount)を使用してアクセスする方法についても説明します。セキュリティ要件については、インポートする個々の Runbook のマニュアルを参照してください。

## フェア シェア

クラウド内のすべての Runbook 間でリソースを共有するために、Azure Automation は任意のジョブが 3 時間実行された後で一時的にそのジョブをアンロードし、次にそのジョブをその最後の[チェックポイント](http://aka.ms/runbookauthor/checkpoints)から再起動します。この間、ジョブの状態は [実行中、リソースを待機中] となります。Runbook がチェックポイントを持っていないか、またはアンロードされる前にジョブがまだ最初のチェックポイントに達していない場合、ジョブは最初から再開されます。

Runbook が同じチェックポイントから、または Runbook の先頭から 3 回連続して再起動した場合、Runbook は終了し、[失敗、リソースを待機中] という状態になります。これは、Runbook が次のチェックポイントに進むことができず再度アンロードされない場合に、Runbook が完了せずに無期限に実行されるのを防ぐためのものです。この場合は、エラー発生時に次の例外を受信します。

ジョブは同じチェックポイントから繰り返し削除されましたので実行を継続できません。Runbook が、その状態を維持しないで時間のかかる操作を実行するということになっていないことを確認してください。

Runbook を作成する際には、2 つのチェックポイント間で任意のアクティビティを実行するのにかかる時間が 3 時間を超えないことを確認してください。この 3 時間制限に決して到達しないように、Runbook にチェックポイントを追加することが必要な場合があります。実行に時間のかかる処理操作を分割することが必要な場合もあります。たとえば、Runbook が大規模な SQL データベースで再インデックス化を実行する可能性があります。この単一処理がフェア シェア制限内で完了しない場合、ジョブはアンロードされ、先頭から再開されます。この場合は再インデックス化処理を複数のステップに分割します。たとえば、一度に 1 つのテーブルを再インデックス化し、各処理の後にチェックポイントを挿入します。こうすれば、最後の処理が完了した後、ジョブは再開することが可能です。

## 関連記事:

- [Azure Automation での Runbook の開始](automation-starting-a-runbook)
- [Azure Automation で Runbook ジョブの状態を表示する方法](automation-viewing-the-status-of-a-runbook-job)
 

<!---HONumber=58_postMigration-->