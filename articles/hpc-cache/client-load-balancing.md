---
title: Azure HPC Cache に対するクライアント接続の負荷分散
description: Azure HPC Cache 用のラウンドロビン負荷分散のために DNS サーバーを構成する方法
author: femila
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/20/2021
ms.author: femila
ms.openlocfilehash: d1346a6060fe1093d7dec37f6fbefb43bee800ab
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131019972"
---
# <a name="load-balance-hpc-cache-client-traffic"></a>HPC Cache のクライアント トラフィックの負荷分散

この記事では、Azure HPC Cache 上のすべてのマウント ポイントにクライアント トラフィックを分散するための基本的な方法について説明します。

すべての HPC Cache には異なる IP アドレスが 3 つ以上あり、スループットの値が大きいキャッシュには最大で 12 個存在する場合があります。 Azure HPC Cache を最大限に活用するには、すべての IP アドレスを使用することが重要です。

クライアント マウントの負荷分散には、次のようなさまざまなオプションがあります。

* 各クライアントに対して異なるマウント IP を手動で選択する
* クライアントのマウント スクリプトに IP アドレスのローテーションを含める
* 使用可能なすべてのアドレス (ラウンドロビン DNS) 間でクライアント要求を自動的にルーティングするように DNS システムを構成する

適切な負荷分散システムは、ワークフローの複雑さ、キャッシュ内の IP アドレスの数、その他の多数の要因に左右されます。 どのアプローチが最も適しているかを判断するのにヘルプが必要な場合は、ご担当の Azure アドバイザーに相談してください。

## <a name="assign-ip-addresses-manually"></a>IP アドレスを手動で割り当てる

キャッシュのマウント IP アドレスは、Azure portal のキャッシュの **[概要]** ページと **[Mount instructions]\(マウント手順\)** ページ、および Azure CLI または PowerShell を使用してキャッシュを作成したときに出力される成功メッセージに表示されます。

**[Mount instructions]\(マウント手順\)** ページを使用して、クライアントごとにカスタマイズされたマウント コマンドを生成できます。 複数のコマンドを作成するときは、 **[キャッシュのマウント アドレス]** のすべての値を選択します。

詳細については、「[Azure HPC Cache をマウントする](hpc-cache-mount.md)」を参照してください。

## <a name="use-scripted-load-balancing"></a>スクリプト化された負荷分散を使用する

使用可能な IP アドレス間でクライアント マウントをプログラムでローテーションするには、いくつかの方法があります。 次に 2 つの例を示します。

### <a name="mount-command-script-cksum-example"></a>マウント コマンド スクリプト cksum の例

こちらのマウント コマンド例では、ハッシュ関数 ``cksum`` と クライアント ホスト名を使用して、HPC Cache 上の使用可能なすべての IP アドレス間に、クライアント接続を自動的に分散します。 すべてのクライアント マシンに一意のホスト名がある場合は、各クライアントでこのコマンドを実行して、使用可能なすべてのマウント ポイントが使用されていることを確認できます。

```bash
mount -o hard,proto=tcp,mountproto=tcp,retry=30 $(X=(10.0.0.{1..3});echo ${X[$(($(hostname|cksum|cut -f 1 -d ' ')%3))]}):/${NAMESPACE} /mnt
```

この例をご自身のワークフローで使用するには、次の項目をカスタマイズします。

* ```X=``` 式には、スペースで区切られたキャッシュのすべてのマウント アドレスの一覧を、順番に並べ替えて使用します。

  式 ``(X=(10.0.0.{7..9})`` では、このマウント アドレスのセット {10.0.0.7, 10.0.0.8, 10.0.0.9} として、変数 X を設定します。 キャッシュのベース IP アドレスと、キャッシュの [概要] ページに表示される正確なアドレスを使用します。 アドレスが連続していない場合は、それらを数字の大きい順に一覧表示します。

* ```%3``` の項目には、キャッシュに存在する実際のマウント IP アドレスの数 (通常は 3、6、9、または 12) を使用します。

  たとえば、キャッシュで 9 つのクライアント マウント IP アドレスが公開されている場合は、``%9`` を使用します。

* 式 ``${NAMESPACE}`` では、クライアントによってアクセスされるストレージ ターゲットの名前空間のパスを使用します。

  定義した変数 (この例では *NAMESPACE*) を使用するか、代わりにリテラル値を渡すことができます。
  
  このセクションの最後にあるコマンドの例では、名前空間のパス ``/blob-target-1`` にリテラル値を使用します。

* クライアント マシンでカスタム ローカル パスを使用する場合は、値 ``/mnt`` を目的のパスに変更します。

設定されたクライアント マウント コマンドの例を次に示します。

```bash
mount -o hard,proto=tcp,mountproto=tcp,retry=30 $(X=(10.7.0.{1..3});echo ${X[$(($(hostname|cksum|cut -f 1 -d ' ')%3))]}):/blob-target-1 /hpc-cache/blob1 
```

### <a name="round-robin-function-example"></a>ラウンド ロビン関数の例

このコード例では、HPC Cache で使用可能なすべての IP アドレスにクライアントを分散させるためのランダム化要素として、クライアントの IP アドレスを使用しています。

```bash
function mount_round_robin() {

  # to ensure the clients are spread out somewhat evenly the default
  # mount point is based on this client's IP octet4 % number of HPC cache mount IPs.

  declare -a MOUNT_IPS="($(echo ${NFS_IP_CSV} | sed "s/,/ /g"))"
  HASH=$(hostname | cksum | cut -f 1 -d ' ')
  DEFAULT_MOUNT_INDEX=$((${HASH} % ${#MOUNT_IPS[@]}))
  ROUND_ROBIN_IP=${MOUNT_IPS[${DEFAULT_MOUNT_INDEX}]}

  DEFAULT_MOUNT_POINT="${BASE_DIR}/default"

  # no need to write again if it is already there
  if ! grep --quiet "${DEFAULT_MOUNT_POINT}" /etc/fstab; then
      echo "${ROUND_ROBIN_IP}:${NFS_PATH} ${DEFAULT_MOUNT_POINT} nfs hard,proto=tcp,mountproto=tcp,retry=30 0 0" >> /etc/fstab
      mkdir -p "${DEFAULT_MOUNT_POINT}"
      chown nfsnobody:nfsnobody "${DEFAULT_MOUNT_POINT}"
  fi
  if ! grep -qs "${DEFAULT_MOUNT_POINT} " /proc/mounts; then
      retrycmd_if_failure 12 20 mount "${DEFAULT_MOUNT_POINT}" || exit 1
  fi
}
```

## <a name="use-dns-load-balancing"></a>DNS 負荷分散を使用する

このセクションでは、Azure HPC Cache 上のすべてのマウント ポイントにクライアント接続を分散するように、DNS システムを構成する方法の基本について説明します。 この方法では、各クライアントで生成されるトラフィックの量は考慮されませんが、クライアントで 1 つまたは 2 つのみが使用されるのではなく、すべてのキャッシュのインターフェイスに均等に分散されるようにします。

このドキュメントには、クライアント用に Azure 環境内で DNS サーバーを設定して管理するための手順は含まれていません。

NFS プロトコルと IP アドレスを使用してクライアントをマウントするのに、DNS は必要ありません。 IP アドレスの代わりにドメイン名を使用してハードウェア NAS システムに接続する場合、またはワークフローに特定の高度なプロトコル設定が含まれている場合は、DNS が "*必要*" です。

クライアントにアドレスを分散するのに使用する DNS システムには、HPC Cache からアクセスする必要はありません。 キャッシュ自体にカスタム DNS システムを使用したほうがよい場合もありますが、そのシステムを構成することは、この種のクライアント ラウンドロビン システムを設定するよりもはるかに複雑です。 [*HPC Cache* の DNS サーバーをカスタム システムに変更する](configuration.md#set-a-custom-dns-configuration)ことを検討している場合は、Azure サポートにご相談ください。

### <a name="configure-round-robin-distribution-for-cache-mount-points"></a>キャッシュのマウント ポイント用のラウンドロビン分散を構成する

ラウンドロビン DNS (RRDNS) システムでは、クライアント要求を複数のアドレスにわたって自動的にルーティングします。

このシステムを設定するには、DNS サーバーの構成ファイルをカスタマイズする必要があります。これにより、HPC Cache のメイン ドメイン アドレスへのマウント要求を受け取ると、HPC Cache システムのすべてのマウント ポイントにわたってトラフィックが割り当てられます。 クライアントでは、サーバー引数としてそのドメイン名を使用して HPC Cache をマウントし、次のマウント IP に自動的にルーティングされます。

RRDNS を構成する手順は、主に次の 2 つです。

1. DNS サーバーの ``named.conf`` ファイルを変更して、HPC Cache へのクエリの循環の順序を設定します。 このオプションを選択すると、サーバーで使用可能なすべての IP 値が順番に表示されます。 次のようなステートメントを追加します。

   ```bash
   options {
       rrset-order {
           class IN A name "hpccache.contoso.com" order cyclic;
       };
   };
   ```

1. 次の例のように、使用可能な IP アドレスごとに A レコードとポインター (PTR) レコードを構成します。

   これらの ``nsupdate`` コマンドは、ドメイン名 hpccache.contoso.com と 3 つのマウント アドレス (10.0.0.10、10.0.0.11、および 10.0.0.12) を使用して、HPC Cache に対して DNS を正しく構成する例を示します。

   ```bash
   update add hpccache.contoso.com. 86400 A 10.0.0.10
   update add hpccache.contoso.com. 86400 A 10.0.0.11
   update add hpccache.contoso.com. 86400 A 10.0.0.12
   update add client-IP-10.contoso.com. 86400 A 10.0.0.10
   update add client-IP-11.contoso.com. 86400 A 10.0.0.11
   update add client-IP-12.contoso.com. 86400 A 10.0.0.12
   update add 10.0.0.10.in-addr.arpa. 86400 PTR client-IP-10.contoso.com
   update add 11.0.0.10.in-addr.arpa. 86400 PTR client-IP-11.contoso.com
   update add 12.0.0.10.in-addr.arpa. 86400 PTR client-IP-12.contoso.com
   ```

   これらのコマンドによって、HPC Cache のマウント アドレスごとに A レコードが作成されるほか、逆引き DNS チェックを適切にサポートするようにポインター レコードが設定されます。

   次の図は、この構成の基本的な構造を示しています。

   :::image type="complex" source="media/round-robin-dns-diagram-hpc.png" alt-text="クライアントのマウント ポイントの DNS 構成を示す図。":::
   <この図は 3 つのカテゴリの要素間の接続を示しています。1 つの HPC Cache ドメイン名 (左側)、3 つの IP アドレス (中央の列)、3 つの内部使用逆引き DNS クライアント インターフェイス (右側の列) です。 左側で "hpccache.contoso.com" というラベルが付いた 1 つの楕円が、IP アドレス: 10.0.0.10、10.0.0.11、10.0.0.12 のラベルが付いた 3 つの楕円を指し示す矢印で結ばれています。 hpccache.contoso.com の楕円から 3 つの IP の楕円への矢印には、"A" というラベルが付いています。 各 IP アドレスの楕円は、クライアント インターフェイスのラベルが付いた楕円と 2 つの矢印で結ばれています。IP 10.0.0.10 の楕円は "client-IP-10.contoso.com" と、IP 10.0.0.11 の楕円は "client-IP-11.contoso.com" と、IP 10.0.0.12 の楕円は "client-IP-11.contoso.com" とつながっています。 IP アドレスの楕円とクライアント インターフェイスの楕円の間をつないでいるのは、2 つの矢印です。一方の矢印は "PTR" のラベルが付いていて、IP アドレスの楕円からクライアント インターフェイスの楕円を指しており、もう一方の矢印は "A" のラベルが付いていて、クライアント インターフェイスの楕円から IP アドレスの楕円を指しています。> :::image-end:::

RRDNS システムが構成されたら、それを使用してマウント コマンドで HPC Cache アドレスを解決するように、クライアント コンピューターに指示します。

## <a name="next-steps"></a>次のステップ

* クライアントの負荷の分散に関してヘルプが必要な場合は、[サポートにお問い合わせ](hpc-cache-support-ticket.md)ください。
* キャッシュのストレージ ターゲットを移動する方法については、[新しい Azure Blob Storage へのデータの事前設定](hpc-cache-ingest.md)に関するページを参照してください。
