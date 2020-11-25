---
title: App Service Environment のネットワーク
description: App Service Environment のネットワークの詳細
author: ccompy
ms.assetid: 6f262f63-aef5-4598-88d2-2f2c2f2bfc24
ms.topic: article
ms.date: 11/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 61059c3e0f9737df6ace338f4252a338ea1f200c
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2020
ms.locfileid: "94663329"
---
# <a name="app-service-environment-networking"></a>App Service Environment のネットワーク

> [!NOTE]
> この記事では、App Service Environment v3 (プレビュー) について説明します。
> 

App Service Environment (ASE) は、Web アプリ、API アプリ、および関数アプリがホストされる Azure App Service のシングル テナント デプロイです。 ASE をインストールするときに、デプロイする Azure Virtual Network (VNet) を選択します。 すべての受信および送信トラフィック アプリケーションは、指定した VNet 内に配置されます。  

ASEv3 では 2 つのサブネットが使用されます。  1 つのサブネットは、受信トラフィックを処理するプライベート エンドポイントに使用されます。 これには、既存のサブネットも新しいものも使用できます。  受信サブネットは、プライベート エンドポイント以外の目的に使用できます。 送信サブネットは、ASE からの送信トラフィックにのみ使用できます。 ASE の送信サブネットでは他のものは使用できません。

## <a name="addresses"></a>アドレス 
ASE には、作成時に次のアドレスがあります。

| アドレスの種類 | description |
|--------------|-------------|
| 受信アドレス | 受信アドレスは、ASE によって使用されるプライベート エンドポイントのアドレスです。 |
| 送信サブネット | 送信サブネットは、ASE サブネットでもあります。 プレビュー期間中、このサブネットは送信トラフィックにのみ使用されます。 |
| Windows 送信アドレス | この ASE の Windows アプリでは、インターネットへの送信呼び出しを行うときに、既定でこのアドレスが使用されます。 |
| Linux 送信アドレス | この ASE の Linux アプリでは、インターネットへの送信呼び出しを行うときに、既定でこのアドレスが使用されます。 |

ASE によって使用されるプライベート エンドポイントを削除すると、ご使用の ASE 内のアプリに接続できなくなります。 ASE に関連付けられている Azure DNS プライベート ゾーンは削除しないでください。  

ASE では、ASE で使用されるインフラストラクチャをサポートするために、送信サブネットのアドレスが使用されます。 ASE で App Service プランをスケーリングする際は、さらに多くのアドレスを使用します。 ASE 内のアプリには、送信サブネットに専用のアドレスがありません。 アプリによって送信サブネットで使用されるアドレスは、時間の経過と共に変化します。

## <a name="ports"></a>ポート

ASE では、ポート 80 と 443 でアプリケーション トラフィックが受信されます。  ASE には、受信または送信ポートの要件はありません。 

## <a name="extra-configurations"></a>追加の構成

ASEv2 とは異なり、ASEv3 では、必要に応じて制限なしに、ネットワーク セキュリティ グループ (NSG) とルート テーブル (UDR) を設定できます。 ASE からのすべての送信トラフィックを NVA デバイスに強制的にトンネリングできます。 ASE へのすべての受信トラフィックの前に WAF デバイスを配置できます。 

## <a name="dns"></a>DNS

ASE 内のアプリでは、VNet の構成で使用されている DNS が使用されます。 一部のアプリで別の DNS サーバーを使用する場合は、アプリの設定 WEBSITE_DNS_SERVER と WEBSITE_DNS_ALT_SERVER を使用して、アプリごとに手動で設定できます。 アプリの設定 WEBSITE_DNS_ALT_SERVER により、セカンダリ DNS サーバーが構成されます。 セカンダリ DNS サーバーは、プライマリ DNS サーバーからの応答がない場合にのみ使用されます。 

## <a name="preview-limitation"></a>プレビューでの制限事項

ASEv3 で利用できないネットワーク機能がいくつかあります。  ASEv3 で利用できないものとしては、次のようなものがあります。

• FTP • リモート デバッグ • 外部ロード バランサーのデプロイ • コンテナー デプロイのためにプライベート コンテナー レジストリにアクセスする機能 • グローバルにピアリングされた Vnet を呼び出す機能 • サービス エンドポイントまたはプライベート エンドポイントのセキュリティで保護されたストレージ アカウントを使用してバックアップ/リストアする機能 • サービス エンドポイントまたはプライベート エンドポイントのセキュリティで保護された keyvault アカウントでアプリ設定の keyvault 参照を使用する機能 • サービス エンドポイントまたはプライベート エンドポイントのセキュリティで保護されたストレージ アカウントに BYOS を使用する機能 • 送信トラフィックでの Network Watcher または NSG フローの使用
    
    

