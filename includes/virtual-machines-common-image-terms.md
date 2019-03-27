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
ms.openlocfilehash: 19a78b772d2813c263017515f18da06fdb20aa70
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/26/2019
ms.locfileid: "55082267"
---
## <a name="terminology"></a>用語集

Azure Marketplace イメージには、次の属性があります。

* **[発行者]**: イメージを作成した組織。 次に例を示します。Canonical、MicrosoftWindowsServer
* **[プラン]**: 発行元によって作成された関連するイメージのグループ名です。 次に例を示します。Ubuntu Server、WindowsServer
* **SKU**:ディストリビューションのメジャー リリースなど、プランのインスタンス。 次に例を示します。18.04-LTS、2019-Datacenter
* **バージョン**:イメージの SKU のバージョン番号。 

プログラムで VM をデプロイした場合に Marketplace イメージを識別するには、これらの値をパラメーターとして個別に指定します。 一部のツールはイメージ *URN* を受け入れます。これは、次のように、これらの値を組み合わせてコロン (:) で区切ったものです:*Publisher*:*Offer*:*Sku*:*Version*。 URN では、バージョン番号を "latest" で置き換えることもできます。この場合、イメージの最新バージョンが選択されます。 

イメージの発行元が追加のライセンスや購入契約を提供する場合は、この契約条件に同意し、プログラムによるデプロイを有効にする必要があります。 プログラムで VM をデプロイする場合は、*購入プラン* パラメーターを指定する必要もあります。 [Marketplace の契約条件でイメージを展開する](#deploy-an-image-with-marketplace-terms)を参照してください。