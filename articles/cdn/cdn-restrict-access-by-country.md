<properties
	pageTitle="国に応じて Azure CDN コンテンツへのアクセスを制限する | Microsoft Azure"
	description="国のフィルタリング機能を使用して Azure CDN コンテンツへのアクセスを制限する方法について説明します。"
	services="cdn"
	documentationCenter=""
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2016"
	ms.author="casoper"/>

#国に応じてコンテンツへのアクセスを制限する

[AZURE.INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

ユーザーがコンテンツを要求した場合、既定では、ユーザーが要求を行った場所に関係なく、コンテンツは提供されます。場合によっては、国によってコンテンツへのアクセスを制限することがあります。このトピックでは、**国のフィルタリング**機能を使用して、国別にアクセスを許可またはブロックするようにサービスを構成する方法について説明します。

>[AZURE.NOTE] 構成を設定した後は、この Azure CDN プロファイルのすべての **Azure CDN from Verizon** エンドポイントに対して構成が適用されます。

このような制限を構成する際に考慮する事項については、トピックの最後にある「[考慮事項](cdn-restrict-access-by-country.md#considerations)」セクションを参照してください。

![国のフィルタリング](./media/cdn-filtering/cdn-country-filtering.png)


##手順 1: ディレクトリのパスを定義する

国フィルターを構成するときは、ユーザーのアクセスを許可または拒否する場所への相対パスを指定する必要があります。"/" を指定してすべてのファイルに国フィルターを適用することも、ディレクトリ パスを指定して選択したフォルダーに適用することもできます。

ディレクトリ パスのフィルターの例:

	/                                 
	/Photos/
	/Photos/Strasbourg

##手順 2: ブロックまたは許可アクションを定義する

**ブロック**: 指定した国のユーザーは、その再帰パスから要求された資産へのアクセスを拒否されます。その場所に対して他の国フィルター オプションが構成されていない場合、他のすべてのユーザーはアクセスを許可されます。

**許可**: 指定した国のユーザーだけが、その再帰パスから要求された資産へのアクセスを許可されます。

##手順 3: 国を定義する

パスに対してブロックまたは許可する国を選択します。詳細については、「[Azure CDN from Verizon Country Codes (Azure CDN from Verizon の国コード)](https://msdn.microsoft.com/library/mt761717.aspx)」を参照してください。

たとえば、/Photos/Strasbourg/ をブロックするルールは、次のようなファイルをフィルターします。

	http://<endpoint>.azureedge.net/Photos/Strasbourg/1000.jpg
	http://<endpoint>.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg


##国コード

**国のフィルタリング**機能では、国コードを使用して保護されたディレクトリに対して許可またはブロックする要求の国を定義します。国コードは、「[Azure CDN from Verizon Country Codes (Azure CDN from Verizon の国コード)](https://msdn.microsoft.com/library/mt761717.aspx)」に記載されています。"EU" (ヨーロッパ) または "AP" (アジア/太平洋) を指定した場合、その地域の国から発信される IP アドレスのサブセットがブロックまたは許可されます。


##<a id="considerations"></a>考慮事項

- 国フィルタリング構成の変更が有効になるまでには、最大で 90 分かかることがあります。
- この機能では、ワイルドカード文字 (例: *) はサポートされていません。
- 相対パスに関連付けられている国フィルタリング構成は、そのパスに再帰的に適用されます。
- 同じ相対パスに適用できるルールは 1 つだけです (同じ相対パスを参照する複数の国フィルターを作成することはできません)。ただし、フォルダーには複数の国フィルターを適用できます。これは、国フィルターの再帰的な性質のためです。つまり、以前に構成されているフォルダーのサブフォルダーに、別の国フィルターを割り当てることができます。

<!---HONumber=AcomDC_0803_2016-->