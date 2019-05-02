---
title: Azure Functions のネットワーク オプション
description: Azure Functions で利用可能なすべてのネットワーク オプションの概要
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: b7af0149a690e3cc3a357a5cb769751e3674d374
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2019
ms.locfileid: "59698203"
---
# <a name="azure-functions-networking-options"></a>Azure Functions のネットワーク オプション

この記事では、Azure Functions のホスティング オプションで利用できるネットワーク機能について説明します。 次のネットワーク オプションはすべて、インターネットのルーティング可能アドレスを使用せずにリソースにアクセスする機能、または関数アプリへのインターネット アクセスを制限する機能を提供します。 

ホスティング モデルには、さまざまなレベルのネットワーク分離機能があります。 正しいものを選択すると、ネットワーク分離の要件を満たすために役立ちます。

関数アプリは、いくつかの方法でホストできます。

* さまざまなレベルの仮想ネットワーク接続性とスケーリングのオプションが設定された、マルチテナント インフラストラクチャ上で実行される一連のプラン オプションがあります。
    * [従量課金プラン](functions-scale.md#consumption-plan)。負荷に応じて動的にスケーリングし、最小限のネットワークの分離オプションを提供します。
    * [Premium プラン](functions-scale.md#premium-plan-public-preview)。このプランでも動的なスケーリングが行われますが、より包括的なネットワークの分離が提供されます。
    * [App Service プラン](functions-scale.md#app-service-plan)。固定されたスケールで動作し、Premium プランと同様のネットワークの分離を提供します。
* [App Service Environment](../app-service/environment/intro.md) で関数を実行できます。 この方法では、関数を仮想ネットワークにデプロイし、完全なネットワーク制御と分離を提供します。

## <a name="matrix-of-networking-features"></a>ネットワーク機能のマトリックス

|                |[従量課金プラン](functions-scale.md#consumption-plan)|⚠ [Premium プラン](functions-scale.md#premium-plan-public-preview)|[[App Service プラン]](functions-scale.md#app-service-plan)|[App Service 環境](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[受信 IP の制限](#inbound-ip-restrictions)|✅はい|✅はい|✅はい|✅はい|
|[仮想ネットワークの統合](#virtual-network-integration)|❌いいえ|❌いいえ|✅はい|✅はい|
|[仮想ネットワーク統合のプレビュー (Azure ExpressRoute とサービス エンドポイント)](#preview-version-of-virtual-network-integration)|❌いいえ|⚠はい|⚠はい|✅はい|
|[ハイブリッド接続](#hybrid-connections)|❌いいえ|❌いいえ|✅はい|✅はい|
|[プライベート サイトへのアクセス](#private-site-access)|❌いいえ| ❌いいえ|❌いいえ|✅はい|

⚠ プレビュー機能は実稼働用ではありません。

## <a name="inbound-ip-restrictions"></a>受信 IP の制限

IP 制限を使用すると、アプリへのアクセスを許可または拒否する IP アドレスの優先順のリストを定義できます。 このリストには、IPv4 アドレスと IPv6 アドレスを含めることができます。 1 つまたは複数のエントリがある場合、リストの末尾には暗黙的な "すべて拒否" が存在します。 IP 制限は、すべての関数ホスティング オプションで有効です。

> [!NOTE]
> Azure portal エディターを使用するには、実行中の関数アプリに Azure portal が直接アクセスできる必要があります。 また、ポータルにアクセスするために使用しているデバイスの IP がホワイトリストに登録されている必要があります。 ネットワーク制限が設定されていても、**[プラットフォーム機能]** タブのどの機能にもアクセスできます。

詳細については、「[Azure App Service の静的なアクセス制限](../app-service/app-service-ip-restrictions.md)」を参照してください。

## <a name="virtual-network-integration"></a>仮想ネットワークの統合

仮想ネットワーク統合により、関数アプリは仮想ネットワーク内のリソースにアクセスできます。 この機能は、Premium プランと App Service プランの両方で使用できます。 アプリが App Service Environment 内にある場合は、既に仮想ネットワーク内にそのアプリが存在しているので、同じ仮想ネットワーク内のリソースに到達するために 仮想ネットワーク統合を使用する必要はありません。

仮想ネットワーク統合により、関数アプリは仮想ネットワーク内のリソースにアクセスできるようになりますが、仮想ネットワークからその関数アプリへの[プライベート サイト アクセス](#private-site-access)は付与されません。

仮想ネットワーク統合を使用すると、アプリから、仮想ネットワークで実行されているデータベースや Web サービスにアクセスできます。 仮想ネットワーク統合では、VM 上でアプリケーション用にパブリック エンドポイントを公開する必要はありません。 代わりに、インターネット ルーティングできないプライベート アドレスを使用できます。

一般提供バージョンの仮想ネットワーク統合は、関数アプリを仮想ネットワークに接続するために VPN ゲートウェイに依存しています。 App Service プランでホストされている関数で使用できます。 この機能の構成方法については、「[アプリを Azure 仮想ネットワークに統合する](../app-service/web-sites-integrate-with-vnet.md#enabling-vnet-integration)」を参照してください。

### <a name="preview-version-of-virtual-network-integration"></a>仮想ネットワーク統合のプレビュー バージョン

仮想ネットワーク統合機能の新しいバージョンは、プレビュー段階にあります。 ポイント対サイト VPN には依存していません。 ExpressRoute またはサービス エンドポイントを越えてのリソースへのアクセスをサポートしています。 Premium プラン、および PremiumV2 にスケーリングされた App Service プランで使用できます。

このバージョンの特徴をいくつか次に示します。

* 使用するためにゲートウェイは必要ありません。
* ExpressRoute で接続された仮想ネットワークと統合することのほかに、追加構成なしで、ExpressRoute 接続をまたいでリソースにアクセスできます。
* 実行中の関数から、サービス エンドポイントのセキュリティで保護されたリソースを使用できます。 これを行うには、仮想ネットワーク統合に使用されるサブネットでサービス エンドポイントを有効にします。
* サービス エンドポイントのセキュリティで保護されたリソースを使用するようにトリガーを構成することはできません。 
* 関数アプリと仮想ネットワークの両方が同じリージョンに存在する必要があります。
* 新しい機能では、Azure Resource Manager を介してデプロイした仮想ネットワーク内に未使用のサブネットが必要です。
* この機能がプレビュー段階の間は、運用環境のワークロードはサポートされません
* この機能では、ルート テーブルとグローバル ピアリングはまだ使用できません。
* 関数アプリの潜在的なインスタンスごとに、1 つのアドレスが使用されます。 サブネットのサイズは割り当て後に変更できないため、最大スケール サイズを容易にサポートできるサブネットを使用してください。 たとえば、80 個のインスタンスにスケーリングできる Premium プランをサポートするには、126 個のホスト アドレスを提供する `/25` サブネットをお勧めします。

プレビュー バージョンの仮想ネットワーク統合の使用の詳細については、「[関数アプリを Azure 仮想ネットワークに統合する](functions-create-vnet.md)」を参照してください。

## <a name="hybrid-connections"></a>ハイブリッド接続

[ハイブリッド接続](../service-bus-relay/relay-hybrid-connections-protocol.md)は、他のネットワークのアプリケーション リソースにアクセスするために使用できる Azure Relay の機能です。 アプリからアプリケーション エンドポイントにアクセスできます。 アプリケーションにアクセスするために使用することはできません。 ハイブリッド接続は、[App Service プラン](functions-scale.md#app-service-plan)および [App Service Environment](../app-service/environment/intro.md) で実行されている関数に使用できます。

Azure Functions で使用されるとき、各ハイブリッド接続は、単一の TCP ホストとポートの組み合わせに相互に関連付けられます。 つまり、TCP リッスン ポートにアクセスしている限り、任意のオペレーティング システムの任意のアプリケーションがハイブリッド接続エンドポイントになることができます。 ハイブリッド接続では、アプリケーション プロトコルやアクセス先は認識されません。 それは、ネットワーク アクセスを提供するだけです。

詳細については、App Service プランで Functions をサポートする[ハイブリッド接続の App Service ドキュメント](../app-service/app-service-hybrid-connections.md)を参照してください。

## <a name="private-site-access"></a>プライベート サイトへのアクセス

プライベート サイト アクセスとは、Azure 仮想ネットワークなどプライベート ネットワークのみからアプリにアクセスできるようにすることです。 プライベート サイトには、内部ロード バランサー (ILB) を使用して App Service Environment が構成されている場合のみアクセスできます。 詳細については、「[App Service Environment で内部ロード バランサーを作成して使用する](../app-service/environment/create-ilb-ase.md)」を参照してください。

他のホスティング オプションで仮想ネットワーク リソースにアクセスする方法は多数あります。 ただし、App Service Environment は、仮想ネットワーク上で関数のトリガーを発生させることができる唯一の方法です。

## <a name="next-steps"></a>次の手順
ネットワークと Azure Functions の詳細については、以下を参照してください。 

* [仮想ネットワーク統合の概要に関するチュートリアル](./functions-create-vnet.md)
* [関数のネットワークに関する FAQ](./functions-networking-faq.md)
* [仮想ネットワーク統合と App Service/Functions に関する詳細情報](../app-service/web-sites-integrate-with-vnet.md)
* [Azure の仮想ネットワークに関する詳細情報](../virtual-network/virtual-networks-overview.md)
* [App Service Environment でさらなるネットワーク機能と制御を可能にする](../app-service/environment/intro.md)
* [ハイブリッド接続を使用して、ファイアウォールを変更せずに個々のオンプレミス リソースに接続する](../app-service/app-service-hybrid-connections.md)
