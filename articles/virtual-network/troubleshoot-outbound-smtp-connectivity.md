---
title: Azure でのアウトバウンド SMTP 接続のトラブルシューティング | Microsoft Docs
description: 推奨されるメール送信方法と Azure でのアウトバウンド SMTP 接続に関する問題のトラブルシューティング方法について説明します。
services: virtual-network
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/28/2021
ms.author: genli
ms.openlocfilehash: 5c4c129514ccf2fe3b55fc173d3fd4b87fd50715
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130248965"
---
# <a name="troubleshoot-outbound-smtp-connectivity-problems-in-azure"></a>Azure でのアウトバウンド SMTP 接続に関する問題のトラブルシューティング

仮想マシン (VM) から TCP ポート 25 上で外部ドメイン (outlook.com や gmail.com など) に直接送信される送信電子メール メッセージは、その VM が特定のサブスクリプションの種類でデプロイされている場合にのみ可能です。

> [!IMPORTANT]
> 以下のすべての例で、このプロセスは仮想マシンと VM スケール セットのリソース (`Microsoft.Compute/virtualMachines` & `Microsoft.Compute/virtualMachineScaleSets`) に "*のみ*" 適用されます。 すべての Azure PaaS (サービスとしてのプラットフォーム) リソース ([Azure App Service](https://azure.microsoft.com/services/app-service) や [Azure Functions](https://azure.microsoft.com/services/functions) など) で、ポート 25 での電子メールの送信はサポートされていません。

## <a name="recommended-method-of-sending-email"></a>メール送信の推奨される方法

Azure VM から、または Azure App Service からメールを送信する場合は、認証済み SMTP リレー サービスを使用することをお勧めします。 (これらのリレー サービスは通常、TCP ポート 587 経由で接続しますが、他のポートもサポートしています)。これらのサービスは、IP やドメインの評価を維持して、外部ドメインによってメッセージが拒否されたり、SPAM フォルダーに格納されたりする可能性を最小限に抑えるために使用されます。 [SendGrid](https://sendgrid.com/partners/azure/) は、このような SMTP リレー サービスの 1 つですが、他にもあります。 また、認証済み SMTP リレー サービスをオンプレミス サーバー上で使用することもできます。

サブスクリプションの種類に関係なく、Azure ではこれらのメール配信サービスの使用は制限されません。

## <a name="enterprise-agreement"></a>Enterprise Agreement

Enterprise Agreement サブスクリプションにデプロイされている VM の場合、TCP ポート 25 上の送信 SMTP 接続はブロックされません。 ただし、その VM からの受信電子メールが外部ドメインによって受け入れられる保証はありません。 電子メールが外部ドメインによって拒否またはフィルター処理された場合は、その外部ドメインの電子メール サービス プロバイダーに連絡して問題を解決する必要があります。 これらの問題は Azure サポートの対象ではありません。

## <a name="pay-as-you-go"></a>従量課金制

Azure プラットフォームでは、従量課金制サブスクリプションにデプロイされている VM の TCP ポート 25 上の送信 SMTP 接続をブロックします。 Azure サブスクリプションが健全な状態にあり、かつ支払履歴が十分である場合、このブロックは削除できます。 [Azure portal](https://portal.azure.com) で Azure Virtual Network リソースの **[診断と解決]** ブレードの **[電子メールを送信できない (SMTP ポート 25)]** セクションに移動することによって、この制限が削除されるように要求できます。 

従量課金制サブスクリプションがこのブロックから除外され、Azure portal で VM が停止および再起動されると、それ以降はそのサブスクリプション内のすべての VM が除外されます。 除外されるのは、リクエストされたサブスクリプションのみであり、インターネットに直接ルーティングされる VM トラフィックのみです。

> [!NOTE]
> Microsoft は、サービス使用条件の違反が発生していると判断された場合に、これらの免除を取り消す権利を留保します。

## <a name="msdn-azure-pass-azure-in-open-education-azure-for-students-visual-studio-and-free-trial"></a>MSDN、Azure Pass、Azure イン オープン プラン、Education、Azure for Students、Visual Studio、無料試用版

Azure プラットフォームでは、次のサブスクリプションの種類にデプロイされている VM の TCP ポート 25 上の送信 SMTP 接続をブロックします。

- MSDN
- Azure Pass
- Azure イン オープン プラン
- Education
- Microsoft Azure for Students
- 無料試用版
- Visual Studio サブスクリプション  

制限は不正使用を防ぐために実施されます。 これらの制限を解除するリクエストは許可されません。

これらのサブスクリプションの種類を使用している場合は、この記事の前の方で概説されているように認証済み SMTP リレー サービスを使用するか、またはサブスクリプションの種類を変更することをお勧めします。

## <a name="cloud-solution-provider"></a>クラウド ソリューション プロバイダー

Azure プラットフォームでは、クラウド ソリューション プロバイダー サブスクリプションにデプロイされている VM の TCP ポート 25 上の送信 SMTP 接続をブロックします。 このブロックは削除できます。 このブロックが削除されるように要求するには、Azure portal で Azure Virtual Network リソースの **[診断と解決]** ブレードの **[電子メールを送信できない (SMTP ポート 25)]** セクションに移動し、サポート リクエストを開きます。

## <a name="microsoft-partner-network-bizspark-plus-or-azure-sponsorship"></a>Microsoft Partner Network、BizSpark Plus、または Azure スポンサー プラン

Azure プラットフォームでは、次のサブスクリプションにデプロイされている VM の TCP ポート 25 上の送信 SMTP 配信の試みをブロックします。

- Microsoft Partner Network (MPN)
- BizSpark Plus
- Azure スポンサー プラン

このブロックは削除できます。 このブロックが削除されるように要求するには、Azure portal で Azure Virtual Network リソースの **[診断と解決]** ブレードの **[電子メールを送信できない (SMTP ポート 25)]** セクションに移動し、サポート リクエストを開きます。

そのサブスクリプションがこのブロックから除外され、VM が停止および再起動されると、それ以降はそのサブスクリプション内のすべての VM が除外されます。 この除外は、要求されたサブスクリプションにのみ、かつインターネットに直接ルーティングされる VM トラフィックにのみ適用されます。

> [!NOTE]
> Microsoft は、サービスの使用条件の違反が発生したことが特定された場合は、これらの除外を取り消す権利を留保します。

## <a name="changing-subscription-type"></a>サブスクリプションの種類の変更

サブスクリプションの種類を Enterprise Agreement から別の種類のサブスクリプションに変更した場合は、デプロイを変更すると送信 SMTP がブロックされることがあります。 サブスクリプションの種類を Enterprise Agreement から別の種類のサブスクリプションに変更する予定があり、かつ TCP ポート 25 上の送信 SMTP が必要な場合は、サブスクリプションの種類を変更する前に、サポートと連携してそのサブスクリプションのブロックを解除するようにしてください。

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください

まだ支援が必要な場合は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。 この問題種別を選択します: **[Technical]\(技術関連\)**  >  **[Virtual Network]\(仮想ネットワーク\)**  >  **[Cannot send email (SMTP/Port 25)]\(メールを送信できない \(SMTP 25 番ポート\)\)** 。
