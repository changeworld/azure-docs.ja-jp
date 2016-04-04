<properties
	pageTitle="Azure ポータルを使用した Azure Search サービスの作成 | Microsoft Azure | ホスト型クラウド検索サービス"
	description="Azure ポータルを使用して Azure Search サービスをプロビジョニングする方法について説明します。"
	services="search"
	authors="ashmaka"
	documentationCenter=""/>

<tags
	ms.service="search"
	ms.devlang="NA"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.date="03/10/2016"
	ms.author="ashmaka"/>

# Azure ポータルを使用した Azure Search サービスの作成

このガイドでは、[Azure ポータル](https://portal.azure.com/)を使用して Azure Search サービスを作成する (またはプロビジョニングする) プロセスについて説明します。

このガイドでは、既に Azure サブスクリプションを持っており、Azure ポータルにログインできることを前提としています。

## I.Azure ポータルで Azure Search を見つける
1. [Azure ポータル](https://portal.azure.com/)にアクセスし、ログインします。
1. 左上隅のプラス記号 ("+") をクリックします
2. [データ + ストレージ] を選択します
3. [Azure Search] を選択します

![](./media/search-create-service-portal/find-search.png)

## II.サービスのサービス名と URL エンドポイントを選択する
1. サービス名は、検索サービスを管理して使用するために API 呼び出しを行う対象となる Azure Search サービスのエンドポイント URL の一部となります。
2. [URL] フィールドに、サービス名を入力します。サービス名:
  * 小文字、数字、ダッシュ ("-") のみを含める必要があります
  * 最初の 2 文字または最後の 1 文字にダッシュ ("-") を使用することはできません
  * 連続するダッシュ ("-") を含めることはできません
  * 2 ～ 60 文字の長さに制限されます


## III.サービスを維持するサブスクリプションを選択する
複数のサブスクリプションがある場合は、この Azure Search サービスを含めるサブスクリプションを選択できます。

## IV.サービスのリソース グループを選択する
新しいリソース グループを作成するか、または既存のリソース グループを選択します。リソース グループとは、一緒に使用される Azure サービスとリソースのコレクションです。たとえば、Azure Search を使用して SQL Database のインデックスを作成する場合、これら両方のサービスを同じリソース グループに含める必要があります。

## V.サービスがホストされる場所を選択する
Azure サービスとして Azure Search は、世界中のデータ センターでホストできます。地域によって[価格が異なることがある](https://azure.microsoft.com/pricing/details/search/)ことに注意してください。

## VI.価格レベルを選択する
[Azure Search は現在、Free、Basic、Standard の複数の価格レベルで提供されています](https://azure.microsoft.com/pricing/details/search/)。各レベルに独自の[容量と制限](search-limits-quotas-capacity.md)があります。

この例では、サービスに Standard レベルを選択しました。

## VII.[作成] ボタンを選択して、サービスをプロビジョニングする

![](./media/search-create-service-portal/create-service.png)

## VIII.サービスを拡張する

サービスのプロビジョニングが完了したら、ニーズに合わせてサービスを拡張できます。Azure Search サービスに、Standard レベルを選択した場合、レプリカとパーティションの 2 つのディメンションでサービスを拡張できます。Basic レベルを選択した場合は、レプリカのみ追加できます。

*__パーティション__*を使用すると、サービスでより多くのドキュメントを格納し、検索できます。

*__レプリカ__*を使用すると、サービスでより高い負荷の検索クエリを処理できます。[サービスは、読み取り専用 SLA を実現するために 2 つのレプリカを必要とし、読み取り/書き込み SLA を実現するために 3 つのレプリカを必要とします](https://azure.microsoft.com/support/legal/sla/search/v1_0/)。

1. Azure ポータルで Azure Search サービスの管理ブレードに移動します。
2. [設定] ブレードで、[スケール] を選択します。
3. レプリカまたはパーティションを追加して、サービスを拡張できます。
  * 36 検索単位を超えてサービスを拡張することはできません。検索単位の総数は、レプリカとパーティションの積になります (レプリカ * パーティション = 合計検索単位)。
  * Basic レベルを選択した場合、3 つのレプリカにのみ拡張できます。Basic サービスは、1 つのパーティションにバインドされます。

![](./media/search-create-service-portal/scale-service.png)

## 次へ
Azure Search サービスをプロビジョニングしたら、データをアップロードし、検索できるように、[Azure Search インデックスを定義する](search-what-is-an-index.md)準備ができます。

<!---HONumber=AcomDC_0316_2016-->