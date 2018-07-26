---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines-windows, virtual-machines-linux
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 02/28/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 47ef014f31c628fed9d7b2b005e67e8138d3e7e9
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38944643"
---
## <a name="terminology"></a>用語集

Azure Marketplace イメージには、次の属性があります。

* **発行元** -イメージを作成した組織です。 例: Canonical、MicrosoftWindowsServer
* 
  **プラン** - 発行元によって作成された関連するイメージのグループ名です。 例: Ubuntu Server、WindowsServer
* 
  **SKU** - ディストリビューションのメジャー リリースなど、プランのインスタンス。 例: 16.04-LTS、2016-Datacenter
* **バージョン** - イメージの SKU のバージョン番号。 

プログラムで VM を配置した場合に Marketplace イメージを識別するには、これらの値をパラメーターとして個別に指定するか、イメージ *URN* を受け付けるツールを使用します。 URN は、*Publisher*:*Offer*:*Sku*:*Version* のように、これらの値を組み合わせてコロン (:) で区切ったものです。 URN では、バージョン番号を "latest" で置き換えることもできます。この場合、イメージの最新バージョンが選択されます。 

イメージの発行元が追加のライセンスや購入契約を提供する場合は、この契約条件に同意し、プログラムによるデプロイを有効にする必要があります。 プログラムで VM をデプロイする場合は、*購入プラン* パラメーターを指定する必要もあります。 [Marketplace の契約条件でイメージを展開する](#deploy-an-image-with-marketplace-terms)を参照してください。