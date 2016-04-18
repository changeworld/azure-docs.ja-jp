<properties 
   pageTitle="Azure ポータルを使用して Azure DNS のレコード セットとレコードを管理する | Microsoft Azure" 
   description="Azure DNS でドメインをホストする際に DNS のレコード セットとレコードを管理する方法について説明します。" 
   services="dns" 
   documentationCenter="na" 
   authors="cherylmc" 
   manager="carmon" 
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="dns"
   ms.devlang="en"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="03/29/2016"
   ms.author="cherylmc"/>

# Azure ポータルを使用して DNS レコードとレコード セットを作成および管理する方法


> [AZURE.SELECTOR]
- [Azure ポータル](dns-operations-recordsets-portal.md)
- [Azure CLI](dns-operations-recordsets-cli.md)
- [PowerShell](dns-operations-recordsets.md)


このガイドでは、Azure ポータルを使用して DNS ゾーンのレコード セットとレコードを作成して管理する方法について説明します。

DNS レコード セットと個々の DNS レコードの違いを理解することは重要です。レコード セットとは、1 つのゾーン内にある同じ名前、同じ種類のレコードのコレクションです。詳細については、「[レコード セットとレコードについて](../dns-getstarted-create-recordset#Understanding-record-sets-and-records)」を参照してください。

## 新しいレコード セットとレコードを作成する

Azure ポータルでレコード セットを作成する方法については、[Azure ポータルを使用した DNS レコードの作成に関するページ](dns-getstarted-create-recordset-portal.md)を参照してください。


## レコード セットを表示する

1. Azure ポータルで、DNS ゾーンのブレードに移動します。

2. 目的のレコード セットを検索し、一覧から選択します。レコード セットをクリックして選択すると、レコード セットのプロパティが表示されます。

	![レコード セットの検索](./media/dns-operations-recordsets-portal/searchset500.png)


## 既存のレコード セットに新しいレコードを追加する

すべてのレコード セットに対して、最大 20 個のレコードを追加できます。レコード セットには、同一の 2 つのレコードを含めることはできません。空のレコード セット (レコードが 0 個) を作成することはできますが、Azure DNS ネーム サーバーには表示されません。レコード セットの種類が CNAME の場合、そこに含めることができるレコードは 1 つまでです。


1. DNS ゾーンの **[Record set properties (レコード セットのプロパティ)]** ブレードで、レコードを追加するレコード セットをクリックします。

	![セットの選択](./media/dns-operations-recordsets-portal/selectset500.png)

2. 各フィールドに入力して、レコードの設定を指定します。

	![レコードの追加](./media/dns-operations-recordsets-portal/addrecord500.png)

2. ブレードの上部にある **[保存]** をクリックして設定を保存してから、ブレードを閉じます。

3. 画面の隅に、レコードが保存されていることを示すメッセージが表示されます。

	![レコードの保存](./media/dns-operations-recordsets-portal/saving150.png)

4. レコードが保存されると、DNS ブレードのレコード セットの値にはレコードの追加が反映されます。


## レコードを更新する

既存のレコード セット内のレコードを更新する場合、使用しているレコードの種類によって更新できるフィールドが異なります。

1. レコード セットの **[Record set properties (レコード セットのプロパティ)]** ブレードで、レコードを検索します。

2. レコードを変更します。レコードを変更するときは、レコードで利用可能ないずれかの設定を変更できます。下の例では、[IP アドレス] フィールドをクリックして、IP アドレスを変更する処理を行っています。

	![レコードの変更](./media/dns-operations-recordsets-portal/modifyrecord500.png)

3. ブレードの上部にある **[保存]** をクリックして設定を保存します。画面の右上隅に、レコードが保存されたことを示す通知が表示されます。

	
	![レコードの保存](./media/dns-operations-recordsets-portal/saved150.png)



3. レコードが保存されると、DNS ブレードのレコード セットの値にはレコードの更新が反映されます。



## レコード セットからレコードを削除する

Azure ポータルを使用して、レコード セットからレコードを削除することができます。なお、レコード セットから最後のレコードを削除しても、レコード セットは削除されません。

1. レコード セットの **[Record set properties (レコード セットのプロパティ)]** ブレードで、レコードを検索します。

2. 削除するレコードをクリックして、**[削除]** をクリックします。

	![レコードの削除](./media/dns-operations-recordsets-portal/removerecord500.png)

3. ブレードの上部にある **[保存]** をクリックして設定を保存します。

3. レコードが削除されると、DNS ブレードのレコード セットの値にはレコードの削除が反映されます。


## <a name="delete"></a>レコード セットを削除する

1. レコード セットの **[Record set properties (レコード セットのプロパティ)]** ブレードで、**[削除]** をクリックします。 

	![レコード セットの削除](./media/dns-operations-recordsets-portal/deleterecordset500.png)

2. レコード セット 'name\_ of \_ record \_set' を削除してもよいかを確認するメッセージが表示されます。

3. 削除するレコード セットの名前が正しいことを確認し、**[はい]** をクリックします。

4. DNS ゾーンのブレードで、レコード セットが表示されなくなっていることを確認できます。


## NS レコードと SOA レコードを使用する

自動的に作成される NS レコードと SOA レコードは、他の種類のレコードとは異なる方法で管理されます。

### SOA レコードを変更する

ゾーンの頂点 (名前は "@") で自動的に作成された SOA レコード セットでレコードの追加または削除を実行することはできませんが、SOA レコード内のすべてのパラメーター ('Host' を除きます) とレコード セットの TTL は変更できます。

### ゾーンの頂点にある NS レコードを変更する

ゾーンの頂点 (名前は "@") に自動的に作成された NS レコード セットのレコードを追加、削除、または変更することはできません。許可されている変更操作は、レコード セットの TTL の変更のみです。

### SOA レコード セットまたは NS レコード セットを削除する

ゾーンの作成時に自動的に作成される、ゾーンの頂点 (名前は "@") の SOA および NS レコード セットは削除できません。これらはゾーンの削除時に自動的に削除されます。

## 次のステップ

Azure DNS の詳細については、「[Azure DNS の概要](dns-overview.md)」を参照してください。DNS 作成の自動化については、「[.NET SDK を使用した DNS ゾーンとレコード セットの作成](dns-sdk.md)」を参照してください。

<!---HONumber=AcomDC_0406_2016-->