---
title: Azure Cosmos Emulator ダウンロードとリリース ノート
description: さまざまなバージョンの Azure Cosmos エミュレーターのリリース ノートとダウンロード情報を入手します。
ms.service: cosmos-db
ms.topic: tutorial
author: milismsft
ms.author: adrianmi
ms.date: 06/20/2019
ms.openlocfilehash: 4dffe169908d0dd3effa4e46140b5f6696805a3e
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2020
ms.locfileid: "77168642"
---
# <a name="azure-cosmos-emulator---release-notes-and-download-information"></a>Azure Cosmos Emulator - リリース ノートとダウンロード情報

この記事では、Azure Cosmos エミュレーター リリース ノートを各リリースで行われた機能更新プログラムを示して説明します。 また、ダウンロードして使用するためのエミュレーターの最新バージョンも記載しています。

## <a name="download"></a>ダウンロード

| | |
|---------|---------|
|**MSI ダウンロード**|[Microsoft ダウンロード センター](https://aka.ms/cosmosdb-emulator)|
|**開始するには**|[Azure Cosmos Emulator を使用してローカルで開発する](local-emulator.md)|

## <a name="release-notes"></a>リリース ノート

### <a name="291"></a>2.9.1

- このリリースでは、クエリ API のサポートに関するいくつかの問題が修正され、Windows Server 2012 などの古い OS との互換性が復元されています。

### <a name="290"></a>2.9.0

- このリリースでは、整合性を "整合性のあるプレフィックス" に設定してユーザーとアクセス許可の上限を引き上げるオプションが追加されています。

### <a name="272"></a>2.7.2

- このリリースでは、MongoDB バージョン 3.6 サーバーのサポートが Cosmos Emulator に追加されました。 サービスのバージョン 3.6 を対象とする MongoDB エンドポイントを起動するには、管理者用のコマンド ラインで "/EnableMongoDBEndpoint=3.6" オプションを使用してエミュレーターを起動します。

### <a name="270"></a>2.7.0

- このリリースでは、.Net Core または x86 .NET ベースのクライアントが使用されている場合に、エミュレーターからの SQL API アカウントに対するクエリの実行を妨げていた回帰が修正されます。

### <a name="246"></a>2.4.6

- このリリースは、2019 年 7 月時点の Azure Cosmos サービスの機能と同等です。ただし、[Azure Cosmos エミュレーターを使用したローカルでの開発](local-emulator.md)に関するページに記載されている例外を除きます。 また、コマンド ラインを介して起動した場合のエミュレーターのシャットダウンに関連するバグ、および直接モード接続を使用して行われる SDK クライアントに対する内部 IP アドレスのオーバーライドに関するバグも修正されます。

### <a name="243"></a>2.4.3

- MongoDB サービスの既定での無効な開始。 既定で、SQL エンドポイントのみが有効になります。 ユーザーは、エミュレーターの "/EnableMongoDbEndpoint" コマンド ライン オプションを使用して、エンドポイントを手動で開始する必要があります。 これは、他のすべてのサービス エンドポイント (Gremlin、Cassandra、Table など) と同様です。
- "/AllowNetworkAccess" で開始した場合のエミュレーターのバグ (Gremlin、Cassandra、および Table エンドポイントが外部クライアントからの要求を適切に処理しない) を修正しました。
- 直接接続のポートをファイアウォール規則の設定に追加します。

### <a name="240"></a>2.4.0

- Pulse Client などのネットワーク監視アプリがホスト コンピューター上にあるときにエミュレーターの起動が失敗する問題を修正しました。
