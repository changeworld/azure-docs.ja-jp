---
title: Azure AD での Azure AD Connect クラウド プロビジョニングの前提条件
description: この記事では、クラウド プロビジョニングに必要な前提条件とハードウェア要件について説明します。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/06/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6dbdd5153186ee47e37856637eac16d6d450cc5a
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2020
ms.locfileid: "94695182"
---
# <a name="prerequisites-for-azure-ad-connect-cloud-provisioning"></a>Azure AD Connect クラウド プロビジョニングの前提条件
この記事では、ID ソリューションとして Azure Active Directory (Azure AD) クラウド プロビジョニングを選択して使用する方法に関するガイダンスを示します。



## <a name="cloud-provisioning-agent-requirements"></a>クラウド プロビジョニング エージェントの要件
Azure AD Connect クラウド プロビジョニングを使用するには、次のものが必要です。
    
- ゲスト ユーザーではない、Azure AD テナントのハイブリッド ID 管理者アカウント。
- Windows 2012 R2 以降を搭載した、プロビジョニング エージェント用のオンプレミス サーバー  このサーバーは、[Active Directory 管理層モデル](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)に基づいた階層 0 のサーバーである必要があります。
- オンプレミスのファイアウォールの構成

>[!NOTE]
>現在、プロビジョニング エージェントは、英語の言語サーバーにのみインストールできます。 英語以外のサーバーに英語の言語パックをインストールすることは有効な回避策ではなく、エージェントのインストールが失敗します。 

ここからは、これらの前提条件に関する詳細な手順について説明します。

### <a name="in-the-azure-active-directory-admin-center"></a>Azure Active Directory 管理センター

1. Azure AD テナントで、クラウド専用のハイブリッド ID 管理者アカウントを作成します。 その方法を採用すると、オンプレミス サービスが利用できなくなった場合にテナントの構成を管理できます。 [クラウド専用のハイブリッド ID 管理者アカウントを追加する](../fundamentals/add-users-azure-active-directory.md)方法について確認してください。 テナントからロックアウトされないようにするには、この手順を実行する必要があります。
1. 1 つ以上の[カスタム ドメイン名](../fundamentals/add-custom-domain.md)を Azure AD テナントに追加します。 ユーザーは、このドメイン名のいずれかを使用してサインインできます。

### <a name="in-your-directory-in-active-directory"></a>Active Directory のディレクトリ内

[IdFix ツール](/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix)を実行して、同期用にディレクトリ属性を準備します。

### <a name="in-your-on-premises-environment"></a>オンプレミスの環境の場合

1. 4 GB 以上の RAM と .NET 4.7.1 以降のランタイムを搭載した、Windows Server 2012 R2 以降が実行されているドメイン参加済みホスト サーバーを特定します。

1. ローカル サーバーの PowerShell 実行ポリシーを、Undefined または RemoteSigned に設定する必要があります。

1. サーバーと Azure AD の間にファイアウォールがある場合は、次の項目を構成します。
   - エージェントが次のポートを介して Azure AD に "*送信*" 要求を発行できるようにします。

        | ポート番号 | 用途 |
        | --- | --- |
        | **80** | TLS/SSL 証明書を検証する際に、証明書失効リスト (CRL) をダウンロードします。  |
        | **443** | サービスを使用したすべての送信方向の通信を処理します。 |
        | **8080** (省略可能) | ポート 443 が使用できない場合、エージェントは、ポート 8080 経由で 10 分おきにその状態をレポートします。 この状態は Azure AD ポータルに表示されます。 |
     
   - ご利用のファイアウォールが送信元ユーザーに応じて規則を適用している場合は、ネットワーク サービスとして実行されている Windows サービスを送信元とするトラフィックに対してこれらのポートを開放します。
   - ファイアウォールまたはプロキシで安全なサフィックスの指定が許可されている場合は、\*.msappproxy.net および \*.servicebus.windows.net への接続を追加します。 そうでない場合は、毎週更新される [Azure データセンターの IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)へのアクセスを許可します。
   - エージェントは、初期登録のために login.windows.net と login.microsoftonline.com にアクセスする必要があります。 これらの URL にもファイアウォールを開きます。
   - 証明書の検証のために、URL mscrl.microsoft.com:80、crl.microsoft.com:80、ocsp.msocsp.com:80、www\.microsoft.com:80 のブロックを解除します。 他の Microsoft 製品でもこれらの URL を証明書の検証に使用しているので、URL のブロックを既に解除している可能性もあります。

>[!NOTE]
> Windows Server Core へのクラウド プロビジョニング エージェントのインストールはサポートされていません。


### <a name="additional-requirements"></a>その他の要件
- [Microsoft .NET Framework 4.7.1](https://www.microsoft.com/download/details.aspx?id=56116) 

#### <a name="tls-requirements"></a>TLS の要件

>[!NOTE]
>トランスポート層セキュリティ (TLS) は、セキュリティで保護された通信を規定するプロトコルです。 TLS 設定を変更すると、フォレスト全体に影響します。 詳細については、「[Windows の WinHTTP における既定の安全なプロトコルとして TLS 1.1 および TLS 1.2 を有効化する更新プログラム](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi)」を参照してください。

Azure AD Connect クラウド プロビジョニング エージェントをホストする Windows サーバーでは、インストールする前に TLS 1.2 を有効にする必要があります。

TLS 1.2 を有効にするには、次の手順に従います。

1. 次のレジストリ キーを設定します。
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. サーバーを再起動します。

## <a name="known-limitations"></a>既知の制限事項
既知の制限事項には、次のようなものがあります。

### <a name="delta-synchronization"></a>差分同期

- 差分同期のグループ スコープ フィルターは、メンバーが 1,500 を超えるとサポートしません。
- グループ スコープ フィルターの一部として使用されているグループを削除すると、そのグループのメンバーであるユーザーが削除されません。 
- スコープ内の OU またはグループの名前を変更すると、差分同期を実行してもユーザーが削除されません。

### <a name="provisioning-logs"></a>プロビジョニング ログ
- プロビジョニング ログを使用すると、作成操作と更新操作が明確に区別されません。  更新時に作成操作、および作成時に更新操作が表示される場合があります。

### <a name="cross-domain-references"></a>クロス ドメイン参照
- メンバー参照を持つユーザーが別のドメインに存在する場合、そのユーザーは、そのユーザーの現在のドメイン同期の一部として同期されることはありません。 
- (例: 同期しているユーザーのマネージャーがドメイン B に存在し、そのユーザーはドメイン A に存在しています。ドメイン A とドメイン B の両方を同期している場合、それらは同期されますが、ユーザー マネージャーによって引き継がれることはありません)。

### <a name="group-re-naming-or-ou-re-naming"></a>グループ名の変更または OU 名の変更
- 特定の構成のスコープ内にある AD でグループまたは OU の名前を変更すると、クラウド プロビジョニング ジョブによって AD での名前の変更が認識されなくなります。 ジョブは検疫されず、正常な状態のままになります。



## <a name="next-steps"></a>次のステップ 

- [プロビジョニングとは](what-is-provisioning.md)
- [Azure AD Connect クラウド プロビジョニングとは](what-is-cloud-provisioning.md)