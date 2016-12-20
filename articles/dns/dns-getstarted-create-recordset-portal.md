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
ms.sourcegitcommit: 42d47741e414b2de177f1fd75b3e1ac3fde96579
ms.openlocfilehash: 194da45bade88c2866aaf6066d312d125f1ac2cb

---

# <a name="create-dns-record-sets-and-records-by-using-the-azure-portal"></a>Azure ポータルを使用した DNS レコード セットとレコードの作成

> [!div class="op_single_selector"]
> * [Azure ポータル](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [Azure CLI](dns-getstarted-create-recordset-cli.md)

この記事では、Azure ポータルを使用してレコードとレコード セットを作成する手順を説明します。 DNS ゾーンを作成した後は、ドメインの DNS レコードを追加します。 そのために、まずは DNS レコードとレコード セットについて理解することが必要です。

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

## <a name="create-a-record-set-and-record"></a>レコード セットとレコードの作成

次の例では、Azure ポータルを使用してレコードとレコード セットを作成する手順を説明します。 ここでは DNS "A" レコードを使用します。

1. ポータルにサインインします。
2. レコード セットを作成する **DNS ゾーン** のブレードに移動します。
3. **[DNS ゾーン]** ブレードの上部にある **[レコード セット]** を選択して **[レコード セットの追加]** ブレードを開きます。

    ![新しいレコード セット](./media/dns-getstarted-create-recordset-portal/newrecordset500.png)

4. **[レコード セットの追加]** ブレードで、レコード セットの名前を入力します (例: "**www**")。

    ![[レコード セットの追加]](./media/dns-getstarted-create-recordset-portal/addrecordset500.png)

5. 作成するレコードの種類を選択します。 たとえば、 **[A]**を選択します。
6. **TTL**を設定します。 ポータルの既定の有効期間は、1 時間です。
7. IP アドレスを 1 行に 1 つずつ追加します。 上記のレコード セット名とレコードの種類を使用すると、www レコード セットの **A** レコードに IPv4 IP アドレスが追加されます。
8. IP アドレスの追加が完了したら、ブレードの下部にある **[OK]** を選択します。 これで、DNS レコード セットが作成されます。

## <a name="next-steps"></a>次のステップ

レコード セットとレコードを管理する方法については、 [Azure ポータルを使用した DNS レコードとレコード セットの管理](dns-operations-recordsets-portal.md)に関するページを参照してください。

Azure DNS の詳細については、「 [Azure DNS の概要](dns-overview.md)」を参照してください。



<!--HONumber=Nov16_HO3-->


