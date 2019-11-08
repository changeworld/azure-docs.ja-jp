---
title: インクルード ファイル
description: インクルード ファイル
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 10/16/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b667745094ccb6952a6b17f5b7ef0cabae4220c5
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73476900"
---
| リソース | 既定の制限 |
| --- | --- |
| コンカレント RDP 接続数 |25* |
| コンカレント SSH 接続数 |50 を超える** |

*他の実行中 RDP セッションや、他の実行中 SSH セッションによって異なる場合があります。<br>
**既存の RDP 接続がある場合、または他の実行中 SSH セッションから使用されている場合は、異なる可能性があります。