---
title: SQL Server VM 上の可用性グループには、VNN リスナーではなく分散ネットワーク名 (DNN) リスナーを使用してください。
description: VNN リスナーではなく DNN リスナーを使用するようにお客様に求めるメッセージ。
ms.topic: include
author: MashaMSFT
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: b4ea8428ae23537cde584bf6944732f65ebd2f26
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92167996"
---
> [!NOTE]
> SQL Server 2019 CU8 以降および Windows 2016 以降をお使いのお客様は、従来の VNN リスナーと Azure Load Balancer を[分散ネットワーク名 (DNN) リスナー](../virtual-machines/windows/availability-group-distributed-network-name-dnn-listener-configure.md)で置き換えることができます。 リスナーとロード バランサーを作成するこの記事の残りの手順はスキップしてください。
