<properties
   pageTitle="発行者アカウントの作成と登録 | Microsoft Azure"
   description="承認のうえで、Azure Marketplace でさまざまなプランの種類を販売できるように Microsoft 販売者アカウントを作成する手順。"
   services="Azure Marketplace"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/09/2015"
   ms.author="hascipio"/>

# Microsoft 販売者アカウントの作成
この記事では、Azure Marketplace 向けの承認済みの Microsoft 販売者になるために必要なアカウントの作成と登録プロセスについて説明します。

## 1\.Microsoft アカウントの作成
> [AZURE.WARNING]発行プロセスを完了するには、Microsoft アカウントを作成する必要があります。このアカウントは、発行ポータルと販売者ダッシュボードの両方の登録とサインインで使用します。複数の Azure Marketplace サービスに対して 1 つの Microsoft アカウントを使用してください。各サービスおよびプランに対し、個別のアカウントを作成する必要はありません。

ユーザー名が含まれたアドレスは、貴社のドメインか IT チームで管理する必要があります (publishing@example.com) など)。支払いや税金に関する情報、またレポートは、このアカウントを通じて配信されます。

  >[AZURE.WARNING]Microsoft アカウントの登録では、"Azure" や "Microsoft" などの単語は使用できません。アカウントの作成および登録プロセスを完了するには、これらの単語を使用しないでください。

1. 会社のドメイン内で、配布リスト (DL) またはセキュリティ グループ (SG) を作成します。
  - DL にオンボーディング チームを追加します。
  - DL は確認用メールを受信するためにライブである必要があります。
  - marketplace@example.com を DL 用の電子メール アドレスとして使用します。
  - これは、内部システムで完了する必要があります。
2. 新しい Chrome Incognito または Internet Explorer InPrivate ブラウズ セッションを開き、既存のアカウントにサインインしていないことを確認します。
3. DL の電子メールを使用して Microsoft アカウントを登録します。
 - Microsoft アカウントは [https://signup.live.com/signup.aspx](https://signup.live.com/signup.aspx) で登録できます。
 - marketplace@example.com を電子メール アドレスとして使用します。
 - これで Microsoft アカウント ID が marketplace@example.com になります。

    <!--[drawing][img-msalive]-->

4. 登録時は、有効な電話番号を使用します。ID 検証が必要な場合は、システムによりテキスト メッセージまたは自動通話として検証コードが送信されます。
5. DL に送信された電子メール アドレスを確認します。

    <!--[drawing][img-email]-->

6. これで、販売者ダッシュボードで新しい Microsoft アカウントを使用する準備ができました。

> [AZURE.IMPORTANT]DL を使用すると、複数のユーザーが電子メール通知を受信できます。この通知は、支払い情報に関するレポートにとって重要です。また、Microsoft アカウントの所有権を譲渡できるようにし、1 人のユーザーに関連付けられないようにすることもできます。

## 2\.販売者ダッシュボード アカウントの作成
Microsoft 販売者ダッシュボードは、会社情報の登録に 1 回使用されます。登録者は、合法的な企業の代表者である必要があります。また、ID を検証する方法として、個人情報を提供する必要があります。登録時に、ユーザーは、会社用に共有されている Microsoft アカウントを使用する必要があり、Azure 発行ポータルで、同じアカウントを使用する必要があります。作成する前に、販売者ダッシュボードのアカウントを会社がまだ所有していないことを確認してください。このプロセスでは、銀行口座の情報、税情報、および会社の住所情報が収集されます。通常、これらは財務またはビジネス用連絡先から取得できます。

> [AZURE.IMPORTANT]プランの作成やデプロイのさまざまなフェーズを進めるために、次のような販売者プロファイル コンポーネントを入力する必要があります。


| 販売者のプロファイル | ドラフトを開始するには | ステージング | 無料とソリューション テンプレートの発行 | 商用プランの発行 |
|----|----|----|----|----|
|会社登録 | 必須 | 必須 | 必須 | 必須 |
|税プロファイル ID | 省略可能 | 省略可能 | 省略可能 | 必須 |
|銀行口座 | 省略可能 | 省略可能 | 省略可能 | 必須 |


> [AZURE.NOTE]ライセンス持ち込み (BYOL) は、仮想マシンのみでサポートされており、**無料**サービスと見なされます。


### 会社アカウントの登録
1. 新しい Internet Explorer InPrivate または Chrome Incognito のブラウズ セッションを開き、個人用アカウントにサインインしていないことを確認します。

2. [http://sellerdashboard.microsoft.com](http://sellerdashboard.microsoft.com) に移動します。

    ![図][img-sd-url]

3. 会社登録の Microsoft アカウントを使用してサインインします (例: marketplace@example.com))。

    ![図][img-signin]

4. [アカウントの保護にご協力ください] ウィザードを完了します。これにより、電話番号または電子メール アドレスから ID が検証されます。

    ![図][img-verify]

5. [**アカウントの詳細**] に移動します。この画面で個人情報を入力します。この情報は ID 検証の目的でのみ使用されます。これには、お客様の名前、電子メール アドレス、住所、ご自宅の電話番号などが含まれます。この情報をマイクロソフト社外の人間と共有することはありません。

    ![図][img-sd-top]

    <!--[drawing][img-sd-info]-->

6. アカウントの種類を **[個人]** ではなく **[会社]** として指定することにより、会社の代理として登録します。**[次へ]** をクリックします。

    ![図][img-sd-type]

7. 会社の詳細情報を入力します。この情報は正確である必要があります。本社、信用照会先は、Microsoft のチームによってチェックされます。

    ![図][img-sd-mktg1]

8. 会社名は発行ポータルで使用されるため、正確である必要があります。

    <!--[drawing][img-sd-mktg2]-->

9. 本社の住所を使用してください。

    ![図][img-sd-addr]

10. 企業の代表者として連絡および認識できる信用照会先を使用してください。

    ![図][img-sd-legal]

11. **[承認のため送信]** をクリックし、完了します。

    ![図][img-sd-submit]


無料プランの発行のみを計画している場合は、セクション 3 の「発行ポータルでのアカウントの登録」に進むことができます。

商用プランの発行を計画している場合は、販売者ダッシュボード アカウントで税金や銀行取引情報などを入力する必要があります。

> [AZURE.IMPORTANT]税金および銀行口座の情報の入力を完了することなく、ステージング環境でプランをテストしたり、プランを運用環境に移行したりすることはできません。

税金と銀行口座の情報を後で更新する場合は、セクション 3「発行ポータルでのアカウントの登録」に進み、後から発行ポータルのリンクを使用すると、この手順に戻ることができます。

### 税金と銀行取引情報の追加
 購入向けに商用プランを発行する場合は、支払い情報と税金情報を追加し、販売者ダッシュボードでの検証のために送信する必要があります。無料プラン (または、BYOL プラン) のみを発行する場合は、この情報を追加する必要はありません。後ほど追加することはできますが、税金情報の検証に時間がかかります。購入向けに商用プランを提供することが確定している場合は、これらの情報を可能な限り早く入力することをお勧めします。

1. Microsoft アカウントを使用して、[販売者ダッシュボード](http://sellerdashboard.microsoft.com)にサインインします。

2. **[アカウント]** タブをクリックし、**[支払いおよび税金]** をクリックします。

3. **[ADD PAYOUT AND TAX INFO]** をクリックします。

4. **[お支払方法の選択]** ページの **[新しいお支払い方法]** で、**[銀行口座]** または **[PayPal]** をクリックします。

> [AZURE.IMPORTANT]Marketplace で顧客が購入した商用オファーがある場合、このアカウントで購入の支払いを受け取ることになります。

5. 銀行口座、または PayPal 口座の詳細を入力します。

6. **[次へ]** をクリックします。

7. **[税金情報]** ページで、永続権を持つ国または地域を選択し、第一市民権を保持する国または地域を選択します。**[次へ]** をクリックします。

8. 税金の詳細を入力し、**[次へ]** をクリックします。

9. **[Submit]** をクリックします。検証のために、税金情報を送信する準備ができていない場合は、**[保存]** または **[保存して終了]** をクリックできます。税金情報の検証には時間がかかるため、検証のために可能な限り早く送信することをお勧めします。

> [AZURE.WARNING]販売者ダッシュボード アカウントで税金および銀行口座情報の入力を完了することなく、実稼働環境で商用プランを進めることはできません。

## 3\.発行ポータルでのアカウントの登録
Azure 発行ポータルは、プランの発行と管理に使用されます。発行ポータルでは、プランの作成プロセスで役に立つ、有用な情報を検索できます。

> [AZURE.WARNING]ここでは、販売者ダッシュボードの登録で使用されたのと同じ会社の Microsoft アカウントを使用する必要があります。マスター発行者アカウントの作成を支援するため、さらにユーザーを追加できます。

1.	新しい Chrome Incognito または Internet Explorer InPrivate ブラウズ セッションを開き、個人用アカウントにサインインしていないことを確認します。
2.	[http://publish.windowsazure.com](http://publish.windowsazure.com) に移動します。
3.	 会社登録の Microsoft アカウント (marketplace@example.com) など) を使用してサインインし、必要に応じて共同管理者を追加できます。
4.	発行者契約の条項を読み、同意すれば (発行ポータルへの初回サインイン時)、これで完了となります。

  >[AZURE.TIP]参加ポリシーについては、[Azure Web サイト](http://azure.microsoft.com/support/legal/marketplace/participation-policies/)で説明されています。

  > 販売者の登録作業で問題が発生した場合は、次の手順でサポート チケットを記録してください。1. [サポート](http://go.microsoft.com/fwlink?LinkId=272975)に問い合わせる。2.**[販売者ダッシュボードの登録とアカウント]** を選択する。3.**[開発者アカウントへの登録]** を選択する。4.連絡方法を選択する。

### 「販売元」の国

> [AZURE.WARNING]Azure Marketplace でサービスを販売するには、登録済みエンティティが、承認されたいずれかの "販売元" の国からのものである必要があります。この制限は、支払いおよび課税上の理由から生じます。"販売元" の国の範囲は今後拡大される予定です。完全な一覧については、[Marketplace 参加ポリシー](http://go.microsoft.com/fwlink/?LinkID=526833)のセクション 1b を参照してください。




## 次のステップ
これで、アカウントが作成され、登録されたので、Azure Marketplace に発行したいアーティファクトのタイプ (仮想マシン、開発者サービス、データ サービス、ソリューション テンプレート) をクリックします。それぞれのプランを発行する方法については、次の記事のいずれかを参照してください。

||仮想マシン イメージ |開発者サービス |データ サービス |ソリューション テンプレート | |----|-----|-----|-----|-----| |**手順 2. プランを作成する** |[技術以外の一般的な前提条件](marketplace-publishing-pre-requisites.md)| [技術以外の一般的な前提条件](marketplace-publishing-pre-requisites.md)| [技術以外の一般的な前提条件](marketplace-publishing-pre-requisites.md)| [技術以外の一般的な前提条件](marketplace-publishing-pre-requisites.md)| || [VM イメージの技術的な前提条件][link-single-vm-prereq] |開発者サービスの技術的な前提条件 | データ サービスの技術的な前提条件 | [ソリューション テンプレートの技術的な前提条件](marketplace-publishing-solution-template-creation-prerequisites.md) | || [VM イメージの発行ガイド][link-single-vm] |開発者向けサービスの発行ガイド | データ サービスの発行ガイド | [ソリューション テンプレートの発行ガイド](marketplace-publishing-solution-template-creation.md) | || [Azure Marketplace のマーケティング コンテンツ ガイド][link-pushstaging] | [Azure Marketplace のマーケティング コンテンツ ガイド][link-pushstaging] | [Azure Marketplace のマーケティング コンテンツ ガイド][link-pushstaging] | [Azure Marketplace のマーケティング コンテンツ ガイド][link-pushstaging] |

## 関連項目
- [概要: Azure Marketplace へのプランの発行方法](marketplace-publishing-getting-started.md)

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

[link-msdndoc]: https://msdn.microsoft.com/ja-JP/library/jj552460.aspx
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

<!---HONumber=Nov15_HO4-->