---
title: Update Management、Change Tracking、および Inventory のオンボード時のエラーをトラブルシューティングする
description: Update Management、Change Tracking、および Inventory ソリューションのオンボード時のエラーをトラブルシューティングする方法について説明します。
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 52ff52ffb558278507bb24e1b1e2054c251b2512
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2018
ms.locfileid: "52879642"
---
# <a name="troubleshoot-errors-when-onboarding-solutions"></a>ソリューションをオンボードする際のエラーをトラブルシューティングする

Update Management または Change Tracking や Inventory などのソリューションをオンボードする際にエラーが発生することがあります。 この記事では、発生する可能性があるさまざまなエラーと、その解決方法について説明します。

## <a name="general-errors"></a>一般エラー

### <a name="computer-group-query-format-error"></a>シナリオ:ComputerGroupQueryFormatError

#### <a name="issue"></a>問題

このエラー コードは、ソリューションを選択するために使用された、コンピューター グループを検索する保存済みクエリの形式が正しくなかったことを意味します。 

#### <a name="cause"></a>原因

クエリを変更したか、システムによってクエリが変更された可能性があります。

#### <a name="resolution"></a>解決策

そのソリューションに対するクエリを削除し、ソリューションを再オンボードすることができます。その際にクエリが再生成されます。 クエリはワークスペース内の、**[保存された検索条件]** にあります。 クエリの名前は **MicrosoftDefaultComputerGroup** です。また、クエリのカテゴリは、このクエリに関連付けられたソリューションの名前です。 複数のソリューションが有効な場合は、**MicrosoftDefaultComputerGroup** が複数回 **[保存された検索条件]** に表示されます。

### <a name="policy-violation"></a>シナリオ:PolicyViolation

#### <a name="issue"></a>問題

このエラー コードは、1 つ以上のポリシーの違反でデプロイが失敗したことを示します。

#### <a name="cause"></a>原因 

操作の完了を阻止するポリシーが存在しています。

#### <a name="resolution"></a>解決策

ソリューションを正常にデプロイするには、指定されたポリシーの変更を検討する必要があります。 定義可能なポリシーが多数あるため、必要になる具体的な変更は、違反しているポリシーによって異なります。 たとえば、リソース グループ内の特定の種類のリソースの内容を変更する権限を拒否するようにポリシーがそのリソース グループで定義されている場合、次のような操作を実行できます。

* ポリシーを完全に削除する
* 別のリソース グループへのオンボードを試す
* ポリシーを変更する。例:
  * ポリシーの対象を特定のリソースに設定し直す (特定の Automation アカウントなど)。
  * 拒否するようにポリシーが構成されているリソースのセットを変更する

Azure Portal の右上にある通知を確認するか、Automation アカウントを含むリソース グループに移動し、**[設定]** の **[デプロイ]** を選択して、失敗したデプロイメントを表示します。 Azure Policy の詳細については、次をご覧ください。[Azure Policy の概要](../../azure-policy/azure-policy-introduction.md?toc=%2fazure%2fautomation%2ftoc.json)。

## <a name="mma-extension-failures"></a>MMA 拡張機能のエラー

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

ソリューションをデプロイすると、関連するさまざまなリソースがデプロイされます。 Microsoft Monitoring Agent 拡張機能または Log Analytics エージェント for Linux は、そのようなリソースの 1 つです。 これらは、仮想マシンのゲスト エージェント (構成済みの Log Analytics ワークスペースとの通信を担当する) によってインストールされる仮想マシン拡張機能です。オンボードしているソリューションが実行を開始した後で利用するバイナリや他のファイルのダウンロードを後から調整することが目的です。
通常は、Notifications Hub に表示される通知によって、MMA または Log Analytics エージェント for Linux のインストール エラーに最初に気付きます。 その通知をクリックすると、特定のエラーの詳しい情報が表示されます。 [リソース グループ] のリソース、さらにリソース内のデプロイ要素にナビゲートすると、発生したデプロイ エラーの詳細が示されます。
MMA または Log Analytics エージェント for Linux のインストールは、さまざまな理由からエラーが発生する可能性があるため、そうしたエラーに対処する手順は問題によって異なります。 具体的なトラブルシューティング手順はこの後で説明します。

次のセクションでは、オンボードの際に発生して MMA 拡張機能のデプロイメントのエラーを引き起こす可能性があるさまざまな問題について説明します。

### <a name="webclient-exception"></a>シナリオ:WebClient 要求で例外が発生した

仮想マシン上の MMA 拡張機能が外部リソースと通信できず、デプロイが失敗します。

#### <a name="issue"></a>問題

返されるエラー メッセージの例を次に示します。

```error
Please verify the VM has a running VM agent, and can establish outbound connections to Azure storage.
```

```error
'Manifest download error from https://<endpoint>/<endpointId>/Microsoft.EnterpriseCloud.Monitoring_MicrosoftMonitoringAgent_australiaeast_manifest.xml. Error: UnknownError. An exception occurred during a WebClient request.
```

#### <a name="cause"></a>原因

このエラーの原因として考えられるものは次のとおりです。

* 特定のポートしか許可しないプロキシが VM に構成されています。

* ファイアウォールの設定によって、必要なポートとアドレスへのアクセスがブロックされた。

#### <a name="resolution"></a>解決策

適切なポートとアドレスが通信のために開いていることを確認します。 ポートとアドレスの一覧は、[ネットワークの計画](../automation-hybrid-runbook-worker.md#network-planning)に関する記事を参照してください。

### <a name="transient-environment-issue"></a>シナリオ:環境の一時的な問題のためにインストールが失敗した

デプロイの際に、別のインストールまたは操作によって妨げられたため、Microsoft Monitoring Agent 拡張機能のインストールが失敗しました。

#### <a name="issue"></a>問題

返されるエラー メッセージの例を次に示します。

```error
The Microsoft Monitoring Agent failed to install on this machine. Please try to uninstall and reinstall the extension. If the issue persists, please contact support.
```

```error
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1618'
```

```error
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.2) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.2\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1601'
```

#### <a name="cause"></a>原因

このエラーの原因として考えられるものは次のとおりです。

* 別のインストールが進行中です。
* テンプレートのデプロイ中にシステムの再起動がトリガーされました。

#### <a name="resolution"></a>解決策

このエラーは本質的に一時的なものです。 デプロイを再試行して拡張機能をインストールしてください。

### <a name="installation-timeout"></a>シナリオ:インストールのタイムアウト

MMA 拡張機能のインストールがタイムアウトのために完了しませんでした。

#### <a name="issue"></a>問題

返されるエラー メッセージの例を次に示します。

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>原因

このエラーは、インストール中に仮想マシンの負荷が高くなったために発生します。

### <a name="resolution"></a>解決策

VM の負荷が低いときに、MMA 拡張機能をインストールしてみてください。

## <a name="next-steps"></a>次の手順

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure フォーラム](https://azure.microsoft.com/support/forums/)を通じて Azure エキスパートから回答を得ることができます。
* [@AzureSupport](https://twitter.com/azuresupport) に問い合わせる – Microsoft Azure 公式アカウントです。Azure コミュニティを適切なリソース (回答、サポート、エキスパート) に結び付けることで、カスタマー エクスペリエンスを向上します。
* さらにヘルプが必要であれば、Azure サポート インシデントを送信できます。 その場合は、 [Azure サポートのサイト](https://azure.microsoft.com/support/options/) に移動して、 **[サポートの要求]** をクリックします。
