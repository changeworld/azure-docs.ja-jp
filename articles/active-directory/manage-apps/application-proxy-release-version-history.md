---
title: Azure AD アプリケーション プロキシは:バージョンのリリース履歴 | Microsoft Docs
description: この記事では、Azure AD アプリケーション プロキシのすべてのリリースを一覧表示し、新機能と修正された問題について説明します
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/07/2020
ms.subservice: app-mgmt
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: f027fbce66a73306165a0ad35d1ba3faa7a5c0bc
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983893"
---
# <a name="azure-ad-application-proxy-version-release-history"></a>Azure AD アプリケーション プロキシは:バージョンのリリース履歴
この記事では、これまでにリリースされた Azure Active Directory (Azure AD) アプリケーション プロキシのバージョンと機能を示します。 Azure AD チームは、アプリケーション プロキシを新機能で定期的に更新しています。 アプリケーション プロキシ コネクタは、新しいバージョンがリリースされると自動的に更新されます。 

最新の機能とバグの修正が適用されるように、コネクタの自動更新が有効になっていることを確認することをお勧めします。 Microsoft では、コネクタの最新バージョンと 1 つ前のバージョンを直接サポートしています。

以下は、関連リソースの一覧です。

リソース |  詳細
--------- | --------- |
アプリケーション プロキシを有効にする方法 | この[チュートリアル](application-proxy-add-on-premises-application.md)では、アプリケーション プロキシを有効にし、コネクタをインストールして登録するための前提条件について説明します。
Azure AD アプリケーション プロキシ コネクタを理解する | [コネクタ管理](application-proxy-connectors.md)とコネクタの[自動アップグレード](application-proxy-connectors.md#automatic-updates)について詳しく説明します。
Azure AD アプリケーション プロキシ コネクタのダウンロード |  [最新のコネクタをダウンロードします](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download)。

## <a name="1515260"></a>1.5.1526.0

### <a name="release-status"></a>リリースの状態

2020 年 4 月 7 日:ダウンロード対象としてリリース済み

### <a name="new-features-and-improvements"></a>新機能と機能強化
-   コネクタはすべての接続に TLS 1.2 のみを使用します。 詳細については、[コネクタの前提条件](application-proxy-add-on-premises-application.md#before-you-begin)に関する記事を参照してください。
- コネクタと Azure サービスの間のシグナリングが改善されました。 これには、コネクタと Azure サービス間の WCF 通信に対する信頼性の高いセッションのサポートと、WebSocket 通信に対する DNS キャッシュの強化が含まれます。
- コネクタとバックエンド アプリケーションの間のプロキシの構成がサポートされます。 詳しくは、「[既存のオンプレミス プロキシ サーバーと連携する](application-proxy-configure-connectors-with-proxy-servers.md)」をご覧ください。

### <a name="fixed-issues"></a>修正された問題
- コネクタから Azure サービスへの通信に対するポート 8080 へのフォールバックが削除されました。
- WebSocket 通信のデバッグ トレースが追加されました。 
- バックエンド アプリケーション Cookie に設定された場合の SameSite 属性の保持が解決されました。

## <a name="156120"></a>1.5.612.0

### <a name="release-status"></a>リリースの状態

2018 年 9 月 20 日:ダウンロード対象としてリリース済み

### <a name="new-features-and-improvements"></a>新機能と機能強化

- QlikSense アプリケーションの WebSocket のサポートを追加しました。 QlikSense をアプリケーション プロキシに統合する方法については、この[チュートリアル](application-proxy-qlik.md)を参照してください。 
- 送信プロキシを簡単に構成できるようにインストール ウィザードを改良しました。 
- TLS 1.2 をコネクタの既定のプロトコルとして設定しました。 
- 新しいエンド ユーザー ライセンス契約 (EULA) を追加しました。  

### <a name="fixed-issues"></a>修正された問題

- コネクタでメモリ リークの原因となっていたバグを修正しました。
- Azure Service Bus のバージョンを更新しました。このバージョンでは、コネクタ タイムアウトの問題に関するバグが修正されています。

## <a name="154020"></a>1.5.402.0

### <a name="release-status"></a>リリースの状態

2018 年 1 月 19 日:ダウンロード対象としてリリース済み

### <a name="fixed-issues"></a>修正された問題

- Cookie でのドメイン変換を必要とするカスタム ドメインのサポートを追加しました。

## <a name="151320"></a>1.5.132.0

### <a name="release-status"></a>リリースの状態 

2017 年 5 月 25 日:ダウンロード対象としてリリース済み 

### <a name="new-features-and-improvements"></a>新機能と機能強化 

コネクタの送信接続の制限の管理を強化しました。 

## <a name="15360"></a>1.5.36.0

### <a name="release-status"></a>リリースの状態

2017 年 4 月 15 日:ダウンロード対象としてリリース済み

### <a name="new-features-and-improvements"></a>新機能と機能強化

- 必要なポート数を減らしてオンボードと管理を簡略化しました。 アプリケーション プロキシで開く必要がある標準送信ポートが次の 2 つだけになりました:443 および 80。 アプリケーション プロキシは引き続き送信接続のみを使用するため、これまでと同様に DMZ にコンポーネントは必要ありません。 詳細については、 [構成ドキュメント](application-proxy-add-on-premises-application.md)を参照してください。  
- 使用する外部のプロキシまたはファイアウォールでサポートされていれば、IP 範囲ではなく DNS でネットワークを開くことができます。 アプリケーション プロキシ サービスで必要な接続は、*.msappproxy.net と *.servicebus.windows.net への接続だけです。


## <a name="earlier-versions"></a>以前のバージョン

使用するアプリケーション プロキシ コネクタのバージョンが 1.5.36.0 より前である場合は、最新バージョンに更新して、完全にサポートされている最新機能を使用できるようにしておいてください。

## <a name="next-steps"></a>次のステップ
- [Azure AD アプリケーション プロキシからのオンプレミス アプリケーションへのリモート アクセス](application-proxy.md)の詳細を確認します。
- アプリケーション プロキシの使用を開始するには、[アプリケーション プロキシを使用したリモート アクセスを行うためのオンプレミス アプリケーションの追加に関するチュートリアル](application-proxy-add-on-premises-application.md)を参照してください。
