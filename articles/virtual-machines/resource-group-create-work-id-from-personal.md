<properties
   pageTitle="AAD の職場または学校の ID を作成する |Microsoft Azure"
   description="Resource Manager デプロイ モデルとクラシック デプロイ モデルで使用する Azure Active Directory の職場または学校の ID を作成する方法について説明します。"
   services="virtual-machines"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure"
   ms.date="09/01/2015"
   ms.author="rasquill"/>

# Azure Active Directory を使用して職場または学校の ID を作成する

MSDN Azure クレジットを活用するために、個人の Azure アカウントを作成した場合や、個人の MSDN サブスクリプションがあり、Azure アカウントを作成した場合、*Microsoft アカウント* ID を使用して作成しています。Azure の優れた機能の中でも[リソース グループ テンプレート](../resource-group-overview.md)はその一例ですが、作業にあたって職場または学校のアカウント (Azure Active Directory で管理される ID) が必要です。


> [AZURE.NOTE]Azure には、リソースの作成と操作に関して 2 種類のデプロイ モデルがあります。[リソース マネージャー デプロイ モデルとクラシック デプロイ モデル](../resource-manager-deployment-model.md)です。この記事では、両方のモデルについて取り上げていますが、最新のデプロイではリソース マネージャー モデルの使用をお勧めします。


個人の Azure アカウントを持つことで得られるメリットの 1 つは、既定の Azure Active Directory ドメインが付属することです。これを使用して職場や学校のアカウントを新規に作成し、アカウントを要求する Azure 機能で利用できます。

> [AZURE.NOTE]管理者からユーザー名とパスワードが提供されている場合は、既に職場または学校の ID を持っている可能性があります (*組織 ID* とも呼ばれます)。その場合、いつでも Azure アカウントの使用を開始して、アカウントが必要な Azure リソースにアクセスできます。これらのリソースを使用できない場合は、この記事をもう一度ご確認ください。詳細については、「[サインインで使用できるアカウント](https://msdn.microsoft.com/library/azure/dn629581.aspx#BKMK_SignInAccounts)」、「[Azure サブスクリプションと Azure AD との関係](https://msdn.microsoft.com/library/azure/dn629581.aspx#BKMK_SubRelationToDir)」をご覧ください。

手順は簡単です。Azure ポータルで署名済み ID を見つけて、既定の Azure Active Directory ドメインを検出し、Azure 共同管理者として新しいユーザーを追加する必要があります。

## Azure ポータルで既定のディレクトリを見つける

まず、個人の Microsoft アカウント ID で [Azure ポータル](https://manage.windowsazure.com) にログインします。ログインしたら、左側の青色のパネルを下にスクロールし、**[ACTIVE DIRECTORY]** をクリックします。

![Azure Active Directory](./media/resource-group-create-work-id-from-personal/azureactivedirectorywidget.png)

最初に、Azure で自分 ID の一部を確認しましょう。次に示すように、メイン ウィンドウで既定のディレクトリが 1 つあることがわかります。

![](./media/resource-group-create-work-id-from-personal/defaultaadlisting.png)

その他の情報を確認してみましょう。既定のディレクトリ列をクリックすると、既定のディレクトリのプロパティが表示されます。

![](./media/resource-group-create-work-id-from-personal/defaultdirectorypage.png)

既定のドメイン名を表示するには、**[DOMAINS]** をクリックします。

![](./media/resource-group-create-work-id-from-personal/domainclicktoseeyourdefaultdomain.png)

ここでは、Azure アカウントが作成されると、onmicrosoft.com のサブドメインとして使用する個人 ID のハッシュ値 (文字列から生成される数字) である個人の既定ドメインが Azure Active Directory によって作成されたことが確認できるはずです。このドメインを使用して、新しいユーザーを追加することになります。

## 既定のドメインで新しいユーザーを作成する

**[ユーザー]** をクリックし、自分の個人アカウントを探します。**[ソース]** 列を確認すると **Microsoft アカウント**であることがわかります。ユーザーを既定の onmicrosoft.com Azure Active Directory ドメインで作成します。

![](./media/resource-group-create-work-id-from-personal/defaultdirectoryuserslisting.png)

次の数ステップでは[手順](https://technet.microsoft.com/library/hh967632.aspx#BKMK_1)の説明に従いますが、特定の例を使用します。

ページ下部で **[+追加]** をクリックします。表示されるページで、新しいユーザー名を入力し、**[ユーザーの種類]** で **[組織内の新しいユーザー]** を選択します。この例では、新しいユーザー名は `ahmet` です。以前に特定した既定のドメインを ahmet のメール アドレスのドメインとして選択してください。矢印をクリックして次へ進みます。

![](./media/resource-group-create-work-id-from-personal/addingauserwithdirectorydropdown.png)

Ahmet の詳細を追加しますが、適切な **[ロール]** 値を選択してください。**[グローバル管理者]** を使用すると確実ですが、使用できる場合には、下位のロールを使用することをお勧めします。この例では、**[ユーザー]** ロールを使用します (これらのロールの詳細については、[こちら](https://msdn.microsoft.com/library/azure/dn468213.aspx#BKMK_1)をご覧ください)。 操作の各ログで Multi-Factor Authentication を使用する必要がない場合は、Multi-Factor Authentication を有効にしないでください。終了したら、矢印をクリックします。

![](./media/resource-group-create-work-id-from-personal/userprofileuseradmin.png)

**[作成]** ボタンをクリックして、Ahmet の一時パスワードを生成、表示します。

![](./media/resource-group-create-work-id-from-personal/gettemporarypasswordforuser.png)

ユーザー名の電子メール アドレスをコピーするか、または[ **パスワードを電子メールで送信**]を使用します。すぐにログオン時にその情報が必要になります。

![](./media/resource-group-create-work-id-from-personal/receivedtemporarypassworddialog.png)

Azure Active Directory から取得した新しいユーザー、**開発者の Ahmet** が表示されます。Azure Active Directory を使用して、職場または学校の新しい ID を作成しました。ただし、この ID には Azure のリソースを使用する権限がまだありません。

![](./media/resource-group-create-work-id-from-personal/defaultdirectoryusersaftercreate.png)

[**パスワードを電子メールで送信**]を使用する場合、次のような電子メールが送信されます。

![](./media/resource-group-create-work-id-from-personal/emailreceivedfromnewusercreation.png)

## サブスクリプションに Azure 共同管理者の権限を追加する

今度は、新しいユーザーが管理ポータルにサインインできるように、サブスクリプションの共同管理者として新しいユーザーを追加する必要があります。そのためには、左下のパネルで [**設定**] をクリックします。

![](./media/resource-group-create-work-id-from-personal/thesettingswidget.png)

メインの設定領域で、上部の **[管理者]** をクリックすると、個人の Microsoft アカウント ID のみが表示されるはずです。ページ下部で **[+追加]** をクリックし、共同管理者を指定します。ここでは、既定のドメインと、作成した新しいユーザーの電子メール アドレスを入力します。次のスクリーン ショットのように、既定のディレクトリのユーザーの横に緑色のチェック マークが表示されます。このユーザーに管理を許可するすべてのサブスクリプションを選択してください。

![](./media/resource-group-create-work-id-from-personal/addingnewuserascoadmin.png)

完了すると、新しい共同管理者 ID を含む、2 人のユーザーが表示されます。ポータルからログアウトします。

![](./media/resource-group-create-work-id-from-personal/newuseraddedascoadministrator.png)

## 新しいユーザーとしてログインしてパスワードを変更する

作成した新しいユーザーとしてログインします。

![](./media/resource-group-create-work-id-from-personal/signinginwithnewuser.png)

すぐに新しいパスワードを作成するように求められます。

![](./media/resource-group-create-work-id-from-personal/mustupdateyourpassword.png)

次のような正常に完了したことを示す内容が表示されます。

![](./media/resource-group-create-work-id-from-personal/successtourdialog.png)


## 次のステップ

新しい Azure Active Directory の ID で [Azure リソース グループ テンプレート](xplat-cli-azure-resource-manager.md)を使用できます。

     azure login
    info:    Executing command login
    warn:    Please note that currently you can login only via Microsoft organizational account or service principal. For instructions on how to set them up, please read http://aka.ms/Dhf67j.
    Username: ahmet@aztrainpassxxxxxoutlook.onmicrosoft.com
    Password: *********
    /info:    Added subscription Azure Pass
    info:    Setting subscription Azure Pass as default
    +
    info:    login command OK
    ralph@local:~$ azure config mode arm
    info:    New mode is arm
    ralph@local:~$ azure group list
    info:    Executing command group list
    + Listing resource groups
    info:    No matched resource groups were found
    info:    group list command OK
    ralph@local:~$ azure group create newgroup westus
    info:    Executing command group create
    + Getting resource group newgroup
    + Creating resource group newgroup
    info:    Created resource group newgroup
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/newgroup
    data:    Name:                newgroup
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags:
    data:
    info:    group create command OK

<!---HONumber=Oct15_HO3-->