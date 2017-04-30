---
title: "Azure Marketplace での仮想マシンの発行 | Microsoft Docs"
description: "この記事では、クラウド パートナー ポータルを使用した仮想マシンの発行に関する詳細な情報を提供します"
services: cloud-partner-portal
documentationcenter: 
author: anuragdalmia
manager: hamidm
ms.service: cloud-partner-portal
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2017
ms.author: andalmia
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 6e5b1eb51c10bb51762f6815eda72add1341a4ad
ms.lasthandoff: 04/19/2017


---
# <a name="publish-a-virtual-machine-to-azure-marketplace"></a>Azure Marketplace への仮想マシンの発行

この記事では、Azure Marketplace への仮想マシンの発行に関連するさまざまな手順を示します。

## <a name="what-are-pre-requisites-for-publishing-a-vm"></a>VM を発行するための前提条件とは

Azure Marketplace で一覧表示するための前提条件

1.  技術

    -   [Azure Marketplace 向け仮想マシン イメージを作成するための技術的な前提条件](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-prerequisites)

    -   [Linux VHD の作成とアップロード](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-create-upload-generic?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

    -   [イメージからの Linux VM の作成とテスト](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-upload-vhd)

    -   [Windows VHD の作成とアップロード](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-prepare-for-upload-vhd-image?toc=/azure/virtual-machines/windows/toc.json)

    -   [イメージからの Windows VM の作成とテスト](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-create-vm-generalized-managed?toc=/azure/virtual-machines/windows/toc.json)

    -   [VHD 作成中に発生する一般的な問題をトラブルシューティングする方法](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-troubleshooting )


2.  技術面以外 (ビジネス要件)

    -   貴社 (またはその子会社) は、Azure Marketplace によってサポートされる販売元の国に所在している必要があります

    -   Azure Marketplace でサポートされている課金モデルと互換性がある方法で、製品のライセンスを取得する必要があります

    -   無償、有償、コミュニティ サポートの活用を問わず、商業的に合理的な方法で、顧客に技術サポートを提供していただきます。

    -   貴社のソフトウェアおよび依存関係にあるすべてのサード パーティ ソフトウェアのライセンス供与を行っていただきます。

    -   Azure Marketplace および Azure 管理ポータルでプランが一覧表示されるための条件に適合するコンテンツを提供する必要があります

    -   Azure Marketplace 参加ポリシーとパブリッシャー契約の条項に同意する必要があります

    - [使用条件](https://azure.microsoft.com/support/legal/website-terms-of-use/)、[Microsoft のプライバシーに関する声明](http://www.microsoft.com/privacystatement/default.aspx)、および [Microsoft Azure 認定プログラム契約](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/)に従うことに同意する必要があります。

## <a name="how-to-create-a-new-vm-offer"></a>新しい VM プランを作成する方法

すべての前提条件が満たされると、仮想マシン (VM) プランの作成を開始する準備ができます。 開始する前に、プランと SKU の概要を紹介します。

### <a name="offer"></a>プラン
仮想マシン プランは、パブリッシャーから提供される製品のクラスに相当します。 新しい種類の製品/仮想マシンを Azure Marketplace で発行にしたい場合は、新しいプランが適しています。 プランとは、SKU のコレクションです。 各プランは、Azure Marketplace で独自のエンティティとして表示されます。 

### <a name="sku"></a>SKU
SKU とは、プランの購入可能な最小単位です。 同じ製品クラス (プラン) 内では、SKU によりさまざまなサポート機能、VM イメージ タイプ、サポートされている課金モデルを区別できます。 

ライセンス持ち込み (BYOL)、従量課金 (PAYG) など、異なる課金モデルをサポートしたい場合は、複数の SKU を追加します。各 SKU が異なる機能セットをサポートし、それぞれの価格が異なる場合は、複数の SKU を追加します。 パブリック クラウドと主権のあるクラウドとで異なる VM イメージがある場合も、複数の SKU を追加します。 

SKU は Azure Marketplace では親プランの下に表示されますが、Azure ポータルではその購入可能エンティティとして表示されます。 

1.  [クラウド パートナー ポータル](http://cloudpartner.azure.com/)にログインします。

2.  左側のナビゲーション バーから、[+ 新規プラン] をクリックして [仮想マシン] を選択します。

    ![publishvm1][1]

3.  これで新しいプランの「エディター」ビューが開いて、作成を開始する準備ができました。

   ![publishvm2][2]

4.  入力が必要な「フォーム」は、「エディター」ビューの左側にあります。 各「フォーム」は、入力するフィールドのセットで構成されます。 必須フィールドには赤いアスタリスク (\*) が付いています。

> VM を作成するためのメイン フォームが 4 つあります。

-   プラン設定

-   SKU

-   Marketplace

-   サポート

## <a name="how-to-fill-out-the-offer-settings-form"></a>プラン設定フォームに記入する方法
プラン設定フォームは、プランの設定を指定する基本的なフォームです。 さまざまなフィールドを以下で説明します。

### <a name="offer-id"></a>プラン ID
これは、パブリッシャー プロファイル内で一意のプラン識別子です。 この ID は、製品 URL、ARM テンプレート、および課金レポートに表示されます。 小文字の英数字またはハイフン (-) のみで構成できます。 この ID はダッシュで終えることはできず、最大で 50 文字の長さにできます。 プランの運用が開始するとこのフィールドがロックされるので注意してください。

たとえば、パブリッシャー **contoso** がプラン ID **sample-vm** のプランを発行すると、Azure Marketplace に **https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview** として表示されます。

### <a name="publisher-id"></a>パブリッシャー ID
このドロップダウンでは、このプランを発行するためのパブリッシャー プロファイルを選択することができます。 プランの運用が開始するとこのフィールドがロックされるので注意してください。

### <a name="name"></a>名前
これは、プランの表示名です。 この名前が [Azure Marketplace](https://azuremarketplace.microsoft.com) と [Azure Portal](https://portal.azure.com/) に表示されます。 最大で 50 文字の長さにできます。 製品の覚えやすいブランド名を含めることをお勧めします。 販売方法である場合を除き、会社名はここには含めないでください。 このプランを貴社の Web サイトでマーケティングしている場合は、この名前が貴社の Web サイトで表示される名前とまったく同じであることを確認してください。

[保存] をクリックして進捗状況を保存します。 次の手順では、プランに SKU を追加します。

## <a name="how-to-create-skus"></a>SKU の作成方法
「SKU」フォームをクリックします。 ここで表示される「SKU の追加」オプションをクリックすると、「SKU ID」を入力することができます。

![publishvm4][4]

これは、SKU のプラン内で一意の識別子です。 この ID は、製品 URL、ARM テンプレート、および課金レポートに表示されます。 小文字の英数字またはハイフン (-) のみで構成できます。 この ID はダッシュで終えることはできず、最大で 50 文字の長さにできます。 プランの運用が開始するとこのフィールドがロックされるので注意してください。 プラン内の SKU を複数にすることができます。 発行しようとしているイメージごとに SKU が必要です。

SKU が追加されると、「SKU」フォーム内の SKU の一覧に表示されます。 SKU 名をクリックすると、その SKU の詳細が表示されします。 いくつかのフィールドの詳細を次に示します。

### <a name="hide-this-sku"></a>この SKU を非表示
このフラグを使用すると、この SKU を [Azure Marketplace](https://azuremarketplace.microsoft.com) と [Azure Portal](https://portal.azure.com/) で顧客に表示するかどうかを設定できます。 SKU をソリューション テンプレートだけで利用可能にし、購入だけでは利用不可にしたい場合は、SKU を非表示にします。

### <a name="cloud-availability"></a>クラウドの可用性 
このフィールドでは、さまざまな Azure クラウドにおける SKU の可用性を定義することができます。

#### <a name="public-azure"></a>パブリック Azure 

この SKU は、Marketplace に統合しているすべてのパブリック Azure リージョン内の顧客へデプロイ可能になります。

#### <a name="azure-government-cloud"></a>Azure Government クラウド 

この SKU は、Azure Government クラウドでデプロイ可能になります。 [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners) に発行する前に、パブリッシャーがソリューションをテストして、想定どおりに動作することを検証することをお勧めします。 ステージングおよびテストするには、試用版アカウントを[ここ](https://azure.microsoft.com/offers/ms-azr-usgov-0044p)でリクエストします。 Microsoft Azure Government は政府用クラウドであり、米国連邦政府、州、地域または部族の顧客、およびこれらの団体にサービスを提供する資格を持つパートナー向けにアクセスが規制されていることにご留意ください。

### <a name="countryregion-availability"></a>国/リージョンの可用性
このフィールドにより、SKU を購入に利用できるようになるリージョンが決定します。 どこで SKU を利用できるようにするかについては、慎重に検討する必要があります。 国によっては、「Microsoft 税送金国」として分類されます。

-   「Microsoft 税送金国」では、Microsoft がお客様から税金を徴収し、政府に納税 (送金) します。

-   他の国では、パートナーがお客様から税金を徴収し、政府に納税します。 これらの国への販売を選択した場合は、その国で税金を計算して支払うことができなければなりません。

![publishvm5][5]

### <a name="pricing"></a>価格 
現在サポートされている価格モデルは 2 種類あります。

#### <a name="bring-your-own-license-byol"></a>ライセンス持ち込み (BYOL)

VM で実行されているソフトウェアのライセンスはお客様が管理します。 Microsoft からはインフラストラクチャ コストのみが請求されます。

#### <a name="usage-based-monthly-billed-sku"></a>使用量ベースで月ごとに課金される SKU

お客様への請求は、パブリッシャーによって VM サイズに設定された料金に基づいて時間単位で行われます。 SKU の **時間単位の課金** モデルの場合、合計額は、ソフトウェア コスト (パブリッシャーが請求) とインフラストラクチャ コスト (Microsoft が請求) を足した金額です。 この合計コストは、購入を検討しているお客様に、時間単位と月単位の料金として表示されます。 この場合は月単位で請求が行われます。

使用量ベースのモデルでは、お客様による追加設定が必要になります。

##### <a name="free-trial"></a>無料試用版

無料試用版をお客様に提供するかどうかを指定できます。 VM をデプロイしてから最初の 30 日間は、お客様にソフトウェア コストが請求されることはありません (無料)。 30 日が経過すると、時間単位モデルでパブリッシャーによって設定された料金に基づいて、時間単位で請求が行われます。

##### <a name="per-core-pricing"></a>コアあたりの価格

SKU のコアあたりの価格を設定できます。 この場合は、1 つのコアに基本価格を入力するだけで、残りのコアの価格が自動的に計算されます。 ポータルで米ドルの価格を入力すると、他のリージョンの価格が自動的に計算されます。 他のリージョンの価格は、**[価格データのエクスポート]** を使用して確認できます。

![publishvm6][6]

##### <a name="discrete-pricing"></a>個別の価格

各コアの価格を別々に設定したい場合は、各コア セットの価格を個別に設定できます。

![publishvm7][7]

##### <a name="export-import-pricing"></a>価格をエクスポート/インポート

ポータルを使用して構成した価格をエクスポートして、Excel のインターフェイスを使用して変更を加えられる柔軟性があります。 これにより、リージョンごとの価格と現地通貨での価格を確認することもできます。 **[エクスポート-価格]** をクリックすると、価格の詳細が事前に入力された Excel ファイルがダウンロードされます。 このファイルを Excel で編集して、**[インポート-価格]** を使用すると、変更内容をインポートできます。 インポートした価格は、ポータルにも反映されます。

この価格 Excel 内では、さまざまなリージョンの価格は現地通貨で表示されます。 ここで使用する換算レートは毎日更新されます。

![publishvm8][8]

##### <a name="important-notes-about-pricing"></a>価格に関する重要な注意事項

-   プランの運用開始後は、価格の変更はできなくなります。 ただし、サポートされているリージョンを追加/削除することはできます。

-   この価格は、[Azure の仮想マシンの価格](http://aka.ms/vmpricingdetails)に加えて、ユーザーに請求されます。

-   すべてのリージョンの価格は、現地通貨で、価格設定時点の通貨レートで設定されます。

-   各リージョンの価格を個別に設定または表示するには、価格スプレッドシートをエクスポートして、カスタム価格とともにインポートします。

### <a name="vm-images"></a>VM イメージ
次のセクションでは、VM イメージを扱います。 このセクションに進む前に、発行する VHD の準備を済ませる必要があります。 VHD を作成するのに役立ついくつかのリンクを示します。

-   [Azure Marketplace 向け仮想マシン イメージを作成するための技術的な前提条件](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-prerequisites)

-   [Linux VHD の作成とアップロード](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-create-upload-generic?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

-   [イメージからの Linux VM の作成とテスト](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-upload-vhd)

-   [Windows VHD の作成とアップロード](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-prepare-for-upload-vhd-image?toc=/azure/virtual-machines/windows/toc.json)

-   [イメージからの Windows VM の作成とテスト](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-create-vm-generalized-managed?toc=/azure/virtual-machines/windows/toc.json)

-   [VHD 作成中に発生する一般的な問題をトラブルシューティングする方法](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-troubleshooting )


VHD の準備ができたら、このセクションの入力を開始できます。 いくつかのフィールドの詳細を次に示します。

#### <a name="recommended-vm-sizes"></a>推奨される VM サイズ

最大 6 個の推奨される仮想マシン サイズを選択します。 これらは、顧客がイメージの購入およびデプロイを決定したとき Azure Marketplace と Azure ポータルの [価格レベル] ブレードで顧客に表示されるものです。 **これらは単なる推奨サイズです。顧客はイメージに指定されたディスクを収容できる任意の VM サイズを選択できます。** 推奨される VM サイズが Azure Portal で顧客にどう表示されるかを以下に示します。

![publishvm9][9]

#### <a name="open-ports"></a>ポートを開く

開いて利用可能にしたいポートを指定します。 これらのポートは、このVM のデプロイ時に開きます。

#### <a name="adding-vm-images"></a>VM イメージの追加

次の手順では、VM イメージを SKU に追加します。 SKU あたり最大 8 個のディスク バージョンを追加できます。 特定の SKU について最も大きな番号のディスク バージョンだけが Azure Marketplace で表示されます。 他のディスク バージョンは API を使用して表示されます。 

最初に **[ディスク バージョン]** セクションの下の [+ 新規バージョン] をクリックします。 これにより、入力する必要があるフィールドのコレクションが表示されます。

##### <a name="vm-image-version"></a>VM イメージ バージョン

VM イメージ バージョンは、[セマンティック バージョン](http://semver.org/)形式に従う必要があります。 バージョンの形式は X.Y.Z で、X、Y、Z は整数です。 異なる SKU のイメージは、メジャー バージョンおよびマイナー バージョンが異なっていてもかまいません。 SKU 内のバージョンはパッチ バージョン (X.Y.Z の Z) が変化する増分変更のみでなければなりません。

##### <a name="os-vhd-url"></a>OS VHD の URL

オペレーティング システム VHD に対して作成した [Shared Access Signature URI](../../marketplace-publishing/marketplace-publishing-vm-image-creation.md#52-get-the-shared-access-signature-uri-for-your-vm-images) を入力します。

この SKU に関連付けられているデータ ディスクがある場合、これを追加するには、「+ 新しいデータ ディスク」リンクをクリックします。 すると、記入する追加フィールドが表示されます。

##### <a name="lun-vhd-url"></a>LUN VHD URL

[Shared Access Signature URI](../../marketplace-publishing/marketplace-publishing-vm-image-creation.md#52-get-the-shared-access-signature-uri-for-your-vm-images) をデータ ディスクで入力します。

##### <a name="lun-number"></a>LUN 番号

この LUN に番号を割り当てます。 この数字は、この SKU 内でこのデータ ディスクに予約されます。

SKU を所定の VM バージョンとデータ ディスクで発行すると、これらはロックされて変更できなくなるので注意してください。 SKU にどの VM バージョンを追加しても、サポートする必要のあるデータ ディスクの数は変更できません。

##### <a name="common-sas-url-issues--fixes"></a>SAS URL に関する一般的な問題と解決策

| **問題**                                                           | **エラー メッセージ**                                                           | **解決策**                                                   | **ドキュメント リンク**                                                                                    |
|---------------------------------------------------------------------|-------------------------------------------------------------------------------|-----------------------------------------------------------|-----------------------------------------------------------------------------------------------------------|
| イメージのコピーに失敗する - SAS URL の "?" が見つからない             | Failure: Copying Images. (エラー: イメージのコピー。) Not able to download blob using provided SAS Uri. (指定された SAS URI を使用して BLOB をダウンロードすることができません。)    | 推奨ツールを使用して SAS URL を更新します                | <https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/> |
| イメージのコピーに失敗する - SAS URL の "st" および "se" パラメーターがない | Failure: Copying Images. (エラー: イメージのコピー。) Not able to download blob using provided SAS Uri. (指定された SAS URI を使用して BLOB をダウンロードすることができません。)    | 開始日と終了日を指定して SAS URL を更新します         | <https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/> |
| イメージのコピーに失敗する - SAS URL の "sp=rl" がない                  | Failure: Copying Images. (エラー: イメージのコピー。) Not able to download blob using provided SAS Uri (指定された SAS URI を使用して BLOB をダウンロードすることができません)     | 「読み取り」と「一覧」として設定したアクセス許可を指定して SAS URL を更新します | <https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/> |
| イメージのコピーに失敗する - SAS URL の VHD 名に空白が含まれる   | Failure: Copying Images. (エラー: イメージのコピー。) Not able to download blob using provided SAS Uri. (指定された SAS URI を使用して BLOB をダウンロードすることができません。)    | 空白のない状態で SAS URL を更新します                   | <https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/> |
| イメージのコピーに失敗する - SAS URL の承認エラー             | Failure: Copying Images. (エラー: イメージのコピー。) Not able to download blob due to authorization error (承認エラーのため BLOB をダウンロードできません) | SAS URL を再生成します                                    | <https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/> |

## <a name="marketplace-form"></a>Marketplace フォーム
仮想マシン プラン内のMarketplace フォームは、[Azure Marketplace](https://azuremarketplace.microsoft.com) と [Azure Portal](https://portal.azure.com/) で表示されるフィールドについて確認します。 いくつかのフィールドの詳細を次に示します。

#### <a name="preview-subscription-ids"></a>サブスクリプション ID をプレビュー

ここに、発行後にプランにアクセスできる Azure サブスクリプション ID の一覧を入力します。 これらのホワイトリストに追加されたサブスクリプションにより、プレビューされたプランを、運用開始する前にテストできます。 パートナー ポータルでは、最大 100 のサブスクリプションをホワイトリストに追加できます。

#### <a name="suggested-categories"></a>推奨されるカテゴリ

提供されたリストから、プランとの最適な関連付けができるカテゴリを 5 つまで選択します。 選択したカテゴリは、[Azure Marketplace](https://azuremarketplace.microsoft.com) と [Azure Portal](https://portal.azure.com/) で利用可能な製品カテゴリにプランをマッピングするために使用されます。

このフォームで指定するデータが表示される場所のいくつかを、次に示します。

##### <a name="azure-marketplace"></a>Azure Marketplace

![publishvm10][10]

![publishvm11][11]

![publishvm15][15]

##### <a name="azure-portal"></a>Azure ポータル

![publishvm12][12]

![publishvm13][13]

##### <a name="logo-guidelines"></a>ロゴのガイドライン

クラウド パートナー ポータルにアップロードされるすべてのロゴは、以下のガイドラインに従っている必要があります。

-   Azure の設計には簡単なカラー パレットがあります。 ロゴのプライマリ カラーとセカンダリ カラーの数は少なくしてください。

-   Azure ポータルのテーマの色は白黒です。 したがって、この色を、ロゴの背景色として使用しないでください。 Azure ポータルでロゴが目立つ色を背景色として使用します。 背景色としてはシンプルなプライマリ カラーをお勧めします。 **透明な背景を使用している場合は、ロゴ/テキストが白、黒または青ではないことを確認します。**

-   ロゴではグラデーションの背景を使用しないでください。

-   ロゴにはテキストを使用しないでください (会社またはブランドの名前であっても)。 ロゴのルック アンド フィールは "フラット" にする必要があり、グラデーションは避ける必要があります。

-   ロゴは拡大しないでください。

##### <a name="hero-logo"></a>Hero ロゴ

Hero ロゴはオプションです。 パブリッシャーは、Hero ロゴをアップロードしなくてもかまいません。 ただし、アップロードされたら、Hero アイコンは削除できません。 その時点で、パートナーは、Hero アイコンの Azure Marketplace ガイドラインに従う必要があります。

###### <a name="guidelines-for-the-hero-logo-icon"></a>Hero ロゴ アイコンに関するガイドライン

-   発行者表示名、SKU のタイトル、およびプランの長い概要は、白のフォント色で表示されます。 このため、Hero アイコンの背景色には明るい色を使用しないでください。 黒、白、および透明は Hero アイコンの背景では使用できません。

-   プランが表示されると、パブリッシャーの表示名、SKU のタイトル、プランの長い概要、作成ボタンが、Hero ロゴ内にプログラムによって埋め込まれます。 したがって、Hero ロゴのデザイン時にはテキストを入力しないでください。 右側にスペースを空けておくだけで、テキスト (つまり、 パブリッシャーの表示名、SKU のタイトル、プランの長い概要) は、その場所にプログラムによって挿入されます。 右側のテキスト用の空のスペースは 415 x 100 です (また、左から 370 ピクセルだけオフセットされます)。

![publishvm14][14]

#### <a name="lead-management"></a>見込み客管理

プランの見込み客管理の設定を記入するには、[ここ](./cloud-partner-portal-get-customer-leads.md#how-to-connect-your-crm-system-with-the-cloud-partner-portal)の手順に従ってください。

## <a name="support-form"></a>サポート フォーム
次に記入するフォームは、サポート フォームです。 このフォームには、エンジニアリング連絡先情報やカスタマー サポート連絡先情報など、あなたの会社のサポート連絡先を記入します。 

## <a name="how-to-publish-an-offer"></a>プランの発行方法
プランの草案が完成したら、次はプランを Azure Marketplace に発行します。 指示に従って、[プランを Azure Marketplace で運用開始します](./Cloud-partner-portal-make-offer-live-on-Azure-Marketplace.md)。 

[1]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm1.png
[2]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm2.png
[3]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm3.png
[4]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm4.png
[5]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm5.png
[6]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm6.png
[7]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm7.png
[8]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm8.png
[9]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm9.png
[10]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm10.png
[11]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm11.png
[12]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm12.png
[13]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm13.png
[14]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm14.png
[15]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm15.png
