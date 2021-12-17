---
title: Avere vFXT DNS - Azure
description: Azure の Avere vFXT を使用してラウンド ロビン負荷分散のために DNS サーバーを構成する
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: rohogue
ms.openlocfilehash: 26f85b4d3a2cc49e55d228fbd73ae7cb3b66bf5d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132337872"
---
# <a name="avere-cluster-dns-configuration"></a>Avere クラスターの DNS 構成

このセクションでは、Avere vFXT クラスターを負荷分散するための DNS システムの構成の基礎について説明します。

このドキュメントには、Azure 環境内で DNS サーバーを設定して管理するための手順は *含まれていません*。

Azure で vFXT クラスターを負荷分散するためにラウンドロビン DNS を使用する代わりに、クライアントをマウントするときに、手動による方法を使用して IP アドレスをクライアント間で均等に割り当てることを検討してください。 [Avere クラスターのマウント](avere-vfxt-mount-clients.md)に関する記事に、いくつかの方法が記載されています。

DNS サーバーを使用するかどうかを決定する際は、以下の点に注意してください。

* NFS クライアントのみがシステムにアクセスする場合、DNS を使用する必要はありません。数値 IP アドレスを使用して、すべてのネットワーク アドレスを指定できます。

* システムが SMB (CIFS) アクセスをサポートする場合、Active Directory サーバーの DNS ドメインを指定する必要があるため DNS が必要になります。

* Kerberos 認証を使用する場合、DNS が必要です。

## <a name="load-balancing"></a>負荷分散

全体的に負荷を分散するには、クライアントに接続する IP アドレスのラウンド ロビン負荷分散を使用するよう DNS ドメインを構成します。

## <a name="configuration-details"></a>構成の詳細

クライアントがクラスターにアクセスするとき、ラウンドロビン DNS (RRDNS) は使用可能なすべてのインターフェイスの間で要求を自動的に分散します。

このシステムを設定するには、DNS サーバーの構成ファイルをカスタマイズする必要があります。これにより、vFXT クラスターのメイン ドメイン アドレスへのマウント要求を受け取ると、vFXT クラスターのすべてのマウント ポイントにわたってトラフィックが割り当てられます。 クライアントでは、サーバー引数としてそのドメイン名を使用して vFXT クラスターがマウントされ、次のマウント IP に自動的にルーティングされます。

RRDNS を構成する手順は、主に次の 2 つです。

1. DNS サーバーの ``named.conf`` ファイルを変更して、vFXT クラスターへのクエリの循環の順序を設定します。 このオプションを選択すると、サーバーで使用可能なすべての IP 値が順番に表示されます。 次のようなステートメントを追加します。

   ```bash
   options {
       rrset-order {
           class IN A name "vfxt.contoso.com" order cyclic;
       };
   };
   ```

1. 次の例のように、使用可能な IP アドレスごとに A レコードとポインター (PTR) レコードを構成します。

   これらの ``nsupdate`` コマンドは、ドメイン名 vfxt.contoso.com と 3 つのマウント アドレス (10.0.0.10、10.0.0.11、および 10.0.0.12) を使用して、vFXT クラスターに対して DNS を正しく構成する例を示します。

   ```bash
   update add vfxt.contoso.com. 86400 A 10.0.0.10
   update add vfxt.contoso.com. 86400 A 10.0.0.11
   update add vfxt.contoso.com. 86400 A 10.0.0.12
   update add client-IP-10.contoso.com. 86400 A 10.0.0.10
   update add client-IP-11.contoso.com. 86400 A 10.0.0.11
   update add client-IP-12.contoso.com. 86400 A 10.0.0.12
   update add 10.0.0.10.in-addr.arpa. 86400 PTR client-IP-10.contoso.com
   update add 11.0.0.10.in-addr.arpa. 86400 PTR client-IP-11.contoso.com
   update add 12.0.0.10.in-addr.arpa. 86400 PTR client-IP-12.contoso.com
   ```

   これらのコマンドによって、クラスターのマウント アドレスごとに A レコードが作成されるほか、逆引き DNS チェックを適切にサポートするようにポインター レコードも設定されます。

   次の図は、この構成の基本的な構造を示しています。

   :::image type="complex" source="media/round-robin-dns-diagram-vfxt.png" alt-text="クライアントのマウント ポイントの DNS 構成を示す図。":::
   <この図は 3 つのカテゴリの要素間の接続を示しています。1 つの vFXT クラスター ドメイン名 (左側)、3 つの IP アドレス (中央の列)、3 つの内部使用逆引き DNS クライアント インターフェイス (右側の列) です。 左側で "vfxt.contoso.com" というラベルが付いた 1 つの楕円が、IP アドレス: 10.0.0.10、10.0.0.11、10.0.0.12 のラベルが付いた 3 つの楕円を指し示す矢印で結ばれています。 vfxt.contoso.com の楕円から 3 つの IP の楕円への矢印には、"A" というラベルが付いています。 各 IP アドレスの楕円は、クライアント インターフェイスのラベルが付いた楕円と 2 つの矢印で結ばれています。IP 10.0.0.10 の楕円は "client-IP-10.contoso.com" と、IP 10.0.0.11 の楕円は "client-IP-11.contoso.com" と、IP 10.0.0.12 の楕円は "client-IP-11.contoso.com" とつながっています。 IP アドレスの楕円とクライアント インターフェイスの楕円の間をつないでいるのは、2 つの矢印です。一方の矢印は "PTR" のラベルが付いていて、IP アドレスの楕円からクライアント インターフェイスの楕円を指しており、もう一方の矢印は "A" のラベルが付いていて、クライアント インターフェイスの楕円から IP アドレスの楕円を指しています。> :::image-end:::

RRDNS システムが構成されたら、それを使用してマウント コマンドでクラスター アドレスを解決するように、クライアント コンピューターに指示します。

## <a name="cluster-dns-settings"></a>クラスター DNS 設定

vFXT クラスターが使用する DNS サーバーを、 **[クラスター]**  >  **[Administrative Network]\(管理ネットワーク)** 設定ページで指定します。 このページでは、以下の内容を設定します。

* DNS サーバー アドレス
* DNS ドメイン名
* DNS 検索ドメイン

このページの使用についての詳細は、Avere クラスター構成ガイドの[DNS 設定](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns)に関するページをお読みください。
