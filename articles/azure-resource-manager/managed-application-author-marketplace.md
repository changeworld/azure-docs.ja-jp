---
title: "Marketplace の Azure マネージ アプリケーション | Microsoft Docs"
description: "Marketplace を介して利用できる Azure マネージ アプリケーションについて説明します。"
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/09/2017
ms.author: gauravbh; tomfitz
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: bd7880bdbca8cbf1abcab2cbade050876e26aea1
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---

# <a name="azure-managed-applications-in-the-marketplace"></a>Marketplace の Azure マネージ アプリケーション

[マネージ アプリケーションの概要](managed-application-overview.md)に関する記事で説明したとおり、Microsoft Azure Marketplace のマネージ アプリケーションを使用すると、MSP、ISV、システム インテグレーター (SI) は、すべての Azure ユーザーに自社のソリューションを提供できます。 このようなソリューションにより、顧客のメンテナンスとサービスのオーバーヘッドは削減されます。 発行者は Marketplace を通じてインフラストラクチャとソフトウェアを販売し、それらにサービスと運用サポートを付加することができます。 

この記事では、MSP、ISV、または SI が Marketplace にアプリケーションを発行し、幅広くユーザーに公開されるようにする方法について説明します。  

## <a name="pre-requisites-for-publishing-a-managed-application"></a>マネージ アプリケーションを発行するための前提条件

Marketplace に掲載するための前提条件

* 技術

    *  Resource Manager テンプレートの基本的な構造と構文については、[Azure Resource Manager テンプレートの作成](resource-group-authoring-templates.md)に関するページを参照してください。
    *  完成したテンプレート ソリューションを表示するには、「[Azure クイック スタート テンプレート](https://azure.microsoft.com/en-us/documentation/templates/)」または[クイック スタート テンプレート リポジトリ](https://github.com/azure/azure-quickstart-templates)を参照してください。
    *  マーケットプレースを通じてアプリケーションをデプロイする顧客向けのインターフェイスの作成については、[ユーザー インターフェイス定義ファイルの作成](managed-application-createuidefinition-overview.md)に関するページを参照してください。

* 技術面以外 (ビジネス要件)

    *   貴社 (またはその子会社) は、Marketplace でサポートされている販売元の国に所在している必要があります。
    *   Marketplace でサポートされている課金モデルに対応した方法で、製品のライセンスを取得する必要があります。
    *   商取引上合理的な方法で、顧客に技術サポートを提供していただきます。 無償、有償、コミュニティ サポートの活用など、方法は問いません。
    *   貴社のソフトウェアおよび依存関係にあるすべてのサード パーティ ソフトウェアのライセンス供与を行っていただきます。
    *   Azure Marketplace および Azure Portal にプランが掲載されるための条件を満たすコンテンツを提供する必要があります
    *   Azure Marketplace 参加ポリシーと発行者契約の条項に同意する必要があります。
    *   使用条件、Microsoft のプライバシーに関する声明、および Microsoft Azure 認定プログラム契約に従うことに同意する必要があります。

## <a name="how-to-create-a-new-azure-application-offer"></a>新しい Azure アプリケーション プランを作成する方法

前提条件を満たしたら、マネージ アプリケーション プランの作成を開始できます。 プランと SKU の概要を見ておきましょう。

### <a name="offer"></a>プラン

マネージ アプリケーション用のプランは、発行者から提供される製品のクラスに相当します。 Marketplace で入手できるようにしたい新しい種類のソリューション/アプリケーションがある場合は、それを新しいプランとして設定することができます。 プランとは、SKU のコレクションです。 各プランは、Marketplace で独自のエンティティとして表示されます。

### <a name="sku"></a>SKU

SKU とは、プランの購入可能な最小単位です。 同じ製品クラス (プラン) 内では、SKU により、サポートされる各種機能、プランが管理対象か管理対象外か、サポートされる課金モデルを区別できます。

SKU は、Marketplace では親プランの下に表示されます。 Azure Portal では、独自の購入可能エンティティとして表示されます。

### <a name="set-up-offer"></a>プランの設定

1.  [クラウド パートナー ポータル](https://cloudpartner.azure.com/)にログインします。
2.  左側のナビゲーション バーから、**[+ 新しいプラン]** をクリックし、**[Azure Applications]\(Azure アプリケーション\)** を選択します。

    ![新しいプラン](./media/managed-application-author-marketplace/newOffer.png)

3.  これで新しいプランの **[エディター]** ビューが開いて、作成を開始できるようになりました。

    ![プラン設定](./media/managed-application-author-marketplace/newOffer_OfferSettings.png)

4.  入力が必要なフォームは、**[エディター]** ビューの左側に表示されます。 各フォームは、入力する一連のフィールドで構成されています。 必須フィールドには赤いアスタリスク (*) が付いています。

 **マネージ アプリケーションを作成するための主なフォームは 4 つあります。**

    *   プラン設定
    *   SKU
    *   Marketplace
    *   サポート

以降のセクションでは、これらのフォームについて詳しく説明します。

## <a name="offer-settings-form"></a>[プランの設定] フォーム

プラン設定フォームは、プランの設定を指定する基本的なフォームです。 各種フィールドは次のとおりです。

* [プラン ID] - このフィールドは、発行者プロファイル内でそのプランを表す一意識別子です。 この ID は、製品 URL、Resource Manager テンプレート、課金レポートに表示されます。 小文字の英数字またはハイフン (-) のみで構成できます。 この ID はダッシュで終えることはできず、最大で 50 文字の長さにできます。 このフィールドは、プランの運用が開始されるとロックされます。
* [Publisher ID]\(発行者 ID\) - このフィールドのドロップダウンでは、このプランを発行するための発行者プロファイルを選択できます。 このフィールドは、プランの運用が開始されるとロックされます。
* [名前] - このフィールドは、プランの表示名です。 これは、Marketplace とポータルに表示される名前です。 最大で 50 文字の長さにできます。 製品の覚えやすいブランド名を含めることをお勧めします。 販売方法である場合を除き、会社名はここに含めないでください。 このプランを貴社の Web サイトでマーケティングしている場合は、この名前が貴社の Web サイトで表示される名前とまったく同じであることを確認してください。

**[保存]** を選択して進捗状況を保存します。 次の手順では、作成したプラン用に SKU を追加します。

## <a name="skus-form"></a>[SKU] フォーム

**[SKU]** フォームを選択します。 ここには、**[New SKU]\(新しい SKU\)** というオプションが表示されます。 このオプションを選択すると、**SKU ID** を入力できます。

![新しい SKU を選択する](./media/managed-application-author-marketplace/newOffer_skus.png)

**SKU ID** は、プラン内の SKU を表す一意識別子です。 この ID は、製品 URL、Resource Manager テンプレート、課金レポートに表示されます。 小文字の英数字またはハイフン (-) のみで構成できます。 この ID はダッシュで終えることはできず、最大で 50 文字の長さにできます。 このフィールドは、プランの運用が開始されるとロックされます。 プラン内の SKU を複数にすることができます。 発行しようとしているイメージごとに SKU が必要です。

**[New SKU]\(新しい SKU\)** を選択したら、次のフォームに入力する必要があります。

![新しい SKU を指定する](./media/managed-application-author-marketplace/newOffer_newsku.png)

### <a name="how-to-fill-sku-details-section"></a>SKU の詳細セクションの入力方法

* [タイトル] - この SKU のタイトルを入力します。 これが、ギャラリーでこの項目に対して表示されます。
* [Summary]\(概要\) - この SKU の簡単な概要を入力します。 このテキストは、タイトルのすぐ下に表示されます。
* [説明] - SKU に関する詳細な説明を入力します。
* [SKU Type]\(SKU の種類\) - 使用できる値は、**[Managed Application]\(マネージ アプリケーション\)** と **[ソリューション テンプレート]** です。 この場合は、**[Managed Application]\(マネージ アプリケーション\)** を選択します。

### <a name="how-to-fill-package-details-section"></a>パッケージの詳細のセクションの入力方法

パッケージのセクションには、入力する必要がある次のフィールドがあります。

![パッケージ](./media/managed-application-author-marketplace/newOffer_newsku_package.png)

**[現在のバージョン]** - アップロードするパッケージのバージョンを指定します。 `{number}.{number}.{number}{number}` 形式にする必要があります。

**パッケージ ファイル** - このパッケージには、.zip ファイルに圧縮されている次のファイルが含まれています。

* **applianceMainTemplate.json** - ソリューション/アプリケーションをデプロイするために使用されるデプロイ テンプレート ファイル。 デプロイ テンプレート ファイルの作成方法の詳細については、[初めての Azure Resource Manager テンプレートの作成](resource-manager-create-first-template.md)に関するページを参照してください。
* **appliancecreateUIDefinition.json** - このファイルは、このソリューション/アプリケーションをプロビジョニングするためのユーザー インターフェイスを生成するために Azure Portal によって使用されます。 詳細については、「[CreateUiDefinition の基本概念](managed-application-createuidefinition-overview.md)」を参照してください。
* **mainTemplate.json** - Microsoft.Solution/appliances リソースのみを含むテンプレート ファイル。 注意すべきこのリソースの主要なプロパティは次のとおりです。

mainTemplate には、次のプロパティが含まれています。

*  kind - Marketplace のマネージ アプリケーションの場合は **Marketplace** を使用してください。
*  ManagedResourceGroupId - applianceMainTemplate.json で定義されているすべてのリソースがデプロイされる、顧客のサブスクリプション内のリソース グループ。
*  PublisherPackageId - パッケージを一意に識別する文字列。 `{publisherId}.{OfferId}.{SKUID}.{PackageVersion}` の形式で値を指定します。
  publisherId と OfferId は、発行ポータルから取得できる場合があります。

  ![プラン ID](./media/managed-application-author-marketplace/UniqueString_pubid_offerid.png)
        
  SKU ID は、次の図で示されているように、取得できます。

  ![SKU ID](./media/managed-application-author-marketplace/UniqueString_skuid.png)
        
  パッケージ バージョンは、次の図で示されているように、取得できます。

  ![パッケージ バージョン](./media/managed-application-author-marketplace/UniqueString_packageversion.png)
    
  前の例を使用すると、**PublisherPackageId** の値は `azureappliance-test.ravmanagedapptest.ravpreviewmanagedsku.1.0.0` です。

  サンプルの mainTemplate.json:

  ```json
  {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "storageAccountNamePrefix": {
        "type": "string",
        "metadata": {
          "description": "Specify the name of the storage account"
        }
      },
      "storageAccountType": {
        "type": "string"
      }
    },
    "variables": {
      "managedResourceGroup": "[concat(resourceGroup().id,uniquestring(resourceGroup().id))]"
    },
    "resources": [{
      "type": "Microsoft.Solutions/appliances",
      "apiVersion": "2016-09-01-preview",
      "name": "[concat(parameters('storageAccountNamePrefix'), '-', 'managed')]",
      "location": "[resourceGroup().location]",
      "kind": "marketplace",
      "properties": {
        "managedResourceGroupId": "[variables('managedResourceGroup')]",
        "PublisherPackageId":"azureappliancetest.ravmanagedapptest.ravpreviewmanagedsku.1.0.0",
        "parameters": {
          "storageAccountName": {
            "value": "[parameters('storageAccountNamePrefix')]"
          },
          "storageAccountType": {
            "value": "[parameters('storageAccountType')]"
          }
        }
      }
    }],
    "outputs": {

    }
  }
  ```

このパッケージには、このアプリケーションを正常にプロビジョニングするために必要となる、その他の入れ子になったテンプレートまたはスクリプトを含める必要があります。 mainTemplate.json、applianceMainTemplate.json、applianceCreateUIDefinition.json は、ルート フォルダーに存在する必要があります。

**Authorizations** - このプロパティは、顧客のサブスクリプション内のリソースにアクセスできる人とそのアクセス レベルを定義します。 このプロパティを使用すると、発行者が顧客に代わってアプリケーションを管理できます。

* PrincipalId - このプロパティは、顧客のサブスクリプション内のリソースに対して特定のアクセス許可を付与されている (Role Definition で表される) ユーザー、ユーザー グループ、またはアプリケーション の Azure Active Directory 識別子です。
* Role Definition - このプロパティは、Azure AD によって提供されている組み込み RBAC ロールすべての一覧です。 顧客に代わってリソースを管理できるようにする、最も適切なロールを選択できます。

複数の承認を追加することができます。 ただし、Active Directory ユーザー グループを作成し、その ID を **PrincipalId** で指定することをお勧めします。 これにより、SKU を更新する必要なく、ユーザー グループにより多くのユーザーを追加することが可能になります。

RBAC の詳細については、「[Azure Portal でのロールベースの Access Control の基礎を確認する](../active-directory/role-based-access-control-what-is.md)」を参照してください。

## <a name="marketplace-form"></a>[Marketplace] フォーム

[Marketplace] フォームでは、[Azure Marketplace](https://azuremarketplace.microsoft.com) と [Azure Portal](https://portal.azure.com/) に表示するフィールドについて確認します。

### <a name="preview-subscription-ids"></a>サブスクリプション ID をプレビュー

ここに、発行後にプランにアクセスできるようにする Azure サブスクリプション ID の一覧を入力します。 これらのホワイトリストに追加されたサブスクリプションにより、プレビューされたプランを、運用開始前にテストできます。 パートナー ポータルでは、最大 100 のサブスクリプションをホワイトリストに追加できます。

### <a name="suggested-categories"></a>推奨されるカテゴリ

入力されたリストから、プランとの最適な関連付けができるカテゴリを 5 つまで選択します。 選択したカテゴリは、[Azure Marketplace](https://azuremarketplace.microsoft.com) と[ポータル](https://portal.azure.com/)で利用可能な製品カテゴリにプランをマッピングするために使用されます。

#### <a name="azure-marketplace"></a>Azure Marketplace

マネージ アプリケーションのサマリーには、次のフィールドが表示されます。

![Marketplace のサマリー](./media/managed-application-author-marketplace/publishvm10.png)

マネージ アプリケーションの概要には、次のフィールドが表示されます。

![Marketplace の概要](./media/managed-application-author-marketplace/publishvm11.png)

マネージ アプリケーションのプランと価格には、次のフィールドが表示されます。

![Marketplace のプラン](./media/managed-application-author-marketplace/publishvm15.png)

#### <a name="azure-portal"></a>Azure ポータル

マネージ アプリケーションのサマリーには、次のフィールドが表示されます。

![ポータルの要約](./media/managed-application-author-marketplace/publishvm12.png)

マネージ アプリケーションの概要には、次のフィールドが表示されます。

![ポータルの概要](./media/managed-application-author-marketplace/publishvm13.png)

#### <a name="logo-guidelines"></a>ロゴのガイドライン

クラウド パートナー ポータルにアップロードされるすべてのロゴには、以下のガイドラインを使用してください。

*   Azure の設計には簡単なカラー パレットがあります。 ロゴのプライマリ カラーとセカンダリ カラーの数は少なくしてください。
*   ポータルのテーマの色は白黒です。 したがって、この色を、ロゴの背景色として使用しないでください。 ポータルでロゴが目立つ色を背景色として使用します。 背景色としてはシンプルなプライマリ カラーをお勧めします。 **透明な背景を使用している場合は、ロゴ/テキストが白、黒または青ではないことを確認します。**
*   ロゴではグラデーションの背景を使用しないでください。
*   ロゴにはテキストを使用しないでください (会社またはブランドの名前であっても)。 ロゴのルック アンド フィールは "フラット" にする必要があり、グラデーションは避ける必要があります。
*   ロゴが伸縮されていないことを確認してください。

#### <a name="hero-logo"></a>Hero ロゴ

Hero ロゴはオプションです。 パブリッシャーは、Hero ロゴをアップロードしなくてもかまいません。 ただし、アップロードされたら、Hero アイコンは削除できません。 その時点で、パートナーは、Hero アイコンの Marketplace ガイドラインに従う必要があります。

##### <a name="guidelines-for-the-hero-logo-icon"></a>Hero ロゴ アイコンに関するガイドライン

*   発行者表示名、SKU のタイトル、およびプランの長い概要は、白のフォント色で表示されます。 このため、Hero アイコンの背景色には明るい色を使用しないでください。 黒、白、透明は Hero アイコンの背景では使用できません。
*   プランが表示されると、パブリッシャーの表示名、SKU のタイトル、プランの長い概要、作成ボタンが、Hero ロゴ内にプログラムによって埋め込まれます。 したがって、Hero ロゴのデザイン時にはテキストを入力しないでください。 右側にスペースを空けておくだけで、テキスト (つまり、発行者の表示名、プランのタイトル、プランの長い概要) がプログラムによってその場所に挿入されます。 右側のテキスト用の空のスペースは 415 x 100 です (また、左から 370 ピクセルだけオフセットされます)。

![publishvm14](./media/managed-application-author-marketplace/publishvm14.png)

## <a name="support-form"></a>[サポート] フォーム

次に記入するフォームは、サポート フォームです。 このフォームには、エンジニアリング連絡先情報やカスタマー サポート連絡先情報など、あなたの会社のサポート連絡先を記入します。

## <a name="how-to-publish-an-offer"></a>プランの発行方法

すべてのセクションを完了したら、**[発行]** を選択して、このプランを顧客が使用できるようにするプロセスを開始します。

## <a name="next-steps"></a>次のステップ

* マネージ アプリケーションの概要については、「[Azure マネージ アプリケーションの概要](managed-application-overview.md)」を参照してください。
* Marketplace からマネージ アプリケーションを使用する方法については、「[Consume Azure managed applications in the Marketplace (Marketplace での Azure マネージ アプリケーションの使用)](managed-application-consume-marketplace.md)」を参照してください。
* サービス カタログ マネージ アプリケーションの発行については、[サービス カタログ向けマネージ アプリケーションの作成と発行](managed-application-publishing.md)に関するページを参照してください。
* サービス カタログ マネージ アプリケーションの使用については、[サービス カタログ マネージ アプリケーションの使用](managed-application-consumption.md)に関するページを参照してください。

