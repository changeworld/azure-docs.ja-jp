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
ms.date: 11/20/2018
ms.author: genli
ms.openlocfilehash: d2e5996da5a1fe3f5b154d57ee509f25e54e30ac
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2020
ms.locfileid: "96862362"
---
# <a name="troubleshoot-outbound-smtp-connectivity-issues-in-azure"></a>Azure でのアウトバウンド SMTP 接続に関する問題のトラブルシューティング

2017 年 11 月 15 日以降、外部ドメイン (outlook.com や gmail.com など) に直接送信される仮想マシン (VM) からのアウトバウンド メール メッセージは、Microsoft Azure の特定のサブスクリプションの種類でのみ使用できます。 TCP ポート 25 を使用するアウトバウンド SMTP 接続はブロックされました (ポート 25 は主に、認証されていないメール配信で使用されます)。

この動作の変更は、2017 年 11 月 15 日以降の新しいサブスクリプションと新しいデプロイにのみ適用されます。

## <a name="recommended-method-of-sending-email"></a>メール送信の推奨される方法

Azure VM からまたは Azure App Services からメールを送信する場合は、(通常は TCP ポート 587 または 443 を介して接続されるが、他のポートもサポートされる) 認証済み SMTP リレー サービスを使用することをお勧めします。 これらのサービスは、サード パーティのメール プロバイダーでメッセージが拒否される可能性を最小限に抑えるために、IP またはドメインの評価を維持する場合に使用されます。 このような SMTP リレー サービスには [SendGrid](https://sendgrid.com/partners/azure/) が含まれますが、この限りではありません。 使用できるオンプレミスで実行されている安全な SMTP リレー サービスが存在する可能性もあります。

サブスクリプションの種類に関係なく、Azure ではこれらのメール配信サービスの使用は制限されません。

## <a name="enterprise-agreement"></a>Enterprise Agreement

Enterprise Agreement の Azure ユーザーの場合、認証済みリレーを使用せずにメールを送信する技術的能力は変わりません。 新規と既存の両方の Enterprise Agreement ユーザーは、Azure VM から外部のメール プロバイダーに直接アウトバウンド メール配信を試行することができます。Azure プラットフォームによる制限はありません。 メール プロバイダーで特定のユーザーからの受信メールが受け入れられることは保証されませんが、配信の試行は、Enterprise Agreement サブスクリプション内の VM の Azure プラットフォームによってブロックされることはありません。 メール プロバイダーと直接協力して、特定のプロバイダーに関連するメッセージ配信やスパム フィルター処理の問題を修正する必要があります。

## <a name="pay-as-you-go"></a>従量課金制

2017 年 11 月 15 日より前に従量課金制のサブスクリプションにサインアップした場合、アウトバウンド メール配信を試行するための技術的能力は変わりません。 これらのサブスクリプション内の Azure VM から外部のメール プロバイダーへの直接のアウトバウンド メール配信は、引き続き試行できます。Azure プラットフォームによる制限はありません。 ここでも、メール プロバイダーで特定のユーザーからの受信メールが受け入れられることは保証されません。ユーザーはメール プロバイダーと直接協力して、特定のプロバイダーに関連するメッセージ配信やスパム フィルター処理の問題を修正する必要があります。

2017 年 11 月 15 日以降に作成された従量課金制サブスクリプションの場合は、技術的な制限により、これらのサブスクリプション内の VM から直接送信されるメールがブロックされます。 Azure VM から外部の電子メール プロバイダーに (認証済み SMTP リレーを使用せずに) 電子メールを直接送信する機能が必要で、支払い履歴のある良好なアカウントがある場合は、Azure portal の **[Diagnose and Solve]\(診断と解決\)** ブレードの **[Connectivity]\(接続\)** セクションで Azure Virtual Network リソースに対する制限を削除するようにリクエストできます。 資格がある場合は、サブスクリプションが有効になるか、次の手順に関する指示が表示されます。 

従量課金制サブスクリプションが除外され、VM が Azure portal で一旦停止された後に開始されると、それ以降はそのサブスクリプション内のすべての VM が除外されます。 除外されるのは、リクエストされたサブスクリプションのみであり、インターネットに直接ルーティングされる VM トラフィックのみです。

> [!NOTE]
> Microsoft は、サービス使用条件の違反が発生していると判断された場合にこの免除を取り消す権利を留保します。

## <a name="msdn-azure-pass-azure-in-open-education-azure-for-students-vistual-studio-and-free-trial"></a>MSDN、Azure Pass、Azure イン オープン プラン、Education、Azure for Students、Visual Studio、無料試用版

2017 年 11 月 15 日以降に MSDN、Azure Pass、Azure イン オープン プラン、Education、Azure Student、無料試用版、または Visual Studio サブスクリプションを作成した場合、技術的な制限により、これらのサブスクリプション内の VM からメール プロバイダーに直接送信されるメールがブロックされます。 制限は不正使用を防ぐために行われます。 この制限を解除する要求は許可されません。

これらのサブスクリプションの種類を使用している場合は、この記事の前の方で概説されているように SMTP リレー サービスを使用するか、またはサブスクリプションの種類を変更することをお勧めします。

## <a name="cloud-service-provider-csp"></a>クラウド サービス プロバイダー (CSP)

CSP を介して Azure リソースを使用している場合は、Azure portal の **[Diagnose and Solve]\(診断と解決\)** ブレードの **[Connectivity]\(接続\)** セクションで Virtual Network リソースに対する制限を削除するようにリクエストできます。 資格がある場合は、サブスクリプションが有効になるか、次の手順に関する指示が表示されます。

## <a name="microsoft-partner-network-mpn-bizspark-plus-or-azure-sponsorship"></a>Microsoft Partner Network (MPN)、BizSpark Plus、または Azure スポンサー プラン

2017 年 11 月 15 日以降に作成された Microsoft Partner Network (MPN)、BizSpark Plus、または Azure スポンサー プランでは、技術的な制限により、これらのサブスクリプション内の VM から直接送信されるメールがブロックされます。 外部のメール プロバイダーに Azure VM から (認証済み SMTP リレーを使用せずに) 直接メールを送信する機能が必要な場合は、次の問題の種類を使用してサポート ケースを開くことによってリクエストできます。 **[Technical]\(技術\)**  >  **[仮想ネットワーク]**  >  **[接続]**  >  **[Cannot send email (SMTP/Port 25)]\(メールを送信できない (SMTP/ポート 25)\)** 。 デプロイで、認証済みリレーを使用するのではなく、メール プロバイダーに直接メールを送信する必要がある理由の詳細を必ず追加してください。 リクエストは Microsoft の裁量でレビューおよび承認されます。 リクエストは、不正行為に関する追加確認が完了した後にのみ許可されます。 

サブスクリプションが除外され、VM が Azure portal で一旦停止された後に開始されると、それ以降はそのサブスクリプション内のすべての VM が除外されます。 除外されるのは、リクエストされたサブスクリプションのみであり、インターネットに直接ルーティングされる VM トラフィックのみです。

## <a name="restrictions-and-limitations"></a>制限事項と制約事項

- [Azure Firewall](https://azure.microsoft.com/services/azure-firewall/) などの Azure PaaS サービスを介してポート 25 のトラフィックをルーティングすることはサポートされていません。

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください

お困りの際は、問題を迅速に解決するために、次の問題の種類を使用して [サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。 **[Technical]\(技術\)**  >  **[仮想ネットワーク]**  >  **[接続]**  >  **[Cannot send email (SMTP/Port 25)]\(メールを送信できない (SMTP/ポート 25)\)** 。
