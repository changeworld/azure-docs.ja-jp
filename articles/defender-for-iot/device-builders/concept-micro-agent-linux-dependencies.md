---
title: マイクロ エージェントでの Linux との依存関係 (プレビュー)
description: この記事では、Defender for IoT マイクロ エージェントでのさまざまな Linux OS との依存関係について説明します。
ms.topic: conceptual
ms.date: 11/09/2021
ms.openlocfilehash: 9fa1d53481e5e7f3b775855e54a0387fc04518ba
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132284115"
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
