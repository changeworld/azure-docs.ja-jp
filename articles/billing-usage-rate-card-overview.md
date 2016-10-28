<properties
   pageTitle="Microsoft Azure リソースの消費を把握する | Microsoft Azure"
   description="Azure Billing Usage API と RateCard API の概念の概要について説明します。これらの API を使用すると、Azure のリソース消費と動向を把握できます。"
   services=""
   documentationCenter=""
   authors="BryanLa"
   manager="mbaldwin"
   editor=""
   tags="billing"/>

<tags
   ms.service="billing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="billing"
   ms.date="08/16/2016"
   ms.author="mobandyo;bryanla"/>

# Microsoft Azure リソースの消費を把握する

ユーザーとパートナーには、Azure のコストを正確に予測し、管理する機能が必要です。資本支出モデルから運営コスト モデルに移行する場合、特に大規模なクラウド デプロイの場合には、ショーバックとチャージバックを分析する機能だけでなく、見積と課金に関するモードの忠実度も提供する必要があります。

このようなニーズに対して、この記事で説明する Azure Resource Usage API と Rate Card API を利用すると、Azure リソースの消費を新しい方法で把握できるようになります。

## Azure Resource Usage API と RateCard API

Azure Resource Usage API と RateCard API は、Azure リソース マネージャーが公開している API ファミリに含まれ、リソース プロバイダーとして実装されています。

### Azure Resource Usage API (プレビュー)
ユーザーとパートナーは、Azure Resource Usage API を使用して推定 Azure 消費データを取得できます。機能は、次のとおりです。

- **Azure ロールベースのアクセス制御** - ユーザーとパートナーは、[Azure ポータル](https://portal.azure.com)または [Azure PowerShell コマンドレット](powershell-install-configure.md)を使用してアクセス ポリシーを構成して、サブスクリプションの使用状況データにアクセスできるユーザーまたはアプリケーションを指定できます。呼び出し元は、認証に Azure Active Directory トークンを使用する必要があります。また、呼び出し元が特定の Azure サブスクリプションの使用状況データにアクセスするには、リーダー、所有者、または共同作成者のロールに呼び出し元を追加する必要があります。

- **時間単位または日単位の集計** - 呼び出し元は、時間単位のバケットまたは日単位のバケットのどちらで Azure 使用状況データを取得するかを指定できます。既定値は日単位です。

- **提供されるインスタンス メタデータ (リソース タグなど)** - 応答には、完全修飾リソース URI (/subscriptions/{subscription-id}/..) などのインスタンスレベルの詳細に加え、リソース グループ情報とリソース タグが含まれます。ユーザーはこのデータのタグを使用して、クロス課金などの使用事例について、確定的にプログラムで使用状況を割り当てることができます。

- **提供されるリソース メタデータ** - 測定名、測定カテゴリ、測定サブカテゴリ、単位、リージョンなど、リソースの詳細も応答で渡されるので、呼び出し元は消費内容をより詳しく理解できます。また、ユーザーがエクスペリエンス全体でデータを関連付けることができるように、現在、Azure ポータル、Azure 使用状況 CSV、EA 課金 CSV など、一般公開されているエクスペリエンス全体でリソース メタデータの用語を揃えています。

- **すべてのプラン タイプの使用状況** - 従量課金制、MSDN、料金コミットメント、料金クレジット、EA など、すべてのプラン タイプについて使用状況データにアクセスできます。

### Azure Resource RateCard API (プレビュー)
ユーザーとパートナーは、Azure Resource RateCard API を使用して、使用できる Azure リソースの一覧と、それぞれの推定料金情報を取得できます。機能は、次のとおりです。

- **Azure ロールベースのアクセス制御** - ユーザーとパートナーは、[Azure ポータル](https://portal.azure.com)または [Azure PowerShell コマンドレット](powershell-install-configure.md)を使用してアクセス ポリシーを構成し、RateCard データにアクセスできるユーザーまたはアプリケーションを指定できます。呼び出し元は、認証に Azure Active Directory トークンを使用する必要があります。また、呼び出し元が特定の Azure サブスクリプションの使用状況データにアクセスするには、リーダー、所有者、または共同作成者のロールに呼び出し元を追加する必要があります。

- **従量課金制、MSDN、料金コミットメント、料金クレジット プランのサポート (EA はサポートされていません)** - この API は、Azure のプラン レベルとサブスクリプション レベルの料金情報を提供しています。この API の呼び出し元は、プラン情報を渡してリソースの詳細と料金を取得する必要があります。EA プランには登録ごとにカスタマイズされた料金があるので、現在は EA 料金を提供できません。

## シナリオ

Usage API と RateCard API を組み合わせて実現できるシナリオ例を次に示します。

- **月間の Azure 使用量** - Usage API と RateCard API を組み合わせて使用すると、時間単位または日単位でバケットの使用状況と料金の推定を分析できるので、月間のクラウドの使用状況をより詳しく把握できます。

- **アラートの設定** - ユーザーとパートナーは、Usage API と RateCard API を使用して推定の消費と料金を取得することで、クラウドの消費に対してリソースベースまたは料金ベースのアラートを設定できます。

- **請求額の予測** - ユーザーとパートナーは推定の消費とクラウドの使用状況を取得し、Machine Learning アルゴリズムを適用して、課金サイクルの終了時に請求される金額を推測することができます。

- **消費前のコスト分析** - ユーザーは、RateCard API を使用し、予定の使用数を指定することで、ワークロードを Azure に移行した場合の請求金額を予測することもできます。他のクラウドまたはプライベート クラウドに既存のワークロードがある場合、その使用状況を Azure の料金に対応付けることで、より正確な Azure の推定使用量を取得できます。その結果、[Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/)で取得したデータがわかりやすくなります。(たとえば) 課金パートナーは、プランを決め、従量課金制以外のプラン タイプ (料金コミットメントや料金クレジットなど) の比較、対照を行うことができるようになります。また、世界各国のさまざまなデータセンターにリソースをデプロイすると、合計コストに直接的な影響があるので、API には、デプロイの決定に必要な what-if 分析の種類を有効にして、コスト見積をリージョン別に変更する機能も用意されています。

- **what-if 分析** -

	- ユーザーとパートナーは、Azure リソースのリージョンや構成を変えた場合に、コスト効率が高くなるかどうかを判断できます。Azure リソースのコストは、稼動している Azure リージョンに基づいて異なる場合があります。ユーザーとパートナーは、what-if 分析を利用してコストを最適化することができます。

	- また、別の Azure プラン タイプの方が Azure リソースの料金が安くなるかどうかについても判断できます。

## パートナー ソリューション

「[Cloudyn は Microsoft Azure Usage API と RateCard API を使用してユーザー向けに ITFM を提供](billing-usage-rate-card-partner-solution-cloudyn.md)」では、Azure Billing API パートナーの [Cloudyn](https://www.cloudyn.com/microsoft-azure/) が提供した統合エクスペリエンスについて説明しています。この記事では、Azure ユーザーが Cloudyn と Azure Billing API を使用して Azure 消費データを把握する方法に関する短時間のビデオなどで、統合エクスペリエンスについて詳しく説明しています。

「[Cloud Cruiser と Microsoft Azure Billing API の統合](billing-usage-rate-card-partner-solution-cloudcruiser.md)」では、[Cloud Cruiser の Express for Azure Pack](http://www.cloudcruiser.com/partners/microsoft/) を WAP ポータルから直接利用して、ユーザーが Microsoft Azure のプライベート クラウドまたはホスト型パブリック クラウドの運用面と料金面の両方を 1 つのユーザー インターフェイスで管理できるようにする方法について説明しています。

## 次のステップ
+ 両方の API の詳細については、「[Azure Billing REST API リファレンス](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c)」を参照してください (Azure リソース マネージャーに用意されている API セットに含まれています)。
+ サンプル コードをすぐに確認するには、「[Azure コード サンプル](https://azure.microsoft.com/documentation/samples/?term=billing)」の「Microsoft Azure Billing API コード サンプル」を参照してください。

## 詳細情報
+ Azure リソース マネージャーの詳細については、「[Azure リソース マネージャーの概要](resource-group-overview.md)」を参照してください。
+ クラウドの使用量を把握するために必要なその他のツール群については、Gartner の記事「[IT 財務管理 (ITFM) ツールの市場ガイド](http://www.gartner.com/technology/reprints.do?id=1-212F7AL&ct=140909&st=sb)」を参照してください。

<!---HONumber=AcomDC_0817_2016-->