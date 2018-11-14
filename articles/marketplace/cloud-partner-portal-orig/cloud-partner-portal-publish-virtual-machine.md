---
title: 仮想マシン プランを作成する | Microsoft Docs
description: 仮想マシンを Azure Marketplace に発行します。
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
ms.date: 09/18/2018
ms.author: pbutlerm
ms.openlocfilehash: d77dad52e75253de02fd079d791861356c4c5e1f
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2018
ms.locfileid: "51247077"
---
# <a name="publish-a-virtual-machine-to-azure-marketplace"></a>Azure Marketplace への仮想マシンの発行

この記事では、Azure Marketplace に仮想マシン プランを公開するために必要な手順について説明します。

## <a name="prerequisites"></a>前提条件

次に示した技術的な前提条件と技術以外の前提条件は、Azure Marketplace への仮想マシンの発行に対して適用されます。

### <a name="technical"></a>技術

-   [Azure Marketplace 向け仮想マシン イメージを作成するための技術的な前提条件](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-prerequisites)

-   [Linux VHD の作成とアップロード](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-create-upload-generic?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

-   [イメージからの Linux VM の作成とテスト](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-upload-vhd)

-   [Windows VHD の作成とアップロード ](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-prepare-for-upload-vhd-image?toc=/azure/virtual-machines/windows/toc.json)

-   [イメージからの Windows VM の作成とテスト](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-create-vm-generalized-managed?toc=/azure/virtual-machines/windows/toc.json)

-   [VHD 作成中に発生する一般的な問題をトラブルシューティングする方法](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-troubleshooting)

-   [Azure Marketplace イメージのセキュリティに関する推奨事項](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images)


### <a name="non-technical-business-requirements"></a>技術面以外 (ビジネス要件)

 -   貴社 (またはその子会社) は、Azure Marketplace によってサポートされる販売元の国に所在していること。

-   Azure Marketplace でサポートされている課金モデルに対応した方法で、製品のライセンスを取得する必要があります。

-   商取引上合理的な方法で、顧客に技術サポートを提供していただきます。 無償、有償、コミュニティ サポートの活用など、方法は問いません。

-   貴社のソフトウェアおよび依存関係にあるすべてのサード パーティ ソフトウェアのライセンス供与を行っていただきます。

-   Azure Marketplace および Azure 管理ポータルでプランが一覧表示されるための条件に適合するコンテンツを提供すること。

-   Azure Marketplace 参加ポリシーと発行者契約の条項に同意すること。

-   [使用条件](https://azure.microsoft.com/support/legal/website-terms-of-use/)、[Microsoft のプライバシーに関する声明](https://www.microsoft.com/privacystatement/default.aspx)、および [Microsoft Azure 認定プログラム契約](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/)に従うことに同意すること。

## <a name="before-you-begin"></a>開始する前に

すべての前提条件を満たしたら、ソリューション テンプレート プランの作成に着手できます。 開始する前に、プランと SKU に関する次の情報を確認しておいてください。

**プラン**

Azure アプリケーション プランは、発行元から提供される製品のクラスに相当します。 Azure Marketplace で新しい種類のソリューションまたはアプリケーションを提供したい場合は、新しいプランが最適な方法となります。 プランとは、SKU のコレクションです。 各プランは、Azure Marketplace で独自のエンティティとして表示されます。

**SKU**

SKU とは、プランの購入可能な最小単位です。 同じ製品クラス (プラン) 内では、サポートされているさまざまな機能を SKU によって区別できます。 たとえば、プランにはマネージド プランとアンマネージド プランがあり、サポートされる課金モデルも多様です。

次のシナリオでは、複数の SKU を追加します。
- ライセンス持ち込み (BYOL)、従量課金制 (PAYG) など、異なる課金モデルをサポートしたい。
- 各 SKU で異なる機能セットがサポートされ、機能セットごとに価格が異なる。

SKU は Azure Marketplace では親プランの下に表示されますが、Azure portal ではその購入可能エンティティとして表示されます。

## <a name="to-create-a-new-offer"></a>新しいプランを作成するには

1.  [Cloud パートナー ポータル](http://cloudpartner.azure.com/)にサインインします。

2.  左側のナビゲーション バーで、**[+ 新しいプラン]** を選択し、**[仮想マシン]** を選択します。

    ![仮想マシン向けの新しいプラン](./media/cloud-partner-portal-publish-virtual-machine/publishvm1.png)

3.  **[新しいプラン]** で、**[エディター]** を選択します。

![新しいプランのエディター](./media/cloud-partner-portal-publish-virtual-machine/publishvm2.png)

4.  **[エディター]** で、次の各ビューに情報を入力します。
    - プラン設定
    - SKU
    - マーケットプレース
    - サポート 各ビューには入力するための一連のフィールドがあります。必須フィールドには赤いアスタリスク (\*) が付いています。

## <a name="to-configure-offer-settings"></a>プランの設定を構成するには

1. [プランの設定] の **[Offer Identity]\(プラン ID\)** で次の各フィールドを構成します。

    **[プラン ID]**

     発行元プロファイル内のプランを表す一意識別子です。 この ID は、製品 URL、Azure Resource Manager テンプレート、および課金レポートに表示されます。 小文字の英数字またはダッシュ (-) のみ使用できます。 この ID はハイフンで終えることはできず、長さは最大で 50 文字です。 たとえば、**contoso** という発行元が **sample-vm** というプラン ID を使用してプランを発行した場合、Azure Marketplace には **https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview** として表示されます。  
    >[!Note]
    >このフィールドは、プランの運用が開始されるとロックされます。

    **[発行元 ID]**

    発行元プロファイルのドロップダウン リスト。 お客様が発行するプランの発行元となるプロファイルを選択します。 
    >[!Note]
    >このフィールドは、プランの運用が開始されるとロックされます。

    **名前**

    オファーの表示名です。 この名前は Azure Marketplace と Azure portal に表示されます。 最大で 50 文字の長さにできます。 プランの名前に関する次のガイダンスに従ってください。
    -  製品の覚えやすいブランド名を使用してください。 
    - 会社名はここに含めないでください (プランのマーケティングの一環である場合を除く)。
    - このプランを貴社の Web サイトでマーケティングしている場合は、この名前が、貴社の Web サイトで表示される名前とまったく同じであることを確認します。

2. **[保存]** を選択して、[Offer Settings]\(プランの設定\) を終了します。

## <a name="to-create-a-sku"></a>SKU を作成するには

1. **[SKU]** を選択します。 
2. **[SKU の追加]** を選択し、SKU ID を入力します。 SKU ID は、プラン内の SKU を表す一意識別子です。 この ID は、製品 URL、Azure Resource Manager テンプレート、および課金レポートに表示されます。 SKU ID の形式は次のとおりです。
    - 最大で 50 文字の長さにできます。
    - 小文字の英数字またはダッシュ (-) のみで構成できます。
    - ID の末尾にはダッシュを使用できません。

    ![SKU を追加する](./media/cloud-partner-portal-publish-virtual-machine/publishvm4.png)


## <a name="configure-sku-details"></a>SKU の詳細を構成する

追加した SKU は、[SKU] ビュー内の SKU の一覧に表示されます。 SKU の詳細を確認するには、SKU 名を選択します。 詳細ビューを使用して、次のフィールドに情報を追加できます。

### <a name="hide-this-sku"></a>この SKU を非表示

SKU の可視性を管理するには、この設定を使用します。 [この SKU を非表示] をオフにすると、[Azure Marketplace](https://azuremarketplace.microsoft.com) と [Azure Portal](https://portal.azure.com/) で顧客に SKU が 表示されます。 SKU をソリューション テンプレートだけで利用可能にし、購入だけでは利用不可にしたい場合は、SKU を非表示にします。

### <a name="cloud-availability"></a>クラウドの可用性

このフィールドでは、さまざまな Azure クラウドにおける SKU の可用性を定義することができます。

**パブリック Azure**

この SKU は、Marketplace に統合しているすべてのパブリック Azure リージョン内の顧客にデプロイできます。

**Azure Government Cloud**

この SKU は、Azure Government クラウドにデプロイできます。 [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners) に発行する前に、パブリッシャーがソリューションをテストして、想定どおりに動作することを検証することをお勧めします。 ステージングおよびテストするには、[試用版アカウントをリクエスト](https://azure.microsoft.com/offers/ms-azr-usgov-0044p)します。 

>[!Note]
>Microsoft Azure Government は政府用クラウドであり、米国連邦政府、州、地域または部族の顧客、およびこれらの団体にサービスを提供する資格を持つパートナー向けにアクセスが規制されています。

### <a name="countryregion-availability"></a>国/リージョンの可用性

このフィールドにより、SKU を購入に利用できるようになるリージョンが識別されます。 どこで SKU を利用できるようにするかについては、慎重に検討する必要があります。 国によっては、「Microsoft 税送金国」として分類されます。

-   「Microsoft 税送金国」では、Microsoft が顧客から税金を徴収し、政府に納税 (送金) します。

-   その他の国では、パートナーが顧客から税金を徴収し、政府に納税する必要があります。 これらの国への販売を選択した場合は、その国で税金を計算して支払うことができなければなりません。

![国/リージョンの可用性を選択する](./media/cloud-partner-portal-publish-virtual-machine/publishvm5.png)

### <a name="pricing"></a>価格

2 つの価格モデルが現在サポートされています。

#### <a name="bring-your-own-license-byol"></a>ライセンス持ち込み (BYOL)

VM で実行されているソフトウェアのライセンスはお客様が管理します。 Microsoft からはインフラストラクチャ コストのみが請求されます。

#### <a name="usage-based-monthly-billed-sku"></a>使用量ベースで月ごとに課金される SKU

お客様への請求は、パブリッシャーによって VM サイズに設定された料金に基づいて時間単位で行われます。 SKU の **時間単位の課金** モデルの場合、合計額は、ソフトウェア コスト (パブリッシャーが請求) とインフラストラクチャ コスト (Microsoft が請求) を足した金額です。 この合計コストは、購入を検討しているお客様に、時間単位と月単位の料金として表示されます。 この場合は月単位で請求が行われます。

使用量ベースのモデルでは、お客様による追加設定が必要になります。

**無料試用版**

無料試用版をお客様に提供するかどうかを指定できます。
VM をデプロイしてから最初の 30/90 日間 (選択によります) は、顧客にソフトウェア コストが請求されることはありません。 無料試用期間が経過すると、時間単位モデルで発行元によって設定された料金に基づいて、時間単位で請求が行われます。

**コアあたりの価格**

SKU のコアあたりの価格を設定できます。 この場合は、1 つのコアに基本価格を入力するだけで、残りのコアの価格が自動的に計算されます。 ポータルで米ドルの価格を入力すると、他のリージョンの価格が自動的に計算されます。 他のリージョンの価格は、**[価格データのエクスポート]** を使用して確認できます。

![コアあたりの価格](./media/cloud-partner-portal-publish-virtual-machine/publishvm6.png)


**個別の価格**

各コアの価格を別々に設定したい場合は、各コア セットの価格を個別に設定できます。

![個別の価格](./media/cloud-partner-portal-publish-virtual-machine/publishvm7.png)

**価格をエクスポート/インポート**

ポータルを使用して構成した価格をエクスポートして、Excel のインターフェイスを使用して変更を加えられる柔軟性があります。 これにより、リージョンごとの価格と現地通貨での価格を確認することもできます。
**[エクスポート-価格]** をクリックすると、価格の詳細が事前に入力された Excel ファイルがダウンロードされます。 このファイルを Excel で編集して、**[インポート-価格]** を使用すると、変更内容をインポートできます。
インポートした価格は、ポータルにも反映されます。

この価格 Excel 内では、さまざまなリージョンの価格は現地通貨で表示されます。 ここで使用する換算レートは毎日更新されます。

![換算レートを含む価格設定のエクスポートとインポートの例](./media/cloud-partner-portal-publish-virtual-machine/publishvm8.png)

>[!IMPORTANT]
>-   価格は、プランの運用開始後に変更することはできません。 ただし、サポートされるリージョンを追加または削除することはできます。
>-   この価格は、 [Azure\' の仮想マシンの価格](https://aka.ms/vmpricingdetails)に加えて、ユーザーに請求されます。
>-   すべてのリージョンの価格は、現地通貨で、価格設定時点の通貨レートで設定されます。
>-   各リージョンの価格を個別に設定または表示するには、価格スプレッドシートをエクスポートして、カスタム価格とともにインポートします。

## <a name="vm-images"></a>VM イメージ

次のセクションでは、VM イメージを扱います。 このセクションに進む前に、発行する VHD の準備を済ませる必要があります。 VHD を作成するのに役立ついくつかのリンクを示します。

-   [Azure Marketplace 向け仮想マシン イメージを作成するための技術的な前提条件](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-prerequisites)

-   [Linux VHD の作成とアップロード](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-create-upload-generic?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

-   [イメージからの Linux VM の作成とテスト](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-upload-vhd)

-   [Windows VHD の作成とアップロード ](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-prepare-for-upload-vhd-image?toc=/azure/virtual-machines/windows/toc.json)

-   [イメージからの Windows VM の作成とテスト](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-create-vm-generalized-managed?toc=/azure/virtual-machines/windows/toc.json)

-   [VHD 作成中に発生する一般的な問題をトラブルシューティングする方法](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-troubleshooting)

VHD の準備ができたら、このセクションの入力を開始できます。
いくつかのフィールドの詳細を次に示します。

### <a name="recommended-vm-sizes"></a>推奨される VM サイズ

最大 6 個の推奨される仮想マシン サイズを選択します。 これらは、顧客がイメージの購入およびデプロイを決定したとき Azure Marketplace と Azure Portal の [価格レベル] ブレードで顧客に表示されるものです。 **これらは単なる推奨サイズです。顧客はイメージに指定されたディスクを収容できる任意の VM サイズを選択できます。**  次の画面は、顧客の Azure Portal に表示される、推奨される VM サイズを示しています。


![推奨される VM サイズ](./media/cloud-partner-portal-publish-virtual-machine/publishvm9.png)


### <a name="open-ports"></a>ポートを開く

開いて利用可能にしたいポートを指定します。 これらのポートは、このVM のデプロイ時に開きます。

### <a name="adding-vm-images"></a>VM イメージの追加

次の手順では、VM イメージを SKU に追加します。 SKU あたり最大 8 個のディスク バージョンを追加できます。 特定の SKU について最も大きな番号のディスク バージョンだけが Azure Marketplace で表示されます。 他のディスク バージョンは API を使用して表示されます。

**[ディスク バージョン]** で、**[+ 新しいバージョン]** を選択します。 これにより、入力する必要がある次のフィールドが表示されます。

#### <a name="vm-image-version"></a>VM イメージ バージョン

VM イメージ バージョンは、[セマンティック バージョン](http://semver.org/)形式に従う必要があります。 バージョンの形式は X.Y.Z で、X、Y、Z は整数です。 異なる SKU のイメージは、メジャー バージョンおよびマイナー バージョンが異なっていてもかまいません。 SKU 内のバージョンはパッチ バージョン (X.Y.Z の Z) が変化する増分変更のみでなければなりません。

#### <a name="os-vhd-url"></a>OS VHD の URL

オペレーティング システム VHD に対して作成した [Shared Access Signature URI](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#52-get-the-shared-access-signature-uri-for-your-vm-images) を入力します。

この SKU に関連付けられているデータ ディスクがある場合は、**[+ 新しいデータ ディスク]** リンクをクリックすることで、これらのディスクを追加することを選択できます。 この操作によって、入力するための追加のフィールドが表示されます。

#### <a name="lun-vhd-url"></a>LUN VHD URL

[Shared Access Signature URI](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#52-get-the-shared-access-signature-uri-for-your-vm-images) をデータ ディスクで入力します。

#### <a name="lun-number"></a>LUN 番号

この LUN に番号を割り当てます。 この数字は、この SKU 内でこのデータ ディスクに予約されます。

>[!Note]
>SKU を所定の VM バージョンとデータ ディスクで発行すると、これらはロックされて変更できなくなります。 SKU にどの VM バージョンを追加しても、サポートする必要のあるデータ ディスクの数は変更できません。

#### <a name="common-sas-url-issues--fixes"></a>SAS URL に関する一般的な問題と解決策

| 問題                                                                 | Message                                                                           | 解決策                                                           |  ドキュメントへのリンク                                                                                |
|---------------------------------------------------------------------  |-------------------------------------------------------------------------------    |-----------------------------------------------------------    |---------------------------------------------------------------------------------------------------    |
| イメージのコピーに失敗する - SAS URL で "?" が見つからない                | Failure: Copying Images. (エラー: イメージのコピー。) Not able to download blob using provided SAS Uri (指定された SAS URI を使用して BLOB をダウンロードすることができません)       | 推奨ツールを使用して SAS URL を更新します                    | https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/     |
| イメージのコピーに失敗する - SAS URL の "st" および "se" パラメーターがない   | Failure: Copying Images. (エラー: イメージのコピー。) Not able to download blob using provided SAS Uri. (指定された SAS URI を使用して BLOB をダウンロードすることができません。)        | 開始日と終了日を指定して SAS URL を更新します             | https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/     |
| イメージのコピーに失敗する - SAS URL の "sp=rl" がない                    | Failure: Copying Images. (エラー: イメージのコピー。) Not able to download blob using provided SAS Uri (指定された SAS URI を使用して BLOB をダウンロードすることができません)         | 「読み取り」と「一覧」として設定したアクセス許可を指定して SAS URL を更新します     | https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/     |
| イメージのコピーに失敗する - SAS URL の VHD 名に空白が含まれる     | Failure: Copying Images. (エラー: イメージのコピー。) Not able to download blob using provided SAS Uri. (指定された SAS URI を使用して BLOB をダウンロードすることができません。)        | 空白のない状態で SAS URL を更新します                       | https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/     |
| イメージのコピーに失敗する - SAS URL の承認エラー               | Failure: Copying Images. (エラー: イメージのコピー。) Not able to download blob due to authorization error (承認エラーのため BLOB をダウンロードできません)     | SAS URL を再生成します                                        | https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/     |


## <a name="to-configure-the-marketplace"></a>Marketplace を構成するには

[Azure Marketplace](https://azuremarketplace.microsoft.com) と [Azure Portal](https://portal.azure.com/) に表示されるプランのフィールドを [Marketplace] ビューを使用して構成します。

### <a name="preview-subscription-ids"></a>サブスクリプション ID をプレビュー

発行されたプランへのアクセスを許可する Azure サブスクリプション ID の一覧です。 これらのホワイトリストに追加されたサブスクリプションにより、プレビューされたオファーを、運用開始前にテストできます。 パートナー ポータルでは、最大 100 サブスクリプションをホワイトリストに追加できます。

### <a name="suggested-categories"></a>推奨されるカテゴリ

提供されたリストから、プランとの最適な関連付けができるカテゴリを 5 つまで選択します。 選択したカテゴリは、[Azure Marketplace](https://azuremarketplace.microsoft.com) と [Azure Portal](https://portal.azure.com/) で利用可能な製品カテゴリにプランをマッピングするために使用されます。

Azure Marketplace と Azure Portal に表示されるマーケットプレース情報の例を次に示します。

**Azure Marketplace**


![publishvm10](./media/cloud-partner-portal-publish-virtual-machine/publishvm10.png)


![publishvm11](./media/cloud-partner-portal-publish-virtual-machine/publishvm11.png)


![publishvm15](./media/cloud-partner-portal-publish-virtual-machine/publishvm15.png)


**Azure Portal**


![publishvm12](./media/cloud-partner-portal-publish-virtual-machine/publishvm12.png)



![publishvm13](./media/cloud-partner-portal-publish-virtual-machine/publishvm13.png)


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

![Hero ロゴの例](./media/cloud-partner-portal-publish-virtual-machine/publishvm14.png)

### <a name="lead-management"></a>見込み客管理

プランの見込み客管理設定を構成するには、[こちらの指示](./cloud-partner-portal-get-customer-leads.md)に従ってください。

## <a name="to-configure-support"></a>サポートを構成するには

[サポート] ビューを使用して次の情報を指定します。

- 貴社のサポート連絡先 (エンジニアリングなど)。
- カスタマー サポートの連絡先。

## <a name="to-publish-the-offer"></a>プランを発行するには

最後の手順で、プランを発行します。 [こちらの指示](./cloud-partner-portal-make-offer-live-on-azure-marketplace.md)に従って、プランの運用を開始します。
