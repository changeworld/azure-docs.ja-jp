<properties
	pageTitle="Azure Automation で Azure 仮想マシンを垂直方向にスケーリングする| Microsoft Azure"
	description="Azure Automation によるアラートの監視に応じて仮想マシンを垂直方向にスケーリングする方法"
	services="virtual-machines"
	documentationCenter=""
	authors="singhkay"
	manager="drewm"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/29/2016"
	ms.author="singhkay"/>

# Azure Automation で Azure 仮想マシンを垂直方向にスケーリングする

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]クラシック デプロイ モデル

垂直方向のスケーリングは、ワークロードへの応答時にコンピューターのリソースを増減するプロセスです。Azure では、仮想マシンのサイズを変更することで実行できます。これは、次のようなシナリオで役立ちます。

- 仮想マシンが頻繁に使用されていない場合、より小さなサイズに変更して、毎月のコストを削減することができます
- 仮想マシンでピーク時の負荷が表れている場合、より大きなサイズに変更して、容量を増やすことができます

これを実現するための手順の概要を、以下に示します

1. Azure Automation をセットアップして、Virtual Machines にアクセスする
2. サブスクリプションに Azure Automation の垂直スケールの Runbook をインポートする
3. Webhook を Runbook に追加する
4. 仮想マシンにアラートを追加する

> [AZURE.NOTE] 最初の仮想マシンのサイズによっては、スケーリングできるサイズが制限される場合があります。これは、その仮想マシンがデプロイされているクラスターの空き容量によるものです。この記事で使用される公開済みの Automation Runbook では、このケースのみを扱い、VM のサイズ ペアを超えない範囲でのみスケーリングします。つまり、Standard\_D1v2 仮想マシンが急に Standard\_G5 にスケールアップしたり、Basic\_A0 にスケールダウンしたりすることはありません。

>| VM サイズのスケーリングのペア | |
|---|---|
| Basic\_A0 | Basic\_A4 |
| Standard\_A0 | Standard\_A4 |
| Standard\_A5 | Standard\_A7 |
| Standard\_A8 | Standard\_A9 |
| Standard\_A10 | Standard\_A11 |
| Standard\_D1 | Standard\_D4 |
| Standard\_D11 | Standard\_D14 |
| Standard\_DS1 | Standard\_DS4 |
| Standard\_DS11 | Standard\_DS14 |
| Standard\_D1v2 | Standard\_D5v2 |
| Standard\_D11v2 | Standard\_D14v2 |
| Standard\_G1 | Standard\_G5 |
| Standard\_GS1 | Standard\_GS5 |

## Azure Automation をセットアップして、Virtual Machines にアクセスする

このセクションでは、次のタスクを実行します。

* Active Directory でユーザーを作成します
* ユーザーのログイン情報を含む、AutomationPSCredential を作成します
* ユーザーがサブスクリプション内のリソースにアクセスできるようにセットアップします

サブスクリプションで Azure Automation Runbook を実行するには、Azure Automation にサブスクリプションへのアクセス許可が必要です。これは、Active Directory で別のユーザーを作成することで行います。次に、ユーザーが Azure に対する認証を行えるようにする AutomationPSCredential を作成して、仮想マシンのサイズを変更する PowerShell コマンドを実行する必要があります。

ユーザーと、AutomationPSCredential を作成するチュートリアルは、次の記事を参照してください。

* [Azure Automation の構成](../automation/automation-configuring.md)

ユーザーを作成したら、そのユーザーを従来のリソースの共同管理者にし、Azure Resource Manager のリソースに対する "所有者" 役割を与えます。

従来のリソースにアクセスできるように、クラシック ポータルでユーザーを共同管理者として追加する必要があります。

![古いポータルの共同管理者](./media/virtual-machines-vertical-scaling-automation/old-portal-automation-user.png)

ユーザーが Azure Resource Manager の Virtual Machines にアクセスできるように、Azure ポータルを使用する必要があります。

![新しいポータルの管理者](./media/virtual-machines-vertical-scaling-automation/new-portal-automation-user.png)

## サブスクリプションに Azure Automation の垂直スケールの Runbook をインポートする

垂直方向へのスケーリングに必要な Runbook は、Azure Automation Runbook ギャラリーに既に公開されています。これを、サブスクリプションにインポートする必要があります。Runbook をインポートする方法は、次の記事を参照してください。

* [Azure Automation 用の Runbook ギャラリーとモジュール ギャラリー](../automation/automation-runbook-gallery.md)

インポートする必要がある Runbook を次の図に示します

![Runbook のインポート](./media/virtual-machines-vertical-scaling-automation/scale-runbooks.png)

## Webhook を Runbook に追加する

Runbook をインポートしたら、仮想マシンからのアラートによって Webhook がトリガーされるように、Runbook に追加する必要があります。Runbook で Webhook を作成する方法の詳細は、次の記事を参照してください。

* [Azure Automation Webhook](../automation/automation-webhooks.md)

Webhook のダイアログを閉じる前に、Webhook をコピーしてください。これは次のセクションで必要になります。

## 仮想マシンにアラートを追加する

1. 仮想マシンの設定を選択する
2. "アラート ルール" を作成する
3. "アラートの追加" を選択する
4. アラートを発生させるメトリックを選択する
5. アラートを発生させるための条件を選択する
6. 手順 5 の条件で満たす必要があるしきい値を選択する
7. 手順 5 と 6 で選択した条件としきい値を監視サービスが確認する期間を選択する
8. 前のセクションからコピーした Webhook を貼り付ける

![アラートを仮想マシン 1 に追加](./media/virtual-machines-vertical-scaling-automation/add-alert-webhook-1.png)

![アラートを仮想マシン 2 に追加](./media/virtual-machines-vertical-scaling-automation/add-alert-webhook-2.png)

<!---HONumber=AcomDC_0330_2016------>