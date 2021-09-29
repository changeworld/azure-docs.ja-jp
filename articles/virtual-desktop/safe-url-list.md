---
title: Azure Virtual Desktop の必要な URL リスト - Azure
description: Azure Virtual Desktop のデプロイを意図したとおりに機能させるためにブロックを解除する必要がある URL の一覧。
author: Heidilohr
ms.topic: conceptual
ms.date: 12/04/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: ea5cd9dffeb8efdeb9dc29fa9d38696973285e47
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128549238"
---
# <a name="required-url-list"></a>必要な URL リスト

Azure Virtual Desktop をデプロイし、使用するには、仮想マシン (VM) で特定の URL にいつでもアクセスできるよう、URL のブロックを解除する必要があります。 この記事では、Azure Virtual Desktop を正しく機能させるためにブロックを解除する必要がある、必要な URL の一覧を示します。 

>[!IMPORTANT]
>Azure Virtual Desktop では、この記事に含まれている URL をブロックするデプロイはサポートされていません。

## <a name="required-url-check-tool"></a>必要な URL チェック ツール

必要な URL チェック ツールでは、URL が検証され、仮想マシンが機能するために必要な URL がアクセス可能かどうかが示されます。 そうでない場合は、このツールによって、アクセス不可 URL が一覧表示されるため、必要に応じてそれらのブロックを解除できます。

次の点に注意することが重要です。

- 必要な URL チェック ツールは、商用クラウドでのデプロイにのみ使用できます。
- 必要な URL チェック ツールでは、ワイルドカードを含む URL をチェックできないため、必ず最初にそれらの URL のブロックを解除してください。

### <a name="requirements"></a>必要条件

必要な URL チェック ツールを使用するには、次のものが必要です。

- VM には .NET 4.6.2 Framework が必要です
- RDAgent バージョン 1.0.2944.400 以降
- WVDAgentUrlTool.exe ファイルは、WVDAgentUrlTool.config ファイルと同じフォルダーにある必要があります

### <a name="how-to-use-the-required-url-check-tool"></a>必要な URL チェック ツールの使用方法

必要な URL チェック ツールを使用するには、次のようにします。

1. VM 上で管理者としてコマンド プロンプトを開きます。
2. 次のコマンドを実行して、ディレクトリを現在のビルド エージェントと同じフォルダーに変更します (この例では RDAgent_1.0.2944.1200)。

    ```console
    cd C:\Program Files\Microsoft RDInfra\RDAgent_1.0.2944.1200
    ```

3. 次のコマンドを実行します。

    ```console
    WVDAgentUrlTool.exe
    ```
 
4. このファイルを実行すると、アクセス可能とアクセス不可 URL の一覧が表示されます。

    たとえば、次のスクリーンショットは、必要な 2 つの非ワイルドカード URL のブロックを解除する必要があるシナリオを示しています。

    > [!div class="mx-imgBorder"]
    > ![アクセス不可 URL 出力のスクリーンショット。](media/noaccess.png)
    
    必要な非ワイルドカード URL のブロックをすべて解除すると、出力は次のようになります。

    > [!div class="mx-imgBorder"]
    > ![アクセス可能 URL 出力のスクリーンショット。](media/access.png)

## <a name="virtual-machines"></a>仮想マシン

Azure Virtual Desktop 用に作成する Azure 仮想マシンには、Azure コマーシャル クラウドで次の URL に対するアクセスが必要です。

|Address|アウトバウンド TCP ポート|目的|サービス タグ|
|---|---|---|---|
|*.wvd.microsoft.com|443|サービス トラフィック|WindowsVirtualDesktop|
|gcs.prod.monitoring.core.windows.net|443|エージェント トラフィック|AzureCloud|
|production.diagnostics.monitoring.core.windows.net|443|エージェント トラフィック|AzureCloud|
|*xt.blob.core.windows.net|443|エージェント トラフィック|AzureCloud|
|*eh.servicebus.windows.net|443|エージェント トラフィック|AzureCloud|
|*xt.table.core.windows.net|443|エージェント トラフィック|AzureCloud|
|*xt.queue.core.windows.net|443|エージェント トラフィック|AzureCloud|
|catalogartifact.azureedge.net|443|Azure Marketplace|AzureCloud|
|kms.core.windows.net|1688|Windows のライセンス認証|インターネット|
|mrsglobalsteus2prod.blob.core.windows.net|443|エージェントと SXS スタックの更新|AzureCloud|
|wvdportalstorageblob.blob.core.windows.net|443|Azure portal のサポート|AzureCloud|
| 169.254.169.254 | 80 | [Azure Instance Metadata Service エンドポイント](../virtual-machines/windows/instance-metadata-service.md) | 該当なし |
| 168.63.129.16 | 80 | [セッション ホストの正常性の監視](../virtual-network/network-security-groups-overview.md#azure-platform-considerations) | 該当なし |

>[!IMPORTANT]
>Azure Virtual Desktop で FQDN タグがサポートされるようになりました。 詳細については、「[Azure Firewall を使用して Windows Virtual Desktop のデプロイを保護する](../firewall/protect-azure-virtual-desktop.md)」を参照してください。
>
>サービスの問題を防ぐために、URL ではなく FQDN タグまたはサービス タグを使用することをお勧めします。 記載した URL とタグは、Azure Virtual Desktop のサイトとリソースにのみ対応します。 他のサービス (Azure Active Directory など) の URL は含まれません。

Azure Virtual Desktop 用に作成する Azure 仮想マシンには、Azure Government クラウドで次の URL に対するアクセスが必要です。

|Address|アウトバウンド TCP ポート|目的|サービス タグ|
|---|---|---|---|
|*.wvd.azure.us|443|サービス トラフィック|WindowsVirtualDesktop|
|gcs.monitoring.core.usgovcloudapi.net|443|エージェント トラフィック|AzureCloud|
|monitoring.core.usgovcloudapi.net|443|エージェント トラフィック|AzureCloud|
|fairfax.warmpath.usgovcloudapi.net|443|エージェント トラフィック|AzureCloud|
|*xt.blob.core.usgovcloudapi.net|443|エージェント トラフィック|AzureCloud|
|*.servicebus.usgovcloudapi.net|443|エージェント トラフィック|AzureCloud|
|*xt.table.core.usgovcloudapi.net|443|エージェント トラフィック|AzureCloud|
|Kms.core.usgovcloudapi.net|1688|Windows のライセンス認証|インターネット|
|mrsglobalstugviffx.blob.core.usgovcloudapi.net|443|エージェントと SXS スタックの更新|AzureCloud|
|wvdportalstorageblob.blob.core.usgovcloudapi.net|443|Azure portal のサポート|AzureCloud|
| 169.254.169.254 | 80 | [Azure Instance Metadata Service エンドポイント](../virtual-machines/windows/instance-metadata-service.md) | 該当なし |
| 168.63.129.16 | 80 | [セッション ホストの正常性の監視](../virtual-network/network-security-groups-overview.md#azure-platform-considerations) | 該当なし |

次の表は、Azure 仮想マシンからアクセスできるオプションの URL の一覧です。

|Address|アウトバウンド TCP ポート|目的|Azure Gov|
|---|---|---|---|
|*.microsoftonline.com|443|Microsoft オンライン サービスへの認証|login.microsoftonline.us|
|*.events.data.microsoft.com|443|テレメトリ サービス|なし|
|www.msftconnecttest.com|443|OS がインターネットに接続されているかどうかの検出|なし|
|*.prod.do.dsp.mp.microsoft.com|443|Windows Update|なし|
|login.windows.net|443|Microsoft Online Services、Microsoft 365 へのサインイン|login.microsoftonline.us|
|*.sfx.ms|443|OneDrive クライアント ソフトウェアの更新|oneclient.sfx.ms|
|*.digicert.com|443|証明書失効の確認|なし|
|*.azure-dns.com|443|Azure DNS 解決|なし|
|*.azure-dns.net|443|Azure DNS 解決|なし|

>[!NOTE]
>現在、Azure Virtual Desktop には、ネットワーク トラフィックを許可するためにブロックを解除できる IP アドレス範囲の一覧がありません。 現時点では、特定の URL のブロック解除のみがサポートされます。
>
>Next Generation Firewall (NGFW) を使用している場合、確実に接続できるよう、Azure IP のために特別に作られた動的リストを使用する必要があります。
>
>Office に関連した安全な URL (必須の Azure Active Directory 関連の URL など) の一覧については、「[Office 365 の URL と IP アドレスの範囲](/office365/enterprise/urls-and-ip-address-ranges)」を参照してください。
>
>サービス トラフィックに関係した URL にはワイルドカード文字 (*) を使用する必要があります。 エージェント関連のトラフィックに * を使用したくない場合、ワイルドカードを使わずに URL を見つける方法は次のとおりです。
>
>1. Azure Virtual Desktop ホスト プールに仮想マシンを登録します。
>2. **イベント ビューアー** を開き、 **[Windows ログ]**  >  **[アプリケーション]**  >  **[WVD-Agent]** に移動して、イベント ID 3701 を探します。
>3. イベント ID 3701 に記載されている URL をブロック解除します。 イベント ID 3701 に記載されている URL はリージョン固有です。 仮想マシンのデプロイ先となるリージョンごとに、適切な URL を使用して、ブロック解除プロセスを繰り返す必要があります。

## <a name="remote-desktop-clients"></a>リモート デスクトップ クライアント

使用するリモート デスクトップ クライアントから、次の URL にアクセスできる必要があります。

|Address|アウトバウンド TCP ポート|目的|クライアント|Azure Gov|
|---|---|---|---|---|
|*.wvd.microsoft.com|443|サービス トラフィック|All|*.wvd.microsoft.us|
|*.servicebus.windows.net|443|データのトラブルシューティング|All|*.servicebus.usgovcloudapi.net|
|go.microsoft.com|443|Microsoft の FWLink|All|なし|
|aka.ms|443|Microsoft URL 短縮ツール|All|なし|
|docs.microsoft.com|443|ドキュメント|All|なし|
|privacy.microsoft.com|443|プライバシー ステートメント|All|なし|
|query.prod.cms.rt.microsoft.com|443|クライアント更新|Windows デスクトップ|None|

>[!IMPORTANT]
>信頼できるクライアント エクスペリエンスを実現するには、これらの URL を開くことが不可欠です。 これらの URL へのアクセスをブロックすることはサポート対象外であり、サービスの機能にも支障が生じます。
>
>これらの URL はクライアントのサイトとリソースにのみ対応します。 この一覧には、他のサービス (Azure Active Directory など) の URL は含まれません。 Azure Active Directory URL は「[Office 365 URL および IP アドレス範囲](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online)」の ID 56、59、および 125 にあります。
