---
title: SSIS 統合ランタイムでの接続診断のトラブルシューティング
description: この記事では、SSIS 統合ランタイムでの接続診断に関するトラブルシューティングのガイダンスを示します
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: meiyl
author: meiyl
ms.reviewer: sawinark
manager: yidetu
ms.date: 06/07/2020
ms.openlocfilehash: 8e520048a6067f134e847953f4f4aa0598d9926e
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2020
ms.locfileid: "86172454"
---
# <a name="troubleshoot-diagnose-connectivity-in-the-ssis-integration-runtime"></a>SSIS 統合ランタイムでの接続診断のトラブルシューティング

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

SSIS 統合ランタイムで SSIS パッケージを実行しているときに接続の問題が発生した場合 (特に、SSIS 統合ランタイムが Azure 仮想ネットワークに参加している場合) は、 Azure Data Factory ポータルの SSIS 統合ランタイムの監視ページにある、この接続の診断機能を使用して、問題の自己診断を試みることができます。 

 ![[監視] ページ - 接続の診断](media/ssis-integration-runtime-diagnose-connectivity-faq/ssis-monitor-diagnose-connectivity.png) ![[監視] ページ - テスト接続](media/ssis-integration-runtime-diagnose-connectivity-faq/ssis-monitor-test-connection.png)
 
この記事では、SSIS 統合ランタイムで接続をテストしているときに見つかる可能性がある最も一般的なエラーについて説明します。 ここでは、考えられる原因とそのエラーを解決するためのアクションについて説明します。 

## <a name="common-errors-potential-causes-and-recommendation-solutions"></a>一般的なエラー、考えられる原因、および推奨される解決策

### <a name="error-code-invalidinput"></a>エラー コード:InvalidInput。
* **エラー メッセージ**:Please verify your input is correct. (入力が正しいことを確認してください。)
* **考えられる原因:** 入力が正しくない。
* **推奨事項:** 入力を確認してください。

### <a name="error-code-firewallornetworkissue"></a>エラー コード:FirewallOrNetworkIssue。
* **エラー メッセージ**:Please verify that this port is open on your firewall/server/NSG and the network is stable. (ファイアウォール、サーバー、または NSG でこのポートが開いていること、およびネットワークが安定していることを確認してください。)
* **考えられる原因:** 
  * サーバーでこのポートが開かれていない。
  * このポートでのアウトバウンド トラフィックが、ネットワーク セキュリティグ ループによって拒否された
  * NVA、Azure ファイアウォール、またはオンプレミスのファイアウォールで、このポートが開かれていない。
* **推奨事項:** 
  * サーバーでこのポートを開きます。
  * このポートでアウトバウンド トラフィックが許可されるように、ネットワーク セキュリティグ ループを更新します。
  * NVA、Azure ファイアウォール、またはオンプレミスのファイアウォールで、このポートを開きます。

### <a name="error-code-misconfigureddnssettings"></a>エラー コード:MisconfiguredDnsSettings。
* **エラー メッセージ**:If you’re using your own DNS server in the VNet joined by your Azure-SSIS IR, please verify that it can resolve your host name. (Azure-SSIS IR が参加している VNet で独自の DNS サーバーを使用している場合は、ホスト名を解決できることを確認してください。)
* **考えられる原因:** 
  *  カスタム DNS の問題
  *  プライベート ホスト名に完全修飾ドメイン名 (FQDN) が使用されていない
* **推奨事項:** 
  *  カスタム DNS の問題を修正して、ホスト名を解決できることを確認してください。
  *  Azure-SSIS IR によって自動的には独自の DNS サフィックスが追加されないため、完全修飾ドメイン名 (FQDN) を使用してください (たとえば、<your_private_server> ではなく、<your_private_server>.contoso.com を使用します)。

### <a name="error-code-servernotallowremoteconenction"></a>エラー コード:ServerNotAllowRemoteConenction。
* **エラー メッセージ**:Please verify that your server allows remote TCP connections through this port. (このポートを介したリモート TCP 接続がサーバーで許可されていることを確認してください。)
* **考えられる原因:** 
  *  サーバーのファイアウォールで、リモート TCP 接続が許可されていない。
  *  サーバーがオンラインではない。
* **推奨事項:** 
  *  サーバーのファイアウォールでリモート TCP 接続を許可します。
  *  サーバーを起動します。
   
### <a name="error-code-misconfigurednsgsettings"></a>エラー コード:MisconfiguredNsgSettings。
* **エラー メッセージ**:Please verify that the NSG of your VNet allows outbound traffic through this port. (このポートを介したアウトバウンド トラフィックが VNet の NSG で許可されていることを確認してください。) If you’re using Azure ExpressRoute and or a UDR, please verify that this port is open on your firewall/server. (Azure ExpressRoute または UDR を使用している場合は、このポートがファイアウォールまたはサーバーで開かれていることを確認してください。)
* **考えられる原因:** 
  *  このポートでのアウトバウンド トラフィックが、ネットワーク セキュリティグ ループによって拒否された。
  *  NVA、Azure ファイアウォール、またはオンプレミスのファイアウォールで、このポートが開かれていない。
* **推奨事項:** 
  *  このポートでアウトバウンド トラフィックが許可されるように、ネットワーク セキュリティグ ループを更新します。
  *  NVA、Azure ファイアウォール、またはオンプレミスのファイアウォールで、このポートを開きます。

### <a name="error-code-genericissues"></a>エラー コード:GenericIssues。
* **エラー メッセージ**:Test connection failed due to generic issues. (一般的な問題が原因でテスト接続に失敗しました。)
* **考えられる原因:** テスト接続で一時的に一般的な問題が発生した。
* **推奨事項:** 後でテスト接続を再試行してください。 再試行しても解決されない場合は、Azure Data Factory サポート チームにお問い合わせください。


### <a name="error-code-pspingexecutiontimeout"></a>エラー コード:PSPingExecutionTimeout。
* **エラー メッセージ**:Test connection timeout, please try again later. (テスト接続がタイムアウトしました。後でもう一度やり直してください。)
* **考えられる原因:** テスト接続がタイムアウトした。
* **推奨事項:** 後でテスト接続を再試行してください。 再試行しても解決されない場合は、Azure Data Factory サポート チームにお問い合わせください。

### <a name="error-code-networkinstable"></a>エラー コード:NetworkInstable。
* **エラー メッセージ**:Test connection irregularly succeeded due to network instability. (ネットワークが不安定なため、テスト接続が不規則にしか成功しませんでした。)
* **考えられる原因:** 一時的なネットワークの問題。
* **推奨事項:** サーバーまたはファイアウォール ネットワークが安定しているかどうかを確認してください。

## <a name="next-steps"></a>次のステップ

- パッケージをデプロイします。 詳しくは、[SSMS を使用した Azure への SSIS プロジェクトのデプロイ](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-deploy-ssms)に関する記事をご覧ください。
- パッケージを実行します。 詳しくは、[SSMS を使用した Azure での SSIS パッケージの実行](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-run-ssms)に関する記事をご覧ください。
- パッケージをスケジュールします。 詳細については、「[Azure で SSIS パッケージのスケジュールを設定する](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms?view=sql-server-ver15)」を参照してください。

