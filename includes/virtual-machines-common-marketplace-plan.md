---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines-windows, virtual-machines-linux
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 09/28/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: f9974aae1e5996ffeaa6cde690a5e10ccba4cc32
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2018
ms.locfileid: "48019014"
---
## <a name="deploy-an-image-with-marketplace-terms"></a>Marketplace の契約条件でイメージを展開する

Azure Marketplace の特定の VM イメージには、プログラムで展開する前に同意する必要がある追加のライセンスと購入契約条件があります。  

このようなイメージから VM を展開するには、イメージの契約条件に同意し、プログラムによるデプロイを有効にする必要があります。 ご使用のサブスクリプションでこれを行う必要があるのは 1 回のみです。 また、イメージからプログラムを使って VM を展開するたびに、*購入プラン*のパラメーターを指定する必要があります。

以下のセクションでは、その方法について説明します。

* Marketplace イメージに追加のライセンス条件があるかどうかを調べる 
* プログラムで契約条件に同意する
* プログラムを使って VM を展開するときに、購入プランのパラメーターを入力する

