<properties 
   pageTitle="CLI を使用した DNS ゾーンの管理 | Microsoft Azure" 
   description="Azure CLI を使用して DNS ゾーンを管理できます。Azure DNS の DNS ゾーンを更新、削除、および作成する方法" 
   services="dns" 
   documentationCenter="na" 
   authors="cherylmc" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="05/09/2016"
   ms.author="cherylmc"/>

# CLI を使用して DNS ゾーンを管理する方法

> [AZURE.SELECTOR]
- [Azure CLI](dns-operations-dnszones-cli.md)
- [PowerShell](dns-operations-dnszones.md)


このガイドでは、クロス プラットフォームの Azure CLI を使用して、DNS ゾーンのリソースを管理する方法を説明します。

以下の手順では、Microsoft Azure CLI を使用します。Azure DNS コマンドを使用するために、必ず最新の Azure CLI (0.9.8 以降) に更新してください。コンピューターに現在インストールされている Azure CLI のバージョンを確認するには、「`azure -v`」と入力します。Windows 用、Linux 用、MAC 用の Azure CLI をインストールできます。詳しくは、「[Azure CLI のインストール](../xplat-cli-install.md)」をご覧ください。

Azure DNS は、Azure リソース マネージャー専用のサービスです。ASM API はありません。リソース マネージャー モードを使用するように Azure CLI が構成されていることを確認する必要があります。このような場合には、`azure config mode arm` コマンドを使用します。<BR> "*エラー: 'dns' は、Azure のコマンドではありません*" というメッセージが表示される場合は、多くの場合、リソース マネージャー モードではなく、ASM モードで Azure CLI を使用していることが原因です。

## 新しい DNS ゾーンの作成

ドメインをホストする新しい DNS ゾーンを作成する方法については、「[CLI を使用した Azure DNS ゾーンの作成](dns-getstarted-create-dnszone-cli.md)」を参照してください。

## DNS ゾーンの取得

DNS ゾーンを取得するには、`azure network dns zone show` を使用します。

	azure network dns zone show myresourcegroup contoso.com

この操作は、DNS ゾーンとその ID、レコード セットの数、タグを返します。


## DNS ゾーンの一覧表示

リソース グループ内の DNS ゾーンを取得するには、`azure network dns zone list` を使用します。

	azure network dns zone list myresourcegroup

## DNS ゾーンの更新

DNS ゾーンのリソースへの変更は、`azure network dns zone set` を使用して行うことができます。これによって、ゾーン内の DNS レコード セットが更新されることはありません (「[DNS レコードの管理方法](dns-operations-recordsets.md)」を参照)。この操作は、ゾーンのリソース自体のプロパティを更新するためだけに使用します。現時点では、この操作は、ゾーンのリソースの Azure リソース マネージャーの "タグ" に限定されています。詳細については、「[Etag とタグ](dns-getstarted-create-dnszone.md#tagetag)」を参照してください。

	azure network dns zone set myresourcegroup contoso.com -t prod=value2

## DNS ゾーンの削除

DNS ゾーンは、`azure network dns zone delete` を使用して削除できます。この操作では、オプションの *-q* スイッチが使用されます。これにより、DNS ゾーンを削除するかどうかの確認メッセージは表示されなくなります。
 
Azure DNS の DNS ゾーンを削除する前に、ゾーンの作成時に自動的にゾーンのルートに作成された NS レコードと SOA レコードを除くすべてのレコード セットを削除する必要があります。

	azure network dns zone delete myresourcegroup contoso.com 



## 次のステップ
DNS ゾーンを作成したら、[レコード セットとレコード](dns-getstarted-create-recordset-cli.md)を作成し、インターネット ドメインの名前解決を開始します。

<!---HONumber=AcomDC_0518_2016-->