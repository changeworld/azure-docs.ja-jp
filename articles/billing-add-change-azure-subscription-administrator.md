<properties
	pageTitle="Azure 管理者ロールを追加または変更する方法 | Microsoft Azure"
	description="Azure 共同管理者、サービス管理者、アカウント管理者を追加または変更する方法について説明します"
	services=""
	documentationCenter=""
	authors="genlin"
	manager="msmbaldwin"
	editor=""
	tags="billing"/>

<tags
	ms.service="billing"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/06/2016"
	ms.author="genli"/>

# Azure 管理者ロールを追加または変更する方法

Microsoft Azure には、3 種類の管理者ロールがあります。

| 管理ロール | 制限 | 説明
| ------------- | ------------- |---------------|
|アカウント管理者 (AA) | 1 Azure アカウントに 1 人 |Azure サブスクリプションをサインアップまたは購入し、[アカウント センター](https://account.windowsazure.com/Home/Index)にアクセスし、多様な管理タスクを実行する権限を持っている個人。管理タスクには、サブスクリプションの作成、サブスクリプションの取り消し、サブスクリプションの料金の変更、サービス管理者の変更などがあります。
| サービス管理者 (SA) | 1 Azure サブスクリプションに 1 人 |このロールには、[Azure ポータル](https://portal.azure.com)でのサービスの管理が許可されます。既定で、新しいサブスクリプションのアカウント管理者はサービス管理者でもあります。|
|[Azure クラシック ポータル](https://manage.windowsazure.com)の共同管理者 (CA)|サブスクリプションあたり 200 人| サービス管理者と同じアクセス権を持っていますが、サブスクリプションと Azure ディレクトリとの関連付けを変更することはできないロール。 |

> [AZURE.NOTE] Azure Active Directory のロールベースのアクセス制御 (RBAC) では、ユーザーを複数のロールに追加できます。詳細については、「[Azure Active Directory のロールベースの Access Control](./active-directory/role-based-access-control-configure.md)」を参照してください。

## サブスクリプションの管理者を追加する方法

**Azure ポータル**

1. [Azure ポータル](https://portal.azure.com)にサインインします。

2. ハブ メニューで **[サブスクリプション]** を選択し、*管理者にアクセス権を与えるサブスクリプション*を選択します。

	![newselectsub](./media/billing-add-change-azure-subscription-administrator/newselectsub.png)

3. サブスクリプション ブレードで、**[設定]**、**[ユーザー]** の順に選択します。

	![newsettings](./media/billing-add-change-azure-subscription-administrator/newsettings.png)
4. [ユーザー] ブレードで、**[追加]**、**[ロールの選択]**、**[所有者]** の順に選択します。

	![newselectrole](./media/billing-add-change-azure-subscription-administrator/newselectrole.png)

	**注**
	- 所有者ロールには、共同管理者と同じアクセス権が付与されます。このロールには、[Azure アカウント センター](https://account.windowsazure.com/subscriptions)へのアクセス権がありません。
	- [Azure ポータル](https://portal.azure.com)を通じて追加した所有者は、[Azure クラシック ポータル](https://manage.windowsazure.com)でサービスを管理できません。

5. 所有者として追加するユーザーの電子メール アドレスを入力し、ユーザーをクリックしてから、**[選択]** をクリックします。

	![newadduser](./media/billing-add-change-azure-subscription-administrator/newadduser.png)

**Azure クラシック ポータル**

1. [Azure クラシック ポータル](https://manage.windowsazure.com/)にサインインします。

2. ナビゲーション ウィンドウで、**[設定]**、**[管理者]**、**[追加]** の順に選択します。</br>

	![addcodmin](./media/billing-add-change-azure-subscription-administrator/addcoadmin.png)

3. 共同管理者として追加する個人の電子メール アドレスを入力し、共同管理者にアクセス権を与えるサブスクリプションを選択します。</br>

	![addcoadmin2](./media/billing-add-change-azure-subscription-administrator/addcoadmin2.png)</br>

次の電子メール アドレスを共同管理者として追加できます。

* **Microsoft アカウント** (旧 Windows Live ID) </br> Microsoft アカウントを使用して、Outlook (Hotmail)、Skype (MSN)、OneDrive、Windows Phone、Xbox LIVE などのすべてのコンシューマー向け Microsoft 製品とクラウド サービスにサインインすることができます。
* **組織アカウント**</br> 組織アカウントとは、Azure Active Directory で作成したアカウントです。組織アカウントのアドレスは、user@&lt;your domain&gt;.onmicrosoft.com のようになります。

### 規則や制限

 * 各サブスクリプションは Azure AD ディレクトリ (既定のディレクトリとも呼ばれます) と関連付けられます。サブスクリプションが関連付けられている既定のディレクトリを探すには、[Azure クラシック ポータル](https://manage.windowsazure.com/)にアクセスし、**[設定]**、**[サブスクリプション]** の順に選択します。サブスクリプション ID をチェックして既定のディレクトリを見つけてください。

 * Microsoft アカウントを使用してログインした場合、他の Microsoft アカウントまたは既定のディレクトリ内のユーザーを共同管理者として追加することしかできません。

 * 組織アカウントを使用してログインした場合、組織内の他の組織アカウントを共同管理者として追加することができます。たとえば、abby@contoso.com はサービス管理者または共同管理者として bob@contoso.com を追加できますが、john@noncontoso.com が既定のディレクトリのユーザーでない限り john@notcontoso.com は追加できません。組織アカウントでログインしているユーザーは、Microsoft アカウント ユーザーをサービス管理者または共同管理者として引き続き追加できます。

 * 組織アカウントで Azure にログインできるようになると、サービス管理者と共同管理者アカウントの要件は次のように変わります。

	ログイン方法| Microsoft アカウントまたは既定のディレクトリ内のユーザーを CA または SA として追加できる |同じ組織内の組織アカウントを CA または SA として追加できる |異なる組織内の組織アカウントを CA または SA として追加できる
	------------- | ------------- |---------------|---------------
	Microsoft アカウント |はい|なし|いいえ
	組織アカウント|はい|はい|いいえ

## サブスクリプションのサービス管理者を変更する方法

サブスクリプションのサービス管理者を変更できるのは、アカウント管理者のみです。

1. アカウント管理者として [Azure アカウント センター](https://account.windowsazure.com/subscriptions)にログオンします。

2. 変更するサブスクリプションを選択します。

3. 右側にある **[サブスクリプション詳細の編集]** をクリックします。</br>

	![editsub](./media/billing-add-change-azure-subscription-administrator/editsub.png)

4. **[サービス管理者]** ボックスに、新しいサービス管理者の電子メール アドレスを入力します。</br>

	![changeSA](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

## アカウント管理者の変更方法

Azure アカウントの所有権を別のアカウントに譲渡するには、「[Azure サブスクリプションの所有権の譲渡](billing-subscription-transfer.md)」を参照してください。

## 次のステップ

* Microsoft Azure でリソース アクセスを制御する方法の詳細については、「[Azure でのリソース アクセスについて](./active-directory/active-directory-understanding-resource-access.md)」を参照してください。

* Azure Active Directory と Azure サブスクリプションの関係の詳細については、「[Azure サブスクリプションを Azure Active Directory に関連付ける方法](./active-directory/active-directory-how-subscriptions-associated-directory.md)」を参照してください。

* Azure Active Directory と Azure サブスクリプションの関係の詳細については、「[Azure Active Directory の管理者ロールの割り当て](./active-directory/active-directory-assign-admin-roles.md)」を参照してください。

<!---HONumber=AcomDC_0803_2016-->