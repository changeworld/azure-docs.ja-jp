---
title: Azure Site Recovery でのトランスポート層セキュリティ
description: Azure Site Recovery を有効にして、暗号化プロトコルであるトランスポート層セキュリティ (TLS) を使用して、ネットワーク経由で転送される際のデータのセキュリティを維持する方法について説明します。
ms.topic: conceptual
ms.date: 11/01/2020
ms.openlocfilehash: 3937fd9f88a844c0257bc6cb66b4c3f97a112987
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128594512"
---
# <a name="transport-layer-security-in-azure-site-recovery"></a>Azure Site Recovery でのトランスポート層セキュリティ

トランスポート層セキュリティ (TLS) は、ネットワーク経由で転送される際のデータのセキュリティを維持する暗号化プロトコルです。 Azure Site Recovery では、TLS を使用して、転送されるデータのプライバシーを保護します。 Azure Site Recovery では、セキュリティ強化のために TLS 1.2 プロトコルを使用するようになりました。

## <a name="enable-tls-on-older-versions-of-windows"></a>以前のバージョンの Windows で TLS を有効にする

コンピューターで以前のバージョンの Windows が実行されている場合は、必ず、以下で説明するように対応する更新プログラムをインストールして、各サポート技術情報の記事で説明されているようにレジストリの変更を行ってください。

|オペレーティング システム  |KB article |
|---------|---------|
|Windows Server 2008 SP2   |   <https://support.microsoft.com/help/4019276>      |
|Windows Server 2008 R2、Windows 7、Windows Server 2012   | <https://support.microsoft.com/help/3140245>         |

>[!NOTE]
>更新プログラムによって、プロトコルに必要なコンポーネントがインストールされます。 インストール後、必要なプロトコルを有効にするには、上記のサポート技術情報の記事で説明されているようにレジストリ キーを更新してください。

## <a name="verify-windows-registry"></a>Windows レジストリの確認

### <a name="configure-schannel-protocols"></a>SChannel プロトコルを構成する

次のレジストリ キーを使用して、TLS 1.2 プロトコルが SChannel コンポーネント レベルで 有効になるようにします。

```reg
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
    "Enabled"=dword:00000001

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
    "DisabledByDefault"=dword:00000000
```

>[!NOTE]
>既定では、上記のレジストリ キーは、Windows Server 2012 R2 以降のバージョンで設定されている値で設定されます。 これらのバージョンの Windows では、レジストリ キーが存在しない場合、作成する必要はありません。

### <a name="configure-net-framework"></a>.NET Framework を構成する

次のレジストリ キーを使用して、強力な暗号化をサポートする .NET Framework を構成します。 [.NET Framework の構成の詳細については、こちら](/dotnet/framework/network-programming/tls#configuring-schannel-protocols-in-the-windows-registry)を参照してください。

```reg
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
    "SystemDefaultTlsVersions"=dword:00000001
    "SchUseStrongCrypto" = dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
    "SystemDefaultTlsVersions"=dword:00000001
    "SchUseStrongCrypto" = dword:00000001
```

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="why-enable-tls-12"></a>なぜ TLS 1.2 を有効にするのですか?

TLS 1.2 は、SSL 2.0、SSL 3.0、TLS 1.0、TLS 1.1 などの以前の暗号化プロトコルより安全性が向上しています。 Azure Site Recovery サービスでは、TLS 1.2 が完全にサポートされています。

### <a name="what-determines-the-encryption-protocol-used"></a>使用される暗号化プロトコルは何によって決まりますか?

暗号化された会話を確立するために、クライアントとサーバーの両方でサポートされている最上位のプロトコル バージョンがネゴシエートされます。 TLS ハンドシェイク プロトコルの詳細については、「[TLS を使用してセキュリティで保護されたセッションを確立する](/windows/win32/secauthn/tls-handshake-protocol#establishing-a-secure-session-by-using-tls)」を参照してください。

### <a name="what-is-the-impact-if-tls-12-is-not-enabled"></a>TLS 1.2 が有効になっていない場合、どのような影響がありますか?

プロトコル ダウングレード攻撃に対するセキュリティを強化するため、Azure Site Recovery では 1.2 より前のバージョンの TLS を無効化し始めています。 これは、レガシ プロトコルと暗号スイートの接続を許可しないようにする、サービス全体での長期的な移行の一環です。 Azure Site Recovery のサービスとコンポーネントでは、TLS 1.2 が完全にサポートされています。 ただし、必要な更新プログラムのない Windows バージョンや特定のカスタマイズされた構成では、TLS 1.2 プロトコルを提供できない場合があります。 この場合、次のうち 1 つ以上の障害が発生する可能性がありますが、これらに限定されるものではありません。

- レプリケーションがソースで失敗する可能性があります。
- Azure Site Recovery コンポーネントの接続がエラー 10054 (既存の接続がリモート ホストによって強制的に切断された) によって失敗します。
- Azure Site Recovery に関連するサービスが、通常どおりに停止または開始されません。

## <a name="additional-resources"></a>その他のリソース

- [トランスポート層セキュリティ プロトコル](/windows/win32/secauthn/transport-layer-security-protocol)
- [展開されたオペレーティング システム全体で TLS 1.2 のサポートを確保する](/security/engineering/solving-tls1-problem#ensuring-support-for-tls-12-across-deployed-operating-systems)
- [.NET Framework でのトランスポート層セキュリティ (TLS) のベスト プラクティス](/dotnet/framework/network-programming/tls)