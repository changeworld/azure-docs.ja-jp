<properties
   pageTitle="CLI を使用して DNS ゾーンのレコード セットとレコードを作成する | Microsoft Azure"
   description="Azure DNS のホスト レコードを作成する方法。CLI を使用したレコード セットとレコードの設定"
   services="dns"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/16/2016"
   ms.author="cherylmc"/>

# CLI を使用した DNS レコード セットとレコードの作成

> [AZURE.SELECTOR]
- [Azure ポータル](dns-getstarted-create-recordset-portal.md)
- [PowerShell](dns-getstarted-create-recordset.md)
- [Azure CLI](dns-getstarted-create-recordset-cli.md)


この記事では、CLI を使用してレコードとレコード セットを作成する手順を説明します。DNS ゾーンを作成した後は、ドメインの DNS レコードを追加する必要があります。そのために、まずは DNS レコードとレコード セットについて理解することが必要です。

[AZURE.INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

## レコード セットとレコードの作成

このセクションでは、レコード セットとレコードの作成方法を説明します。この例では、DNS ゾーン "contoso.com" に相対名 "www" を持つレコード セットを作成します。レコードの完全修飾名は、"www.contoso.com" になります。レコードの種類は "A" で、Time to Live (TTL) は 60 秒です。この手順を完了すると、空のレコード セットが作成されます。

ゾーンの頂点 (この場合は "contoso.com") にレコード セットを作成するには、レコード名 "@" (引用符を含みます) を使用します。これは、一般的な DNS の規則です。

### 1\.レコード セットの作成

レコード セットを作成するには、`azure network dns record-set create` を使用します。リソース グループ、ゾーン名、レコード セットの相対名、レコードの種類、TTL を指定します。`--ttl` パラメーターを定義しないと、値は既定で 4 (秒) になります。この手順を完了すると、空の "www" レコード セットが作成されます。

*使用法: network dns record-set create <resource-group> <dns-zone-name> <name> <type> <ttl>*

	azure network dns record-set create myresourcegroup  contoso.com  www A  60

### 2\.レコードの追加

新しく作成した "www" レコード セットを使用するには、レコードを追加する必要があります。`azure network dns record-set add-record` を使用して、レコードをレコード セットに追加します。

レコード セットにレコードを追加するためのパラメーターは、レコード セットの種類によって異なります。たとえば、"A" という種類のレコード セットを使用する場合、レコードの指定に使用できるのは、`-a <IPv4 address>` パラメーターのみです。

次のコマンドを使用して、IPv4 *A* レコードを "www" レコード セットに追加できます。

*使用法: network dns record-set add-record <resource-group> <dns-zone-name> <record-set-name> <type>*

	azure network dns record-set add-record myresourcegroup contoso.com  www A  -a 134.170.185.46

## その他のレコードの種類の例

次の例では、各種のレコード セットを作成する方法を示します。各レコード セットには、1 つのレコードが含まれています。

[AZURE.INCLUDE [dns-add-record-cli-include](../../includes/dns-add-record-cli-include.md)]

## 次のステップ

レコード セットとレコードを管理する方法については、「[CLI を使用した DNS レコードとレコード セットの管理](dns-operations-recordsets-portal.md)」を参照してください。

Azure DNS の詳細については、「[Azure DNS の概要](dns-overview.md)」を参照してください。

<!---HONumber=AcomDC_0817_2016-->