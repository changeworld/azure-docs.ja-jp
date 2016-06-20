<properties
   pageTitle="Azure ポータルを使用して DNS ゾーンのレコード セットとレコードを作成する | Microsoft Azure"
   description="Azure DNS のホスト レコードを作成する方法と、Azure ポータルを使用してレコード セットとレコードを作成する方法について説明します。"
   services="dns"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/06/2016"
   ms.author="cherylmc"/>



# Azure ポータルを使用した DNS レコード セットとレコードの作成


> [AZURE.SELECTOR]
- [Azure ポータル](dns-getstarted-create-recordset-portal.md)
- [PowerShell](dns-getstarted-create-recordset.md)
- [Azure CLI](dns-getstarted-create-recordset-cli.md)


この記事では、Azure ポータルを使用してレコードとレコード セットを作成する手順を説明します。DNS ゾーンを作成した後は、ドメインの DNS レコードを追加します。そのために、まずは DNS レコードとレコード セットについて理解することが必要です。

[AZURE.INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]


## レコード セットとレコードの作成

次の例では、Azure ポータルを使用してレコードとレコード セットを作成する手順を説明します。ここでは DNS "A" レコードを使用します。

1. ポータルにサインインします。

2. レコード セットを作成する **DNS ゾーン**のブレードに移動します。

3. **DNS ゾーン**のブレードの上部にある **[レコード セット]** を選択して **[レコード セットの追加]** ブレードを開きます。

	![新しいレコード セット](./media/dns-getstarted-create-recordset-portal/newrecordset500.png)

4. **[レコード セットの追加]** ブレードで、レコード セットの名前を入力します(例: "**www**")。

	![レコード セットの追加](./media/dns-getstarted-create-recordset-portal/addrecordset500.png)

5. 作成するレコードの種類を選択します。たとえば、**[A]** を選択します。

6. **TTL** を設定します。ポータルの既定の有効期間は、1 時間です。

7. IP アドレスを 1 行に 1 つずつ追加します。上記のレコード セット名とレコードの種類を使用すると、www レコード セットの **A** レコードに IPv4 IP アドレスが追加されます。

8. IP アドレスの追加が完了したら、ブレードの下部にある **[OK]**を選択します。これで、DNS レコード セットが作成されます。


## 次のステップ

レコード セットとレコードを管理する方法については、[Azure ポータルを使用した DNS レコードとレコード セットの管理](dns-operations-recordsets-portal.md)に関するページを参照してください。

Azure DNS の詳細については、「[Azure DNS の概要](dns-overview.md)」を参照してください。

<!---HONumber=AcomDC_0608_2016-->