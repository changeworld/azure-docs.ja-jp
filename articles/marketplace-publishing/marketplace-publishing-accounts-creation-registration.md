<properties
   pageTitle="発行者アカウントの作成と登録プロセス | Microsoft Azure"
   description="承認の上で、Azure Marketplace において、さまざまなプラン タイプを販売できる Microsoft 販売者アカウントの作成手順。"
   services="Azure Marketplace"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace-publishing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/09/2015"
   ms.author="hascipio"/>

# Microsoft の販売者アカウントの作成
この記事では、Azure Marketplace 向けの承認済みの Microsoft 販売者になるために必要な登録とアカウントの作成について説明します。

## 1\.Microsoft アカウント (MSA) を作成する
> [AZURE.WARNING]発行プロセスを完了するには、Microsoft アカウントを作成する必要があります。このアカウントは、発行ポータルと販売者ダッシュボードの両方の登録とログインで使用します。複数の Azure Marketplace サービスに対して 1 つの Microsoft アカウントを使用してください。各サービスおよびプラン に対し、個別のアカウントを作成する必要はありません。

ユーザー名が含まれたアドレスは、貴社のドメインか IT チームで管理する必要があります (publishing@yourcompany.com) など)。支払いや税金に関する情報、またレポートは、このアカウントを通じて配信されます。

  >[AZURE.WARNING]"Azure" といった単語と同様に、MSA アカウントの登録では、"Microsoft" は使用できません。アカウントの作成および登録プロセスを完了するには、これらの単語を使用しないでください。

1. 会社のドメイン内では、配布リスト (DL) またはセキュリティ グループ (SG) を作成します。
  - DL にオンボーディング チームを追加します。
  - DL は確認用メールを受信するためにライブである必要があります。
  - これは、内部システムで完了する必要があります。例。 marketplace@yourcompany.com
2. 新しい Chrome Incognito または IE InPrivate ブラウズ セッションを開き、既存のアカウントにログインしていないことを確認します。
3. DL 電子メールを使用して Microsoft アカウント (MSA) に登録します。
 - Microsoft アカウントは [https://signup.live.com/signup.aspx](https://signup.live.com/signup.aspx) で登録できます。
 - marketplace@yourcompany.com を電子メール アドレスとして使用します。
 - MSA ID は marketplace@yourcompany.com になります。

    ![図][img-msalive]

4. 登録時は、有効な電話番号を使用します。ID 検証が必要な場合は、システムによりテキスト メッセージまたは自動通話として検証コードが送信されます。
5. DL に送信される電子メール アドレスを確認します。

    ![図][img-email]

6. 販売者ダッシュ ボードで新しい MSA を使用する準備ができました。

> [AZURE.IMPORTANT]DL を使用することで、複数の人々へ、支払い情報の重要なレポートを送信し、MSA の所有権を確実に転送し、1 人のユーザーに関連付けられていないことを保証することができます。

## 2\.販売者ダッシュボード アカウントの作成
Microsoft Seller Dashboard は、会社情報の登録に 1 回使用されます。登録者は、合法的な企業の代表者である必要があります。また、ID を検証する方法として、個人情報を提供する必要があります。登録時に、ユーザーは、会社用に共有されている Microsoft アカウント (MSA) を使用する必要があり、Azure 発行ポータルで、同じアカウントを使用する必要があります。作成する前に、販売者ダッシュボードのアカウントを会社が既に所有していないことを確認してください。このプロセスでは、銀行口座の情報、税情報、および会社の住所情報が収集されます。通常、これらは財務またはビジネス用連絡先から取得できます。

> [AZURE.IMPORTANT]プランの作成およびデプロイメントのさまざまなフェーズを進めるために、次のような販売者プロファイル コンポーネントを入力する必要があります。


| 販売者のプロファイル | ドラフトを開始するには | ステージング | 無料 & ソリューション テンプレートの発行 | 商用プランの発行 |
|----|----|----|----|----|
|会社登録 | 必須 | 必須 | 必須 | 必須 |
|税プロファイル ID | 省略可能 | 省略可能 | 省略可能 | 必須 |
|銀行口座 | 省略可能 | 省略可能 | 省略可能 | 必須 |


> [AZURE.NOTE]BYOL は仮想マシンでのみサポートされ、**無料**で提供されるものとみなされます。


### 会社アカウントの登録
1. 新しい IE InPrivate または Chrome Incognito ブラウズ セッションを開き、個人アカウントにログインしていないことを確認します。

2. [http://sellerdashboard.microsoft.com](http://sellerdashboard.microsoft.com) に移動します。

    ![図][img-sd-url]

3. 会社登録の Microsoft アカウント (MSA) を使用してサインインします。 (例: AzureStore@yourcompany.com)

    ![図][img-signin]

4. [アカウントの保護にご協力ください] ウィザードを完了します。これにより、電話番号または電子メール アドレスから ID が検証されます。

    ![図][img-verify]

5. [**アカウントの詳細**] に移動します。この画面で個人情報を入力します。この情報は ID 検証の目的でのみ使用されます。これには、お客様の名前、電子メール アドレス、住所、ご自宅の電話番号などが含まれます。この情報をマイクロソフト社外の人間と共有することはありません。

    ![図][img-sd-top]

    ![図][img-sd-info]

6. アカウント タイプを会社として指定することにより、会社の代理として登録します。**[次へ]** をクリックします。

    ![図][img-sd-type]

7. [会社詳細] を入力します。この情報は正確である必要があります。本社、信用照会先は、Microsoft のチームによってチェックされます。

    ![図][img-sd-mktg1]

8. 以下の詳細において、会社名は、発行者ポータルで使用されます。そのため、正確である必要があります。

    ![図][img-sd-mktg2]

9. 本社の住所を使用してください。

    ![図][img-sd-addr]

10. 企業の代表者として連絡および認識できる信用照会先を使用してください。

    ![図][img-sd-legal]

11. **[承認のため送信]** をクリックし、完了します。

    ![図][img-sd-submit]


無料プランの発行のみを計画している場合は、セクション 3 の「**発行ポータルでのアカウントの登録**」までスキップできます。

商用プランの発行を計画している場合は、販売者ダッシュボード アカウントで税金や銀行取引情報などを入力する必要があります。セクション 2.2 をご覧ください。

> [AZURE.IMPORTANT]税金および銀行口座情報の入力を完了することなく、ステージング環境でプランをテストし、実稼働環境でプランを進めることはできません。

税金と銀行口座情報を後ほど更新する場合は、次の手順 3 に進むことができます。**発行ポータルでのアカウントの登録**を行った後で、発行ポータルのリンクを使用してこの手順に戻ってください。

### 税金と銀行取引情報の追加
 購入向けに商用プランを発行する場合は、支払い情報と税金情報を追加し、販売者ダッシュ ボードでの検証のために送信する必要があります。無料プラン (または、BYOL プラン) のみを発行する場合は、この情報を追加する必要はありません。後ほど追加することはできますが、税金情報の検証に時間がかかります。購入向けに商用プランを提供することが確定している場合は、これらの情報を可能な限り早く入力することをお勧めします。

1. Microsoft アカウントを使用して、[販売者ダッシュ ボード](http://sellerdashboard.microsoft.com)にサインインします。

2. **[アカウント]** タブをクリックし、[payout & tax] をクリックします。

3. **[ADD PAYOUT AND TAX INFO]** をクリックします。

4. **[Choose a payment method]** ページの新しい支払い方法の下にある **[銀行口座]** または **[PayPal]** をクリックします。

> [AZURE.IMPORTANT]Marketplace で顧客が購入した商用オファーがある場合、このアカウントで購入の支払いを受け取ることになります。

5. 銀行口座、または PayPal 口座の詳細を入力します。

6. **[次へ]** をクリックします。

7. **[税金情報]** ページで、永続権を持つ国または地域を選択し、次に、第一市民権を保持する国または地域を選択して、[次へ] をクリックします。

8. 税金の詳細を入力し、**[次へ]** をクリックします。

9. **[Submit]** をクリックします。検証のために、税金情報を送信する準備ができていない場合は、**[保存]** または **[保存して終了]** をクリックできます。税金情報の検証には時間がかかるため、検証のために可能な限り早く送信することをお勧めします。

> [AZURE.WARNING]販売者ダッシュ ボード アカウントで税金および銀行口座情報の入力を完了することなく、実稼働環境で商用プランを進めることはできません。

## 3\.発行ポータルでアカウントを登録します。
Azure 発行ポータルは、プランの発行及び管理に使用されます。発行ポータルでは、プランの作成プロセスで役に立つ、有用な情報を検索することができます。

> [AZURE.WARNING]ここでは、販売者ダッシュ ボードの登録で使用されている会社の同じ Microsoft アカウントを使用する必要があります。マスター発行者アカウントの作成を支援するため、さらにユーザーを追加できます。

1.	新しい Chrome Incognito または IE InPrivate ブラウズ セッションを開き、個人アカウントにログインしていないことを確認します。
2.	[http://publish.windowsazure.com](http://publish.windowsazure.com) に移動します。
3.	 会社登録の Microsoft アカウント (MSA) (例、AZStore@yourcompany.com) を使用してサインインし、必要に応じて、共同管理者を追加することができます。
4.	発行者契約書の条項を読み、同意すれば (発行ポータルへの初回ログイン時)、これで完了となります。

  >[AZURE.TIP]参加ポリシーについては、[こちら](http://azure.microsoft.com/support/legal/marketplace/participation-policies/)を参照してください。

  > 販売者の登録操作で問題が発生した場合は、次の手順でサポート チケットをログしてください。 1. [サポート](http://go.microsoft.com/fwlink?LinkId=272975)に問い合わせる。 2. **[Seller Dashboard registration and your account]** を選択する。 3. **[Registering for a developer account]** を選択する。 4.**[連絡方法]** を選択する。

### 「販売元」の国

> [AZURE.WARNING]Microsoft Azure Marketplace でサービスを販売するには、登録済みエンティティが承認されたいずれかの「販売元」の国からのものである必要があります。この制限は、支払いおよび課税上の理由から生じます。近い将来、このような国がさらに拡大されるように積極的に取り組んでいますので、しばらくお待ちください。完全なリストについては、[http://go.microsoft.com/fwlink/?LinkID=526833](http://go.microsoft.com/fwlink/?LinkID=526833) でリンクされるドキュメントのセクション 1b をご覧ください。




## 次のステップ
これで、アカウントが作成され、登録されました。Azure Marketplace に発行したいアーティファクトのタイプ (仮想マシン、開発者サービス、データ サービス、ソリューション テンプレート) をクリックします。それぞれのプランを発行する方法については、次の記事のいずれかを参照してください。

||仮想マシン イメージ |開発者サービス |データ サービス |ソリューション テンプレート | |----|-----|-----|-----|-----| |**手順 2: プランを作成する** |[技術以外の一般的な前提条件](marketplace-publishing-pre-requisites.md)| [技術以外の一般的な前提条件](marketplace-publishing-pre-requisites.md)| [技術以外の一般的な前提条件](marketplace-publishing-pre-requisites.md)| [技術以外の一般的な前提条件](marketplace-publishing-pre-requisites.md)| || [VM イメージの技術的な前提条件][link-single-vm-prereq] |開発者サービスの技術的な前提条件 | データ サービスの技術的な前提条件 | [ソリューション テンプレートの技術的な前提条件](marketplace-publishing-solution-template-creation-prerequisites.md) | || [VM イメージの発行ガイド][link-single-vm] |開発者向けサービスの発行ガイド | データ サービスの発行ガイド | [ソリューション テンプレートの発行ガイド](marketplace-publishing-solution-template-creation.md) | || [Azure Marketplace のマーケティング コンテンツ ガイド][link-pushstaging] | [Azure Marketplace のマーケティング コンテンツ ガイド][link-pushstaging] | [Azure Marketplace のマーケティング コンテンツ ガイド][link-pushstaging] | [Azure Marketplace のマーケティング コンテンツ ガイド][link-pushstaging] |

## 関連項目
- [Getting Started: How to publish an offer to the Azure Marketplace (概要: Azure Marketplace へのプランの発行方法)](marketplace-publishing-getting-started.md)

[img-msalive]: media/marketplace-publishing-accounts-creation-registration/creating-msa-account-msa-live.jpg
[img-email]: media/marketplace-publishing-accounts-creation-registration/creating-msa-account-msa-verifyemail.jpg
[img-sd-url]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-incognito.jpg
[img-signin]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-login.jpg
[img-verify]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-verify.jpg
[img-sd-top]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-acc-details.jpg
[img-sd-info]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal.jpg
[img-sd-type]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-acc-type.jpg
[img-sd-mktg1]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-det1.jpg
[img-sd-mktg2]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-det2.jpg
[img-sd-addr]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-add.jpg
[img-sd-legal]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-cmp.jpg
[img-sd-submit]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-approval.jpg

[link-msdndoc]: https://msdn.microsoft.com/JA-JP/library/jj552460.aspx
[link-sellerdashboard]: http://sellerdashboard.microsoft.com/
[link-pubportal]: https://publish.windowsazure.com
[link-single-vm]: marketplace-publishing-vm-image-creation.md
[link-single-vm-prereq]: marketplace-publishing-vm-image-creation-prerequisites.md
[link-multi-vm]: marketplace-publishing-solution-template-creation.md
[link-multi-vm-prereq]: marketplace-publishing-solution-template-creation-prerequisites.md
[link-datasvc]: marketplace-publishing-data-service-creation.md
[link-datasvc-prereq]: marketplace-publishing-data-service-creation-prerequisites.md
[link-devsvc]: marketplace-publishing-dev-service-creation.md
[link-devsvc-prereq]: marketplace-publishing-dev-service-creation-prerequisites.md
[link-pushstaging]: marketplace-publishing-push-to-staging.md

<!---HONumber=Oct15_HO3-->