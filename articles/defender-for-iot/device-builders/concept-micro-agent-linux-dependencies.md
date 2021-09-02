---
title: マイクロ エージェントでの Linux との依存関係 (プレビュー)
description: この記事では、Defender for IoT マイクロ エージェントでのさまざまな Linux OS との依存関係について説明します。
ms.topic: conceptual
ms.date: 07/19/2021
ms.openlocfilehash: e878052e534ce7740fff1fdd462d2c95fcb11609
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114481768"
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
|  | sudo | Package |  |  |
|  | uuid-runtime | Package |  |  |
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

## <a name="next-steps"></a>次のステップ

[Defender for IoT マイクロ エージェントをインストールする (プレビュー)](quickstart-standalone-agent-binary-installation.md)。