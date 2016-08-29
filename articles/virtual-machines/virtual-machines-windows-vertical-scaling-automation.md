<properties
	pageTitle="Azure Automation で Azure 仮想マシンを垂直方向にスケーリングする | Microsoft Azure"
	description="Azure Automation で監視アラートに応じて Windows 仮想マシンを垂直方向にスケーリングする方法"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="singhkays"
	manager="drewm"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/29/2016"
	ms.author="singhkay"/>

# Azure Automation で Azure 仮想マシンを垂直方向にスケーリングする

垂直方向のスケーリングは、ワークロードに応じてコンピューターのリソースを増減するプロセスです。Azure では、仮想マシンのサイズを変更することで実行できます。これは、次のようなシナリオで役立ちます。

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

最初に、仮想マシンをスケーリングするために使用する runbook をホストする、Azure Automation アカウントを作成する必要があります。最近、Automation サービスでは、ユーザーの代わりに非常に簡単に Runbook を自動的に実行するためのサービス プリンシパルをセットアップする "アカウントとして実行" 機能が導入されました。詳しくは、次の記事を参照してください。

* [Azure 実行アカウントを使用した Runbook の認証](../automation/automation-sec-configure-azure-runas-account.md)

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

<!---HONumber=AcomDC_0817_2016-->