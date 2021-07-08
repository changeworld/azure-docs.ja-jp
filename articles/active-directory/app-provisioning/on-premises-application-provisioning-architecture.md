---
title: Azure AD オンプレミス アプリケーション プロビジョニングのアーキテクチャ | Microsoft Docs
description: オンプレミス アプリケーション プロビジョニングのアーキテクチャの概要について説明します。
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 05/28/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 696f2f9e54e350b2cce952cbec8d745779e1d00a
ms.sourcegitcommit: ff1aa951f5d81381811246ac2380bcddc7e0c2b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/07/2021
ms.locfileid: "111570360"
---
# <a name="azure-ad-on-premises-application-provisioning-architecture"></a>Azure AD オンプレミス アプリケーション プロビジョニングのアーキテクチャ

>[!IMPORTANT]
> オンプレミス プロビジョニング プレビューは現在、招待のみのプレビューとなります。 この機能へのアクセスは、[こちら](https://aka.ms/onpremprovisioningpublicpreviewaccess)からリクエストできます。 今後数か月の間に、より多くのお客様とコネクタにプレビューを公開し、一般提供に向けて準備を進めていく予定です。

## <a name="overview"></a>概要

次の図は、オンプレミス アプリケーション プロビジョニングのしくみを示しています。

![アーキテクチャ](.\media\on-premises-application-provisioning-architecture\arch-3.png)

オンプレミス アプリケーションへのユーザー プロビジョニングには、主要なコンポーネントが 3 つあります。

- プロビジョニング エージェントは、Azure AD とオンプレミス環境とを接続する役割を担います。
- ECMA ホストは、Azure AD からのプロビジョニング要求を、ターゲット アプリケーションへの要求に変換します。 Azure AD とアプリケーションとの間のゲートウェイとして機能します。 Microsoft Identity Manager で使用される既存の ECMA2 コネクタをインポートすることができます。 ただし、SCIM アプリケーションまたは SCIM ゲートウェイを構築済みである場合、ECMA ホストは必要ありません。
- Azure AD プロビジョニング サービスは、同期エンジンとして機能します。

>[!NOTE]
> MIM Sync は必要ありません。 ただし、ECMA ホストにインポートする ECMA コネクタを、事前に MIM Sync を使用して構築、テストすることはできます。


### <a name="firewall-requirements"></a>ファイアウォールの要件

企業ネットワークへのインバウンド接続を開放する必要はありません。 プロビジョニング エージェントは、プロビジョニング サービスへのアウトバウンド接続のみを使用します。つまり、受信接続用のファイアウォール ポートを開放する必要はありません。 接続はすべてアウトバウンドであり、セキュリティで保護されたチャネル上で行われるため、境界 (DMZ) ネットワークは不要です。 

## <a name="agent-best-practices"></a>エージェントのベスト プラクティス
- Azure AD Connect プロビジョニング エージェントの自動更新サービスが実行されていることを確認します。 エージェントをインストールすると、既定で有効になります。 Microsoft がデプロイをサポートするには自動更新が必要です。
- エージェントと Azure との間のアウトバウンド TLS 通信に対しては、どのような形式のインライン検査も行わないでください。 この種のインライン検査では、通信フローが低下します。
- エージェントは Azure とアプリケーションの両方と通信を行う必要があるため、エージェントの配置場所はこの 2 つの接続の待ち時間に影響します。 各ネットワーク接続を最適化することによって、エンド ツー エンドのトラフィック待機時間を最小化することができます。 各接続は次の方法で最適化できます。
- ホップの両端間の距離を短縮します。
- 経由する適切なネットワークを選択します。 たとえば、パブリック インターネットではなく、プライベート ネットワークを経由した方が、専用リンクのため高速である可能性があります。

## <a name="provisioning-agent-questions"></a>プロビジョニング エージェントに関する質問
**プロビジョニング エージェントの一般提供 (GA) バージョンは何ですか。**

プロビジョニング エージェントの最新の GA バージョンについては、「[Azure AD Connect プロビジョニング エージェント:バージョンのリリース履歴](provisioning-agent-release-version-history.md)」を参照してください。

**プロビジョニング エージェントのバージョンを確認する方法を教えてください。**

 1. プロビジョニング エージェントがインストールされている Windows サーバーにサインインします。
 2. [コントロール パネル]、[プログラムのアンインストールまたは変更] メニューの順に移動します
 3. エントリ Microsoft Azure AD Connect プロビジョニング エージェントに対応するバージョンを探します

**Microsoft からプロビジョニング エージェントの更新プログラムは自動的にプッシュされますか。**

はい。Windows サービス Microsoft Azure AD Connect Agent Updater が稼働している場合、Windows ではプロビジョニング エージェントが自動的に更新されます。 問題のトラブルシューティングをサポートするには、エージェントが最新であることが必要です。

**Azure AD Connect や Microsoft Identity Manager (MIM) を実行しているのと同じサーバーにプロビジョニング エージェントをインストールできますか。**

はい、Azure AD Connect や MIM を実行しているのと同じサーバーにプロビジョニング エージェントをインストールすることができます。ただし、それらは必須ではありません。

**送信 HTTP 通信にプロキシ サーバーを使用するようにプロビジョニング エージェントを構成するにはどうすればよいですか。**

プロビジョニング エージェントは送信プロキシの使用をサポートしています。 構成するには、エージェントの構成ファイル **C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config** を編集します。ファイルの末尾近くにある </configuration> の終了タグの直前に次の行を追加します。 変数 [proxy-server] と [proxy-port] をお客様のプロキシ サーバー名とポート値に置き換えてください。
```
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
                usesystemdefault="true"
                proxyaddress="http://[proxy-server]:[proxy-port]"
                bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```
**プロビジョニング エージェントが Azure AD テナントと通信できること、ファイアウォールがエージェントに必要なポートをブロックしていないことを確認するにはどうすればよいですか。**

必要なポートがすべて開いているかどうかを確認することもできます。

**プロビジョニング エージェントをアンインストールするにはどうすればよいですか。**
1. プロビジョニング エージェントがインストールされている Windows サーバーにサインインします。
2. [コントロール パネル]、[プログラムのアンインストールまたは変更] メニューの順に移動します
3. 次のプログラムをアンインストールします。
     - Microsoft Azure AD Connect プロビジョニング エージェント
     - Microsoft Azure AD Connect Agent Updater
     - Microsoft Azure AD Connect Provisioning Agent Package


## <a name="next-steps"></a>次の手順

- [アプリ プロビジョニング](user-provisioning.md)
- [Azure AD ECMA Connector Host の前提条件](on-premises-ecma-prerequisites.md)
- [Azure AD ECMA Connector Host のインストール](on-premises-ecma-install.md)
- [Azure AD ECMA Connector Host の構成](on-premises-ecma-configure.md)
