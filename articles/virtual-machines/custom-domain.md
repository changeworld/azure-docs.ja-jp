---
title: カスタム ドメインを作成して使用する
description: Azure 内の仮想マシンにカスタム ドメインを接続します。
author: mimckitt
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/01/2021
ms.author: jamesser
ms.reviewer: cynthn
ms.openlocfilehash: c4797420904b6dc03550f658c2aa950a4de99c9c
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2021
ms.locfileid: "107250928"
---
# <a name="add-custom-domain-to-azure-vm-or-resource"></a>Azure VM またはリソースにカスタム ドメインを追加する

Azure では、ご利用の VM またはリソースにカスタム ドメインを接続する方法が複数あります。 パブリック IP (Virtual Machine、Load Balancer、Application Gateway) を持つリソースの場合、最も簡単な方法は、対応するドメイン レジストラーに A レコード セットを作成することです。 

## <a name="prerequisites"></a>前提条件 
- Web サーバーが実行されている VM が必要です。 [クイックスタート](./linux/quick-create-cli.md)を使用して、VM を作成し、NGINX を追加することができます。

- VM には、Web からアクセスできる必要があります (ポート 80 または 443 を開く)。 デプロイのセキュリティ保護を強化するために、最初に、ご利用の VM をロード バランサーまたは Application Gateway の内側に配置します。 詳細については、[クイックスタート: ロード バランサー](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal?tabs=option-1-create-load-balancer-standard)に関するページを参照してください。

- 既存のドメインと DNS 設定へのアクセス権が必要です。 詳細については、[Azure App Service のカスタム ドメインを購入](../app-service/manage-custom-dns-buy-domain.md)に関するページをご覧ください。


## <a name="add-custom-domain-to-vm-public-ip-address"></a>VM のパブリック IP アドレスにカスタム ドメインを追加する

Azure portal で仮想マシンを作成すると、仮想マシン用のパブリック IP リソースが自動的に作成されます。 ご利用のパブリック IP アドレスは、VM の [概要] ページに表示されます。 
 
:::image type="content" source="media/custom-domain/essentials.png" alt-text="[VM の概要] ページの [要点] セクションにパブリック IP アドレスが表示されている。":::

IP アドレスを選択すると、それに関する詳細情報が表示されます。 **[IP 割り当て]** が **[静的]** に設定されていることを確認します。 VM またはリソースが再起動またはシャットダウンされても、静的 IP アドレスは変わりません。

:::image type="content" source="media/custom-domain/ip-config.png" alt-text="IP アドレスが静的かどうかを確認できるようにパブリック IP 構成が表示されている。":::

IP アドレスが静的でない場合は、FQDN を作成する必要があります。 

1. ポータルで VM を選択します。 
1. 左側のメニューで **[プロパティ]** を選択します。
1. **[パブリック IP アドレス/DNS 名ラベル]** で、使用する IP アドレスを選択します。
2. **[DNS 名ラベル]** の下に、使用するプレフィックスを入力します。
3. ページの最上部で **[保存]** を選択します。
4. 左側のメニューの **[概要]** を選択して、VM の概要ブレードに戻ります。
5. *[DNS 名]* が正しく表示されていることを確認します。 

ブラウザーを開き、自分の IP アドレスまたは FQDN を入力し、ご利用の VM 上で実行されている Web コンテンツが表示されていることを確認します。
 
自分の静的 IP または FQDN を確認したら、ご利用のドメイン プロバイダーにアクセスし、[DNS の設定] に移動します。

そこに移動したら、自分のパブリック IP アドレスまたは FQDN をポイントする "*A レコード*" を追加します。 たとえば、GoDaddy ドメイン レジストラーの場合の手順は次のとおりです。
1. サインインし、使用するカスタム ドメインを選択します。
2. **[Domains]\(ドメイン\)** セクションで **[Manage All]\(すべてを管理\)** を選択し、次に **[DNS | Manage Zones]\(DNS | ゾーンを管理\)** を選択します。
3. **[Domain Name]\(ドメイン名\)** にカスタム ドメインを入力し、 **[Search]\(検索\)** を選択します。
4. [DNS Management]\(DNS 管理\) ページで [Add]\(追加\) を選択し、[Type]\(タイプ\) リストで [A] を選択します。
5. A エントリのフィールドに入力します。
    - タイプ: **[A]** を選択したままにします。
    - ホスト: 「 **@** 」を入力します。
    - ポイント先: ご利用の VM のパブリック IP アドレスまたは FQDN を入力します。 
    - TTL: [one Hour]\(1 時間\) を選択したままにします。
6. **[保存]** を選択します。

A レコード エントリが DNS レコード テーブルに追加されます。
 
レコードが作成されると、DNS の伝達には通常 1 時間ほどかかりますが、最大で 48 時間かかることもあります。 


 
## <a name="next-steps"></a>次のステップ
[Application Gateway での TLS 終端とエンド ツー エンド TLS の概要](../application-gateway/ssl-overview.md)。

 
