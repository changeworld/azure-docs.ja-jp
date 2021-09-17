---
title: 予測実行の軽減に関するガイダンス
description: Azure での予測実行のサイドチャネルの脆弱性の軽減に関して詳細に説明します。
services: virtual-machines
author: cynthn
keywords: spectre,メルトダウン,スペクター
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.subservice: security
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: cynthn
ms.openlocfilehash: 764484d427ee0500d87b39798e60d301547c36f9
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2021
ms.locfileid: "122698087"
---
# <a name="guidance-for-mitigating-speculative-execution-side-channel-vulnerabilities"></a>予測実行のサイドチャネルの脆弱性を軽減するためのガイダンス

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: フレキシブルなスケール セット :heavy_check_mark: 均一スケール セット

[!INCLUDE [virtual-machines-common-mitigate-se](../../includes/virtual-machines-common-mitigate-se.md)]
