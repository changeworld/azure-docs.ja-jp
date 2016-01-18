<properties
	pageTitle="Azure 管理者ロールを追加または変更する方法 | Microsoft Azure"
	description="Azure 共同管理者、サービス管理者、アカウント管理者を追加または変更する方法について説明します"
	services="billing"
	documentationCenter=""
	authors="genlin"
	manager="msmbaldwin"
	editor="meerak"
	/>

<tags
	ms.service="billing"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/05/2015"
	ms.author="genli"/>

# Azure 管理者ロールを追加または変更する方法

## 説明済みの管理者ロール

Microsoft Azure には、3 種類の管理者ロールがあります。

| 管理ロール | 制限 | 説明
| ------------- | ------------- |---------------|
|アカウント管理者 (AA) | 1 Azure アカウントに 1 人 |Azure サブスクリプションをサインアップまたは購入し、[アカウント センター](https://account.windowsazure.com/Home/Index)にアクセスし、多様な管理タスクを実行する権限を持っている個人。管理タスクには、サブスクリプションの作成、サブスクリプションの取り消し、サブスクリプションの料金の変更、サービス管理者の変更などがあります。
| サービス管理者 (SA) | 1 Azure サブスクリプションに 1 人 |この管理者には、[Azure ポータル](https://manage.windowsazure.com/)でのサービスの管理が許可されます。既定で、新しいサブスクリプションのアカウント管理者はサービス管理者でもあります。|
|共同管理者 (CA)|サブスクリプションあたり 200 人|サービス管理者と同じアクセス権を持っていますが、サブスクリプションと Azure ディレクトリとの関連付けを変更することはできない個人。|

**注** Azure Active Directory のロール ベースの Access Control (RBAC) では、ユーザーを複数のロールに追加できます。詳細については、「[Azure Active Directory のロールベースの Access Control](./active-directory/role-based-access-control-configure.md)」を参照してください。
## サブスクリプションの共同管理者を追加する方法
1. [Azure クラシック ポータル](https://manage.windowsazure.com/)にサインインします。
2. ナビゲーション ウィンドウで、**[設定]**、**[管理者]**、**[追加]** の順に選択します。</br>![addcodmin](./media/billing-add-change-azure-subscription-administrator/addcoadmin.png)
3. 共同管理者として追加する個人の電子メール アドレスを入力し、共同管理者にアクセス権を与えるサブスクリプションを選択します。</br>![addcoadmin2](./media/billing-add-change-azure-subscription-administrator/addcoadmin2.png)</br>

次の電子メール アドレスを共同管理者として追加できます。

* **Microsoft アカウント** (旧 Windows Live ID) </br> Microsoft アカウントを使用して、Outlook (Hotmail)、Skype (MSN)、OneDrive、Windows Phone、Xbox LIVE などのすべてのコンシューマー向け Microsoft 製品とクラウド サービスにサインインすることができます。
* **組織アカウント**</br> 組織アカウントとは、Azure Active Directory で作成したアカウントです。組織アカウントのアドレスは、user@<your domain>.onmicrosoft.com のような形式です。

**注**

 * 各サブスクリプションは Azure Active Directory (または既定のディレクトリ) と関連付けられます。サブスクリプションが関連付けられている既定のディレクトリを探すには、[Azure クラシック ポータル](https://manage.windowsazure.com/)にアクセスし、**[設定]**、**[サブスクリプション]** の順に選択します。サブスクリプション ID をチェックして既定のディレクトリを見つけてください。

 * Microsoft アカウントを使用してログインした場合、他の Microsoft アカウントまたは既定のディレクトリ内のユーザーを共同管理者として追加することしかできません。
 * 組織アカウントを使用してログインした場合、組織内の他の組織アカウントを共同管理者として追加することができます。たとえば、abby@contoso.com はサービス管理者または共同管理者として bob@contoso.com を追加できますが、john@noncontoso.com が既定のディレクトリのユーザーでない限り john@notcontoso.com は追加できません。組織アカウントでログインしているユーザーは、Microsoft アカウント ユーザーをサービス管理者または共同管理者として引き続き追加できます。
 * 組織アカウントで Azure にログインできるようになると、サービス管理者と共同管理者アカウントの要件は次のように変わります。

| ログイン方法| Microsoft アカウントまたは既定のディレクトリ内のユーザーを CA または SA として追加できる |同じ組織内の組織アカウントを CA または SA として追加できる |異なる組織内の組織アカウントを CA または SA として追加できる
| ------------- | ------------- |---------------|---------------|
|Microsoft アカウント |はい|いいえ|いいえ|
|組織アカウント|はい|はい|いいえ|

## サブスクリプションのサービス管理者を変更する方法
サブスクリプションのサービス管理者を変更できるのは、アカウント管理者のみです。

1. アカウント管理者を使用して [Azure 管理ポータル](https://account.windowsazure.com/subscriptions)にログオンします。
2. 変更するサブスクリプションを選択します。
3. 右側にある **[サブスクリプション詳細の編集]** をクリックします。</br>![editsub](./media/billing-add-change-azure-subscription-administrator/editsub.png)

4. **[サービス管理者]** ボックスに、新しいサービス管理者の電子メール アドレスを入力します。![changeSA](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

## アカウント管理者の変更方法

Azure アカウントの所有権を別のアカウントに譲渡するには、「[Azure サブスクリプションの譲渡](../billing-subscription-transfer.md)」を参照してください。

<!---HONumber=AcomDC_0107_2016-->