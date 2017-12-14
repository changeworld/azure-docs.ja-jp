---
title: "Azure Marketplace 販売者ガイド | Microsoft Docs"
description: "このガイドは、IT プロフェッショナルと開発者を対象とした Azure Certified 取得済み仮想マシン イメージおよびマネージ アプリケーションの販売に関心がある、独立系ソフトウェア ベンダー (ISV) のビジネス ユーザーと製品マネージャー向けに作成されています。"
documentationcenter: 
author: divacc
manager: prkumar
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/03/2017
ms.author: divacc@microsoft.com
ms.openlocfilehash: 9a9e9b3116c0d787162284197c8dbbd77374cff3
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2017
---
# <a name="azure-marketplace-seller-guide"></a>Azure Marketplace 販売者ガイド

Microsoft Azure Marketplace 販売者ガイドをご覧くださりありがとうございます。 このガイドは、IT プロフェッショナルと開発者を対象とした Azure Certified 取得済み仮想マシン イメージおよびマネージ アプリケーションの販売に関心がある、独立系ソフトウェア ベンダー (ISV) のビジネス ユーザーと製品マネージャー向けに作成されています。 Azure のお客様は世界中に存在します。[Azure Marketplace](https://azuremarketplace.microsoft.com/) を利用すれば、自社製品をきわめて多くのお客様にリーチさせることができます。

> [!NOTE]
> [マネージ アプリケーション](https://docs.microsoft.com/azure/managed-applications/overview)を販売したい場合は、そのアプリケーションを[こちら](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)の手順に従って Azure Marketplace のリストに掲載する必要があります。

> [!NOTE]
> 完成したサービスとしてのソフトウェア (SaaS) 製品を _IT プロフェッショナルと開発者_に販売したい場合は、各種オプションの詳細について saas-on-amp@microsoft.com までお問い合わせください。

> [!NOTE]
> 完成したサービスとしてのソフトウェア (SaaS) 製品を[ビジネス ユーザーに](https://appsource.microsoft.com)販売したい場合は、その製品を _AppSource_ のリストに掲載するための各種オプションをご確認ください。


このガイドの目的は、次のトピックに関する詳細をどこで確認できるか説明することです。

- Azure Marketplace とは何ですか?
- 製品が Marketplace にマッチしているかどうかはどのような方法で判断できますか?
- Marketplace で販売するメリットは何ですか?
- Marketplace で販売するための (技術面および技術面以外の) 前提条件は何ですか?
- Azure と互換性のある仮想ハード ディスク (VHD) はどのような方法で構築すればよいですか?
- 販売者として申請と登録を行うにはどうすればよいですか?
- プランはどのように作成して公開すればよいですか?
- 使用可能なリソースをマーケットで探すにはどうすればよいですか?
- Marketplace では、どのようなレポートと洞察が得られますか?
- ヘルプとサポートはどこで提供されますか?

それでは始めましょう。

## <a name="whats-the-azure-marketplace"></a>Azure Marketplace とは何ですか?

Azure Marketplace は、アプリケーションとサービスのオンライン マーケットプレースです。ここで、スタートアップから大企業に至るまで、さまざまな ISV が自社のソリューションを世界中の Azure のお客様に提供します。 Marketplace を利用すると、Azure 公開元が自社の仮想マシン イメージ、マネージ アプリケーション、および SaaS アプリケーションを、クラウドベースのアプリケーションやモバイル ソリューションを Azure で短期間で開発しようとしている他のプロフェッショナルに配布および販売することができます。 Azure Marketplace は、データ処理、データ ストレージ、分析レイヤーを備えたエンド ツー エンドの分析アプリケーションから階層型の eコマース アプリ (データ、サービス、インターネット) まで、幅広いサービスをサポートしています。

クラウドのお客様は、独自のニーズを満たすソリューションを検索する際に、いくつかの課題に直面します。 Marketplace では、お客様がこれらの課題を解決し、革新的な ISV のソリューションを見つけ出すための手段を提供しています。詳細は次の表のとおりです。

| 顧客ニーズ | Azure Marketplace のソリューション |
| --- | --- |
| ビジネス上のニーズと技術的なニーズを満たすため、追加のクラウド プラットフォーム機能が必要 | Azure の補完的なアプリケーションとサービスのポートフォリオを提供し、その数を増やし続ける |
| 適切なアプリケーションまたはサービスを見つけ出すことが困難と判明 | アプリケーションやサービスの発見、検索、購入が可能なワンストップ ショップを提供する |
| サードパーティ製のアプリケーションとサービスをデプロイするためのスケーラブルなしくみが必要 | サードパーティ製のアプリケーションとサービス向けにスケーラブルなデプロイの作成と構成ができるようにする |
| 新しいアプリケーションとサービスを既存のソリューションと統合して使用する必要がある | Azure でサードパーティ製のアプリケーションとサービスを既存のソリューションと簡単に統合できる |

Azure Marketplace は、高品質な Azure パートナー エコシステムの幅広い選択肢と強みを世界中のお客様に提供します。 主な利点:

- Microsoft とパートナーから提供される Azure ベースのサービスの場所が統合済み。
- 5,000 以上の販売提案。
- 統合されたプラットフォーム エクスペリエンス。
- 合理的な構成、デプロイ、管理。

## <a name="is-azure-marketplace-right-for-my-business"></a>Azure Marketplace は自分の会社のビジネスにマッチしているでしょうか?

ここまでで、Azure Marketplace が自社のビジネスに適しているかどうか、気になり始めた方もいるかもしれません。 では、適しているとすれば、何が得られるのでしょうか。 Marketplace は、新たな販売の機会を提供します。

- **Azure のソリューションの幅広いポートフォリオを新たな顧客市場に売り込む**。 これは、マイクロソフト オンライン サブスクリプション プログラム (MOSP) と Microsoft Enterprise Agreement のお客様が利用している Azure サービスに付随した Marketplace のアップセルやクロスセルの販売提案です。 Marketplace のサービスを、顧客のソリューションと Azure のシナリオ面に簡単に組み込むことができます。
- **ビジネス価値を高め、既存および新規の顧客アカウントの取引規模を拡大する**。 Marketplace は、取引規模の拡大、ワークロードをクラウドに移行する際の顧客の問題点への対処、および取引の収益性の向上に役立ちます。 包括的なソリューションを販売することでビジネス価値を高め、Azure クラウド プラットフォームのギャップに対応して顧客要件を満たします。
- **Marketplace のアプリケーションとサービスを販売することで、さらに幅広い潜在顧客にアピールする**。 Marketplace を利用すると、新規顧客の開拓と維持が容易になります。 今日の多くの企業は、変化の激しいインフラストラクチャ環境に適応するために、ワークロードをクラウドに切り替える必要に迫られています。 このギャップを解消するための、適切なアプリケーションとサービスを提供できます。
- **Marketplace での販売提案を Azure サービスとバンドルすることで、Azure の機能を補完し、拡張する**。 Marketplace を使用して、顧客とのシナリオ ベースの会話を組み立てることができます。 エンド ツー エンドのソリューションを紹介すれば、特定のプラットフォームに関するギャップや顧客ニーズにも対処できます。 さらに、ソリューションのバンドルを販売すれば、Azure プラットフォームのエコシステムを利用し、さまざまな顧客の問題に対処して、売上を伸ばすこともできます。

## <a name="what39s-the-customer-base-for-the-marketplace"></a>Marketplace の顧客ベースはどのようなものですか?

Marketplace のお客様は多岐にわたります。 さらに、Azure は、すべてのクラウド プロバイダーの急速に成長する顧客ベースの 1 つを保持しています。 このため、公共セクターと民間セクター、新興企業や大企業、さらには業界の枠も越えた、IT プロフェッショナルおよび開発者にアクセスできます。

## <a name="how-does-marketplace-work"></a>Marketplace のしくみはどのようなものですか?

非常に簡単です。 承認を得た後、Azure Certified 取得済み仮想マシン イメージまたはマネージ アプリケーションを作成し、Marketplace に公開すれば、 Azure のお客様がその製品を数分で検索、購入、およびデプロイできるようになります。 これに加えて、お客様は安心してソリューションをデプロイできます。 Azure での使用条件を満たす環境が構成済みで、インフラストラクチャを数分以内に使用可能であることがわかっているためです。

クラウド パートナー ポータルは、Marketplace での販売提案を作成するためのハブです。 公開の準備を整えるためにプランの認定を受けるには、特定の前提条件を満たす必要があります。 これについては、次のセクションで説明します。


## <a name="whats-next"></a>次の手順

Azure Marketplace はまさに自社の製品にぴったりだ、と考えていることでしょう。 では、どのように始めればよいのでしょうか。 このセクションでは、仮想マシン イメージの場合の Marketplace の開始手順について重点的に説明します (図 1)。 
* Azure Certified を取得する。
* 製品を販売するための承認を得る。
* Microsoft Cloud パートナー ポータルでプランを作成して公開する。 

![Azure Marketplace での販売のためのプロセス](./media/cloud-partner-portal-seller-guide/processforselling.png)

図 1: Azure Marketplace での販売のためのプロセス

公開しようとしているワークロード (仮想マシンまたはマネージ アプリケーション) に応じて、プランを公開するための異なるプロセスに従います。 以下の内容では、"_仮想マシン プラン_" を公開する場合について重点的に説明します。

> [!NOTE]
> [マネージ アプリケーション](https://docs.microsoft.com/azure/managed-applications/overview)を販売したい場合は、そのアプリケーションを[こちら](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)の手順に従って Azure Marketplace のリストに掲載する必要があります。

> [!NOTE]
> 完成したサービスとしてのソフトウェア (SaaS) 製品を _IT プロフェッショナルと開発者_に販売したい場合は、各種オプションの詳細について saas-on-amp@microsoft.com までお問い合わせください。


最初に技術面および技術面以外の一連の前提条件を満たし、仮想マシン イメージを準備します。 次に、製品をノミネートし、販売者として登録します。 最後に、マーケティングのコンテンツを追加して、公開用に送信します。 販売提案は、Marketplace で提供開始する前に、プレビューまたはステージング環境で確認することができます。

初めて Azure Marketplace の販売提案を作成する際は、基本的な配布準備のために 4 週間程度の時間を取る必要があります。 可能であれば、販売提案をリリースする前の 6 週間で構築を行います。これにより、メディアや公開タスクの時間を確保できます。

## <a name="how-do-i-become-azure-certified"></a>Azure Certified を取得するにはどうすればよいですか?

Azure Marketplace の販売提案を作成するための最初の手順は、Azure Certified を取得することです。 つまり、会社情報をまとめ、参加ポリシーに同意し、必要なツールをダウンロードして、技術コンポーネントを構築することを指します (図 2) 。

![Azure Certified を取得するための要件](./media/cloud-partner-portal-seller-guide/azurecertified.png)

図 2: Azure Certified を取得するための要件


### <a name="technical-prerequisites"></a>技術的な前提条件

プランをリリースする前に、慎重に確認を行い、技術的な前提条件すべてを満たしてください。 Windows または Linux だけでなく、テスト ツールにリンクされた Azure 対応 VHD へのアクセスも必要になります。

リモート デスクトップ プロトコルを使用してクラウドで直接 Azure VHD を開発することをお勧めします。 ただし、VHD をダウンロードして、[オンプレミスのインフラストラクチャ](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-on-premise)を使用する以外の方法がない場合は、この方法で開発することができます。

詳細については、[技術的な前提条件](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-prerequisites)に関するページを参照してください。

### <a name="nontechnical-prerequisites"></a>技術面以外の前提条件

Marketplace に参加するには、技術面以外の前提条件をいくつか満たす必要があります。 最初に、[Azure Marketplace 参加ポリシー](https://azure.microsoft.com/support/legal/marketplace/participation-policies/)の条項を確認し、同意します。 Marketplace で提供されるソフトウェアとサービスは、以下の要件のうち少なくとも 1 つを満たしている必要があります。

- **Azure 上で稼働する**。 ソフトウェアまたはサービスの基本機能は、Microsoft Azure 上で稼働するものである必要があります。
- **Azure にデプロイされている**。 一覧情報で、ソフトウェアまたはサービスが Azure にどのようにデプロイされているかを記述する必要があります。
- **Azure サービスに統合されている、または Azure サービスを拡張している**。 一覧情報で、次を記述する必要があります。
  - ソフトウェアまたはサービスが統合されている、または拡張している Azure サービス
  - ソフトウェアまたはサービスが Azure サービスにどのように統合されているか、または Azure サービスをどのように拡張しているか

また、Azure Marketplace 参加ポリシーに記載されているとおり、次のビジネス要件も満たす必要があります。

- 貴社 (またはその子会社) は、Marketplace によってサポートされる販売元の国に所在している必要があります。
- Marketplace でサポートされている課金モデルに対応した方法で、製品のライセンスを取得する必要があります。
- 無償、有償、コミュニティ サポートの活用を問わず、商業的に合理的な方法で、顧客に技術サポートを提供していただきます。
- 貴社のソフトウェアおよび依存関係にあるすべてのサード パーティ ソフトウェアにライセンス供与する必要があります。
- [azure.microsoft.com](../../C:/Users/Lisa.Rosenberger/Desktop/azure.microsoft.com) および Azure ポータルにプランを一覧表示するための所定の条件を、コンテンツが満たしている必要があります。

最後に、[使用条件](https://azure.microsoft.com/support/legal/website-terms-of-use/)、[Microsoft のプライバシーに関する声明](http://www.microsoft.com/privacystatement/default.aspx)、および [Microsoft Azure Certified プログラム契約](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/)に同意する必要があります。 

よく寄せられる質問の一覧については、[Azure Marketplace の FAQ](https://azure.microsoft.com/marketplace/faq/) を参照してください。


### <a name="azure-certification"></a>Azure 認定

"_Azure Certified_" ステータスの獲得は、オンボード プロセスが正常に完了したことを表します。 この状態になることで、顧客には、信頼できるパートナーから提供された Azure テクノロジを使用して実行または構築された、高品質なソリューションが自社の IT プロフェッショナルと開発者の元に提供されるという確信を持つことができます。 Azure Certified のソリューションに含まれるものは次のとおりです。

- グローバル審査。
- Azure プラットフォームとの互換性の判定。
- オンライン イメージの安全性コンプライアンス。
- ウイルスまたはマルウェアがないこと。
- サポートされる課金モデル。

## <a name="how-do-i-nominate-my-product-and-get-approved"></a>製品をノミネートし、承認を得るためにはどうすればよいですか?

ここでは、Marketplace で製品を販売するための承認を得ます (図 3)。 

Microsoft では、自社製品のノミネート、公開プロセスの完了、販売者としての登録をスムーズに進められるようにしています。 公開しようとしているワークロード (仮想マシンまたはマネージ アプリケーション) に応じて、異なるノミネート プロセスに従います。

![Azure Marketplace で販売するための承認を得る](./media/cloud-partner-portal-seller-guide/gettingapprovedsteps.png)

図 3: Azure Marketplace で販売するための承認を得る手順

"_仮想マシン オファー_" を公開したい場合は、[こちら](https://createopportunity.azurewebsites.net/)で製品をノミネートします。 承認には "_最大 3 営業日_" かかります。

承認されると、次のものが届きます。

- デベロッパー センターでのアプリケーション料金を 99 ドル免除するプロモーション コードと、クラウド パートナー ポータルでのプロファイルが記載された電子メール。
- Azure Certified ステータスへの技術面における事前承認と、プランを作成し VHD の認定を受けるためのオプション (プランを作成する前に、デベロッパー センターでのアプリケーションが承認されている必要があります)。
- クラウド パートナー ポータルにアクセスする手順と、公開プロセスの概要。
- プロセスの説明を受け、質問するための、Microsoft オンボード チームへの電話問い合わせの資格。
- 2 つ目のプランを発行する資格。 2 回目となるプランでは、承認を得る必要がありません。 クラウド パートナー ポータルに直接アクセスできます。ただし、仮想マシンについては、引き続き公開プロセスを通じて認定を受ける必要があります。
- 公開に関する支援を依頼するためのガイダンス  (質問は Marketplace の公開元の[サポート リンク](https://support.microsoft.com/en-us/getsupport?oaspworkflow=start_1.0.0.0&wf=0&wfName=productselection&prid=16230&ccsid=636282352448485256)で行ってください)。

最後に、Microsoft 販売者として[アカウントを登録](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-accounts-creation-registration)します。 承認と審査には "_最大 2 週間_" かかりますので、この間にクラウド パートナー ポータルで Azure Marketplace の販売提案を作成してください。

## <a name="how-do-i-publish-my-offer-on-the-azure-marketplace"></a>Azure Marketplace で販売提案を発行するにはどうすればよいですか?

仮想マシン イメージの認定を取得して販売提案を発行する準備は整いました。 プランを発行するには、クラウド パートナー ポータルを使用します。 クラウド パートナー ポータルは、ソリューションを公開および管理するためのハブと考えることができます。 基本的には、VHD をアップロードし、マーケティングのコンテンツと SKU 詳細を追加して、認定および確認のための販売提案を送信するだけです。 Marketplace で運用を開始する前に、販売提案をプレビューし、その表示を確認します。

## <a name="what-about-best-practices"></a>ベスト プラクティスについて

Marketplace で販売者となるメリットを最大限に活用するためのベスト プラクティスをいくつか用意しました。

### <a name="azure-test-drives"></a>Azure の体験版

[Azure の体験版](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/test-drives?page=1)は、潜在顧客に製品を発表し、購入前の試用という選択肢を提供するための優れた手段です。 これにより、コンバージョンが向上し、有望な潜在顧客を獲得することができます。

連絡先を入力した顧客が、構築済みの体験版を使用できるようになります。 試用版では、製品の主な機能とメリットを、実際のシナリオを通じて試用することができます。 それに加えて、 プランに関連した体験版を公開することにより、最大 36% の潜在顧客をさらに獲得できます。

体験版を公開するために必要なすべてのガイダンスは、オンボード プロセス中に Microsoft Cloud パートナー ポータルで提供されます。 Azure の体験版の詳細については、[こちら](https://azuremarketplace.azureedge.net/documents/azure-marketplace-test-drive-program.pdf)を参照してください。

###<a name="lead-generation"></a>潜在顧客の生成

製品を Azure Marketplace に掲載することの重要な価値の 1 つは、御社の製品に関心があるか、御社の製品をデプロイしたことがある適格な潜在顧客が送信されるという点にあります。 さらに良いことに、それらの潜在顧客は御社の CRM に直接送信されるため、迅速なアクションの実行やフォローアップが可能になり、より多くのチャンスの創出や取引の成立につながります。 潜在顧客の生成の統合には数分かかります。この処理は、Microsoft Cloud パートナー ポータルでの公開プロセス中に行われます。

### <a name="go-to-market-checklist"></a>Go-to-Market チェックリスト

組織のグローバル リーチの拡大を支援する [Go-to-Market プログラム](https://partner.microsoft.com/go-to-market/)の詳細を確認してください。 [パートナー マーケティング センター](http://smartpartnermarketing.microsoft.com/isv)でリソースを活用することもできます。

リリースの前に、Marketplace の販売提案がより早く顧客の目に留まるよう、いくつかの手順を実施することをお勧めします。 次のチェックリストを使用して、準備ができているかどうかを確認してください。

- **Marketplace で販売提案が入手できることを発表済み**であり、ブログの投稿、電子メールの送信、プレス リリースの発行をその手段として利用。
- **自分の Web サイトで販売提案をプロモーション**し、Marketplace 上にある自社の販売提案に顧客を誘導している。
- **体験版を公開し**、顧客が休憩時間中にプランを Azure 上で実際に使用できるようにしている。
- **オンデマンドでの潜在顧客の生成を有効にしており**、顧客がアプリケーションをデプロイするためにクリックするたびに、顧客の名前と連絡先情報を受信できる。
- 販売機会を広げるために、Microsoft の**パートナー マネージャーと協力**できている (パートナー マネージャーがいる場合)。

## <a name="what-about-reports"></a>レポートについて

Marketplace では、注文、使用状況、顧客に関するレポートが提供されます。このレポートには、Microsoft Cloud パートナー ポータルからアクセスできます。 有用な洞察や分析のほか、生データが誘導可能なテーブルとして提供され、CSV または XLS ファイルとしてダウンロードすることができます。

Insights の機能とメリットには、たとえば次のようなものがあります。

- レポートの種類: Insights ホーム ページ上での注文、使用状況、デプロイ、顧客の傾向に関する簡易スナップショット。
- 注文、使用状況、顧客に関する詳細なデータ。
- 月次の概要または 6 か月ごとの傾向ビューとして表示される注文と使用状況。
- 次のカテゴリ別の使用状況/注文:
  - 市場
  - チャネル
  - 注目のプラン
  - Marketplace ライセンス タイプ

詳細なレポートでは、会社名、郵便番号レベルに至るまでの地理的な所在地などの顧客情報が表示されます。これにより、顧客を比較したり販売者に報酬を支払ったりできます。 次の一覧は、Microsoft が提供している、顧客に関する具体的な属性です。

- Reseller
- FirstName
- LastName
- Email
- CompanyName
- TransactionDate
- SubscriptionName
- AzureSubscriptionId (PAYG のお客様のみ)
- CloudInstanceName
- OrderCount
- CustomerCountryRegion
- CustomerCity
- CustomerCommunicationCulture
- CustomerZipCode

ヘルプ ドキュメント、用語集、録画したデモなどのトレーニング資料もご用意しています。 レポートに関するヘルプまたはサポートが必要な場合は、Microsoft Cloud パートナー ポータルからサポート チケットを開くことができます。

ISV 販売者コミュニティへの皆様のご参加をお待ちしております。

---
