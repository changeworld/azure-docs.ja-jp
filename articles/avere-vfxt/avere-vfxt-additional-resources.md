---
title: Avere vFXT for Azure に関するその他のリンク
description: Avere vFXT for Azure に関する追加情報へのリンク
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: afba39d1af700650cfbf7226dff36729a76a0bda
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/31/2018
ms.locfileid: "50669789"
---
# <a name="additional-documentation"></a>その他のドキュメント

この記事では、Avere コントロール パネル管理インターフェイスと関連トピックに関するその他のドキュメントへのリンクを示します。 

## <a name="avere-cluster-documentation"></a>Avere クラスターのドキュメント

Avere クラスターに関するその他のドキュメントは、Web サイト (<http://library.averesystems.com/>) で確認できます。 これらのドキュメントでは、クラスターの機能と、クラスターの設定を構成する方法を理解できます。 

* 「[FXT Cluster Creation Guide](<http://library.averesystems.com/#fxt_cluster>)」 (FXT クラスターの作成ガイド) は、物理ハードウェア ノードで構成されたクラスターを対象にしていますが、ドキュメントの一部の情報は vFXT クラスターにも関連しています。 特に、初めて vFXT クラスターを管理する管理者は以下のセクションを読むと役に立ちます。
  * 「[Customizing Support and Monitoring Settings](<http://library.averesystems.com/create_cluster/4_8/html/config_support.html#config-support>)」 (サポートと監視の設定のカスタマイズ) では、サポート アップロードの設定をカスタマイズする方法と、リモート監視を有効にする方法について説明しています。 
  * 「[Configuring VServers and Global Namespace](<http://library.averesystems.com/create_cluster/4_8/html/config_vserver.html#config-vserver>)」 (VServers とグローバル名前空間の構成) には、クライアントに接続する名前空間の作成に関する情報が記載されています。
  * 「[Configuring DNS for the Avere cluster](<http://library.averesystems.com/create_cluster/4_8/html/config_network.html#dns-overview>)」 (Avere クラスター向け DNS の構成) では、ラウンドロビン DNS を構成する方法について説明しています。
  * 「[Adding Backend Storage](<http://library.averesystems.com/create_cluster/4_8/html/config_core_filer.html#add-core-filer>)」 (バックエンド ストレージの追加) では、コア ファイラーを追加する方法について説明しています。

* 「[Cluster Configuration Guide](<http://library.averesystems.com/#operations>)」 (クラスター構成ガイド) は、Avere クラスターの設定とオプションの詳細なリファレンスです。 vFXT クラスターには、これらのオプションのサブセットを使用しますが、同じ構成ページのほとんどが適用されます。

* 「[Dashboard Guide](<http://library.averesystems.com/#operations>)」 (ダッシュボード ガイド) では、Avere コントロール パネルのクラスター監視機能の使用方法について説明しています。

## <a name="vfxt-creation-and-management-documentation"></a>vFXT の作成と管理のドキュメント

クラウド クラスターの作成および管理ユーティリティである vfxt.py の使用に関する詳細なガイドが、<https://github.com/AvereSystems/vFXT.py/blob/master/docs/README.md> に提供されています。  
