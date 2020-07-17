---
author: vhorne
ms.service: dns
ms.topic: include
ms.date: 11/25/2018
ms.author: victorh
ms.openlocfilehash: 74031a8dbc9b64d6a09533789eed1296ff334d47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "67181836"
---
DNS ゾーンは、特定のドメインの DNS レコードをホストするために使用されます。 Azure DNS でドメインのホストを開始するには、そのドメイン名用に DNS ゾーンを作成する必要があります。 ドメインの DNS レコードはすべて、この DNS ゾーン内に作成されます。

たとえば、ドメイン "contoso.com" に、"mail.contoso.com" (メール サーバー用) や "www.contoso.com" (Web サイト用) など、複数の DNS レコードを含めることができます。

Azure DNS に DNS ゾーンを作成する場合:

* ゾーンの名前がリソース グループ内で一意であること、および作成するゾーンがまだ存在していないことが必要です。 それ以外の場合は、操作が失敗します。
* 同じゾーン名は、別のリソース グループまたは別の Azure サブスクリプション内であれば再利用できます。
* 複数のゾーンで同じ名前を共有できますが、各インスタンスには異なるネーム サーバー アドレスが割り当てられます。 ドメイン名レジストラーで構成できるアドレスのセットは 1 つだけです。

> [!NOTE]
> 自社で所有していないドメイン名でも、Azure DNS 内にそのドメイン名で DNS ゾーンを作成できます。 ただし、ドメイン名レジストラーでドメイン名の正しい名前サーバーとして Azure DNS ネーム サーバーを構成する場合は、ドメインを所有する必要があります。
> 
> 詳細については、「[Azure DNS へのドメインの委任](../articles/dns/dns-domain-delegation.md)」を参照してください。