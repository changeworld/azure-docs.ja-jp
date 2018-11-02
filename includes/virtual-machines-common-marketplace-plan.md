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
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2018
ms.locfileid: "49437007"
---
## <a name="deploy-an-image-with-marketplace-terms"></a>Marketplace の契約条件でイメージを展開する

Azure Marketplace の一部の VM イメージには、プログラムでデプロイする前に同意する必要がある追加のライセンスと購入契約条件があります。  

このようなイメージから VM をデプロイするには、イメージの契約条件に同意し、なおかつプログラムによるデプロイを有効にする必要があります。 これを行う必要があるのはサブスクリプションごとに 1 回だけです。 その後、イメージからプログラムを使って VM をデプロイするたびに、*購入プラン*のパラメーターを指定する必要があります。

以下のセクションでは、その方法について説明します。

* Marketplace イメージに追加のライセンス条件があるかどうかを調べる 
* プログラムで契約条件に同意する
* プログラムを使って VM を展開するときに、購入プランのパラメーターを入力する

