<properties
	pageTitle="Azure 共同管理者、サービス管理者、アカウント管理者を追加または変更する方法 | Microsoft Azure"
	description="Azure 共同管理者、サービス管理者、アカウント管理者を追加または変更する方法について説明します"
	services="billing"
	documentationCenter=""
	authors="genlin"
	manager="jarrettr"
	editor="meerak"
	tags="billing"
	/>

<tags
	ms.service="billing"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/20/2015"
	ms.author="genli"/>

# Azure 共同管理者、サービス管理者、アカウント管理者を追加または変更する方法
## 管理者ロール

Microsoft Azure には、3 種類の管理者ロールがあります。

| 管理ロール | 制限 | 説明
| ------------- | ------------- |---------------|
|アカウント管理者 | 1 Azure アカウントに 1 人 |Azure サブスクリプションをサインアップまたは購入し、アカウント センターにアクセスし、多様な管理タスクを実行する権限を持っている個人。管理タスクには、サブスクリプションの作成、サブスクリプションの取り消し、サブスクリプションの料金の変更、サービス管理者の変更などがあります。
| サービス管理者 | 1 Azure サブスクリプションに 1 人 |サブスクリプションに割り当てられたアカウントで、サブスクリプションの Microsoft Azure 管理ポータルにアクセスする権限を持っている個人。既定で、新しいサブスクリプションのアカウント管理者はサービス管理者でもあります。|
|共同管理者|1 サブスクリプションに (サービス管理者に加えて) 200 人|サービス管理者と同じアクセス権を持っていますが、サブスクリプションと Azure ディレクトリとの関連付けを変更することはできない個人。|

## サブスクリプションの共同管理者を追加する
1. [Microsoft Azure 管理ポータル](https://manage.windowsazure.com/)にサインインします。
2. ナビゲーション ウィンドウで、**[設定]**、**[管理者]**、**[追加]** の順に選択します。</br>![addcodmin](./media/billing-add-change-azure-subscription-administrator/addcoadmin.png)
3. 共同管理者として追加する個人の電子メール アドレスを入力し、共同管理者にアクセス権を与えるサブスクリプションを選択します。</br>![addcoadmin2](./media/billing-add-change-azure-subscription-administrator/addcoadmin2.png)</br>

次の電子メール アドレスを共同管理者として追加できます。

* **Microsoft アカウント** (旧 Windows Live ID) </br> Microsoft アカウントを使用して、Outlook (Hotmail)、Skype (MSN)、OneDrive、Windows Phone、Xbox LIVE などのすべてのコンシューマー向け Microsoft 製品とクラウド サービスにサインインすることができます。
* **組織アカウント**</br> 組織アカウントとは、Azure Active Directory で作成したアカウントです。組織アカウントのアドレスは、user@<your domain>.onmicrosoft.com のような形式です。

**注:** * Microsoft アカウントを使用してログインした場合、他の Microsoft アカウントを共同管理者として追加することしかできません。これは、セキュリティを考慮して、組織アカウント以外のアカウントが、特定のアカウント (janedoe@contoso.com など) が有効かどうかを確認できないようにするためです。 * 組織アカウントを使用してログインした場合、組織内の他の組織アカウントを共同管理者として追加することができます。たとえば、abby@contoso.com はサービス管理者または共同管理者として bob@contoso.com を追加できますが、john@notcontoso.com は追加できません。組織アカウントでログインしたユーザーは、サービス管理者または共同管理者として Microsoft アカウント ユーザーを追加できます。 * 組織アカウントで Azure にログインできるようになると、サービス管理者と共同管理者アカウントの要件は次のように変わります。

| ログイン方法| Microsoft アカウントを共同管理者またはサービス管理者として追加できる |同じ組織内の組織アカウントを共同管理者またはサービス管理者として追加できる |異なる組織の組織アカウントを共同管理者またはサービス管理者として追加できる
| ------------- | ------------- |---------------|---------------|
|Microsoft アカウント |はい|いいえ|いいえ|
|組織アカウント|はい|はい|いいえ|

## サブスクリプションのサービス管理者を変更する
サブスクリプションのサービス管理者を変更できるのは、アカウント管理者のみです。

1. アカウント管理者を使用して [Azure 管理ポータル](https://account.windowsazure.com/subscriptions)にログオンします。
2. 変更するサブスクリプションを選択します。
3. 右側にある **[サブスクリプション詳細の編集]** をクリックします。</br>![editsub](./media/billing-add-change-azure-subscription-administrator/editsub.png)

4. **[サービス管理者]** ボックスに、新しいサービス管理者の電子メール アドレスを入力します。![changeSA](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

## アカウント管理者を変更する (Azure アカウントの所有権を別のアカウントに譲渡する)

Azure アカウントの所有権を別のアカウントに譲渡するには、「[Azure サブスクリプションの譲渡](../billing-subscription-transfer.md)」を参照してください。

<!---HONumber=Nov15_HO1-->