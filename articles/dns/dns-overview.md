<properties 
   pageTitle="Azure DNS の概要 | Microsoft Azure" 
   description="Microsoft Azure の Azure DNS ホスティング サービスの概要と Microsoft Azure でのドメインのホストの開始" 
   services="dns" 
   documentationCenter="na" 
   authors="joaoma" 
   manager="adinah" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="02/09/2016"
   ms.author="joaoma"/>

# Azure DNS の概要

インターネット上のどの Web サイトやサービスの背後にも IP アドレスがあります。たとえば、www.microsoft.com では IP アドレス 134.170.185.46 が使用されています。ドメイン ネーム システム (DNS) は、Web サイトまたはサービスの名前をその IP アドレスに変換する (または解決する) 役割を担います。

Azure DNS は、DNS ドメインのホスティング サービスであり、Microsoft Azure インフラストラクチャを使用した名前解決を提供します。Azure でドメインをホストすることで、その他の Azure サービスと同じ資格情報、API、ツール、課金情報を使用して DNS レコードを管理できます。

Azure DNS 内の DNS ドメインは、DNS ネーム サーバーの Azure のグローバル ネットワーク上でホストされます。エニーキャスト ネットワークが使用されるため、各 DNS クエリには、使用できる最も近い DNS サーバーが応答します。これによって、ドメインには高速なパフォーマンスと高可用性の両方が提供されます。

このサービスは、Azure リソース マネージャー (ARM) に基づいています。そのため、ロールに基づくアクセス制御、監査ログ、リソース ロックなどの ARM 機能を利用できます。

ドメインとレコードは、Azure ポータル、Azure PowerShell コマンドレット、およびクロス プラットフォームの Azure CLI を使用して管理できます。DNS の自動管理を必要とするアプリケーションは、REST API および SDK を使用してサービスと統合できます。

Azure DNS では、現在、ドメイン名の購入はサポートされていません。ドメインを購入するには、サード パーティのドメイン名レジストラーを使用してください。通常、これには、少額の年間料金がかかります。これらのドメインは、DNS レコードの管理用に Azure DNS でホストすることができます。詳細については、[Azure DNS へのドメインの委任](dns-domain-delegation.md)に関するページを参照してください。


## 次のステップ

[DNS ゾーン作成の概要](dns-getstarted-create-dnszone.md)

[.NET SDK を使用した Azure の操作の自動化](dns-sdk.md)

[Azure DNS REST API リファレンス](https://msdn.microsoft.com/library/azure/mt163862.aspx)




 

<!---HONumber=AcomDC_0309_2016-->