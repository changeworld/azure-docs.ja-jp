---
title: チュートリアル:Azure FXT Edge Filer クラスターでネットワークを構成する
description: Azure FXT Edge Filer クラスターを作成した後にネットワーク設定をカスタマイズする方法
author: femila
ms.author: femila
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 10/07/2021
ms.openlocfilehash: a51f6654c8e82a46c1a31f2dd030cd2d5af754cd
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132347306"
---
# <a name="tutorial-configure-the-clusters-network-settings"></a>チュートリアル:クラスターのネットワーク設定を構成する

新しく作成した Azure FXT Edge Filer クラスターを使用する前に、いくつかのネットワーク設定を確認し、実際のワークフローに合わせてカスタマイズする必要があります。

このチュートリアルでは、新しいクラスターのために調整することが必要な場合があるネットワーク設定について説明します。

学習内容:

> [!div class="checklist"]
>
> * クラスターの作成後に更新が必要な可能性のあるネットワーク設定
> * AD サーバーまたは DNS サーバーが必要な Azure FXT Edge Filer のユース ケース
> * FXT クラスターへのクライアント要求を自動的に負荷分散するようラウンド ロビン DNS (RRDNS) を構成する方法

これらの手順を完了するまでの時間は、ご使用のシステムに必要な構成の変更の数によって異なります。

* このチュートリアルを最後まで読み、いくつかの設定を確認する必要があるだけの場合は、10 分から 15 分かかります。
* ラウンド ロビン DNS を構成する必要がある場合、そのタスクに 1 時間以上かかることがあります。

## <a name="adjust-network-settings"></a>ネットワーク設定を調整する

ネットワーク関連のいくつかのタスクは、新しい Azure FXT Edge Filer クラスターの設定の一部です。 この一覧を確認し、ご使用のシステムにどれを適用するかを決定します。

クラスターのネットワーク設定の詳細については、クラスター構成ガイドの[ネットワーク サービスの構成](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html)に関する項目をお読みください。

* クライアント側ネットワークに対してラウンド ロビン DNS を構成する (省略可能)

  [FXT Edge Filer クラスター用の DNS の構成](#configure-dns-for-load-balancing)に関するセクションの説明に従って DNS システムを構成して、クラスターのトラフィックを負荷分散します。

* NTP 設定を確認する

* Active Directory およびユーザー名とグループ名のダウンロードを構成する (必要な場合)

  ご使用のネットワーク ホストが Active Directory または他の種類の外部ディレクトリ サービスを使用している場合、クラスターのディレクトリ サービスの構成を変更して、クラスターがユーザー名とグループ情報をダウンロードする方法を設定する必要があります。 詳細については、クラスター構成ガイドの **クラスター** > **ディレクトリ サービス** に関する項目をお読みください。

  SMB サポートが必要な場合、AD サーバーが必要です。 SMB の設定を開始する前に AD を構成します。

* VLAN を定義する (省略可能)
  
  ご自分のクラスターの VServer とグローバル名前空間を定義する前に、必要な追加の VLAN を構成します。 詳細については、クラスター構成ガイドの [VLAN の操作](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html#vlan-overview)に関する項目をお読みください。

* プロキシ サーバーを構成する (必要な場合)

  ご自分のクラスターで外部アドレスに到達するためにプロキシ サーバーを使用する場合は、これらの手順に従って設定します。

  1. **[プロキシの構成]** 設定ページで、プロキシ サーバーを定義します。
  1. **[クラスター]**  >  **[全般設定]** ページまたは **[Core Filer Details]\(コア ファイラーの詳細\)** ページを使用して、プロキシ サーバーの構成を適用します。
  
  詳細については、クラスター構成ガイドの [Web プロキシの使用](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/proxy_overview.html)に関する項目をお読みください。

* クラスターで使用するために[暗号化証明書](#encryption-certificates)をアップロードする (省略可能)

### <a name="encryption-certificates"></a>暗号化証明書

FXT Edge Filer クラスターでは、これらの機能に対して X.509 証明書を使用します。

* クラスターの管理トラフィックを暗号化する

* クライアントの代理としてサードパーティの KMIP サーバーに対して認証する

* クラウド プロバイダーのサーバー証明書を検証する

クラスターに証明書をアップロードする必要がある場合、 **[クラスター]**  >  **[証明書]** 設定ページを使用します。 詳細については、クラスター構成ガイドの[クラスター > 証明書](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_certificates.html)のページをお読みください。

クラスター管理の通信を暗号化するには、 **[クラスター]**  >  **[全般設定]** 設定ページを使用して、管理向けの TLS に使用する証明書を選択します。

管理コンピューターがクラスターの[暗号化標準](supported-ciphers.md)を満たしていることを確認してください。

> [!Note]
> クラウド サービスのアクセス キーは、 **[Cloud Credentials]\(クラウド資格情報\)** 構成ページを使用して格納します。 上の「[コア ファイラーを追加する](add-storage.md#add-a-core-filer)」セクションでは例を示しています。詳細については、クラスター構成ガイドの[クラウド資格情報](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_credentials.html)に関するセクションをお読みください。

## <a name="configure-dns-for-load-balancing"></a>負荷分散のために DNS を構成する

このセクションでは、ご自分の FXT Edge Filer クラスター内のすべてのクライアント側 IP アドレスに対してクライアントの負荷を分散するためのラウンド ロビン DNS (RRDNS) システムの構成の基礎について説明します。

### <a name="decide-whether-or-not-to-use-dns"></a>DNS を使用するかどうかを決定する

常に負荷分散をお勧めしますが、必ずしも DNS を使用する必要はありません。 たとえば、クライアント ワークフローの種類によっては、クラスターをマウントするときに、スクリプトを使用して、クライアントに対して均等にクラスター IP アドレスを割り当てることが理にかなっている場合があります。 「[クラスターのマウント](mount-clients.md)」に、いくつかの方法が記載されています。

DNS サーバーを使用するかどうかを決定する際は、以下の点に注意してください。

* ご使用のシステムにアクセスするのが NFS クライアントのみの場合、DNS は必要ありません。 数値 IP アドレスを使用して、すべてのネットワーク アドレスを指定できます。

* システムが SMB (CIFS) アクセスをサポートする場合、Active Directory サーバーの DNS ドメインを指定する必要があるため DNS が必要になります。

* Kerberos 認証を使用する場合、DNS が必要です。

### <a name="round-robin-dns-configuration-details"></a>ラウンド ロビン DNS の構成の詳細

ラウンドロビン DNS (RRDNS) システムでは、クライアント要求を複数のアドレスにわたって自動的にルーティングします。

このシステムを設定するには、DNS サーバーの構成ファイルをカスタマイズする必要があります。これにより、FXT Edge Filer のメイン ドメイン アドレスへのマウント要求を受け取ると、クラスターのすべてのマウント ポイント全体にトラフィックが割り当てられます。 クライアントでは、サーバー引数としてそのドメイン名を使用してクラスターがマウントされ、次のマウント IP に自動的にルーティングされます。

RRDNS を構成する手順は、主に次の 2 つです。

1. DNS サーバーの ``named.conf`` ファイルを変更して、FXT クラスターへのクエリの循環の順序を設定します。 このオプションを選択すると、サーバーで使用可能なすべての IP 値が順番に表示されます。 次のようなステートメントを追加します。

   ```bash
   options {
       rrset-order {
           class IN A name "fxt.contoso.com" order cyclic;
       };
   };
   ```

1. 次の例のように、使用可能な IP アドレスごとに A レコードとポインター (PTR) レコードを構成します。

   これらの ``nsupdate`` コマンドは、ドメイン名 fxt.contoso.com と 3 つのマウント アドレス (10.0.0.10、10.0.0.11、および 10.0.0.12) を使用して、Azure FXT Edge Filer クラスターに対して DNS を正しく構成する例を示します。

   ```bash
   update add fxt.contoso.com. 86400 A 10.0.0.10
   update add fxt.contoso.com. 86400 A 10.0.0.11
   update add fxt.contoso.com. 86400 A 10.0.0.12
   update add client-IP-10.contoso.com. 86400 A 10.0.0.10
   update add client-IP-11.contoso.com. 86400 A 10.0.0.11
   update add client-IP-12.contoso.com. 86400 A 10.0.0.12
   update add 10.0.0.10.in-addr.arpa. 86400 PTR client-IP-10.contoso.com
   update add 11.0.0.10.in-addr.arpa. 86400 PTR client-IP-11.contoso.com
   update add 12.0.0.10.in-addr.arpa. 86400 PTR client-IP-12.contoso.com
   ```

   これらのコマンドによって、クラスターのマウント アドレスごとに A レコードが作成されるほか、逆引き DNS チェックを適切にサポートするようにポインター レコードが設定されます。

   次の図は、この構成の基本的な構造を示しています。

   :::image type="complex" source="media/round-robin-dns-diagram-fxt.png" alt-text="クライアントのマウント ポイントの DNS 構成を示す図。":::
   <この図は 3 つのカテゴリの要素間の接続を示しています。1 つの FXT Edge Filer クラスター ドメイン名 (左側)、3 つの IP アドレス (中央の列)、3 つの内部使用逆引き DNS クライアント インターフェイス (右側の列) です。 左側の "fxt.contoso.com" というラベルが付いた 1 つの楕円が、IP アドレス: 10.0.0.10、10.0.0.11、10.0.0.12 のラベルが付いた 3 つの楕円を指し示す矢印で結ばれています。 fxt.contoso.com の楕円から 3 つの IP の楕円への矢印には、"A" というラベルが付いています。 各 IP アドレスの楕円は、クライアント インターフェイスのラベルが付いた楕円と 2 つの矢印で結ばれています。IP 10.0.0.10 の楕円は "client-IP-10.contoso.com" と、IP 10.0.0.11 の楕円は "client-IP-11.contoso.com" と、IP 10.0.0.12 の楕円は "client-IP-11.contoso.com" とつながっています。 IP アドレスの楕円とクライアント インターフェイスの楕円の間をつないでいるのは、2 つの矢印です。一方の矢印は "PTR" のラベルが付いていて、IP アドレスの楕円からクライアント インターフェイスの楕円を指しており、もう一方の矢印は "A" のラベルが付いていて、クライアント インターフェイスの楕円から IP アドレスの楕円を指しています。> :::image-end:::

RRDNS システムが構成されたら、それを使用してマウント コマンドで FXT クラスター アドレスを解決するように、クライアント コンピューターに指示します。

### <a name="enable-dns-in-the-cluster"></a>クラスター内で DNS を有効にする

クラスターが使用する DNS サーバーを、 **[クラスター]**  >  **[Administrative Network]\(管理ネットワーク\)** 設定ページで指定します。 このページでは、以下の内容を設定します。

* DNS サーバー アドレス
* DNS ドメイン名
* DNS 検索ドメイン

詳細については、クラスター構成ガイドの [DNS 設定](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns)に関する項目をお読みください。

## <a name="next-steps"></a>次のステップ

これは、Azure FXT Edge Filer クラスターの基本的な構成の最後の手順です。

* システムの LED およびその他のインジケーターについては、[ハードウェアの状態の監視](monitor.md)に関するページを参照してください。
* クライアントが FXT Edge Filer クラスターをマウントする方法の詳細については、「[クラスターのマウント](mount-clients.md)」を参照してください。
* FXT Edge Filer クラスターの運用と管理の詳細については、[クラスター構成ガイド](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html)を参照してください。
