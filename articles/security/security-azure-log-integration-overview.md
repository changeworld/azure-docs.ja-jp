<properties
   pageTitle="Microsoft Azure ログ統合の概要 | Microsoft Azure"
   description="Azure ログ統合と、その主な機能およびしくみについて紹介します。"
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="MBaldwin"
   editor="TerryLanfear"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/24/2016"
   ms.author="TomSh"/>

# Microsoft Azure ログ統合の概要 (プレビュー)

Azure ログ統合と、その主な機能およびしくみについて紹介します。

## Overview

Azure でホストされるサービスとしてのプラットフォーム (PaaS) とサービスとしてのインフラストラクチャ (IaaS) によって、大量のデータがセキュリティ ログに生成されます。このログには、ポリシー違反、内部および外部の脅威、法規制遵守に関する問題、および異常なネットワーク、ホスト、ユーザー アクティビティに関する強力な洞察、およびインテリジェンスを提供する重要な情報が含まれます。

Azure ログ統合を使用すると、未加工のログを、Azure リソースからオンプレミスのセキュリティ情報/イベント管理 (SIEM) システムに統合できます。Azure ログ統合は、Windows *(WAD)* の仮想マシンから Azure 診断を収集するだけでなく、Web アプリケーション ファイアウォール (WAF) などのパートナー ソリューションによる診断も収集します。この統合は、すべての資産に対してオンプレミスまたはクラウドの統合ダッシュボードを提供します。これによりセキュリティ イベントの集計、関連付け、分析を実行し、警告を生成できます。

![Azure ログ統合][1]

## 統合できるログ

Azure サービスでは、すべてのサービスの広範なログ記録を作成します。これらのログは、主に次の 2 つのタイプに分類されます。

- **コントロール/管理ログ**。Azure Resource Manager の CREATE、UPDATE、および DELETE 操作の可視性を提供します。Azure 監査ログは、このタイプのログです。
- **データ プレーン ログ**。Azure リソース使用の一環として発生するイベントの可視性を提供します。仮想マシンの Windows イベントのシステム ログ、セキュリティ ログ、およびアプリケーション ログは、このタイプのログです。

Azure ログ統合では、現在、Azure 監査ログ、仮想マシンのログ、および Azure Security Center の警告の統合がサポートされています。

Azure ログ統合に関する質問がある場合は、[AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com) 宛に電子メールを送信してください。

## 次のステップ

このドキュメントでは、Azure ログ統合について紹介しました。Azure ログ統合と、サポートされているログのタイプの詳細については、次をご覧ください。

- [Azure ログ用の Microsoft Azure ログ統合 (プレビュー)](https://www.microsoft.com/download/details.aspx?id=53324) – Azure ログ統合の詳細情報、システム要件、およびインストール手順のダウンロード センター。
- [Azure ログ統合の使用](security-azure-log-integration-get-started.md) – このチュートリアルでは、Azure ログ統合のインストール、Azure ストレージのログ、Azure 監査ログ、および Security Center の警告の統合について説明します。
- [パートナーの構成手順](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – このブログ投稿では、Splunk、HP ArcSight、IBM QRadar などのパートナー ソリューションを使用できるように、Azure ログ統合を構成する方法について説明します。
- [Azure ログ統合のよく寄せられる質問 (FAQ)](security-azure-log-integration-faq.md) – この FAQ は、Azure ログ統合について寄せられる質問とその回答です。
- [Azure ログ統合への Security Center の警告の統合](../security-center/security-center-integrating-alerts-with-log-integration.md) – このドキュメントでは、Azure Security Center の警告を、Azure 診断および Azure 監査ログによって収集された仮想マシンのセキュリティ イベントとともに、ログ分析または SIEM ソリューションと同期させる方法について説明します。
- [Azure 診断および Azure 監査ログの新機能](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) – このブログ投稿では、Azure 監査ログと、Azure リソースの操作の洞察を得るのに役立つその他の機能を紹介します。

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png

<!---HONumber=AcomDC_0921_2016-->