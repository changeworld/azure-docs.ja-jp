---
title: ソリューション テンプレートを発行する | Microsoft Docs
description: Azure Marketplace にソリューション テンプレートを発行します。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: pbutlerm
ms.openlocfilehash: ded952ac6418ae3d9916b3ae8b8dbacb0c9c5a84
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2018
ms.locfileid: "48807766"
---
# <a name="publish-a-solution-template-to-azure-marketplace"></a>Azure Marketplace にソリューション テンプレートを発行する

この記事では、Azure Marketplace にソリューション テンプレート プランを発行する手順について説明します。

## <a name="prerequisites"></a>前提条件

次の技術的な前提条件と技術以外の前提条件は、Azure Marketplace へのソリューション テンプレートの登録に適用されます。

### <a name="technical"></a>技術

- [Azure Resource Manager テンプレートについての理解](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)

- Azure クイック スタート テンプレート:

    - [Azure クイック スタート テンプレート ドキュメント](https://azure.microsoft.com/documentation/templates/)

    - [GitHub 上の Azure クイック スタート ドキュメント](https://github.com/azure/azure-quickstart-templates)

 - [Azure portal ユーザー インターフェイス ファイルの作成](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)

### <a name="non-technical-business-requirements"></a>技術面以外 (ビジネス要件)

-   貴社 (またはその子会社) は、Azure Marketplace によってサポートされる販売元の国に所在している必要があります。

-   Azure Marketplace でサポートされている課金モデルに対応した方法で、製品のライセンスを取得する必要があります。

-   無償、有償、コミュニティ サポートの活用を問わず、商業的に合理的な方法で、顧客に技術サポートを提供していただきます。

-   貴社のソフトウェアおよび依存関係にあるすべてのサード パーティ ソフトウェアのライセンス供与を行っていただきます。

-   Azure Marketplace および Azure の管理ポータルでプランが一覧表示されるための条件に適合するコンテンツを提供する必要があります。

-   Azure Marketplace 参加ポリシーと発行元契約の条項に同意します。

-   使用条件、Microsoft のプライバシーに関する声明、および Microsoft Azure 認定プログラム契約に従うことに同意します。

## <a name="before-you-begin"></a>開始する前に

すべての前提条件を満たしたら、ソリューション テンプレート プランの作成を開始できます。 開始する前に、プランと SKU に関する次の情報を確認しておいてください。

**プラン**

Azure アプリケーション プランは、発行元から提供される製品のクラスに相当します。 Azure Marketplace で新しい種類のソリューションまたはアプリケーションを提供したい場合は、新しいプランが最適な方法となります。 プランとは、SKU のコレクションです。 各プランは、Azure Marketplace で独自のエンティティとして表示されます。

**SKU**

SKU とは、プランの購入可能な最小単位です。 同じ製品クラス (プラン) 内では、サポートされているさまざまな機能を SKU によって区別できます。 たとえば、プランにはマネージド プランとアンマネージド プランがあり、サポートされる課金モデルも多様です。

次のシナリオでは、複数の SKU を追加します。
- ライセンス持ち込み (BYOL)、従量課金制 (PAYG) など、異なる課金モデルをサポートしたい。
- 各 SKU で異なる機能セットがサポートされ、機能セットごとに価格が異なる。

SKU は Azure Marketplace では親プランの下に表示されますが、Azure portal ではその購入可能エンティティとして表示されます。

## <a name="to-create-a-new-offer"></a>新しいプランを作成する

1.  [クラウド パートナー ポータル](http://cloudpartner.azure.com/)にサインインします。

2.  左側のナビゲーション バーで、**[+ 新しいプラン]** を選択し、**[Azure Applications]\(Azure アプリケーション\)** を選択します。

    ![新しいオファーを作成する](./media/cloud-partner-portal-publish-managed-app/newOffer.png)

3.  **[新しいプラン]** の **[エディター]** を選択します。

    ![新しいプランのエディター](./media/cloud-partner-portal-publish-managed-app/newOffer_OfferSettings.png)

4.  **[エディター]** で、次の各ビューに情報を入力します。
    - プラン設定
    - SKU
    - マーケットプレース
    - サポート 各ビューには、入力すべき一連のフィールドが表示されます。必須フィールドには赤いアスタリスク (\*) が付いています。

## <a name="to-configure-offer-settings"></a>プランの設定を構成する

1. [プランの設定] の **[Offer Identity]\(プラン ID\)** で次の各フィールドを構成します。

    **[プラン ID]**

     発行元プロファイル内のプランを表す一意識別子です。 この ID は、製品 URL、ARM テンプレート、課金レポートに表示されます。 小文字の英数字またはダッシュ (-) のみ使用できます。 この ID はダッシュで終えることはできず、長さは最大で 50 文字です。 
    >[!Note]
    >このフィールドは、プランの運用が開始されるとロックされます。

    **[発行元 ID]**

    発行元プロファイルのドロップダウン リスト。 お客様が発行するプランの発行元となるプロファイルを選択します。 
    >[!Note]
    >このフィールドは、プランの運用が開始されるとロックされます。

    **名前**

    プランの表示名です。 この名前は Azure Marketplace と Azure portal に表示されます。 最大で 50 文字の長さにできます。 プランの名前に関する次のガイダンスに従ってください。
    -  製品の覚えやすいブランド名を使用してください。 
    - 会社名はここに含めないでください (プランのマーケティングの一環である場合を除く)。
    - このプランを貴社の Web サイトでマーケティングしている場合は、この名前が、貴社の Web サイトで表示される名前とまったく同じであることを確認します。

2. **[保存]** を選択して、[プランの設定] を終了します。
オファーの拡張

## <a name="to-create-skus"></a>SKU を作成する
------------------

1. **[SKU]** を選択します。 

    ![新しい SKU](./media/cloud-partner-portal-publish-managed-app/newOffer_skus.png)

    SKU ID は、プラン内の SKU を表す一意識別子です。 この ID は、製品 URL、ARM テンプレート、課金レポートに表示されます。 SKU ID は、
    - 最大で 50 文字の長さにできます。
    - 小文字の英数字またはダッシュ (-) のみで構成できます。
    - ID の末尾にはダッシュを使用できません。

    >[!Note]
    >追加された SKU は、[SKU] ビュー内の SKU の一覧に表示されます。 SKU の詳細を確認するには、SKU 名を選択します。 

2. **[新しい SKU]** を選択して、次のキャプチャ画面に示されている情報を入力します。 

    ![SKU の詳細](./media/cloud-partner-portal-publish-managed-app/newOffer_newsku.png)


### <a name="sku-settings"></a>SKU の設定

次の SKU の設定を指定します。

- **[タイトル]** - SKU のタイトル。 このタイトルは、ギャラリーでこの項目に対して表示されます。

- **[概要]** - SKU の概要の簡単な説明  (最大文字数は 100 文字です)。

- **[説明]** - SKU の詳細な説明。

- **[SKU の種類]** - [Managed Application (Preview)]\(マネージド アプリケーション (プレビュー)\) または [Solution Template]\(ソリューション テンプレート\) を値として選択できるドロップダウン リスト。 このシナリオでは、**[Solution Template]\(ソリューション テンプレート\)** を選択します。

- **[Cloud Availability]\(クラウドの可用性\)** - SKU の場所。 既定値は [パブリック Azure] です。

### <a name="package-details"></a>パッケージの詳細

SKU の設定が完了したら、次のパッケージの詳細情報を指定します。

![パッケージの詳細](./media/cloud-partner-portal-publish-managed-app/newOffer_newsku_ST_package.png)

- **[現在のバージョン]** - アップロードするパッケージのバージョン。 次のような形式になっています。

- **[パッケージ ファイル]** - このパッケージには、次のファイルが含まれています (ファイルは .zip ファイルに保存されています)。

    -   MainTemplate.json - デプロイ テンプレート ファイル。ソリューションまたはアプリケーションをデプロイしたり、そのソリューションに対して定義されたリソースを作成したりするために使用されます。 詳細については、[デプロイ テンプレート ファイルの作成方法](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)に関するページを参照してください。

    -   createUIDefinition.json - このファイルは、このソリューションまたはアプリケーションをプロビジョニングするためのユーザー インターフェイスを生成するために Azure portal によって使用されます。 詳細については、「[マネージド アプリケーション用の Azure portal のユーザー インターフェイスを作成する](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)」を参照してください。

    >[!IMPORTANT]
    >このパッケージには、このアプリケーションをプロビジョニングするために必要となる、その他の入れ子になったテンプレートまたはスクリプトを含める必要があります。 mainTemplate.json と createUIDefinition.json は、ルート フォルダーに存在する必要があります。

## <a name="to-configure-the-marketplace"></a>Marketplace を構成する

[Azure Marketplace](https://azuremarketplace.microsoft.com) および [Azure portal](https://portal.azure.com/) に表示されるプランのフィールドを [Marketplace] ビューを使用して構成します。

### <a name="preview-subscription-ids"></a>サブスクリプション ID をプレビュー

発行されたプランへのアクセスを許可する Azure サブスクリプション ID の一覧です。 これらのホワイトリストに追加されたサブスクリプションにより、プレビューされたオファーを、運用開始前にテストできます。 パートナー ポータルでは、最大 100 サブスクリプションをホワイトリストに追加できます。

### <a name="suggested-categories"></a>推奨されるカテゴリ

提供されたリストから、プランとの最適な関連付けができるカテゴリを 5 つまで選択します。 選択したカテゴリは、[Azure Marketplace](https://azuremarketplace.microsoft.com) と [Azure Portal](https://portal.azure.com/) で利用可能な製品カテゴリにプランをマッピングするために使用されます。

Azure Marketplace と Azure portal に表示されるマーケットプレース情報の例を次に示します。

**Azure Marketplace**

![publishvm10](./media/cloud-partner-portal-publish-managed-app/publishvm10.png)


![publishvm11](./media/cloud-partner-portal-publish-managed-app/publishvm11.png)


![publishvm15](./media/cloud-partner-portal-publish-managed-app/publishvm15.png)


**Azure Portal**


![publishvm12](./media/cloud-partner-portal-publish-managed-app/publishvm12.png)


![publishvm13](./media/cloud-partner-portal-publish-managed-app/publishvm13.png)


### <a name="logo-guidelines"></a>ロゴのガイドライン

Cloud パートナー ポータルにアップロードされるロゴについては、次のガイドラインに従ってください。

-   Azure の設計には簡単なカラー パレットがあります。 ロゴのプライマリ カラーとセカンダリ カラーの数は少なくしてください。

-   Azure ポータルのテーマの色は白黒です。 これらの色を、ロゴの背景色として使用しないでください。 Azure portal でロゴが目立つ色を使用してください。 背景色としてはシンプルなプライマリ カラーをお勧めします。

    >[!Note] 
    >透明な背景を使用している場合は、ロゴとテキストが白、黒または青ではないことを確認します。

-   ロゴではグラデーションの背景を使用しないでください。

-   ロゴにテキストを配置しないでください。 これは会社名やブランド名であっても同様です。 ロゴのルック アンド フィールは "*フラット*" にする必要があり、グラデーションは避ける必要があります。

-   ロゴは拡大しないでください。

#### <a name="hero-logo"></a>Hero ロゴ

Hero ロゴはオプションです。 パブリッシャーは、Hero ロゴをアップロードしなくてもかまいません。 ただし、いったんアップロードしたロゴは削除できません。 パートナーは、Hero アイコンの Azure Marketplace ガイドラインに従う必要があります。

#### <a name="guidelines-for-the-hero-logo-icon"></a>Hero ロゴ アイコンに関するガイドライン

-   発行元の表示名、プランのタイトル、およびプランの長い概要は、白色のフォントで表示されます。 背景には明るい色を使用しないでください。 黒、白、透明は Hero アイコンの背景では使用できません。

-   プランが表示されると、発行元の表示名、プランのタイトル、プランの長い概要、作成ボタンが、Hero ロゴ内にプログラムによって埋め込まれます。 Hero ロゴのデザイン時にはテキストを入力しないでください。 ロゴの右側のスペースは空けておいてください。 このスペースは 415 x 100 ピクセルで、左から 370 ピクセルだけオフセットされます。

![publishvm14](./media/cloud-partner-portal-publish-managed-app/publishvm14.png)

## <a name="to-configure-support"></a>サポートを構成する

[サポート] ビューを使用して次の情報を指定します。

- 貴社のサポート連絡先 (エンジニアリングなど)。
- カスタマー サポートの連絡先。

## <a name="to-publish-the-offer"></a>プランを発行する

最後の手順で、プランを発行します。 **[発行]** を選択します。
