---
title: Azure DNS で DNS レコード セットとレコードを管理する
description: Azure DNS には、ドメインをホストしている場合に、DNS レコード セットとレコードを管理する機能が用意されています。
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 04/28/2021
ms.author: rohink
ms.openlocfilehash: 8ac76671dc16fb51cea35154cd7862ad1dee66f6
ms.sourcegitcommit: 49bd8e68bd1aff789766c24b91f957f6b4bf5a9b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/29/2021
ms.locfileid: "108226971"
---
# <a name="manage-dns-records-and-record-sets-by-using-the-azure-portal"></a>Azure ポータルを使用した DNS レコードとレコード セットの管理

この記事では、Azure ポータルを使用して DNS ゾーンのレコード セットとレコードを管理する方法について説明します。

DNS レコード セットと個々の DNS レコードの違いを理解することは重要です。 レコード セットとは、1 つのゾーン内にある同じ名前、同じ種類のレコードのコレクションです。 詳細については、「 [Azure ポータルを使用した DNS レコード セットとレコードの作成](./dns-getstarted-portal.md)」を参照してください。

## <a name="create-a-new-record-set-and-record"></a>新しいレコード セットとレコードを作成する

Azure ポータルでレコード セットを作成する方法については、 [Azure ポータルを使用した DNS レコードの作成に関するページ](./dns-getstarted-portal.md)を参照してください。

## <a name="view-a-record-set"></a>レコード セットを表示する

1. Azure portal で **[DNS ゾーン]** の概要ページに移動します。

1. レコード セットを検索して選択すると、レコード セットのプロパティが開きます。

    :::image type="content" source="./media/dns-operations-recordsets-portal/overview.png" alt-text="contosotest.com ゾーンの概要ページのスクリーンショット。":::

## <a name="add-a-new-record-to-a-record-set"></a>レコード セットに新しいレコードを追加する

すべてのレコード セットに対して、最大 20 個のレコードを追加できます。 レコード セットには、同一の 2 つのレコードを含めることはできません。 空のレコード セット (レコードが 0 個) を作成することはできますが、Azure DNS ネーム サーバーには表示されません。 レコード セットの種類が CNAME の場合、そこに含めることができるレコードは 1 つまでです。

1. DNS ゾーンの **[レコード セットのプロパティ]** ページで、レコードを追加するレコード セットをクリックします。

    :::image type="content" source="./media/dns-operations-recordsets-portal/select-record.png" alt-text="www レコード セットを選択するスクリーンショット。":::

1. 各フィールドに入力して、レコード セットのプロパティを指定します。

    :::image type="content" source="./media/dns-operations-recordsets-portal/record-page.png" alt-text="レコードの追加ページのスクリーンショット。":::

1. ページの上部にある **[保存]** をクリックして設定を保存します。 その後、ページを閉じます。

レコードが保存されると、 **[DNS ゾーン]** ページの値にはレコードの追加が反映されます。

## <a name="update-a-record"></a>レコードを更新する

既存のレコード セット内のレコードを更新する場合、使用しているレコードの種類によって更新できるフィールドが異なります。

1. レコード セットの **[レコード セットのプロパティ]** ページで、レコードを検索します。

1. レコードを変更します。 レコードを変更するときは、レコードで利用可能な設定を変更できます。 下の例では、 **[IP アドレス]** フィールドが選択され、IP アドレスが変更されています。

    :::image type="content" source="./media/dns-operations-recordsets-portal/update-record-page.png" alt-text="レコードの更新ページのスクリーンショット。":::

1. ページの上部にある **[保存]** をクリックして設定を保存します。 画面の右上隅に、レコードが保存されたことを示す通知が表示されます。

    :::image type="content" source="./media/dns-operations-recordsets-portal/record-saved.png" alt-text="レコードが正常に保存されたスクリーンショット。":::

レコードが保存されると、 **[DNS ゾーン]** ページのレコード セットの値にはレコードの更新が反映されます。

## <a name="remove-a-record-from-a-record-set"></a>レコード セットからレコードを削除する

Azure ポータルを使用して、レコード セットからレコードを削除することができます。 レコード セットから最後のレコードを削除しても、レコード セットは削除されません。

1. レコード セットの **[レコード セットのプロパティ]** ページで、レコードを検索します。

1. レコードの横にある **[...]** を選択し、 **[削除]** を選択してレコード セットからレコードを削除します。

    :::image type="content" source="./media/dns-operations-recordsets-portal/delete-record.png" alt-text="レコードを削除する方法のスクリーンショット。":::

1. ページの上部にある **[保存]** をクリックして設定を保存します。

1. レコードが削除されると、 **[DNS ゾーン]** ページのレコードの値にはレコードの削除が反映されます。

## <a name="delete-a-record-set"></a><a name="delete"></a>レコード セットの削除

1. レコード セットの **[レコード セットのプロパティ]** ページで、 **[削除]** を選択します。

    :::image type="content" source="./media/dns-operations-recordsets-portal/delete-record-set.png" alt-text="レコード セットを削除する方法のスクリーンショット。":::

1. レコード セットを削除してもよいかを確認するメッセージが表示されます。

1. 削除するレコード セットの名前が正しいことを確認し、 **[はい]** を選択します。

1. **[DNS ゾーン]** ページで、レコード セットが表示されなくなっていることを確認します。

## <a name="work-with-ns-and-soa-records"></a>NS レコードと SOA レコードを使用する

自動的に作成される NS レコードと SOA レコードは、他の種類のレコードとは異なる方法で管理されます。

### <a name="modify-soa-records"></a>SOA レコードを変更する

ゾーンの頂点 (名前は "\@") に自動的に作成された SOA レコード セットのレコードを追加または削除することはできません。 ただし、"ホスト" を除く SOA レコードおよびレコード セットの TTL 内のパラメーターを変更することはできます。

### <a name="modify-ns-records-at-the-zone-apex"></a>ゾーンの頂点にある NS レコードを変更する

ゾーンの頂点にある NS レコード セットは各 DNS ゾーンで自動的に作成されます。 ゾーンに割り当てられている Azure DNS ネーム サーバーの名前が含まれています。

複数の DNS プロバイダーによる共同ホスト ドメインをサポートする目的で、この NS レコード セットにネーム サーバーを追加できます。 このレコード セットの TTL とメタデータを変更することもできます。 ただし、あらかじめ入力されている Azure DNS ネーム サーバーを削除または変更することはできません。

この制限は、ゾーンの頂点にある NS レコード セットにのみ適用されます。 (子ゾーンの委任に使用される) ゾーンの他の NS レコード セットは制約なしで変更できます。

### <a name="delete-soa-or-ns-record-sets"></a>SOA レコード セットまたは NS レコード セットを削除する

ゾーンの作成時に自動的に作成されるゾーンの頂点 (name = "\@") の SOA および NS レコード セットを削除することはできません。 ゾーンを削除すると、自動的に削除されます。

## <a name="next-steps"></a>次のステップ

* Azure DNS の詳細については、「 [Azure DNS の概要](dns-overview.md)」を参照してください。
* DNS 作成の自動化については、「 [.NET SDK を使用した DNS ゾーンとレコード セットの作成](dns-sdk.md)」を参照してください。
* 逆引き DNS レコードについて詳しくは、「[逆引き DNS と Azure でのサポートの概要](dns-reverse-dns-overview.md)」をご覧ください。
* Azure DNS エイリアス レコードの詳細については、「[Azure DNS エイリアス レコードの概要](dns-alias.md)」を参照してください。