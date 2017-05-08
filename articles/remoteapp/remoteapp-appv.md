---
title: "Azure RemoteApp で App-V アプリを使用する | Microsoft Docs"
description: "Azure RemoteApp で App-V アプリを使用する方法について説明します。"
services: remoteapp
documentationcenter: 
author: ericorman
manager: mbaldwin
ms.assetid: e2292cb2-5c89-4b2b-ab11-74dbacd07c31
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 0e82c639ea7bfceb89f0cf9c59ab657a951f2d5f
ms.lasthandoff: 03/31/2017


---
# <a name="using-app-v-apps-in-azure-remoteapp"></a>Azure RemoteApp で App-V アプリを使用する
> [!IMPORTANT]
> Azure RemoteApp の提供は、2017 年 8 月 31 日で終了します。 詳細については、 [お知らせ](https://go.microsoft.com/fwlink/?linkid=821148) をご覧ください。
> 
> 

ドメイン参加を必要とする Azure RemoteApp ハイブリッド コレクションで App-V アプリケーションを使用できます。

開始前に、最新の更新プログラムで App-V 5.1 クライアントをインストールしてください。 App-V クライアントを含む [カスタム イメージ](remoteapp-create-custom-image.md) を作成する必要があります。  

既存の App-V インフラストラクチャを Azure RemoteApp で簡単に利用できます。 ハイブリッド コレクションはお使いのドメイン コントローラーにアクセスできる Azure VNET にデプロイされ、VM はドメインに参加するため、既存の App-V インフラストラクチャとデプロイメント手法を活用し、Azure RemoteApp に App-V アプリケーションを簡単にホストできます。 現在の App-V デプロイメントに基づいて次のような項目を考慮する必要があります。

| 構成オプション |  | Positive | Negative |
| --- | --- | --- | --- |
| 配信方法 |ストリーミング (オンデマンド) |アプリケーションが常に最新の状態である |最初の待ち時間 |
| マウントされている |最速。アプリは既に VM 上に存在する。 |肥大化 - イメージ領域を占有する (127 GB 上限) | |
| アプリ場所ストレージ |共有コンテンツ |アプリは Azure RemoteApp インスタンスのメモリ内で実行される |アプリが置かれているストリーミング (ファイル) サーバーのメモリを占有し、接続を低下させる |
| ディスク (キャッシュ済み) |高速実行。 アプリはコンテンツ リソースの可用性に依存しない |肥大化 - イメージ領域を占有する (127 GB 上限) | |
| 対象設定 |User |完全なスタンドアロンの App-V インフラストラクチャを必要とする | |
| グローバル (コンピューター) |パブリッシング サーバーを利用した事前公開または対象設定 |(大きな) アプリを更新する場合、Azure イメージを更新する必要があります。 イメージの一部の領域を占有します。 | |

 カスタム イメージとハイブリッド コレクションを作成したら、アプリケーションを公開し、ユーザーを割り当てれば、Azure RemoteApp にホストされている既存の App-V アプリケーションを、場所を問わず、あらゆるデバイスに配信できます。


