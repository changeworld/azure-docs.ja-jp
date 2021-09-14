---
title: Azure SignalR Service に関する制限の表
description: Azure SignalR Service に関するシステム制限について説明します。
services: signalr
documentationcenter: signalr
author: sffamily
manager: cfowler
editor: ''
ms.service: signalr
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/11/2020
ms.author: zhshang
ms.openlocfilehash: f5b197448529e953d5d7d188b00a88d60e87e900
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123454292"
---
| リソース | 既定の制限 | 上限 | 
| --- | --- | --- |
| Free レベルのインスタンスあたりの Azure SignalR Service ユニット数 |1 |1 |
| Standard レベルのインスタンスあたりの Azure SignalR Service ユニット数 |100 |100 |
| リージョン別での、Free レベルのサブスクリプションあたりの Azure SignalR Service ユニット数|5 |5 |
| リージョン別での、サブスクリプションあたりの合計 Azure SignalR Service ユニット数 |150 |無制限 |
| Free レベルの 1 日あたりの接続 |20 |20 |
| Standard レベルの 1 日あたりの接続 |1,000 |1,000|
| Free レベルの 1 日あたりの組み込みメッセージ|20,000 |20,000 |
| Free レベルの 1 日あたりの追加メッセージ|0 |0 |
| Standard レベルの 1 日あたりの組み込みメッセージ|1,000,000 |1,000,000 |
| Standard レベルの 1 日あたりの追加メッセージ|無制限 |無制限 |

サブスクリプションの既定の制限を更新するように要求するには、サポート チケットを開きます。

接続とメッセージがどのようにカウントされるかの詳細については、「[Azure SignalR Service でのメッセージと接続](../articles/azure-signalr/signalr-concept-messages-and-connections.md)」を参照してください。

要件が制限を超えている場合は、Free レベルから Standard レベルに切り替え、ユニットを追加します。 詳細については、「[Azure SignalR Service のインスタンスをスケーリングする方法](../articles/azure-signalr/signalr-howto-scale-signalr.md)」を参照してください。 

要件が 1 つのインスタンスの制限を超える場合は、インスタンスを追加します。 詳細については、「[複数のインスタンスでの SignalR Service のスケーリング方法](../articles/azure-signalr/signalr-howto-scale-multi-instances.md)」を参照してください。
