---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 05/31/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 33dff710d83bd12a8db343a89c6e4576d1397ba7
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38763097"
---
いいえ。サーバー エンドポイントの削除は、サーバーの再起動とは異なります。 サーバー エンドポイントを削除して再作成する操作が、同期、クラウドの階層化などの Azure File Sync の側面に関する問題を解決するために適した解決策になることはほぼありません。サーバー エンドポイントの削除は破壊的な操作です。階層化されたファイルがサーバー エンドポイントの名前空間以外に存在する場合 (詳細については、「[Why do tiered files exist outside of the server endpoint namespace](../articles/storage/files/storage-files-faq.md#afs-tiered-files-out-of-endpoint)」(階層化されたファイルがサーバー エンドポイントの名前空間以外に存在するのはなぜですか) を参照してください) にデータが失われたり、サーバー エンドポイントの名前空間内に存在する階層化されたファイルにアクセスできなくなったりする可能性があります。 このような問題は、サーバー エンドポイントを再作成しても解決しません。 クラウドの階層化を有効にしていない場合でも、階層化されたファイルがサーバー エンドポイントの名前空間内に存在する可能性があります。 そのため、特定のフォルダーで Azure File Sync の使用を停止したい場合や、Microsoft エンジニアから明示的に指示された場合を除き、サーバー エンドポイントを削除しないことをお勧めします。 サーバー エンドポイントの削除の詳細については、「[サーバー エンドポイントを削除する](../articles/storage/files/storage-sync-files-server-endpoint.md#remove-a-server-endpoint)」を参照してください。    