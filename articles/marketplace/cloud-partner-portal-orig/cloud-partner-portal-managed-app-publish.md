---
title: Azure マネージド アプリケーションを Azure Marketplace に発行する
description: Azure マネージド アプリケーションを Azure Marketplace に発行する
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: qianw211
manager: pbutlerm
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: bc044c8b59c939163336ecab01546fc26a7a2643
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2018
ms.locfileid: "48808026"
---
<a name="publish-an-azure-managed-application-to-azure-marketplace"></a>Azure マネージド アプリケーションを Azure Marketplace に発行する 
========================================================

この記事では、Azure Marketplace へのマネージド アプリケーション オファーの発行に関連するさまざまな手順を示します。

<a name="pre-requisites-for-publishing-a-managed-application"></a>マネージド アプリケーションを発行するための前提条件 
---------------------------------------------------

Azure Marketplace で一覧表示するための前提条件

1.  技術

    -   [Azure リソース マネージャーのテンプレートの作成](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)

    -   Azure クイック スタート テンプレート:

        -   <https://azure.microsoft.com/documentation/templates/>

        -   <https://github.com/azure/azure-quickstart-templates>

    -   UI 定義を作成する

        -   [ユーザー インターフェイス定義ファイルを作成する](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)

2.  技術面以外 (ビジネス要件)

    -   貴社 (またはその子会社) は、Azure Marketplace によってサポートされる販売元の国に所在している必要があります。

    -   Azure Marketplace でサポートされている課金モデルと互換性がある方法で、製品のライセンスを取得する必要があります

    -   無料、有料、またはコミュニティ サポート利用のテクニカル サポートを顧客に提供する責任があります。

    -   貴社のソフトウェアおよび依存関係にあるすべてのサード パーティ ソフトウェアのライセンス供与を行っていただきます。

    -   Azure Marketplace および Azure 管理ポータルでプランが一覧表示されるための条件に適合するコンテンツを提供する必要があります

    -   Azure Marketplace 参加ポリシーとパブリッシャー契約の条項に同意する必要があります

    -   使用条件、Microsoft のプライバシーに関する声明、および Microsoft Azure 認定プログラム契約に従うことに同意する必要があります。

<a name="how-to-create-a-new-azure-application-offer"></a>新しい Azure アプリケーション プランを作成する方法 
-------------------------------------------

すべての前提条件を満たしたら、マネージド アプリケーション オファーの作成を開始できます。 開始する前に、プランと SKU の概要を紹介します。

**プラン**

Azure アプリケーション オファーは、発行元から提供される製品のクラスに相当します。 Azure Marketplace に発行する新しいソリューション/アプリケーションがある場合、新しいオファーがその方法です。 プランとは、SKU のコレクションです。 各プランは、Azure Marketplace で独自のエンティティとして表示されます。

**SKU**

SKU とは、購入可能な最小単位のオファーです。 同じ製品クラス (オファー) 内では、SKU により、サポートされる各種機能、オファーが管理対象か管理対象外か、課金モデルを区別できます。

ライセンス持ち込み (BYOL)、従量課金制 (PAYG) など、異なる課金モデルをサポートしたい場合は、複数の SKU を追加します。 

各 SKU が異なる機能セットをサポートし、それぞれの価格が異なる場合は、複数の SKU を追加します。

SKU は Azure Marketplace では親オファーの下に表示されますが、azure.com ではその購入可能エンティティとして表示されます。

1.  [クラウド パートナー ポータル](http://cloudpartner.azure.com)にサインインします。

2.  左側のナビゲーション バーから、\"[+ 新しいプラン]\" をクリックし、\"[Azure Applications]\(Azure アプリケーション\)\" を選択します。

    ![新しいオファー](./media/cloud-partner-portal-publish-managed-app/newOffer.png)

3.  これで新しいオファーの \"エディター\" ビューが開いて、作成を開始できるようになります。

4.  入力が必要な \"フォーム\" は、\"エディター\" ビューの左側に表示されます。 各 \"フォーム\" は、入力する一連のフィールドで構成されています。必須フィールドには赤いアスタリスク (\*) が付いています。

    > マネージド アプリケーションを作成するための主なフォームは 4 つあります

    -   プラン設定

    -   SKU

    -   マーケットプレース

    -   サポート

<a name="how-to-fill-out-the-offer-settings-form"></a>プラン設定フォームに記入する方法 
---------------------------------------

プラン設定フォームは、プランの設定を指定する基本的なフォームです。
さまざまなフィールドを以下で説明します。

**Offer ID (オファー ID)**

発行元プロファイル内のオファーを表す一意識別子です。
製品 URL、Resource Manager テンプレート、課金レポートに表示されます。 小文字の英数字またはダッシュ (-) のみ使用できます。 ダッシュで終えることはできず、最大 50 文字です。 このフィールドは、プランの運用が開始されるとロックされます。

**Publisher ID (パブリッシャー ID)**

このドロップダウンでは、このプランを発行するためのパブリッシャー プロファイルを選択することができます。 このフィールドは、プランの運用が開始されるとロックされます。

**名前**

オファーの表示名です。 この名前が Azure Marketplace と Azure portal に表示されます。 最大で 50 文字の長さにできます。 製品の覚えやすいブランド名を含めることをお勧めします。 販売方法である場合を除き、会社名はここに含めないでください。 このプランを貴社の Web サイトでマーケティングしている場合は、この名前が貴社の Web サイトで表示される名前とまったく同じであることを確認してください。

\"[保存]\" をクリックして進捗状況を保存します。 次の手順では、プランに SKU を追加します。

<a name="how-to-create-skus"></a>SKU の作成方法 
------------------

\"SKU\" フォームをクリックします。 ここで表示される \"SKU の追加\" オプションをクリックすると、\"SKU ID\" を入力することができます。

![新しいオファーの SKU](./media/cloud-partner-portal-publish-managed-app/newOffer_skus.png)

\"SKU ID\" は、プラン内の SKU を表す一意識別子です。 この ID は、製品 URL、ARM テンプレート、および課金レポートに表示されます。 小文字の英数字またはハイフン (-) のみで構成できます。
ダッシュで終えることはできず、最大で 50 文字の長さにできます。 このフィールドは、プランの運用が開始されるとロックされます。 プラン内の SKU を複数にすることができます。 発行しようとしているイメージごとに SKU が必要です。

SKU が追加されると、\"SKU\" フォーム内の SKU の一覧に表示されます。 SKU 名をクリックすると、その SKU の詳細が表示されします。 いくつかのフィールドの詳細を次に示します。

[新しい SKU] をクリックした後、次のフォームに入力する必要があります。

![新しいオファー - 新しい SKU](./media/cloud-partner-portal-publish-managed-app/newOffer_newsku.png)

### <a name="how-to-fill-sku-details-section"></a>SKU の詳細セクションの入力方法 

**[タイトル]** - この SKU のタイトルを入力します。 これが、ギャラリーでこの項目に対して表示されます。

**[Summary]\(概要\)** - この SKU の簡単な概要を入力します。 このテキストは、タイトルのすぐ下に表示されます。

**[説明]** - SKU に関する詳細な説明を入力します。

**[Sku Type]\(SKU の種類\)** - 使用できる値は、[Managed Application]\(マネージド アプリケーション\) と [ソリューション テンプレート] です。 この場合は、[Managed Application]\(マネージド アプリケーション\) を選択します。

### <a name="how-to-fill-package-details-section"></a>パッケージの詳細のセクションの入力方法 

パッケージのセクションには、入力する必要がある次のフィールドがあります。

![新しいオファー - 新しい SKU パッケージ](./media/cloud-partner-portal-publish-managed-app/newOffer_newsku_package.png)

**[現在のバージョン]** - アップロードするパッケージのバージョンを指定します。
次のような形式になっています。

**パッケージ ファイル** - パッケージには、.zip ファイルに圧縮されている次のファイルが含まれています。

applianceMainTemplate.json - デプロイ テンプレート ファイル。ソリューション/アプリケーションをデプロイしたり、その中で定義されたリソースを作成したりする目的で使用されます。 デプロイ テンプレート ファイルの作成方法について詳しくは、<https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template> をご覧ください

applianceCreateUIDefinition.json - このファイルは、このソリューション/アプリケーションをプロビジョニングするためのユーザー インターフェイスを生成するために azure.com のポータルによって使用されます。 このファイルの作成方法について詳しくは、<https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview> をご覧ください

mainTemplate.json - Microsoft.Solution/appliances リソースのみを含むテンプレート ファイル。 注意すべきこのリソースの主要なプロパティは次のとおりです。

-   \"kind\": Marketplace マネージド アプリケーションのシナリオの場合、値は \"Marketplace\" にする必要があります
-   \"ManagedResourceGroupId\": applianceMainTemplate.json で定義されているすべてのリソースがデプロイされる、顧客のサブスクリプション内のリソース グループ。
-   \"PublisherPackageId\": パッケージを一意に識別する文字列。 

この値は、<パブリッシャー ID>.<オファー ID>-preview<SKUID>.<パッケージ バージョン> のように連結して作成する必要があります。
これらの各値は、次に示すように取得できます。

このパッケージには、このアプリケーションを正常にプロビジョニングするために必要となる、その他の入れ子になったテンプレートまたはスクリプトを含める必要があります。 mainTemplate.json、applianceMainTemplate.json、applianceCreateUIDefinition.json は、ルート フォルダーに存在する必要があります。

**Authorizations** - このプロパティは、顧客のサブスクリプション内のリソースにアクセスできる人とそのアクセス レベルを定義します。 これを使用すると、発行者が顧客に代わってアプリケーションを管理できます。

-   PrincipalId - 顧客のサブスクリプション内のリソースに対して特定のアクセス許可を付与されている (Role Definition で表される) ユーザー、ユーザー グループ、またはアプリケーション の Azure Active Directory 識別子です。

-   Role Definition - Azure AD によって提供されている組み込み RBAC ロールすべての一覧です。 顧客に代わってリソースを管理できるようにする、最も適切なロールを選択できます。

複数の承認を追加することができます。 ただし、Active Directory ユーザー グループを作成し、その ID を \"PrincipalId\. で指定することをお勧めします。  これにより、SKU を更新する必要なく、ユーザー グループにより多くのユーザーを追加することが可能になります。

RBAC について詳しくは、<https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is> をご覧ください

<a name="marketplace-form"></a>Marketplace フォーム
----------------

Azure アプリケーション オファー内の Marketplace フォームは、[Azure Marketplace](https://azuremarketplace.microsoft.com) と [Azure Portal](https://portal.azure.com/) で表示されるフィールドについて確認します。 いくつかのフィールドの詳細を次に示します。

#### <a name="preview-subscription-ids"></a>サブスクリプション ID をプレビュー

ここに、発行後にオファーにアクセスできるようにする Azure サブスクリプション ID の一覧を入力します。 これらのホワイトリストに追加されたサブスクリプションにより、プレビューされたオファーを、運用開始する前にテストできます。 パートナー ポータルでは、最大 100 サブスクリプションをホワイトリストに追加できます。

#### <a name="suggested-categories"></a>推奨されるカテゴリ

入力されたリストから、プランとの最適な関連付けができるカテゴリを 5 つまで選択します。 選択したカテゴリは、[Azure Marketplace](https://azuremarketplace.microsoft.com) と [Azure Portal](https://portal.azure.com/) で利用可能な製品カテゴリにプランをマッピングするために使用されます。

このフォームで指定するデータが表示される場所のいくつかを、次に示します。

##### <a name="azure-marketplace"></a>Azure Marketplace

![publishvm10](./media/cloud-partner-portal-publish-managed-app/publishvm10.png)

![publishvm11](./media/cloud-partner-portal-publish-managed-app/publishvm11.png)

![publishvm15](./media/cloud-partner-portal-publish-managed-app/publishvm15.png)

##### <a name="portal-at-azurecom"></a>azure.com のポータル

![publishvm12](./media/cloud-partner-portal-publish-managed-app/publishvm12.png)

![publishvm13](./media/cloud-partner-portal-publish-managed-app/publishvm13.png)

##### <a name="logo-guidelines"></a>ロゴのガイドライン

クラウド パートナー ポータルにアップロードされるすべてのロゴは、以下のガイドラインに従っている必要があります。

-   Azure の設計には簡単なカラー パレットがあります。 ロゴのプライマリ カラーとセカンダリ カラーの数は少なくしてください。

-   ポータル azure.com のテーマの色は白黒です。 この色を、ロゴの背景色として使用しないでください。 ポータル azure.com でロゴが目立つ色を背景色として使用します。
    背景色としてはシンプルなプライマリ カラーをお勧めします。 **透明な背景を使用している場合は、ロゴとテキストが白、黒または青ではないことを確認します。**

-   ロゴではグラデーションの背景を使用しないでください。

-   ロゴにはテキストを使用しないでください (会社またはブランドの名前であっても)。
    ロゴのルック アンド フィールは "フラット" にする必要があり、グラデーションは避ける必要があります。

-   ロゴを伸縮しないでください。

##### <a name="hero-logo"></a>Hero ロゴ

Hero ロゴはオプションです。 パブリッシャーは、Hero ロゴをアップロードしなくてもかまいません。 ただし、アップロードされたら、Hero アイコンは削除できません。 その場合、パートナーは、Hero アイコンの Azure Marketplace ガイドラインに従う必要があります。

###### <a name="guidelines-for-the-hero-logo-icon"></a>Hero ロゴ アイコンに関するガイドライン

-   発行者表示名、プランのタイトル、およびオファーの長い概要は、白のフォント色で表示されます。 Hero アイコンの背景色には明るい色を使用しないでください。 黒、白、透明は Hero アイコンの背景では使用できません。

-   オファーが一覧表示されるときに、発行元の表示名、プランのタイトル、プランの長い概要、作成ボタンが、Hero ロゴ内にプログラムによって埋め込まれます。 したがって、Hero ロゴのデザイン時にテキストを入力する必要はありません。 パブリッシャーの表示名、プランのタイトル、オファーの長い概要などの右側には空白を設けておいてください。プログラムで組み込まれます。
    右側のテキスト用の空のスペースは 415 x 100 で、左から 370 ピクセルだけオフセットされます。

![publishvm14](./media/cloud-partner-portal-publish-managed-app/publishvm14.png)

<a name="support-form"></a>サポート フォーム
------------

次に記入するフォームは、サポート フォームです。 このフォームでは、貴社のサポート連絡先が要求されます。  たとえば、エンジニアリング連絡先情報や顧客サポートの連絡先情報などです。

<a name="how-to-publish-an-offer"></a>プランの発行方法
-----------------------

オファーの草案が完成したら、次はオファーを Azure Marketplace に発行します。 \"発行\" ボタンをクリックすると、オファーをライブにするプロセスが始まります。
