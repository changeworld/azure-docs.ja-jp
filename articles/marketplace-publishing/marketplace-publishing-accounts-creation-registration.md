<properties
   pageTitle="発行者アカウントの作成と登録 | Microsoft Azure"
   description="承認のうえで、Azure Marketplace でさまざまなプランの種類を販売できるように Microsoft 開発者アカウントを作成する手順。"
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
   ms.date="01/07/2016"
   ms.author="hascipio"/>

# Microsoft 開発者アカウントの作成
この記事では、Azure Marketplace の承認済みの Microsoft 開発者になるために必要なアカウントの作成と登録のプロセスについて説明します。

## 1\.Microsoft アカウントの作成
> [AZURE.WARNING] 発行プロセスを開始するには、まず Microsoft アカウントを作成する必要があります。このアカウントは、**Microsoft デベロッパー センター**と **Azure 発行ポータル**の両方に対する登録とサインインに使用します。複数の Azure Marketplace サービスに対して 1 つの Microsoft アカウントを使用してください。各サービスおよびプランに対し、個別のアカウントを作成する必要はありません。

ユーザー名が含まれたアドレスは、貴社のドメインか IT チームで管理する必要があります (publishing@example.com) など)。支払いや税金に関する情報、またレポートは、このアカウントを通じて配信されます。

  >[AZURE.WARNING] Microsoft アカウントの登録では、"Azure" や "Microsoft" などの単語は使用できません。アカウントの作成および登録プロセスを完了するには、これらの単語を使用しないでください。

### このサンプルについての指示

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
4. 登録時は、有効な電話番号を使用します。ID 検証が必要な場合は、システムによりテキスト メッセージまたは自動通話として検証コードが送信されます。
5. DL に送信された電子メール アドレスを確認します。
6. これで、Microsoft デベロッパー センターで新しい Microsoft アカウントを使用する準備ができました。

> [AZURE.TIP] DL を使用すると、複数のユーザーが電子メール通知を受信できます。この通知は、支払い情報に関するレポートにとって重要です。また、Microsoft アカウントの所有権を譲渡できるようにし、1 人のユーザーに関連付けられないようにすることもできます。

## 2\.Microsoft デベロッパー センター アカウントの作成
Microsoft デベロッパー センターは、会社情報を登録するために 1 回だけ使用します。登録者は、合法的な企業の代表者である必要があります。また、ID を検証する方法として、個人情報を提供する必要があります。登録時には、会社用に共有されている Microsoft アカウントを使用する必要があり、**Azure 発行ポータルでも同じアカウントを使用する必要があります。** 作成する前に、会社がまだ Microsoft デベロッパー センターのアカウントを所有していないことを確認してください。作成中は、会社の住所情報、銀行口座情報、および税金に関する情報が収集されます。通常、これらは財務またはビジネス用連絡先から取得できます。

> [AZURE.IMPORTANT] プランの作成とデプロイのさまざまなフェーズを進めるために、次のような開発者プロファイル コンポーネントを完成させる必要があります。


| 開発者プロファイル | ドラフトを開始するには | ステージング | 無料とソリューション テンプレートの発行 | 商用プランの発行 |
|----|----|----|----|----|
|会社登録 | 必須 | 必須 | 必須 | 必須 |
|税プロファイル ID | 省略可能 | 省略可能 | 省略可能 | 必須 |
|銀行口座 | 省略可能 | 省略可能 | 省略可能 | 必須 |


> [AZURE.NOTE] ライセンス持ち込み (BYOL) は、仮想マシンのみでサポートされており、**無料**サービスと見なされます。


### 会社アカウントの登録
1. 新しい Internet Explorer InPrivate または Chrome Incognito のブラウズ セッションを開き、個人用アカウントにサインインしていないことを確認します。

2. [http://dev.windows.com/registration?accountprogram=azure](http://dev.windows.com/registration?accountprogram=azure) にアクセスします。

3. 会社登録の Microsoft アカウントを使用してサインインします (例: marketplace@example.com))。

    ![図][img-signin]

4. [アカウントの保護にご協力ください] ウィザードを完了します。これにより、電話番号または電子メール アドレスから ID が検証されます。

    ![図][img-verify]

5. [登録] の [アカウント情報] セクションで、ドロップダウン メニューから **[アカウントの国/地域]** を選択します。

    > [AZURE.WARNING] **"販売元" の国:** Azure Marketplace でサービスを販売するには、登録済みエンティティが、承認されたいずれかの "販売元" の国に所在している必要があります。この制限は、支払いおよび課税上の理由から生じます。「販売元」の国の範囲は今後拡大される予定です。詳細については、「[Microsoft Azure Marketplace 参加ポリシー](http://go.microsoft.com/fwlink/?LinkID=526833)」を参照してください。

6. [アカウントの種類] として、**[個人]** または **[会社]** を選択します。

    > [AZURE.IMPORTANT] アカウントの種類と、自社に最適な選択について理解を深めるには、「[アカウントの種類、場所、料金](https://msdn.microsoft.com/library/windows/apps/jj863494.aspx)」を参照してください。

7. **発行者表示名**を入力します。通常は、会社名を使用します。

    > [AZURE.TIP] 現時点では、Azure 発行ポータルでは発行者表示名は使用されませんが、登録プロセスを完了するために入力する必要があります。

8. アカウントの連絡先情報を入力します。

    > [AZURE.IMPORTANT] デベロッパー センターで会社を承認するための検証プロセスで使用されるため、連絡先情報は正確に入力してください。

9. 会社のアカウントの場合は、**[会社の承認者]** にも連絡先情報を入力する必要があります。これは、あなたが組織を代表してアカウントを作成する権限があることを証明できる社内の担当者です。完了したら、**[次へ]** をクリックし、**[支払い] セクション**に移動します。

10. アカウントの料金を支払うための支払い情報を入力します。登録コストが免除されるプロモーション コードがある場合は、ここで入力できます。ない場合は、クレジット カード情報 (またはサポートされている市場であれば PayPal 情報) を入力します。完了したら、**[次へ]** をクリックし、**確認画面**に移動します。

11. アカウント情報を見直し、すべて正しいことを確認します。次に、**Microsoft Azure Marketplace 発行者契約**の契約条件を読んで同意します。チェック ボックスをオンにすると、これらの条件を読んで同意したと見なされます。

12. **[完了]** をクリックし、登録内容を確認します。確認メッセージが開発者の電子メール アドレスに送信されます。

13. 無料プランの発行のみを計画している場合は、**[Azure Marketplace 発行ポータルに移動する]** をクリックします。また、このドキュメントのセクション 3「[発行ポータルでのアカウントの登録](#3-register-your-account-in-the-publishing-portal)」にスキップできます。

14. 商用プランの発行を計画している場合は、**[アカウント情報を更新する]** をクリックします。そこで、デベロッパー センター アカウントに税金と銀行口座の情報を入力する必要があります。

> [AZURE.IMPORTANT] 税金および銀行口座の情報の入力を完了することなく、ステージング環境でプランをテストしたり、プランを運用環境に移行したりすることはできません。

税金と銀行口座の情報を後で更新する場合は、セクション 3「[発行ポータルでのアカウントの登録](#3-register-your-account-in-the-publishing-portal)」に進み、後から発行ポータル内のリンクを使用して、この手順に戻ることができます。

### 税金と銀行取引情報の追加
 購入向けに商用プランを発行する場合は、支払い情報と税金情報を追加し、デベロッパー センターでの検証のために送信する必要があります。無料プラン (または、BYOL プラン) のみを発行する場合は、この情報を追加する必要はありません。後ほど追加することはできますが、税金情報の検証に時間がかかります。購入向けに商用プランを提供することが確定している場合は、これらの情報を可能な限り早く入力することをお勧めします。

**銀行口座情報**

1. Microsoft アカウントを使用して、[Microsoft デベロッパー センター](http://dev.windows.com/registration?accountprogram=azure)にサインインします。

2. 左側の **[支払いアカウント]** をクリックし、**[支払方法の選択]** で **[銀行口座]** または **[PayPal]** をクリックします。

    > [AZURE.IMPORTANT] Marketplace で顧客が購入した商用オファーがある場合、このアカウントで購入の支払いを受け取ることになります。

3. 支払い情報を入力し、問題がないことを確認して **[保存]** をクリックします。

    > [AZURE.IMPORTANT] 支払いアカウントを更新または変更する必要がある場合は、上記と同じ手順に従い、現在の情報の代わりに新しい情報を使用します。支払いアカウントを変更すると、最大で支払い期間 1 回分の遅延が支払いに発生する可能性があります。この遅延は、支払いアカウントが初めて設定された場合と同様に、アカウント変更の確認が必要になるために発生します。アカウントが確認された後も、お支払いは引き続き一括払いになります。期日が現在の支払い期間内にある支払いは、次の支払い期間に追加されます。

4. **[次へ]** をクリックします。

**税金情報**

1. 必要に応じて、Microsoft アカウントを使用して [Microsoft デベロッパー センター](http://dev.windows.com/registration?accountprogram=azure)にサインインします。

2. 左側のメニューの **[税プロファイル]** をクリックします。

3. **[税フォームの設定]** ページで、永続権を持つ国または地域を選択し、第一市民権を保持する国または地域を選択します。**[次へ]** をクリックします。

4. 税金の詳細を入力し、**[次へ]** をクリックします。

> [AZURE.WARNING] Microsoft デベロッパー センター アカウントで税金と銀行口座の情報の入力を完了することなく、商用プランを運用環境に移行することはできません。

## 3\.発行ポータルでのアカウントの登録
Azure 発行ポータルは、プランの発行と管理に使用されます。発行ポータルでは、プランの作成プロセスで役に立つ、有用な情報を検索できます。

> [AZURE.WARNING] ここでは、Microsoft デベロッパー センターの登録で使用したのと同じ会社用 Microsoft アカウントを使用する必要があります。マスター発行者アカウントの作成を支援するため、さらにユーザーを追加できます。

1.	新しい Chrome Incognito または Internet Explorer InPrivate ブラウズ セッションを開き、個人用アカウントにサインインしていないことを確認します。

2.	[http://publish.windowsazure.com](http://publish.windowsazure.com) に移動します。

3.	会社登録の Microsoft アカウント (marketplace@example.com) など) を使用してサインインします。必要に応じて共同管理者を追加できます。

  >[AZURE.TIP] 参加ポリシーについては、[Azure Web サイト](https://azure.microsoft.com/support/legal/marketplace/participation-policies/)を参照してください。

  > デベロッパー センターの登録で問題が発生した場合は、次の手順でサポート チケットを記録してください。
  1. [サポート](https://support.microsoft.com/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&supportregion=ja-JP&pesid=15635&ccsid=635847950577064286)に問い合わせる。
  2.**[デベロッパー センター]** を選択する。
  3.**[プロファイル]** を選択する。
  4.連絡方法を選択する。






## 次のステップ
これで、アカウントが作成され、登録されたので、Azure Marketplace に発行したいアーティファクトのタイプ (仮想マシン、開発者サービス、データ サービス、ソリューション テンプレート) をクリックします。それぞれのプランを発行する方法については、次の記事のいずれかを参照してください。

| 仮想マシン イメージ | 開発者サービス | データ サービス | ソリューション テンプレート |
|----|-----|-----|-----|-----|
|**ステップ 2: プランの作成** |[一般的な技術面以外の前提条件](marketplace-publishing-pre-requisites.md)| [一般的な技術面以外の前提条件](marketplace-publishing-pre-requisites.md)| [一般的な技術面以外の前提条件](marketplace-publishing-pre-requisites.md)| [一般的な技術面以外の前提条件](marketplace-publishing-pre-requisites.md)|
| [VM イメージの技術的な前提条件][link-single-vm-prereq] | 開発者サービスの技術的な前提条件 | [データ サービスの技術的な前提条件](marketplace-publishing-data-service-creation-prerequisites.md) | [ソリューション テンプレートの技術的な前提条件](marketplace-publishing-solution-template-creation-prerequisites.md) |
| [VM イメージの発行ガイド][link-single-vm] | 開発者サービスの発行ガイド | [データ サービスの発行ガイド](marketplace-publishing-data-service-creation.md) | [ソリューション テンプレートの発行ガイド](marketplace-publishing-solution-template-creation.md) |
| [Azure Marketplace のマーケティング コンテンツ ガイド][link-pushstaging] | [Azure Marketplace のマーケティング コンテンツ ガイド][link-pushstaging] | [Azure Marketplace のマーケティング コンテンツ ガイド][link-pushstaging] | [Azure Marketplace のマーケティング コンテンツ ガイド][link-pushstaging] |

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

[link-msdndoc]: https://msdn.microsoft.com/library/jj552460.aspx
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

<!---HONumber=AcomDC_0128_2016-->