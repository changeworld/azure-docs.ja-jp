---
title: Azure Firewall を使用して Azure Virtual Desktop を保護する
description: Azure Firewall を使用して Azure Virtual Desktop のデプロイを保護する方法について説明します
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 10/06/2021
ms.author: victorh
ms.openlocfilehash: 9ac87633dbb4d1b21dfe4fa4430012d91243b8d5
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2021
ms.locfileid: "129620509"
---
# <a name="use-azure-firewall-to-protect-azure-virtual-desktop-deployments"></a>Azure Firewall を使用して Azure Virtual Desktop のデプロイを保護する

Azure Virtual Desktop は、Azure で実行されるデスクトップおよびアプリの仮想化サービスです。 エンド ユーザーが Azure Virtual Desktop 環境に接続すると、そのセッションはホスト プールによって実行されます。 ホスト プールとは、Azure Virtual Desktop にセッション ホストとして登録する Azure 仮想マシンのコレクションです。 これらの仮想マシンは仮想ネットワーク内で実行され、仮想ネットワークのセキュリティ制御の対象となります。 Azure Virtual Desktop サービスへの発信インターネット アクセスが正常に動作している必要があり、エンド ユーザー向けに送信インターネット アクセスが必要な場合もあります。 Azure Firewall は、環境をロックダウンし、送信トラフィックをフィルター処理するのに役立ちます。

[ ![Azure Virtual Desktop のアーキテクチャ](media/protect-windows-virtual-desktop/windows-virtual-desktop-architecture-diagram.png) ](media/protect-windows-virtual-desktop/windows-virtual-desktop-architecture-diagram.png#lightbox)

この記事のガイドラインに従って、Azure Firewall を使用して、Azure Virtual Desktop ホスト プールの保護を強化します。

## <a name="prerequisites"></a>前提条件

 - デプロイされた Azure Virtual Desktop 環境とホスト プール
 - 少なくとも 1 つの Firewall Manager ポリシーを使用してデプロイされた Azure Firewall

   詳細については、[Azure portal を使用したホスト プールの作成方法に関するチュートリアル](../virtual-desktop/create-host-pools-azure-marketplace.md)を参照してください。

Azure Virtual Desktop 環境の詳細については、「[Azure Virtual Desktop 環境](../virtual-desktop/environment-setup.md)」を参照してください。

## <a name="host-pool-outbound-access-to-azure-virtual-desktop"></a>Azure Virtual Desktop へのホスト プールの送信アクセス

Azure Virtual Desktop 用に作成する Azure 仮想マシンが正常に機能するためには、いくつかの完全修飾ドメイン名 (FQDN) へのアクセス権が必要です。 Azure Firewall には、この構成を簡略化するための Azure Virtual Desktop FQDN タグが用意されています。 Azure Virtual Desktop プラットフォームの送信トラフィックを許可するには、次の手順に従います。

Azure Firewall ポリシーを作成し、ネットワーク ルールとアプリケーション ルールのルール コレクションを作成することが必要になります。 ルール コレクションに優先順位と許可アクションまたは拒否アクションを指定します。

### <a name="create-network-rules"></a>ネットワーク ルールを作成する

| 名前      | 送信元の種類 | source                    | Protocol | 宛先ポート | 変換先の型 | 宛先                       |
| --------- | ----------- | ------------------------- | -------- | ----------------- | ---------------- | --------------------------------- |
| 規則の名前 | IP アドレス  | VNet またはサブネットの IP アドレス | TCP      | 80                | IP アドレス       | 169.254.169.254、168.63.129.16    |
| 規則の名前 | IP アドレス  | VNet またはサブネットの IP アドレス | TCP      | 443               | サービス タグ      | AzureCloud、WindowsVirtualDesktop |
| 規則の名前 | IP アドレス  | VNet またはサブネットの IP アドレス | TCP、UDP | 53                | IP アドレス       | *                                 |
|規則名  | IP アドレス  | VNet またはサブネットの IP アドレス | TCP      | 1688              | IP アドレス       | 23.102.135.246                    |

> [!NOTE]
> 一部のデプロイでは、DNS 規則を必要としない場合があります。 たとえば、Azure Active Directory ドメイン コントローラーは、DNS クエリを 168.63.129.16 の Azure DNS に転送します。

### <a name="create-application-rules"></a>アプリケーション ルールを作成する

| 名前      | 送信元の種類 | source                    | Protocol   | 変換先の型 | 宛先                                                                                 |
| --------- | ----------- | ------------------------- | ---------- | ---------------- | ------------------------------------------------------------------------------------------- |
| 規則の名前 | IP アドレス  | VNet またはサブネットの IP アドレス | Https:443  | FQDN タグ         | WindowsVirtualDesktop、WindowsUpdate、Windows Diagnostics、MicrosoftActiveProtectionService |

> [!IMPORTANT]
> Azure Virtual Desktop では TLS 検査を使用しないことが推奨されます。 詳細については、[プロキシ サーバー ガイドライン](../virtual-desktop/proxy-server-support.md#dont-use-ssl-termination-on-the-proxy-server)に関するページをご覧ください。

## <a name="host-pool-outbound-access-to-the-internet"></a>インターネットへのホスト プールの送信アクセス

組織のニーズによっては、エンド ユーザーに対して安全な送信インターネット アクセスを使用できるようにすることが必要になる場合があります。 許可される送信先の一覧が適切に定義されている場合 ([Microsoft 365 アクセス](/microsoft-365/enterprise/microsoft-365-ip-web-service)用のものなど)、Azure Firewall アプリケーションとネットワーク ルールを使用して必要なアクセスを構成できます。 これにより、最適なパフォーマンスを得るため、エンド ユーザーのトラフィックがインターネットに直接ルーティングされます。 Windows 365 または Intune に対してネットワーク接続を許可する必要がある場合は、[Windows 365 のネットワーク要件](/windows-365/requirements-network#allow-network-connectivity)、および [Intune のネットワーク エンドポイント](/mem/intune/fundamentals/intune-endpoints)に関する記事を参照してください。

セキュリティで保護された既存のオンプレミスの Web ゲートウェイを使用して送信ユーザー インターネット トラフィックをフィルター処理する場合は、明示的なプロキシ構成を使用して、Azure Virtual Desktop ホスト プールで実行されている Web ブラウザーやその他のアプリケーションを構成できます。 例については、「[Microsoft Edge コマンド ライン オプションを使用してプロキシ設定を構成する方法](/deployedge/edge-learnmore-cmdline-options-proxy-settings)」を参照してください。 これらのプロキシ設定は、エンド ユーザーのインターネット アクセスにのみ影響し、Azure Firewall 経由で直接 Azure Virtual Desktop プラットフォームの送信トラフィックを許可します。

## <a name="control-user-access-to-the-web"></a>Web へのユーザー アクセスを制御する

管理者は、別の Web サイト カテゴリへのユーザー アクセスを許可または拒否できます。 アプリケーション コレクションに、特定の IP アドレスから、許可または拒否する Web カテゴリに対するルールを追加します。 すべての [Web カテゴリ](web-categories.md)をご確認ください。

## <a name="additional-considerations"></a>その他の注意点

要件によっては、追加のファイアウォール規則を構成することが必要になる場合があります。

- NTP サーバー アクセス

  既定では、Windows を実行している仮想マシンは、UDP ポート 123 経由で `time.windows.com` に接続して時間を同期します。 このアクセスを許可するネットワーク ルール、または環境内で使用するタイム サーバーを作成します。

## <a name="next-steps"></a>次のステップ

- Azure Virtual Desktop の詳細を、「[Azure Virtual Desktop とは](../virtual-desktop/overview.md)」で確認する
