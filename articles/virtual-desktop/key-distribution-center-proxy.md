---
title: Kerberos キー配布センター プロキシ Windows Virtual Desktop のセットアップ - Azure
description: Kerberos キー配布センター プロキシを使用するように Windows Virtual Desktop ホスト プールを設定する方法について説明します。
author: Heidilohr
ms.topic: how-to
ms.date: 03/20/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 21db7ed0cf13a3ed282929b09847c6c3ba8a36ed
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107307250"
---
# <a name="configure-a-kerberos-key-distribution-center-proxy-preview"></a>Kerberos キー配布センター プロキシを構成する (プレビュー)

> [!IMPORTANT]
> 現在、この機能はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されており、運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

金融機関や政府機関など、セキュリティ意識の高いお客様は、多くの場合、スマートカードを使用してサインインしています。 スマートカードでは、多要素認証 (MFA) を要求することで、デプロイのセキュリティを強化できます。 ただし、Windows Virtual Desktop セッションの RDP の部分については、Kerberos 認証用の Active Directory (AD) ドメイン コントローラーを使用した直接接続または "照準線" がスマートカードに必要になります。 この直接接続がないと、ユーザーはリモート接続から組織のネットワークに自動的にサインインできません。 Windows Virtual Desktop デプロイのユーザーは、KDC プロキシ サービスを使用して、この認証トラフィックをプロキシし、リモートでサインインできます。 KDC プロキシでは、Windows Virtual Desktop セッションのリモート デスクトップ プロトコルに対する認証が可能なため、ユーザーは安全にサインインできます。 これにより、自宅での作業がはるかに簡単になり、特定のディザスター リカバリーのシナリオを円滑に実行できます。

ただし、KDC プロキシを設定するには、通常は Windows Server 2016 以降で Windows Server Gateway ロールを割り当てる必要があります。 リモート デスクトップ サービス ロールを使用して Windows Virtual Desktop にサインインするにはどうすればよいですか? これに答えるために、コンポーネントについて簡単に見てみましょう。

認証が必要になる Windows Virtual Desktop サービスには、次の 2 つのコンポーネントがあります。

- ユーザーがアクセスできるデスクトップまたはアプリケーションの一覧を提供する Windows Virtual Desktop クライアントのフィード。 この認証プロセスは Azure Active Directory で実行されるため、このコンポーネントについては、この記事では取り上げません。
- 使用可能なリソースの 1 つをユーザー選択した結果として得られる RDP セッション。 このコンポーネントでは、Kerberos 認証を使用し、リモート ユーザーには KDC プロキシが必要です。

この記事では、Azure portal で Windows Virtual Desktop クライアントのフィードを構成する方法について説明します。 RD ゲートウェイ ロールを構成する方法については、[RD ゲートウェイ ロールのデプロイ](/azure/virtual-desktop/rd-gateway-role)に関するページを参照してください。

## <a name="requirements"></a>必要条件

KDC プロキシを使用して Windows Virtual Desktop セッション ホストを構成するには、次のことが必要です。

- Azure portal と Azure 管理者アカウントにアクセスします。
- リモート クライアント コンピューターでは、Windows 10 または Windows 7 が実行されている必要があり、[Windows デスクトップ クライアント](/windows-server/remote/remote-desktop-services/clients/windowsdesktop)がインストールされている必要があります。
- コンピューターに KDC プロキシが既にインストールされている必要があります。 その方法については、[Windows Virtual Desktop の RD ゲートウェイ ロールの設定](rd-gateway-role.md)に関するページを参照してください。
- コンピュータの OS は、Windows Server 2016 以降である必要があります。

これらの要件を満たしていることを確認したら、作業を開始できます。

## <a name="how-to-configure-the-kdc-proxy"></a>KDC プロキシを構成する方法

KDC プロキシを構成するには、次の手順を実行します。

1. Azure Portal に管理者としてサインインします。

2. [Windows Virtual Desktop] ページにアクセスします。

3. KDC プロキシを有効にするホスト プールを選択し、 **[RDP プロパティ]** を選択します。

    > [!div class="mx-imgBorder"]
    > ![[Azure portal] ページのスクリーンショット。ホスト プールを選択したユーザー、ホスト プールの例の名前、RDP プロパティの順に表示されます。](media/rdp-properties.png)

4. **[詳細設定]** タブを選択し、次の形式でスペースなしで値を入力します。

    
    > kdcproxyname:s:\<fqdn\>
    

    > [!div class="mx-imgBorder"]
    > ![選択した [詳細設定] タブが表示されたスクリーンショット。手順 4 で説明したように値を入力します。](media/advanced-tab-selected.png)

5. **[保存]** を選択します。

6. これで、選択したホスト プールで、手順 4 で入力した kdcproxyname 値が含まれる RDP 接続ファイルの発行が開始されます。

## <a name="next-steps"></a>次のステップ

KDC プロキシのリモート デスクトップ サービス側を管理し、RD ゲートウェイ ロールを割り当てる方法については、[RD ゲートウェイ ロールのデプロイ](rd-gateway-role.md)に関するページを参照してください。

KDC プロキシ サーバーのスケーリングに関心がある場合、KDC プロキシの高可用性を設定する方法については、「[RD Web およびゲートウェイ Web フロントに高可用性を追加する](/windows-server/remote/remote-desktop-services/rds-rdweb-gateway-ha)」を参照してください。
