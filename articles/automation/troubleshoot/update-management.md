---
title: Update Management のエラーをトラブルシューティングする
description: Update Management の問題をトラブルシューティングする方法を説明します。
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: b77d1210ff48a4bd30834fcbad64173bf77b1290
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064601"
---
# <a name="troubleshooting-issues-with-update-management"></a>Update Management の問題をトラブルシューティングする

この記事では、Update Management の使用中に発生する可能性がある問題を解決する方法を説明します。

## <a name="windows"></a>Windows

ソリューションまたは仮想マシンをオンボードしようとして問題が発生した場合は、ローカル コンピューター上の **[アプリケーションとサービス ログ]** にある **Operations Manager** イベント ログで、イベント ID **4502** のイベントと **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent** を含むイベント メッセージを確認してください。

次のセクションでは、具体的なエラー メッセージと、考えられる個別の解決策を示します。 他のオンボードの問題については、[ソリューションをオンボードする際のトラブルシューティング](onboarding.md)に関する記事を参照してください。

### <a name="machine-already-registered"></a>シナリオ: マシンが違うアカウントに既に登録されている

#### <a name="issue"></a>問題

次のエラー メッセージが表示されます。

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

#### <a name="cause"></a>原因

マシンが既に Update Management 用の別のワークスペースにオンボードされています。

#### <a name="resolution"></a>解決策

[Hybrid Runbook グループを削除する](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group)ことにより、マシン上の古いアーティファクトのクリーンアップを実行してから、再試行します。

### <a name="machine-unable-to-communicate"></a>シナリオ: マシンがサービスと通信できない

#### <a name="issue"></a>問題

次のいずれかのエラー メッセージが表示されます。

```
Unable to Register Machine for Patch Management, Registration Failed with Exception System.Net.Http.HttpRequestException: An error occurred while sending the request. ---> System.Net.WebException: The underlying connection was closed: An unexpected error occurred on a receive. ---> System.ComponentModel.Win32Exception: The client and server can't communicate, because they do not possess a common algorithm
```

```
Unable to Register Machine for Patch Management, Registration Failed with Exception Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value.
```

```
The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.
```

#### <a name="cause"></a>原因

ネットワーク通信をブロックしているプロキシ、ゲートウェイ、またはファイアウォールが存在する可能性があります。

#### <a name="resolution"></a>解決策

ネットワークを見直し、適切なポートとアドレスが許可されていることを確認します。 Update Management および Hybrid Runbook Worker で必要なポートとアドレスの一覧については、[ネットワーク要件](../automation-hybrid-runbook-worker.md#network-planning)を参照してください。

### <a name="unable-to-create-selfsigned-cert"></a>シナリオ: 自己署名証明書を作成できない

#### <a name="issue"></a>問題

次のいずれかのエラー メッセージが表示されます。

```
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

#### <a name="cause"></a>原因

Hybrid Runbook Worker が自己署名証明書を生成できませんでした。

#### <a name="resolution"></a>解決策

フォルダー **C:\ProgramData\Microsoft\Crypto\RSA** への読み取りアクセスがシステム アカウントにあることを確認してから、再試行します。

## <a name="linux"></a>Linux

### <a name="scenario-update-run-fails-to-start"></a>シナリオ: 更新プログラムの実行を開始できない

#### <a name="issue"></a>問題

Linux マシンで更新プログラムの実行を開始できません。

#### <a name="cause"></a>原因

Linux ハイブリッド worker が異常です。

#### <a name="resolution"></a>解決策

次のログ ファイルのコピーを作成し、トラブルシューティングのために保存します。

```
/var/opt/microsoft/omsagent/run/automationworker/worker.log
```

### <a name="scenario-update-run-starts-but-encounters-errors"></a>シナリオ: 更新プログラムの実行が開始されるがエラーが発生する

#### <a name="issue"></a>問題

更新プログラムの実行が開始されますが、実行中にエラーが発生します。

#### <a name="cause"></a>原因

次の原因が考えられます。

* パッケージ マネージャーが異常です。
* 特定のパッケージがクラウドに基づく修正プログラムと干渉しています。
* その他の理由。

#### <a name="resolution"></a>解決策

Linux 上で正常に開始した後に更新プログラムの実行中にエラーが発生した場合は、実行で影響を受けるマシンからの出力を確認します。 コンピューターのパッケージ マネージャーからの特定のエラー メッセージが見つかれば、調査して対処することができます。 Update Management で更新プログラムを展開するには、パッケージ マネージャーが正常である必要があります。

場合によっては、Update Management とパッケージの更新プログラムが干渉して、更新プログラムの展開が完了されていないことがあります。 この場合、これらのパッケージを今後の更新プログラムの実行から除外するか、それらを手動でインストールする必要があります。

修正プログラムの問題を解決できない場合は、次のログ ファイルをコピーし、トラブルシューティングのために、次の更新プログラムの展開が開始される**前に**保存します。

```
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="next-steps"></a>次の手順

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure フォーラム](https://azure.microsoft.com/support/forums/)を通じて Azure エキスパートから回答を得ることができます。
* [@AzureSupport](https://twitter.com/azuresupport) に問い合わせる – Microsoft Azure 公式アカウントです。Azure コミュニティを適切なリソース (回答、サポート、エキスパート) に結び付けることで、カスタマー エクスペリエンスを向上します。
* さらにヘルプが必要であれば、Azure サポート インシデントを送信できます。 その場合は、 [Azure サポートのサイト](https://azure.microsoft.com/support/options/) に移動して、 **[サポートの要求]** をクリックします。