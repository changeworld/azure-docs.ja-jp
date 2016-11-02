<properties
   pageTitle="Azure ポータルを使用して Azure DNS のレコード セットとレコードを管理する | Microsoft Azure"
   description="Azure DNS でドメインをホストする際に DNS のレコード セットとレコードを管理する方法について説明します。"
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
   ms.date="08/16/2016"
   ms.author="cherylmc"/>

# Azure ポータルを使用した DNS レコードとレコード セットの管理


> [AZURE.SELECTOR]
- [Azure ポータル](dns-operations-recordsets-portal.md)
- [Azure CLI](dns-operations-recordsets-cli.md)
- [PowerShell](dns-operations-recordsets.md)


この記事では、Azure ポータルを使用して DNS ゾーンのレコード セットとレコードを管理する方法について説明します。

DNS レコード セットと個々の DNS レコードの違いを理解することは重要です。レコード セットとは、1 つのゾーン内にある同じ名前、同じ種類のレコードのコレクションです。詳細については、「[Azure ポータルを使用した DNS レコード セットとレコードの作成](dns-getstarted-create-recordset-portal.md)」を参照してください。

## 新しいレコード セットとレコードを作成する

Azure ポータルでレコード セットを作成する方法については、[Azure ポータルを使用した DNS レコードの作成に関するページ](dns-getstarted-create-recordset-portal.md)を参照してください。


## レコード セットを表示する

1. Azure ポータルで、**DNS ゾーン** ブレードに移動します。

2. レコード セットを検索して選択します。レコード セットのプロパティが表示されます。

	![レコード セットの検索](./media/dns-operations-recordsets-portal/searchset500.png)


## レコード セットに新しいレコードを追加する

すべてのレコード セットに対して、最大 20 個のレコードを追加できます。レコード セットには、同一の 2 つのレコードを含めることはできません。空のレコード セット (レコードが 0 個) を作成することはできますが、Azure DNS ネーム サーバーには表示されません。レコード セットの種類が CNAME の場合、そこに含めることができるレコードは 1 つまでです。


1. DNS ゾーンの **[Record set properties (レコード セットのプロパティ)]** ブレードで、レコードを追加するレコード セットをクリックします。

	![レコード セットの選択](./media/dns-operations-recordsets-portal/selectset500.png)

2. 各フィールドに入力して、レコード セットのプロパティを指定します。

	![レコードの追加](./media/dns-operations-recordsets-portal/addrecord500.png)

2. ブレードの上部にある **[保存]** をクリックして設定を保存します。次に、ブレードを閉じます。

3. 画面の隅に、レコードが保存されていることを示すメッセージが表示されます。

	![レコード セットの保存](./media/dns-operations-recordsets-portal/saving150.png)

レコードが保存されると、**DNS ゾーン** ブレードの値にはレコードの追加が反映されます。


## レコードを更新する

既存のレコード セット内のレコードを更新する場合、使用しているレコードの種類によって更新できるフィールドが異なります。

1. レコード セットの **[Record set properties (レコード セットのプロパティ)]** ブレードで、レコードを検索します。

2. レコードを変更します。レコードを変更するときは、レコードで利用可能な設定を変更できます。下の例では、**[IP アドレス]** フィールドを選択して、IP アドレスを変更する処理を行っています。

	![レコードの変更](./media/dns-operations-recordsets-portal/modifyrecord500.png)

3. ブレードの上部にある **[保存]** をクリックして設定を保存します。画面の右上隅に、レコードが保存されたことを示す通知が表示されます。

	![保存されたレコード セット](./media/dns-operations-recordsets-portal/saved150.png)


レコードが保存されると、**DNS ゾーン** ブレードのレコード セットの値にはレコードの更新が反映されます。


## レコード セットからレコードを削除する

Azure ポータルを使用して、レコード セットからレコードを削除することができます。なお、レコード セットから最後のレコードを削除しても、レコード セットは削除されません。

1. レコード セットの **[Record set properties (レコード セットのプロパティ)]** ブレードで、レコードを検索します。

2. 削除するレコードをクリックして、**[削除]** を選択します。

	![レコードの削除](./media/dns-operations-recordsets-portal/removerecord500.png)

3. ブレードの上部にある **[保存]** をクリックして設定を保存します。

3. レコードが削除されると、**DNS ゾーン** ブレードのレコードの値にはレコードの削除が反映されます。


## <a name="delete"></a>レコード セットを削除する

1. レコード セットの **[Record set properties (レコード セットのプロパティ)]** ブレードで、**[削除]** をクリックします。

	![レコード セットの削除](./media/dns-operations-recordsets-portal/deleterecordset500.png)

2. レコード セットを削除してもよいかを確認するメッセージが表示されます。

3. 削除するレコード セットの名前が正しいことを確認し、**[はい]** をクリックします。

4. **DNS ゾーン**のブレードで、レコード セットが表示されなくなっていることを確認します。


## NS レコードと SOA レコードを使用する

自動的に作成される NS レコードと SOA レコードは、他の種類のレコードとは異なる方法で管理されます。

### SOA レコードを変更する

ゾーンの頂点 (名前は "@") に自動的に作成された SOA レコード セットのレコードを追加または削除することはできません。ただし、("ホスト" を除く) SOA レコードおよびレコード セットの TTL 内のパラメーターを変更することはできます。

### ゾーンの頂点にある NS レコードを変更する

ゾーンの頂点 (名前は "@") に自動的に作成された NS レコード セットのレコードを追加、削除、または変更することはできません。許可されている変更操作は、レコード セットの TTL の変更のみです。

### SOA レコード セットまたは NS レコード セットを削除する

ゾーンの作成時に自動的に作成される、ゾーンの頂点 (名前は "@") の SOA および NS レコード セットは削除できません。ゾーンを削除すると、自動的に削除されます。

## 次のステップ

-   Azure DNS の詳細については、「[Azure DNS の概要](dns-overview.md)」を参照してください。
-   DNS 作成の自動化については、「[.NET SDK を使用した DNS ゾーンとレコード セットの作成](dns-sdk.md)」を参照してください。
-   逆引き DNS レコードの詳細については、「[PowerShell を使用してサービスの逆引き DNS レコードを管理する方法](dns-reverse-dns-record-operations-ps.md)」を参照してください。

<!---HONumber=AcomDC_0824_2016-->