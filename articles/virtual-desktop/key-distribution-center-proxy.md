---
title: Kerberos キー配布センター プロキシ Azure Virtual Desktop のセットアップ - Azure
description: Kerberos キー配布センター プロキシを使用するように Azure Virtual Desktop ホスト プールを設定する方法。
author: Heidilohr
ms.topic: how-to
ms.date: 05/04/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 55c68902cebbb6832a9c09c5390d9f43d381bf21
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/09/2021
ms.locfileid: "111757555"
---
# <a name="configure-a-kerberos-key-distribution-center-proxy"></a>Kerberos キー配布センター プロキシを構成する

金融機関や政府機関など、セキュリティ意識の高いお客様は、多くの場合、スマートカードを使用してサインインしています。 スマートカードでは、多要素認証 (MFA) を要求することで、デプロイのセキュリティを強化できます。 ただし、Azure Virtual Desktop セッションの RDP の部分については、Kerberos 認証用の Active Directory (AD) ドメイン コントローラーを使用した直接接続または "照準線" がスマートカードに必要になります。 この直接接続がないと、ユーザーはリモート接続から組織のネットワークに自動的にサインインできません。 Azure Virtual Desktop デプロイのユーザーは、KDC プロキシ サービスを使用して、この認証トラフィックをプロキシし、リモートでサインインできます。 KDC プロキシでは、Azure Virtual Desktop セッションのリモート デスクトップ プロトコルに対する認証が可能なため、ユーザーは安全にサインインできます。 これにより、自宅での作業がはるかに簡単になり、特定のディザスター リカバリーのシナリオを円滑に実行できます。

ただし、KDC プロキシを設定するには、通常は Windows Server 2016 以降で Windows Server Gateway ロールを割り当てる必要があります。 リモート デスクトップ サービス ロールを使用して Azure Virtual Desktop にサインインするにはどうすればよいですか? これに答えるために、コンポーネントについて簡単に見てみましょう。

認証が必要になる Azure Virtual Desktop サービスには、次の 2 つのコンポーネントがあります。

- ユーザーがアクセスできるデスクトップまたはアプリケーションの一覧を提供する Azure Virtual Desktop クライアントのフィード。 この認証プロセスは Azure Active Directory で実行されるため、このコンポーネントについては、この記事では取り上げません。
- 使用可能なリソースの 1 つをユーザー選択した結果として得られる RDP セッション。 このコンポーネントでは、Kerberos 認証を使用し、リモート ユーザーには KDC プロキシが必要です。

この記事では、Azure portal で Azure Virtual Desktop クライアントのフィードを構成する方法について説明します。 RD ゲートウェイ ロールを構成する方法については、[RD ゲートウェイ ロールのデプロイ](/windows-server/remote/remote-desktop-services/remote-desktop-gateway-role)に関するページを参照してください。

## <a name="requirements"></a>必要条件

KDC プロキシを使用して Azure Virtual Desktop セッション ホストを構成するには、次のことが必要です。

- Azure portal と Azure 管理者アカウントにアクセスします。
- リモート クライアント コンピューターでは、Windows 10 または Windows 7 が実行されている必要があり、[Windows デスクトップ クライアント](/windows-server/remote/remote-desktop-services/clients/windowsdesktop)がインストールされている必要があります。 現在、Web クライアントはサポートされていません。
- コンピューターに KDC プロキシが既にインストールされている必要があります。 その方法については、[Azure Virtual Desktop の RD ゲートウェイ ロールの設定](/windows-server/remote/remote-desktop-services/remote-desktop-gateway-role)に関するページを参照してください。
- コンピュータの OS は、Windows Server 2016 以降である必要があります。

これらの要件を満たしていることを確認したら、作業を開始できます。

## <a name="how-to-configure-the-kdc-proxy"></a>KDC プロキシを構成する方法

KDC プロキシを構成するには、次の手順を実行します。

1. Azure Portal に管理者としてサインインします。

2. [Azure Virtual Desktop] ページにアクセスします。

3. KDC プロキシを有効にするホスト プールを選択し、 **[RDP プロパティ]** を選択します。

4. **[詳細設定]** タブを選択し、次の形式でスペースなしで値を入力します。

    
    > kdcproxyname:s:\<fqdn\>
    

    > [!div class="mx-imgBorder"]
    > ![選択した [詳細設定] タブが表示されたスクリーンショット。手順 4 で説明したように値を入力します。](media/advanced-tab-selected.png)

5. **[保存]** を選択します。

6. これで、選択したホスト プールで、手順 4 で入力した kdcproxyname 値が含まれる RDP 接続ファイルの発行が開始されます。

## <a name="next-steps"></a>次のステップ

KDC プロキシのリモート デスクトップ サービス側を管理し、RD ゲートウェイ ロールを割り当てる方法については、[RD ゲートウェイ ロールのデプロイ](/windows-server/remote/remote-desktop-services/remote-desktop-gateway-role)に関するページを参照してください。

KDC プロキシ サーバーのスケーリングに関心がある場合、KDC プロキシの高可用性を設定する方法については、「[RD Web およびゲートウェイ Web フロントに高可用性を追加する](/windows-server/remote/remote-desktop-services/rds-rdweb-gateway-ha)」を参照してください。