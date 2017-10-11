---
title: "プライベートのテンプレートの概要 |Microsoft ドキュメント"
description: "追加して管理し、Azure ポータル、Azure CLI または PowerShell を使用してプライベート テンプレートを共有します。"
services: marketplace-customer
documentationcenter: 
author: VybavaRamadoss
manager: asimm
editor: 
tags: marketplace, azure-resource-manager
keywords: 
ms.assetid: 6ec20778-b578-4885-acb5-104b0e51ea1a
ms.service: marketplace
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: vybavar
ms.openlocfilehash: 01657619cbe579c6818a790cc3ab95a33936a565
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-private-templates-on-the-azure-portal"></a>Azure ポータルのプライベート テンプレートの概要します。
[Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)テンプレートは宣言型のテンプレートが、展開を定義するために使用します。 ソリューションで、展開にリソースを定義し、パラメーターとに異なる環境での値を入力する変数を指定できます。 テンプレートは、JSON と配置の値を構築するために使用できる式で構成されます。

新しいを使用する**テンプレート**で機能、 [Azure Portal](https://portal.azure.com)と共に、 **Microsoft.Gallery**リソース プロバイダーの拡張機能として、 [Azure Marketplace](https://azure.microsoft.com/marketplace/)を作成するユーザーを有効にするを管理し、個人のライブラリからプライベートのテンプレートを展開します。

このチュートリアルでは追加、管理およびプライベートを共有する**テンプレート**Azure ポータルを使用します。

## <a name="guidance"></a>ガイダンス
次の提案を使用して、フル活用役立つ**テンプレート**ソリューションを使用する場合。

* A**テンプレート**カプセル化するリソース、リソース マネージャー テンプレートと追加のメタデータを含むです。 Marketplace の項目に非常によく似た動作します。 主な違いは、パブリックの Marketplace の項目ではなくプライベートなアイテムであることです。
* **テンプレート**ライブラリは、配置をカスタマイズする必要があるユーザーに適しています。
* **テンプレート**Azure 内の単純なリポジトリが必要なユーザーでうまく機能します。
* 既存のリソース マネージャー テンプレートを使用して開始します。 テンプレートを見つける[github](https://github.com/Azure/azure-quickstart-templates)または[テンプレートのエクスポート](../azure-resource-manager/resource-manager-export-template.md)既存のリソース グループからです。
* **テンプレート**はそれらを公開しているユーザーに関連付けられています。 パブリッシャーの名前は、読み取りアクセス権を持つすべてのユーザーに表示されます。
* **テンプレート**リソース マネージャーのリソースは、公開した後に変更することはできません。

## <a name="add-a-template-resource"></a>テンプレート リソースを追加します。
作成する方法を次の 2 つが、**テンプレート**Azure ポータルでのリソース。

### <a name="method-1--create-a-new-template-resource-from-a-running-resource-group"></a>方法 1: 実行中のリソース グループから新しいテンプレート リソースを作成します。
1. Azure ポータルの既存のリソース グループに移動します。 選択**テンプレートのエクスポート**で**設定**です。
2. リソース マネージャー テンプレートをエクスポートすると、使用して、**テンプレートの保存**に保存するボタン、**テンプレート**リポジトリです。 テンプレートのエクスポートの完全な詳細を検索[ここ](../azure-resource-manager/resource-manager-export-template.md)です。
   <br /><br />
   ![リソース グループのエクスポート](media/rg-export-portal1.PNG)  <br />
3. 選択、**テンプレートへの保存**コマンド ボタンをクリックします。
   <br /><br />
4. 次の情報を入力します。
   
   * [名前]: テンプレート オブジェクトの名前 (注: これは、Azure リソース マネージャーのベース名です。 すべての名前付けの制限が適用されます、いったん作成された後は変更できません)。
   * [説明]: テンプレートに関する簡単な概要。
     
     ![テンプレートを保存します。](media/save-template-portal1.PNG)  <br />
5. **[Save]**(保存) をクリックします。
   
   > [!NOTE]
   > エクスポート テンプレート ブレードは、エクスポートされたリソース マネージャー テンプレートが、エラーが、テンプレートにこのリソース マネージャー テンプレートを保存することができる場合、通知を示します。 確認して、エクスポートされたリソース マネージャー テンプレートを再展開する前にすべてのリソース マネージャー テンプレートの問題を修正することを確認します。
   > 
   > 

### <a name="method-2--add-a-new-template-resource-from-browse"></a>方法 2: 参照から新しいテンプレート リソースを追加します。
追加することも、新しい**テンプレート**スクラッチを使用してから、+ でのコマンド ボタンの追加**参照 > テンプレート**です。 名前、説明、およびリソース マネージャー テンプレート JSON を提供する必要があります。

![テンプレートを追加します。](media/add-template-portal1.PNG)  <br />

> [!NOTE]
> Microsoft.Gallery は、テナント ベースの Azure リソース プロバイダーです。 テンプレート リソースは、作成したユーザーに関連付けられています。 関連付けられていないが、特定のサブスクリプション。 サブスクリプションは、テンプレートを展開する場合にのみ選択する必要があります。
> 
> 

## <a name="view-template-resources"></a>テンプレート リソースの表示
すべて**テンプレート**できるでわかるように**参照 > テンプレート**です。 これが含まれます**テンプレート**だけでなくさまざまなレベルのアクセス許可と共有されているものを作成しました。 詳細は、[アクセス制御](#access-control-for-a-tenant-resource-provider)以下のセクションです。

![テンプレートの表示](media/view-template-portal1.PNG)  <br />

詳細を表示することができます、**テンプレート**をリスト内の項目をクリックします。

![テンプレートの表示](media/view-template-portal2c.png)  <br />

## <a name="edit-a-template-resource"></a>テンプレート リソースを編集します。
編集のフローを開始することができます、**テンプレート**や編集コマンド ボタンをクリックして参照リストに項目を右クリックします。

![テンプレートを編集します。](media/edit-template-portal1a.PNG)  <br />

説明またはリソース マネージャー テンプレートのテキストを編集することができます。 リソース マネージャー リソース名であるために、名前を編集することはできません。 編集するときに、リソース マネージャー テンプレートの JSON 検証を確認することは有効な JSON があること。 選択**OK**し**保存**更新されたテンプレートを保存します。

![テンプレートを編集します。](media/edit-template-portal2a.PNG)  <br />

1 回、**テンプレート**が保存される、確認通知が表示されます。

![テンプレートを編集します。](media/edit-template-portal3b.png)  <br />

## <a name="deploy-a-template-resource"></a>テンプレート リソースを展開します。
いずれかを展開することができます**テンプレート**のある**読み取り**に対する権限。 配置のフローは、標準の Azure テンプレートで展開ブレードを起動します。 展開を進めるにリソース マネージャー テンプレート パラメーターの値を入力します。

![テンプレートを展開します。](media/deploy-template-portal1b.png)  <br />

## <a name="share-a-template-resource"></a>テンプレート リソースを共有します。
A**テンプレート**リソースを同僚と共有できます。 同様に動作共有[Azure 上のリソースのロールの割り当て](../active-directory/role-based-access-control-configure.md)です。 **テンプレート**所有者は、テンプレート リソースと対話できる他のユーザーにアクセス許可を提供します。 ユーザーまたは共有する人々 のグループ、**テンプレート**でできるようになります、リソース マネージャー テンプレートとそのギャラリー プロパティを参照してください。

### <a name="access-control-for-the-microsoftgallery-resources"></a>Microsoft.Gallery リソースのアクセス制御
| Role | アクセス許可 |
| --- | --- |
| Owner |により、共有を含むテンプレートのリソースへのフル コントロール |
| 閲覧者 |テンプレート リソースの読み取りおよび Execute(Deploy) を許可します。 |
| 寄稿者 |テンプレート リソース上には、編集および削除の権限を許可します。 ユーザーは他のユーザーとテンプレートを共有することはできません。 |

選択**共有**を右クリックして、または特定の項目のビューのブレードで、参照項目にします。 これにより、共有のエクスペリエンスが起動します。

![共有のテンプレート](media/share-template-portal1a.png)  <br />

 ロールとユーザーまたはグループを特定のアクセスを提供するを選択できるようになりました**テンプレート**です。 利用可能な役割は、所有者、閲覧者およびコントリビューターです。 詳細は、[アクセス制御](#access-control-for-a-tenant-resource-provider)前のセクションです。

![共有のテンプレート](media/share-template-portal2b.png)  <br />

![共有のテンプレート](media/share-template-portal3b.png)  <br />

をクリックして**選択**と**Ok**です。 ユーザーまたはリソースに追加するグループが表示されます。

![共有のテンプレート](media/share-template-portal4b.png)  <br />

> [!NOTE]
> テンプレートは、同じ Azure Active Directory テナントのユーザーとグループとのみ共有できます。 テナントに含まれていない電子メール アドレスを持つテンプレートを共有する場合の招待に送信されますユーザーに確認するゲストとしてテナントに参加します。
> 
> 

## <a name="next-steps"></a>次のステップ
* リソース マネージャー テンプレートの作成方法については、次を参照してください[テンプレートの作成。](../azure-resource-manager/resource-group-authoring-templates.md)
* リソース マネージャー テンプレートで使用できる関数を理解するのを参照してください[テンプレート関数。](../azure-resource-manager/resource-group-template-functions.md)
* テンプレートのデザイン方法の詳細については、次を参照してください[Azure Resource Manager テンプレートのデザインのベスト プラクティス。](../azure-resource-manager/best-practices-resource-manager-design-templates.md)

