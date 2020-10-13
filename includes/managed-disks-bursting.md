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
ms.openlocfilehash: 634deb655620f99c7b7b2b55819c58308eaa5a92
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2020
ms.locfileid: "91275161"
---
Azure には、Virtual Machines とディスクの両方でディスク記憶域の IOPS と帯域幅 (MB/秒) パフォーマンスを高めるバーストと呼ばれる機能が用意されています。 バーストは、予期しないディスク トラフィックの処理やバッチ ジョブの処理などさまざまなシナリオで役立ちます。 VM とディスク レベルのバーストを効果的に活用すれば、高いベースライン パフォーマンスとバースト パフォーマンスを VM とディスクの両方で実現できます。 そうすることで、高いベースライン パフォーマンスとバースト パフォーマンスを VM とディスクの両方で実現することができます。 

ディスクと VM でのバーストは、互いに独立していることに注意してください。 バーストするディスクがある場合に、バーストする VM においてディスクのバーストを許可する必要はありません。 バーストする VM がある場合に、バーストするディスクにおいて VM のバーストを許可する必要はありません。 
