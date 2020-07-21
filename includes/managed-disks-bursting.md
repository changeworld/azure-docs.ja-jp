---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 04/27/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 2af8b429b6addf6da32b34773525c51a36624e78
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85242214"
---
Azure には、Virtual Machines とディスクの両方でディスク記憶域の IOPS と帯域幅 (MB/秒) パフォーマンスを高めるバーストと呼ばれる機能が用意されています。 バーストは、予期しないディスク トラフィックの処理やバッチ ジョブの処理などさまざまなシナリオで役立ちます。 VM とディスク レベルのバーストを効果的に活用すれば、高いベースライン パフォーマンスとバースト パフォーマンスを VM とディスクの両方で実現できます。 そうすることで、高いベースライン パフォーマンスとバースト パフォーマンスを VM とディスクの両方で実現することができます。 

ディスクと VM でのバーストは、互いに独立していることに注意してください。 バーストするディスクがある場合に、バーストする VM においてディスクのバーストを許可する必要はありません。 バーストする VM がある場合に、バーストするディスクにおいて VM のバーストを許可する必要はありません。 