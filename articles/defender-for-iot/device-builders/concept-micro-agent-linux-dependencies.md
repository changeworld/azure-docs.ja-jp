---
title: マイクロ エージェントでの Linux との依存関係 (プレビュー)
description: この記事では、Defender for IoT マイクロ エージェントでのさまざまな Linux OS との依存関係について説明します。
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: 102cf31dbe19c068de344f1f7f294a6f3f0a722b
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2021
ms.locfileid: "122967560"
---
# <a name="micro-agent-linux-dependencies-preview"></a>マイクロ エージェントでの Linux との依存関係 (プレビュー)

この記事では、Defender for IoT マイクロ エージェントでのさまざまな Linux OS との依存関係について説明します。 

## <a name="linux-dependencies"></a>Linux の依存関係

次の表は、各コンポーネントでの Linux との依存関係を示しています。 

| コンポーネント | 依存関係 | 型 | IoT SDK で必須 | メモ |
|--|--|--|--|--|
| **Core** |  |  |  |  |
|  | libcurl-openssl (libcurl) | ライブラリ | ✔ |  |
|  | libssl | ライブラリ | ✔ |  |
|  | uuid | ライブラリ | ✔ |  |
|  | pthread | ulibc コンパイル フラグ | ✔ |  |
|  | libuv1 | ライブラリ |  |  |
|  | sudo | パッケージ |  |  |
|  | uuid-runtime | パッケージ |  |  |
| **システム情報コレクター** |  |  |  |  |
|  | uname | システム コール |  |  |
| **ベースライン コレクター** |  |  |  |  |
|  | BusyBox | Linux コンパイル フラグ |  |  |
|  | Bash | Linux コンパイル フラグ |  |  |
| **プロセス コレクター** |  |  |  |  |
|  | CONFIG_CONNECTOR=y | カーネル構成 |  |  |
|  | CONFIG_PROC_EVENTS=y | カーネル構成 |  |  |
| **ネットワーク コレクター** |  |  |  |  |
|  | libpcap | ライブラリ |  |  |
|  | CONFIG_PACKET=y | カーネル構成 |  |  |
|  | CONFIG_NETFILTER =y | カーネル構成 |  | 省略可能 – パフォーマンスの向上 |
| **ログイン コレクター** |  |  |  |  |
|  | Wtmp、btmp | ログ ファイル |  | [utmp](https://en.wikipedia.org/wiki/Utmp) |

## <a name="next-steps"></a>次の手順

[Defender for IoT マイクロ エージェントをインストールする (プレビュー)](quickstart-standalone-agent-binary-installation.md)。