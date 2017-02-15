---
title: "Azure Portal を使用して DNS ゾーンのレコード セットとレコードを作成する | Microsoft Docs"
description: "Azure DNS のホスト レコードを作成する方法と、Azure ポータルを使用してレコード セットとレコードを作成する方法について説明します。"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f93905f4-e82e-45db-b490-878d318e6aba
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: bfbffe7843bc178cdf289c999925c690ab82e922
ms.openlocfilehash: b3951106fe2e8607e65bd0ae47fa2ea3346b8ca5

---

# <a name="create-dns-record-sets-and-records-by-using-the-azure-portal"></a>Azure ポータルを使用した DNS レコード セットとレコードの作成

> [!div class="op_single_selector"]
> * [Azure ポータル](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [Azure CLI](dns-getstarted-create-recordset-cli.md)

この記事では、Azure ポータルを使用してレコードとレコード セットを作成する手順を説明します。 そのために、まずは DNS レコードとレコード セットについて理解することが必要です。

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

このページのすべての例では、DNS レコードの種類として "A" を使用します。 他のレコード タイプのプロセスも同様です。

新しいレコードの名前とタイプが既存のレコードと同じ場合は、その新しいレコードを既存のレコード セットに追加する必要があります。「&mdash;[Azure ポータルを使用した DNS レコードとレコード セットの管理](dns-operations-recordsets-portal.md)」をご覧ください。 新しいレコードの名前とタイプが既存のすべてのレコードと異なる場合は、以下で説明するように、新しいレコード セットを作成する必要があります。

## <a name="create-records-in-a-new-record-set"></a>新しいレコード セットのレコードの作成

次の例では、Azure ポータルを使用してレコードとレコード セットを作成する手順を説明します。

1. ポータルにサインインします。
2. レコード セットを作成する **DNS ゾーン** のブレードに移動します。
3. **[DNS ゾーン]** ブレードの上部にある **[+ レコード セット]** を選択して **[レコード セットの追加]** ブレードを開きます。

    ![新しいレコード セット](./media/dns-getstarted-create-recordset-portal/newrecordset500.png)

4. **[レコード セットの追加]** ブレードで、レコード セットの名前を入力します (例: "**www**")。

    ![[レコード セットの追加]](./media/dns-getstarted-create-recordset-portal/addrecordset500.png)

5. 作成するレコードの種類を選択します。 たとえば、 **[A]**を選択します。
6. **TTL**を設定します。 ポータルの既定の有効期間は、1 時間です。
7. レコード セットの各レコードの詳細を追加します。 この場合、レコード タイプが "A" であるため、A レコードの IP アドレス (1 行に&1; つの IP アドレス) を追加する必要があります。
8. IP アドレスの追加が完了したら、ブレードの下部にある **[OK]** を選択します。 これで、DNS レコード セットが作成されます。

### <a name="verify-name-resolution"></a>名前解決の確認

Azure DNS ネーム サーバーに DNS レコードが存在することをテストするには、nslookup、dig、[Resolve-DnsName PowerShell コマンドレット](https://technet.microsoft.com/library/jj590781.aspx)などの DNS ツールを使用します。

Azure DNS の新しいゾーンを使用するためのドメインの委任をまだ行っていない場合は、[ゾーンのネーム サーバーの&1; つに DNS クエリを直接送信する](dns-getstarted-create-dnszone.md#test-name-servers)必要があります。 次のコマンドを実際のレコード ゾーンの正しい値に置き換えてください。

    nslookup
    > set type=A
    > server ns1-01.azure-dns.com
    > www.contoso.com

    Server:  ns1-01.azure-dns.com
    Address:  40.90.4.1

    Name:    www.contoso.com
    Address:  1.2.3.4

## <a name="next-steps"></a>次のステップ

[ドメイン名を Azure DNS ネーム サーバーに委任する](dns-domain-delegation.md)方法を確認します。

レコード セットとレコードを管理する方法については、 [Azure ポータルを使用した DNS レコードとレコード セットの管理](dns-operations-recordsets-portal.md)に関するページを参照してください。



<!--HONumber=Dec16_HO2-->


