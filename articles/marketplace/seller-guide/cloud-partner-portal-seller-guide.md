---
title: "Microsoft Azure Marketplace 販売者ガイド | Microsoft Docs"
description: "このガイドは、IT プロフェッショナルと開発者を対象とした Azure 認定仮想マシン イメージの販売に関心がある、独立系ソフトウェア ベンダー (ISV) のビジネス ユーザーと製品マネージャー向けに作成されています。"
documentationcenter: 
author: rupeshazure
manager: hamidm
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: rupesk@microsoft.com
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: aa6ae2b7e6ad0c6f91503d026ec8fd6933b6cfbb
ms.lasthandoff: 04/12/2017


---
# <a name="azure-marketplace-sellers-guide"></a>Azure Marketplace 販売者ガイド

Microsoft Azure Marketplace 販売者ガイドをご覧くださりありがとうございます。 このガイドは、IT プロフェッショナルと開発者を対象とした Azure 認定仮想マシン イメージの販売に関心がある、独立系ソフトウェア ベンダー (ISV) のビジネス ユーザーと製品マネージャー向けに作成されています。 Azure のお客様は世界中に存在します。[Azure Marketplace](https://azuremarketplace.microsoft.com/) を利用すれば、自社製品をきわめて多くのお客様にリーチさせることができます。


> [!NOTE]
> 完成したサービスとしてのソフトウェア (SaaS) をビジネス ユーザーに販売したい場合は、それらを [AppSource](https://appsource.microsoft.com) で一覧表示するための各種オプションをご確認ください。

このガイドの目的は、次のトピックに関する詳細をどこで確認できるか説明することです。

- Azure Marketplace とは何ですか?
- 製品が Azure Marketplace にマッチしているかどうかはどのような方法で判断できますか?
- Azure Marketplace で販売するメリットは何ですか?
- Azure Marketplace で販売するための (技術面および技術面以外の) 前提条件は何ですか?
- Azure と互換性のある仮想ハード ディスク (VHD) はどのような方法で構築すればよいですか?
- 販売者として申請と登録を行うにはどうすればよいですか?
- プランはどのように作成して公開すればよいですか?
- 使用可能なリソースをマーケットで探すにはどうすればよいですか?
- Azure Marketplace では、どのようなレポートと洞察が得られますか?
- ヘルプとサポートはどこで提供されますか?

作業開始

## <a name="whats-the-azure-marketplace"></a>Azure Marketplace とは何ですか?

Azure Marketplace は、新興企業から大企業に至るまで、ISV が自社のソリューションを世界中の Azure のお客様に提供できるようにする、アプリケーションとサービスのオンライン マーケットプレースです。 Azure Marketplace を利用すると、クラウド ベースのアプリケーションやモバイル ソリューションを Azure で短期間で開発しようとしている他の開発者、ISV、IT プロフェッショナル向けに、革新的な仮想マシン イメージを Azure の公開元として配布し販売することができます。 Azure Marketplace では、データ処理、データ ストレージ、分析レイヤーを備えたエンド ツー エンドの分析アプリケーションや、階層型の eコマース アプリ (データ、サービス、インターネット) など、幅広いサービスをサポートしています。

クラウドのお客様は、独自のニーズを満たす適切なソリューションを検索する際に、いくつかの課題に直面します。 Azure Marketplace では、お客様がこれらの課題を解決し、革新的な ISV のソリューションを見つけ出せるようなしくみを提供しています。詳細は次の表のとおりです。

| 顧客ニーズ | Azure Marketplace のソリューション |
| --- | --- |
| ビジネス上のニーズと技術的なニーズを満たすため、追加のクラウド プラットフォーム機能が必要 | Azure の補完的なアプリケーションとサービスのポートフォリオを提供し、その数を増やし続ける |
| 適切なアプリケーションまたはサービスを見つけ出すことが困難と判明 | アプリケーションやサービスの発見、検索、購入が可能なワンストップ ショップを提供する |
| サードパーティ製のアプリケーションとサービスをデプロイするためのスケーラブルなしくみが必要 | サードパーティ製のアプリケーションとサービス向けにスケーラブルなデプロイの作成と構成ができるようにする |
| 新しいアプリケーションとサービスを既存のソリューションと統合して使用する必要がある | Azure でサードパーティ製のアプリケーションとサービスを既存のソリューションと簡単に統合できる |

Azure Marketplace は、高品質な Azure パートナー エコシステムの幅広い選択肢と強みを世界中のお客様に提供します。 主な利点は次のとおりです。

- Microsoft とパートナーから提供される Azure ベースのサービスの場所が統合済み
- 5,000 以上のプラン
- 統合されたプラットフォーム エクスペリエンス
- 合理的な構成、デプロイ、管理

## <a name="is-the-azure-marketplace-right-for-my-business"></a>Azure Marketplace は自分の会社のビジネスにマッチしているでしょうか?

ここまでで、Azure Marketplace が自社のビジネスに適しているかどうか、また、適しているとすれば何が得られるのか、気になり始めた方もいるかもしれません。 Azure Marketplace は、Azure で使用可能なソフトウェアとサービスを顧客に提供するという、新たな販売の機会を提供します。

- **Azure のソリューションの幅広いポートフォリオを新たな顧客市場に売り込む**: これは、マイクロソフト オンライン サブスクリプション プログラム (MOSP) と Microsoft Enterprise Agreement のお客様を対象とする Azure サービスを使用した Marketplace のサービスのアップセルやクロスセルのチャンスとなります。 Marketplace のサービスを、顧客のソリューションと Azure のシナリオ面に簡単に組み込むことができます。
- **ビジネス価値を高め、既存および新規の顧客アカウントの取引規模を拡大する**: Azure Marketplace は、取引規模の拡大、ワークロードをクラウドに移行する際の顧客の問題点への対処、取引の収益性の向上に役立ちます。 簡単に言えば、包括的なソリューションを販売することでビジネス価値を高め、Azure クラウド プラットフォームの大きなギャップに対応することで顧客要件を満たすことができます。
- **Marketplace のアプリケーションとサービスを販売することで、さらに幅広い潜在顧客にアピールする**: Azure Marketplace を利用すると、新規顧客の開拓と維持が容易になります。 今日の多くの企業は、変化の激しいインフラストラクチャ環境に適応するために、ワークロードをクラウドに切り替える必要に迫られています。 このギャップを解消するための、適切なアプリケーションとサービスを提供できます。
- **Marketplace でのプランを Azure サービスとバンドルすることで、Azure の機能を補完し、拡張する**: Azure Marketplace を使用して、顧客とのシナリオ ベースの会話を組み立てることができます。これにより、顧客と現実的かつ具体的な話し合いが可能です。 関係のあるエンド ツー エンド (E2E) のソリューションを紹介すれば、特定のプラットフォームに関するギャップや顧客ニーズにも対処することができます。 さらに、ソリューションのバンドルを販売すれば、Azure プラットフォームのエコシステムを最大限に活用できます。これにより、ほとんどの顧客の問題に対処し、売上も伸ばすことができます。

## <a name="what39s-the-customer-base-for-the-azure-marketplace"></a>Azure Marketplace の顧客ベースはどのようなものですか?

Azure Marketplace には多種多様な数多くのお客様がいます。 Azure は、すべてのクラウド プロバイダーの急速に成長する顧客ベースの 1 つを保持しているため、公共または民間、業界、新興企業や大企業といった枠を越えた、数え切れないほどの IT プロフェッショナルと開発者にリーチすることができます。

## <a name="how-does-the-azure-marketplace-work"></a>Azure Marketplace のしくみはどのようなものですか?

非常に簡単です。認定を取得した後、Azure 認定仮想マシン イメージを作成し、Azure Marketplace に公開すれば、Azure のお客様がその製品を数分で検索、購入、デプロイできるようになります。 これに加えて、Azure での使用条件を満たす環境が構成済みであることと、インフラストラクチャが数分以内に使用可能であることも確認できるため、お客様は安心してソリューションをデプロイできます。

クラウド パートナー ポータル (CPP) は、Azure Marketplace でプランを作成するためのハブです。 仮想マシン イメージには、完全にインストールされたオペレーティング システムと 1 つ以上のアプリケーションが事前に構成されています。 公開の準備を整えるためにイメージの認定を受けるには、このガイドの次のセクションで説明する、特定の前提条件を満たす必要があります。


## <a name="whats-next"></a>次の手順

ガイドをここまで確認した方の中には、&quot;Azure Marketplace は "_確かに_" 自社の製品にマッチするが、どうやって始めればよいだろう?&quot; と思う方もいるかもしれません。 このセクションでは、そのことについて説明します。 次の図は、Azure Marketplace の開始と利用のための情報を示しています。Azure 認定を取得し、製品を販売するための承認を得たうえで、CPP でプランを作成します (図 1)。

![Azure Marketplace での販売のためのプロセス](./media/cloud-partner-portal-seller-guide/processforselling.png)

図 1: Azure Marketplace での販売のためのプロセス

大まかに言うと、技術面および技術面以外の一連の前提条件を満たしてから、仮想マシン イメージを準備します。 次に、製品をノミネートし、販売者として登録します。 最後に、マーケティングのコンテンツを追加して、公開用に送信します。 プランは、Azure Marketplace で提供開始する前に、プレビューまたはステージング環境で確認することができます。

初めて Azure Marketplace のプランを作成する際は、基本的なオンボードのために "_4 週間_" 程度の時間を取る必要があります。 可能であれば、プランをリリースする前の "_6 週間_" で構築を行います。これにより、メディアや公開に関して考慮するための時間を多く確保できます。

## <a name="how-do-i-become-azure-certified"></a>Azure 認定を取得するにはどうすればよいですか?

Azure Marketplace 用のプランを作成するための最初の手順は、Azure 認定を取得することです。 つまり、会社情報をまとめ、参加ポリシーに同意し、必要なツールをダウンロードして、技術コンポーネントを構築することを指します (図 2) 。

![Azure 認定を取得するための要件](./media/cloud-partner-portal-seller-guide/azurecertified.png)

図 2: Azure 認定を取得するための要件

### <a name="technical-prerequisites"></a>技術的な前提条件

プランをリリースする前に、必ず慎重に確認を行い、すべての技術的な前提条件が満たされているようにしてください。 テスト ツールにリンクされた、Azure と互換性のある VHD に対してだけでなく、Windows または Linux へのアクセスも必要です。

Azure VHD はクラウドでもオンプレミスでも開発できますが、リモート デスクトップ プロトコルを使用して、クラウドで直接開発することをお勧めします。 ただし、最終手段として、VHD をダウンロードし、[オンプレミスのインフラストラクチャ](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-on-premise)を使用して開発することは可能です。

詳細については、[技術的な前提条件](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-prerequisites)に関するページを参照してください。

### <a name="non-technical-prerequisites"></a>技術面以外の前提条件

Azure Marketplace に参加するには、技術面以外の前提条件をいくつか満たす必要があります。 最初に、[Azure Marketplace 参加ポリシー](https://azure.microsoft.com/support/legal/marketplace/participation-policies/)の条項を確認し、同意します。 Azure Marketplace で提供されるソフトウェアとサービスは、以下の要件のうち少なくとも 1 つを満たしている必要があることに注意してください。

- **Microsoft Azure 上で稼働する**: ソフトウェアまたはサービスの基本機能は、Microsoft Azure 上で稼働するものである必要があります。
- **Microsoft Azure にデプロイ可能**: ソフトウェアまたはサービスを Microsoft Azure 上にデプロイする方法を、一覧情報の中に記述する必要があります。
- **Microsoft Azure サービスと統合可能、または Azure サービスを拡張可能**: "_どの_" Azure サービスでソフトウェアまたはサービスとの統合または拡張が可能か、"_どのように_" Azure サービスでのソフトウェアまたはサービスとの統合または拡張が可能かを、一覧情報の中に記述する必要があります。

また、Azure Marketplace 参加ポリシーに記載されているとおり、次のビジネス要件も満たす必要があります。

- 貴社 (またはその子会社) は、Azure Marketplace によってサポートされる販売元の国に所在している必要があります。
- Azure Marketplace でサポートされている課金モデルに対応した方法で、製品のライセンスを取得する必要があります。
- 無償、有償、コミュニティ サポートの活用を問わず、商業的に合理的な方法で、顧客に技術サポートを提供していただきます。
- 貴社のソフトウェアおよび依存関係にあるすべてのサード パーティ ソフトウェアにライセンス供与する必要があります。
- [azure.microsoft.com](../../C:/Users/Lisa.Rosenberger/Desktop/azure.microsoft.com) および Azure 管理ポータルにプランを一覧表示するための条件に適合するコンテンツを提供する必要があります。

最後に、[使用条件](https://azure.microsoft.com/support/legal/website-terms-of-use/)、[Microsoft のプライバシーに関する声明](http://www.microsoft.com/privacystatement/default.aspx)、[Microsoft Azure Certified プログラム契約](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/)に従うことに同意する必要があります。 

よく寄せられる質問の一覧については、[Azure Marketplace の FAQ](https://azure.microsoft.com/marketplace/faq/) を参照してください。

### <a name="virtual-machine-image-preparation"></a>仮想マシン イメージの準備

すべての前提条件を確認し、必要なタスクを完了したら、「[Azure Marketplace 向け仮想マシン イメージ作成ガイド](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation)」で詳しく説明されている、仮想マシン イメージのプランの作成を始められます。

このガイドでは、SKU の基盤として Azure Marketplace にデプロイする VHD を準備する方法を説明します。 

> [!NOTE]
> プロセスは、Linux ベースの SKU または Windows ベースの SKU のどちらを提供するかによって異なります。

> [!NOTE]
>  このプロセスは、次に示すとおり、[アカウントの作成および登録](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-accounts-creation-registration)と並行して実行できます。

### <a name="azure-certification"></a>Azure 認定

"_Azure 認定_" 状態は、オンボード プロセスが正常に完了したことを表します。 この状態になることで、顧客には、信頼できるパートナーから提供された Azure テクノロジを使用して実行または構築された、高品質なソリューションが自社の IT プロフェッショナルと開発者の元に提供されるという確信を持つことができます。 Azure 認定ソリューションには次のことが必要です。

- グローバル審査
- Azure プラットフォームとの互換性の判定
- オンライン イメージの安全性コンプライアンス
- ウイルスまたはマルウェアがないこと
- サポートされた課金モデル

## <a name="how-do-i-nominate-my-product-and-get-approved"></a>製品をノミネートし、承認を得るためにはどうすればよいですか?

ここでは、Azure Marketplace で製品を販売するための承認を得ます (図 3)。 Microsoft では、自社製品のノミネート、公開プロセスの完了、販売者としての登録をスムーズに進められるようにしています。

![Azure Marketplace で販売するための承認を得る](./media/cloud-partner-portal-seller-guide/gettingapprovedsteps.png)

図 3: Azure Marketplace で販売するための承認を得る手順

承認を得るための最初の手順として、登録や公開を行う前に製品を[ノミネート](https://createopportunity.azurewebsites.net/)します。 承認には "_最大 3 営業日_" かかることに注意してください。

承認されると、次のものが届きます。

- デベロッパー センターでのアプリケーション料金を 99 ドル免除するプロモーション コードと、CPP でのプロファイルが記載された電子メール。
- Azure 認定状態になるための技術的な事前承認と、プランを作成し VHD の認定を受けるためのオプション  (プランを作成する前に、デベロッパー センターでのアプリケーションが承認されている必要があります)。
- ポータル アカウントの詳細の公開に関する指示と、公開プロセスの概要。
- プロセスの説明を受け、質問するための、Microsoft オンボード チームへの電話問い合わせの資格。
- 2 つ目のプランを発行する資格。 2 回目となるプランでは、承認を得る必要がありません。 直接 CPP に移動させることができます。ただし、仮想マシンはそれまでと同様、公開プロセスを通じて認定される必要があります。
- 公開に関する支援を依頼するためのガイダンス  (質問は Marketplace の公開元の[サポート リンク](https://support.microsoft.com/getsupport?wf=0&amp;tenant=ClassicCommercial&amp;oaspworkflow=start_1.0.0.0&amp;locale=en-us&amp;supportregion=en-us&amp;pesid=15635&amp;ccsid=636233723471685249)で行ってください)。

最後に、Microsoft 販売者として[アカウントを登録](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-accounts-creation-registration)します。 承認と審査には "_最大 2 週間_" かかりますので、この間に CPP で Azure Marketplace のプランを作成してください。

## <a name="how-do-i-create-my-offer-in-the-cloud-partner-portal"></a>クラウド パートナー ポータルでプランを作成するにはどうすればよいですか?

これで、プランを作成し、仮想マシン イメージの認定を受ける準備が整いました。 これを行うには、クラウド パートナー ポータル (図 4) を使用します。 CPP は、[ソリューションの公開と管理](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-getting-started)のためのハブと考えることができます。

![クラウド パートナー ポータルで新しいプランを提供する](./media/cloud-partner-portal-seller-guide/newoffersetup.png)

図 4: クラウド パートナー ポータルで新しいプランを提供する

## <a name="what-about-best-practices"></a>ベスト プラクティスについて

Azure Marketplace で販売者となるメリットを最大限に活用していただくために、時間をかけなくても大きな効果を発揮するいくつかのツールとベスト プラクティスを用意しました。

## <a name="azure-test-drives"></a>Azure の体験版

[Azure の体験版](https://azure.microsoft.com/marketplace/test-drives/)は、潜在顧客に製品を発表し、購入前の試用という選択肢を提供するための優れた手段です。 これにより、コンバージョンを向上させ、見込みの高い潜在顧客を獲得することができます。

顧客は、連絡先を入力すると、構築済みの体験版を使用できるようになります。製品の主な機能とメリットを、実際の実装シナリオを通じて自ら試用することができます。 このように、Azure の体験版を使用すれば、多くの顧客が製品の魅力を実際に目にすることができます。

現時点では、製品の体験版の公開は、従来の公開ポータルでのみ実施できます。 これを行う方法のドキュメントについては、[こちら](https://github.com/Azure/AzureTestDrive/wiki)のページに移動し、"_新しい体験版の公開_" に関するセクションをご確認ください。

Azure の体験版については、[こちら](https://azuremarketplace.azureedge.net/documents/azure-marketplace-test-drive-program.pdf)を参照してください。

## <a name="go-to-market-checklist"></a>Go-to-Market チェックリスト

組織のグローバル リーチの拡大を支援する [Go-to-Market プログラム](https://partner.microsoft.com/go-to-market/)の詳細を確認してください。 [パートナー マーケティング センター](http://smartpartnermarketing.microsoft.com/isv)でリソースを活用することもできます。

リリースの前に、Azure Marketplace のプランがより早く顧客の目に留まるようにするために、いくつかの手順を実施することをお勧めします。 プランをリリースする準備ができているかどうかは、次のチェックリストですばやく確認できます。

- ブログの投稿、電子メールの送信、プレス リリースの発行を通じて、**プランが Azure Marketplace で入手できることを発表済みである**。
- **自分の Web サイトでプランをプロモーション**し、学習、検索、試用、購入、デプロイが可能な Azure Marketplace 上の自社プランに顧客を誘導している。
- **体験版を公開し**、顧客が休憩時間中にプランを Azure 上で実際に使用できるようにしている。
- **オンデマンドでの潜在顧客の生成を有効にしており**、顧客がアプリケーションをデプロイするためにクリックするたびに、顧客の名前と連絡先情報を受信できる。
- 販売機会を広げるために、Microsoft の**パートナー マネージャーと協力**できている (パートナー マネージャーがいる場合)。

## <a name="what-about-reports"></a>レポートについて

Azure Marketplace では、注文、使用状況、顧客に関するレポートが提供されます。このレポートには、Azure Marketplace の[公開元レポート ポータル](https://reports.azure.com)経由でアクセスできます (図 5)。 生データは誘導可能なテーブルとして提供され、CSV または XLS ファイルとしてダウンロードすることができます。

![公開元レポート ポータルを通じてレポートにアクセスする](./media/cloud-partner-portal-seller-guide/accessingreports.png)

図 5: 公開元レポート ポータルを通じてレポートにアクセスする

この[ビデオ](https://player.vimeo.com/video/200859918)では、レポートの機能と利点について、少しだけ紹介しています。たとえば以下の内容です。

- レポートの種類: ホーム ページ上での注文、使用状況、顧客の傾向に関する簡易スナップショット
- 注文、使用状況、顧客に関する詳細なデータ
- 月次の概要または 6 か月ごとの傾向ビューとして表示される注文と使用状況
- 標準として示されるいくつかの洞察
- 次のカテゴリ別の使用状況/注文:

-
  - 市場
  - チャネル
  - 注目のプラン
  - Marketplace ライセンス タイプ

詳細なレポートでは、会社名、連絡先情報、郵便番号をはじめとする地理的な所在地などの顧客情報が表示されます。これにより、顧客を比較することができます。 次の一覧は、Microsoft が提供している、顧客に関する具体的な属性です。

- Reseller
- FirstName
- LastName
- 電子メール
- CompanyName
- TransactionDate
- SubscriptionName
- AzureSubscriptionId
- CloudInstanceName
- OrderCount
- CustomerCountryRegion
- CustomerCity
- CustomerCommunicationCulture
- CustomerZipCode

ヘルプ ドキュメント、用語集、録画したデモなどのトレーニング資料もご用意しています。 レポートに関するヘルプまたはサポートが必要な場合は、[サポート チケット](https://support.microsoft.com/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&locale=en-us&supportregion=en-us&pesid=15635&ccsid=636233723471685249)を開くことができます。

