---
title: Azure DNS プライベート ゾーンの仮想ネットワーク リンク サブリソースとは
description: Azure DNS プライベート ゾーンの仮想ネットワーク リンク サブリソースの概要
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 04/26/2021
ms.author: rohink
ms.openlocfilehash: fa1729029d7576dac1dab2ea77cfe1c257441b42
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131500813"
---
# <a name="what-is-a-virtual-network-link"></a>仮想ネットワーク リンクとは

Azure でプライベート DNS ゾーンを作成したら、仮想ネットワークをそれにリンクする必要があります。 リンクされると、その仮想ネットワークでホストされている VM はプライベート DNS ゾーンにアクセスできるようになります。 すべてのプライベート DNS ゾーンには、仮想ネットワーク リンクの子リソースのコレクションがあります。 これらのリソースは、仮想ネットワークへの接続をそれぞれ表しています。 仮想ネットワークは、登録または解決仮想ネットワークとしてプライベート DNS ゾーンにリンクできます。

## <a name="registration-virtual-network"></a>登録仮想ネットワーク

プライベート DNS ゾーンと仮想ネットワークの間に[リンクを作成](./private-dns-getstarted-portal.md#link-the-virtual-network)する場合を考えます。 [自動登録](./private-dns-autoregistration.md)を有効にするオプションがあります。 この設定を有効にすると、仮想ネットワークはプライベート DNS ゾーンの登録仮想ネットワークになります。 仮想ネットワークにデプロイするどの仮想マシンに対しても、DNS レコードは自動的に作成されます。 DNS レコードは、仮想ネットワークに既にデプロイされている仮想マシンに対しても作成されます。

仮想ネットワークの観点からは、プライベート DNS ゾーンはその仮想ネットワークの登録ゾーンになります。 プライベート DNS ゾーンには、複数の登録仮想ネットワークを含めることができます。 ただし、どの仮想ネットワークでも、それに関連付けることができる登録ゾーンは 1 つだけです。

## <a name="resolution-virtual-network"></a>解決仮想ネットワーク

自動登録を使用せずに、プライベート DNS ゾーンに仮想ネットワークをリンクすることを選択した場合。 仮想ネットワークは、解決仮想ネットワークとしてのみ扱われます。 この仮想ネットワークにデプロイされた仮想マシンに対する DNS レコードは、プライベート ゾーンに自動的に作成されません。 ただし、仮想ネットワークにデプロイされた仮想マシンでは、プライベート ゾーンにある DNS レコードを正常にクエリできます。 これらのレコードには、プライベート DNS ゾーンにリンクされた他の仮想ネットワークから手動作成および自動登録されたレコードが含まれます。

1 つのプライベート DNS ゾーンは複数の解決仮想ネットワークを持つことができ、仮想ネットワークには複数の解決ゾーンを関連付けることができます。

## <a name="limits"></a>制限

プライベート DNS ゾーンにリンクできる登録ネットワークと解決ネットワークの数を把握するには、「[Azure DNS の制限](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-dns-limits)」を参照してください。

## <a name="other-considerations"></a>その他の注意事項

* クラシック デプロイ モデルを使用してデプロイされた仮想ネットワークはサポートされていません。

* プライベート DNS ゾーンと仮想ネットワークの間に作成できるリンクは 1 つだけです。

* 1 つのプライベート DNS ゾーンの下にある各仮想ネットワーク リンクは、プライベート DNS ゾーンのコンテキスト内で一意の名前を持っている必要があります。 異なるプライベート DNS ゾーンには、同じ名前のリンクを含めることができます。

* 仮想ネットワーク リンクを作成したら、仮想ネットワーク リンク リソースの [リンクの状態] フィールドを確認します。 仮想ネットワークのサイズによっては、リンクが操作可能になって、リンクの状態が *[完了]* に変わるまでに数分かかる場合があります。

* 仮想ネットワークを削除すると、さまざまなプライベート DNS ゾーンにある、それに関連付けられたすべての仮想ネットワーク リンクと自動登録された DNS レコードが自動的に削除されます。

## <a name="next-steps"></a>次のステップ

* [Azure portal](./private-dns-getstarted-portal.md#link-the-virtual-network) を使用して仮想ネットワークをプライベート DNS ゾーンにリンクする方法について確認します。

* [Azure PowerShell](./private-dns-getstarted-powershell.md) または [Azure CLI](./private-dns-getstarted-cli.md) を使用して Azure DNS のプライベート ゾーンを作成する方法を確認します。

* [プライベート ゾーンのシナリオ](./private-dns-scenarios.md)に関しては、Azure DNS のプライベート ゾーンで実現できるいくつかの一般的なシナリオを参照してください。

* Azure DNS のプライベート ゾーンに関する一般的な質問と回答については、[プライベート DNS の FAQ](./dns-faq-private.yml) に関する記事をご覧ください。特定の種類の操作で期待できる固有の動作についても説明があります。
