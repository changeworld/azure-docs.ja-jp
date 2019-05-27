---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines-windows, virtual-machines-linux
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 10/09/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 4d2235eaea457c89d01a632afa5dd5a862bec344
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/23/2019
ms.locfileid: "66117285"
---
## <a name="deploy-an-image-with-marketplace-terms"></a>Marketplace の契約条件でイメージを展開する

Azure Marketplace の一部の VM イメージには、プログラムでデプロイする前に同意する必要がある追加のライセンスと購入契約条件があります。  

このようなイメージから VM をデプロイするには、イメージの契約条件に同意し、なおかつプログラムによるデプロイを有効にする必要があります。 これを行う必要があるのはサブスクリプションごとに 1 回だけです。 その後、イメージからプログラムを使って VM をデプロイするたびに、*購入プラン*のパラメーターを指定する必要があります。

以下のセクションでは、その方法について説明します。

* Marketplace イメージに追加のライセンス条件があるかどうかを調べる 
* プログラムで契約条件に同意する
* プログラムを使って VM を展開するときに、購入プランのパラメーターを入力する

