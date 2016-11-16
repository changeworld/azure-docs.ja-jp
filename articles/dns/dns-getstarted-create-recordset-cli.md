---
title: "CLI を使用して DNS ゾーンのレコード セットとレコードを作成する | Microsoft Docs"
description: "Azure DNS のホスト レコードを作成する方法。CLI を使用したレコード セットとレコードの設定"
services: dns
documentationcenter: na
author: sdwheeler
manager: carmonm
editor: 
ms.assetid: 02b897d3-e83b-4257-b96d-5c29aa59e843
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: sewhee
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 40accee35eca32eefd4afc2315c70d51e0edcdcd


---
# <a name="create-dns-record-sets-and-records-by-using-cli"></a>CLI を使用した DNS レコード セットとレコードの作成
> [!div class="op_single_selector"]
> * [Azure ポータル](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [Azure CLI](dns-getstarted-create-recordset-cli.md)
> 
> 

この記事では、CLI を使用してレコードとレコード セットを作成する手順を説明します。 DNS ゾーンを作成した後は、ドメインの DNS レコードを追加する必要があります。 そのために、まずは DNS レコードとレコード セットについて理解することが必要です。

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

## <a name="create-a-record-set-and-record"></a>レコード セットとレコードの作成
このセクションでは、レコード セットとレコードの作成方法を説明します。 この例では、DNS ゾーン "contoso.com" に相対名 "www" を持つレコード セットを作成します。 レコードの完全修飾名は、"www.contoso.com" になります。 レコードの種類は "A" で、Time to Live (TTL) は 60 秒です。 この手順を完了すると、空のレコード セットが作成されます。

ゾーンの頂点 (この例では "contoso.com") にレコード セットを作成するには、レコード名 "@", (引用符を含みます) を使用します。 これは、一般的な DNS の規則です。

### <a name="1-create-a-record-set"></a>1.レコード セットの作成
レコード セットを作成するには、`azure network dns record-set create` を使用します。 リソース グループ、ゾーン名、レコード セットの相対名、レコードの種類、TTL を指定します。 `--ttl` パラメーターを定義しないと、値は既定で 4 (秒) になります。 この手順を完了すると、空の "www" レコード セットが作成されます。

*使用法: network dns record-set create <resource-group> <dns-zone-name> <name> <type> <ttl>*

    azure network dns record-set create myresourcegroup  contoso.com  www A  60

### <a name="2-add-records"></a>2.レコードの追加
新しく作成した "www" レコード セットを使用するには、レコードを追加する必要があります。 `azure network dns record-set add-record`を使用して、レコードをレコード セットに追加します。

レコード セットにレコードを追加するためのパラメーターは、レコード セットの種類によって異なります。 たとえば、"A" という種類のレコード セットを使用する場合、レコードの指定に使用できるのは、 `-a <IPv4 address>`パラメーターのみです。

次のコマンドを使用して、IPv4 *A* レコードを "www" レコード セットに追加できます。

*使用法: network dns record-set add-record <resource-group> <dns-zone-name> <record-set-name> <type>*

    azure network dns record-set add-record myresourcegroup contoso.com  www A  -a 134.170.185.46

## <a name="additional-record-type-examples"></a>その他のレコードの種類の例
次の例では、各種のレコード セットを作成する方法を示します。 各レコード セットには、1 つのレコードが含まれています。

[!INCLUDE [dns-add-record-cli-include](../../includes/dns-add-record-cli-include.md)]

## <a name="next-steps"></a>次のステップ
レコード セットとレコードを管理する方法については、「 [CLI を使用した DNS レコードとレコード セットの管理](dns-operations-recordsets-portal.md)」を参照してください。

Azure DNS の詳細については、「 [Azure DNS の概要](dns-overview.md)」を参照してください。




<!--HONumber=Nov16_HO2-->


