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
ms.date: 10/20/2017
ms.author: gauravbh
ms.openlocfilehash: b4fda06f85c7dab52ff38558b0d928193e0694f6
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
---
# <a name="azure-managed-applications-in-the-marketplace"></a>Marketplace の Azure マネージ アプリケーション

ベンダーは、Azure マネージ アプリケーションを使用して、各自のソリューションを、すべての Azure Marketplace ユーザーに提供できます。 これらのベンダーには、マネージ サービス プロバイダー (MSP)、独立系ソフトウェア ベンダー (ISV)、およびシステム インテグレーター (SI) を含めることができます。 マネージ アプリケーションは、顧客のメンテナンスとサービスのオーバーヘッドを削減します。 ベンダーは Marketplace を通じてインフラストラクチャとソフトウェアを販売できます。 また、サービスと運用サポートをマネージ アプリケーションに付加できます。 詳細については、[マネージ アプリケーションの概要](overview.md)に関するページをご覧ください。

この記事では、アプリケーションを Marketplace に発行して、顧客が幅広く利用できるようにする方法について説明します。

## <a name="prerequisites-for-publishing-a-managed-application"></a>マネージ アプリケーションを発行するための前提条件

この記事を完了するには、マネージ アプリケーション定義の .zip ファイルを既に用意している必要があります。 詳細については、[サービス カタログ アプリケーションの作成](publish-service-catalog-app.md)に関する記事を参照してください。

さらに、いくつかのビジネス上の前提条件があります。 それらを次に示します。

* 貴社またはその子会社は、Marketplace によって販売がサポートされる国に所在している必要があります。
* Marketplace でサポートされている課金モデルに対応した方法で、製品のライセンスを取得する必要があります。
* 商取引上合理的な方法で、顧客が技術サポートを利用できるようにします。 無償、有償、コミュニティ サポートの活用など、方法は問いません。
* 貴社のソフトウェアおよび依存関係にあるすべてのサード パーティ ソフトウェアにライセンスを供与します。
* Marketplace および Azure Portal にプランが掲載されるための条件を満たすコンテンツを提供します。
* Azure Marketplace 参加ポリシーと発行者契約の条項に同意します。
* 使用条件、Microsoft のプライバシーに関する声明、および Microsoft Azure 認定プログラム契約に従うことに同意します。

## <a name="set-up-your-account-for-publishing-portal"></a>発行ポータルのアカウントを設定する

発行ポータルは、プランの発行と管理に使用されます。 Marketplace アプリケーションを発行するには、Azure Marketplace の承認済みの Microsoft 開発者アカウントが必要です。 承認済みのアカウントを登録していない場合は、「[Microsoft 開発者アカウントの作成](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md)」をご覧ください。

承認済みの **Microsoft デベロッパー センター** アカウントを持っていても、[Azure 発行ポータル](https://cloudpartner.azure.com/)をこれまで使用したことがない場合は、発行ポータルに登録する必要があります。

1. 新しい Chrome Incognito または Internet Explorer InPrivate ブラウズ セッションを開き、個人用アカウントにサインインしていないことを確認します。
2. [https://cloudpartner.azure.com/](https://cloudpartner.azure.com/) に移動します。
3. 新規ユーザーが発行ポータルに初めてサインインする場合は、デベロッパー センター アカウントと同じ電子メール ID でサインインする必要があります。 現在、デベロッパー センター アカウントと発行ポータル アカウントはリンクされています。

発行ポータルで、会社の他のメンバーを後で[共同管理者](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md#4-steps-to-add-a-co-admin-in-the-publishing-portal)として追加できます。 発行ポータルで共同管理者として追加されている場合は、共同管理者アカウントでサインインできます。

> [!TIP]
> 参加ポリシーについては、 [Azure Web サイト](https://azure.microsoft.com/support/legal/marketplace/participation-policies/)を参照してください。
>
>

## <a name="create-a-new-azure-application-offer"></a>新しい Azure アプリケーション プランの作成

前提条件を満たしたら、マネージ アプリケーション プランを作成できます。

### <a name="set-up-an-offer"></a>プランの設定

マネージ アプリケーション用のプランは、発行者から提供される製品のクラスに相当します。 Marketplace で入手できるようにする新しい種類のアプリケーションがある場合は、それを新しいプランとして設定できます。 プランとは、SKU のコレクションです。 各プランは、Marketplace で独自のエンティティとして表示されます。

1. [クラウド パートナー ポータル](https://cloudpartner.azure.com/)にサインインします。

1. 左側のナビゲーション バーで、**[+ 新しいプラン]** > **[Azure Applications]\(Azure アプリケーション\)** を選択します。

   ![新しいプラン](./media/publish-marketplace-app/newOffer.png)

1. **[エディター]** ビューに必要なフォームが表示されます。 各フォームについては、この記事の後半で説明します。

   ![プラン設定](./media/publish-marketplace-app/newOffer_OfferSettings.png)

## <a name="offer-settings-form"></a>[プランの設定] フォーム

**[プランの設定]** フォームには、次のフィールドがあります。

* **プラン ID**: この一意識別子は、発行元プロファイル内のプランを特定します。 この ID は、製品 URL、Resource Manager テンプレート、課金レポートに表示されます。 小文字の英数字またはハイフン (-) のみで構成できます。 ID の末尾にはダッシュを使用できません。 最大 50 文字に制限されます。 プランの運用が開始されると、このフィールドはロックされます。
* **発行元 ID**: このドロップダウン リストを使用して、このプランを発行するための発行元プロファイルを選択します。 プランの運用が開始されると、このフィールドはロックされます。
* **名前**: このプランの表示名は、Marketplace とポータルに表示されます。 最大で 50 文字の長さにできます。 製品の覚えやすいブランド名を使用してください。 また、販売方法である場合を除き、ここでは会社名を使用しないでください。 このプランを貴社の Web サイトでマーケティングしている場合は、この名前が、貴社の Web サイトで表示される名前とまったく同じであることを確認します。

操作が終わったら、**[保存]** を選択して進捗状況を保存します。

## <a name="skus-form"></a>[SKU] フォーム

次の手順では、作成したプラン用に SKU を追加します。

SKU とは、プランの購入可能な最小単位です。 同じ製品クラス (プラン) 内では、SKU を使用して次を区別できます。

* サポートされる各種機能
* プランが管理対象か管理対象外か
* サポートされる課金モデル

SKU は、Marketplace では親プランの下に表示されます。 Azure Portal では、独自の購入可能エンティティとして表示されます。

1. **[SKU]** > **[新しい SKU]** を選択します。

   ![新しい SKU を選択する](./media/publish-marketplace-app/newOffer_skus.png)

1. **SKU ID** を入力します。 SKU ID は、プラン内の SKU を表す一意識別子です。 この ID は、製品 URL、Resource Manager テンプレート、課金レポートに表示されます。 小文字の英数字またはハイフン (-) のみで構成できます。 ID の末尾にはダッシュを使用できず、最大 50 文字に制限されます。 プランの運用が開始されると、このフィールドはロックされます。 プラン内の SKU を複数にすることができます。 発行するイメージごとに SKU が必要です。

1. 次のフォームの **[SKU Details]\(SKU の詳細\)** セクションに入力します。

   ![新しい SKU を指定する](./media/publish-marketplace-app/sku-settings.png)

   次のフィールドに入力します。

   * **タイトル**: この SKU のタイトルを入力します。 このタイトルは、ギャラリーでこの項目に対して表示されます。
   * **概要**: この SKU の簡単な概要を入力します。 このテキストは、タイトルの下に表示されます。
   * **説明**: SKU に関する詳細な説明を入力します。
   * **SKU の種類**: 使用できる値は、*[Managed Application]\(マネージ アプリケーション\)* と *[ソリューション テンプレート]* です。 この場合は、*[Managed Application]\(マネージ アプリケーション\)* を選択します。
   * **提供先の国/地域**: マネージ アプリケーションを利用できる国を選択します。

      ![国を選択する](./media/publish-marketplace-app/select-country.png)

   * **価格**: アプリケーションを管理するための価格を指定します。 価格を設定する前に利用可能な国を選択します。

1. 新しいパッケージを追加します。 次のフォームの **[パッケージの詳細]** セクションに入力します。

   ![パッケージ](./media/publish-marketplace-app/new-package.png)

   次のフィールドに入力します。

   * **現在のバージョン**: アップロードするパッケージのバージョンを入力します。 `{number}.{number}.{number}{number}` 形式にする必要があります。
   * **パッケージ ファイルの選択**: このパッケージには、.zip パッケージに圧縮された必須ファイルが含まれています。 詳細については、[サービス カタログ アプリケーションの作成](publish-service-catalog-app.md)に関する記事を参照してください。
   * **PrincipalId**: このプロパティは、顧客のサブスクリプション内のリソースへのアクセス権が付与されているユーザー、ユーザー グループ、またはアプリケーション の Azure Active Directory (Azure AD) 識別子です。 ロールの定義では、アクセス許可について説明します。
   * **Role Definition**: このプロパティは、Azure AD によって提供されているロールベースのアクセス制御 (RBAC) の組み込みロールの一覧です。 最も適切なロールを選択して使用することで、顧客に代わってリソースを管理できます。

複数の承認を追加できますが、 AD ユーザー グループを作成し、その ID を **PrincipalId** で指定することをお勧めします。 これにより、SKU を更新せずに、より多くのユーザーをユーザー グループに追加できます。

RBAC の詳細については、[Azure Portal での RBAC の使用](../active-directory/role-based-access-control-what-is.md)に関するページをご覧ください。

## <a name="marketplace-form"></a>[Marketplace] フォーム

[Marketplace] フォームでは、[Azure Marketplace](https://azuremarketplace.microsoft.com) と [Azure Portal](https://portal.azure.com/) に表示するフィールドについて確認します。

### <a name="preview-subscription-ids"></a>サブスクリプション ID をプレビュー

発行されたプランにアクセスできる Azure サブスクリプション ID の一覧を入力します。 このホワイトリストに追加されたサブスクリプションを使用して、プレビューされたプランを運用開始前にテストできます。 パートナー ポータルでは、最大 100 個のサブスクリプションのホワイト リストをコンパイルできます。

### <a name="suggested-categories"></a>推奨されるカテゴリ

プランとの最適な関連付けが可能な最大 5 つのカテゴリを、一覧から選択します。 このカテゴリは、[Azure Marketplace](https://azuremarketplace.microsoft.com) と [Azure Portal](https://portal.azure.com/) で利用可能な製品カテゴリに、プランをマッピングするときに使用されます。

#### <a name="azure-marketplace"></a>Azure Marketplace

マネージ アプリケーションの概要には、次のフィールドが表示されます。

![Marketplace の概要](./media/publish-marketplace-app/publishvm10.png)

マネージ アプリケーションの **[概要]** タブには、次のフィールドが表示されます。

![Marketplace の概要](./media/publish-marketplace-app/publishvm11.png)

マネージ アプリケーションの **[プランと価格]** タブには、次のフィールドが表示されます。

![Marketplace のプラン](./media/publish-marketplace-app/publishvm15.png)

#### <a name="azure-portal"></a>Azure ポータル

マネージ アプリケーションの概要には、次のフィールドが表示されます。

![ポータルの概要](./media/publish-marketplace-app/publishvm12.png)

マネージ アプリケーションの概要には、次のフィールドが表示されます。

![ポータルの概要](./media/publish-marketplace-app/publishvm13.png)

#### <a name="logo-guidelines"></a>ロゴのガイドライン

クラウド パートナー ポータルにアップロードするロゴについては、必ず以下のガイドラインに従ってください。

*   Azure の設計には簡単なカラー パレットがあります。 ロゴのプライマリ カラーとセカンダリ カラーの数は制限してください。
*   ポータルのテーマの色は白黒です。 この色を、ロゴの背景色として使用しないでください。 ポータルでロゴが目立つ色を使用します。 背景色としてはシンプルなプライマリ カラーをお勧めします。 "*透明な背景を使用している場合は、ロゴおよびテキストが白、黒、または青ではないことを確認します。*"
*   ロゴではグラデーションの背景を使用しないでください。
*   ロゴにはテキストを使用しないでください (会社またはブランドの名前であっても)。 ロゴのルック アンド フィールはフラットにして、グラデーションは避ける必要があります。
*   ロゴが伸縮されていないことを確認してください。

#### <a name="hero-logo"></a>Hero ロゴ

Hero ロゴはオプションです。 発行元は、Hero ロゴをアップロードしなくてもかまいません。 Hero アイコンがアップロードされたら、削除できません。 その時点で、パートナーは、Hero アイコンの Marketplace ガイドラインに従う必要があります。

Hero ロゴ アイコンに関するガイドラインに従ってください。

*   発行元表示名、プランとのタイトル、およびプランの長い概要は、白のフォント色で表示されます。 したがって、Hero アイコンの背景には明るい色を使用しないでください。 黒、白、または透明の背景は、Hero アイコンでは許可されていません。
*   プランが表示されると、プログラムによって Hero ロゴ内に要素が埋め込まれます。 埋め込み要素として、発行元表示名、プラン タイトル、プランの概要 (長文)、**[作成]** ボタンなどがあります。 したがって、Hero ロゴのデザイン時にはテキストを入力しないでください。 テキストはプログラムによってその場所に挿入されるため、右側にはスペースを空けておいてください。 右側のテキスト用の空のスペースは 415 x 100 ピクセルです。 左からのオフセットは 370 ピクセルです。

    ![Hero ロゴの例](./media/publish-marketplace-app/publishvm14.png)

## <a name="support-form"></a>[サポート] フォーム

**[サポート]** フォームには、会社のサポート連絡先情報を入力します。 たとえば、エンジニアリングやカスタマー サポートの連絡先情報です。

## <a name="publish-an-offer"></a>プランの発行

すべてのセクションを完了したら、**[発行]** を選択して、このプランを顧客が使用できるようにするためのプロセスを開始します。

## <a name="next-steps"></a>次のステップ

* マネージ アプリケーションの概要については、[マネージ アプリケーションの概要](overview.md)に関するページをご覧ください。
* サービス カタログ マネージ アプリケーションの発行については、「[サービス カタログ マネージ アプリケーションの作成と発行](publish-service-catalog-app.md)」を参照してください。
