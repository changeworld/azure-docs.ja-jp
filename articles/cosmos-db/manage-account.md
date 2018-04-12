---
title: Azure Portal を使用して Azure Cosmos DB アカウントを管理する | Microsoft Docs
description: Azure Portal を使用して Azure Cosmos DB アカウントを管理する方法について説明します。 Azure Portal を使用してアカウントを表示、コピー、削除、およびアカウントにアクセスする方法について説明したガイドを紹介します。
keywords: Azure Portal, Azure, Microsoft Azure
services: cosmos-db
documentationcenter: ''
author: kirillg
manager: jhubbard
editor: cgronlun
ms.assetid: 00fc172f-f86c-44ca-8336-11998dcab45c
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: kirillg
ms.openlocfilehash: e5820cb17cfbaa15f10f24881f02a37aec617267
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/05/2018
---
# <a name="how-to-manage-an-azure-cosmos-db-account"></a>Azure Cosmos DB アカウントの管理方法
グローバルな整合性の設定、キーの操作、Azure Cosmos DB アカウントの削除を Azure Portal で行う方法について説明します。

## <a id="consistency"></a>Azure Cosmos DB の整合性の設定の管理
アプリケーションのセマンティクスに応じて、適切な整合性レベルを選択します。 「[Using consistency levels to maximize availability and performance in Azure Cosmos DB (整合性レベルを使用した Azure Cosmos DB の可用性とパフォーマンスの最大化)][consistency]」を参考にして、Azure Cosmos DB で利用できる整合性レベルについて把握してください。 Azure Cosmos DB の整合性、可用性、パフォーマンスは、お使いのデータベース アカウントで利用できるすべての整合性レベルで保証されます。 データベース アカウントに高い整合性レベルを設定するには、データを 1 つの Azure リージョンに限定し、グローバルで使用できないようにする必要があります。 一方で、低い整合性レベル (制限のある有界整合性制約、セッション、または最終的) に設定した場合、任意の数の Azure リージョンをデータベース アカウントに関連付けることができます。 次に、データベース アカウントの既定の整合性レベルを選択するための簡単な手順を説明します。

### <a name="to-specify-the-default-consistency-for-an-azure-cosmos-db-account"></a>Azure Cosmos DB アカウントの既定の整合性を指定するには
1. [Azure Portal](https://portal.azure.com/) で、Azure Cosmos DB アカウントにアクセスします。
2. アカウント ページで、**[既定の整合性]** をクリックします。
3. **[既定の整合性]** ページで、新しい一貫性レベルを選び、**[保存]** をクリックします。
    ![既定の一貫性セッション][5]

## <a id="keys"></a>アクセス キーおよびパスワードを表示、コピー、および再生成する
Azure Cosmos DB アカウントを作成すると、サービスによって 2 つのマスター アクセス キー (または MongoDB API アカウント用に 2 つのパスワード) が生成されます。これらのアクセス キーは、Azure Cosmos DB アカウントにアクセスする際の認証に使用できます。 2 つのアクセス キーが提供されるので、Azure Cosmos DB アカウントを中断することなくキーを再生成できます。 

[Azure Portal](https://portal.azure.com/) で、**[Azure Cosmos DB アカウント]** ページのリソース メニューにある **[キー]** ページにアクセスし、Azure Cosmos DB アカウントにアクセスするときに使用するアクセス キーの表示、コピー、再生成を行います。 MongoDB API アカウントの場合は、リソース メニューから **[接続文字列]** ページにアクセスし、アカウントへのアクセスに使用されるパスワードを表示、コピー、および再生成します。

![Azure Portal のスクリーン ショット、[キー] ページ](./media/manage-account/keys.png)

> [!NOTE]
> **[キー]** ページには、[データ移行ツール](import-data.md)からアカウントに接続する際に使用できるプライマリおよびセカンダリ接続文字列も含まれています。
> 
> 

読み取り専用キーもこのページで入手できます。 読み取りとクエリは読み取り専用操作ですが、作成、削除、置換はそうではありません。

### <a name="copy-an-access-key-or-password-in-the-azure-portal"></a>Azure ポータルでアクセス キーまたはパスワードをコピーする
**[キー]** ページ (または MongoDB API アカウントの場合は **[接続文字列]** ページ) で、コピーするキーまたはパスワードの右側にある [**コピー]** ボタンをクリックします。

![Azure Portal の [キー] ページでアクセス キーを表示およびコピーする](./media/manage-account/copykeys.png)

### <a name="regenerate-access-keys-and-passwords"></a>アクセス キーおよびパスワードを再生成する
接続のセキュリティを高めるために、Azure Cosmos DB アカウントのアクセス キー (または MongoDB API アカウントのパスワード) は定期的に変更する必要があります。 一方のアクセス キー/パスワードを使用して Azure Cosmos DB アカウントへの接続を維持しながら、もう一方のアクセス キーを再生成できるように、2 つのアクセス キーが割り当てられます。

> [!WARNING]
> アクセス キーを再生成すると、現在のキーに依存するすべてのアプリケーションが影響を受けます。 アクセス キーを使用して Azure Cosmos DB アカウントにアクセスするすべてのクライアントを更新し、新しいキーが使用されるようにする必要があります。
> 
> 

Azure Cosmos DB アカウントを使用するアプリケーションまたはクラウド サービスがある場合は、キーを再生成すると、キーを切り替えない限り接続が失われます。 次の手順は、キー/パスワードの切り替えに含まれるプロセスの概要を示します。

1. Azure Cosmos DB アカウントのセカンダリ アクセス キーを参照するように、アプリケーション コードのアクセス キーを更新します。
2. Azure Cosmos DB アカウントのプライマリ アクセス キーを再生成します。 [Azure Portal](https://portal.azure.com/) で、Azure Cosmos DB アカウントにアクセスします。
3. **[Azure Cosmos DB アカウント]** ページで **[キー]** (または MongoDB アカウントの場合は **[接続文字列]****) をクリックします。
4. **[キー]**/**[接続文字列]** ページで [再生成] ボタンをクリックしてから **[OK]** をクリックして、新しいキーの生成を確定します。
    ![アクセス キーを再生成する](./media/manage-account/regenerate-keys.png)
5. (再生成してから約 5 分後に) 新しいキーが使用できることを確認したら、新しいプライマリ アクセス キーを参照するようにアプリケーション コードのアクセス キーを更新します。
6. セカンダリ アクセス キーを再生成します。
   
    ![アクセス キーを再生成する](./media/manage-account/regenerate-secondary-key.png)

> [!NOTE]
> 新しく生成されたキーを使用して Azure Cosmos DB アカウントにアクセスできるようになるまで、数分かかることがあります。
> 
> 

## <a name="get-the-connection-string"></a>接続文字列を取得する
接続文字列を取得するには、次の操作を行います。 

1. [Azure Portal](https://portal.azure.com) で、Azure Cosmos DB アカウントにアクセスします。
2. リソース メニューで **[キー]** (または MongoDB API アカウントの場合は **[接続文字列]**) をクリックします。
3. **[プライマリ接続文字列]** ボックスまたは **[セカンダリ接続文字列]** ボックスの横にある **[コピー]** ボタンをクリックします。 

[Azure Cosmos DB データベース移行ツール](import-data.md)で接続文字列を使用する場合は、接続文字列の末尾にデータベース名を追加します。 `AccountEndpoint=< >;AccountKey=< >;Database=< >`

## <a id="delete"></a> Azure Cosmos DB アカウントの削除
使用しなくなった Azure Cosmos DB アカウントを Azure Portal から削除するには、アカウント名を右クリックし、**[アカウントの削除]** をクリックします。

![Azure Portal で Azure Cosmos DB アカウントを削除する方法](./media/manage-account/deleteaccount.png)

1. [Azure Portal](https://portal.azure.com/) で、削除する Azure Cosmos DB アカウントにアクセスします。
2. **[Azure Cosmos DB アカウント]** ページで、アカウントを右クリックし、**[アカウントの削除]** をクリックします。 
3. 確認ページで、Azure Cosmos DB アカウント名を入力して、そのアカウントを削除することを確認します。
4. **[削除]** ボタンをクリックします。

![Azure Portal で Azure Cosmos DB アカウントを削除する方法](./media/manage-account/delete-account-confirm.png)

## <a id="next"></a>次のステップ
[Azure Cosmos DB アカウントの概要](http://go.microsoft.com/fwlink/p/?LinkId=402364)について学ぶ。

<!--Image references-->
[5]: ./media/manage-account/documentdb_change_consistency-1.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: consistency-levels.md
[azureregions]: https://azure.microsoft.com/regions/#services
[offers]: https://azure.microsoft.com/pricing/details/cosmos-db/
