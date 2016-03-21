<properties
  pageTitle="Azure IoT Suite と Azure Active Directory | Microsoft Azure"
  description="Azure IoT Suite が Azure Active Directory を使用してアクセス許可を管理する方法を説明します。"
  services=""
  suite="iot-suite"
  documentationCenter=""
  authors="aguilaaj"
  manager="timlt"
  editor=""/>

<tags
  ms.service="iot-suite"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="na"
  ms.date="11/17/2015"
  ms.author="araguila"/>
  
# azureiotsuite.com サイトでのアクセス許可

## サインイン時に行われること

ユーザーが [azureiotsuite.com][lnk-azureiotsuite] に初めてサインインすると、選択されている Azure Active Directory (AAD) テナントと Azure サブスクリプションに基づいて、ユーザーのアクセス許可レベルが決定されます。

1.  サイトは最初に、ログイン ユーザー名の隣に表示されるテナントの一覧を設定するため、Azure からユーザーが属している AAD テナントを取得します。この時点で、サイトは、一度に 1 つのテナントのユーザー トークンしか取得できません。その結果、ユーザーが右上隅のドロップダウンを使用して異なるテナントに切り替えると、サイトはユーザーをそのテナントに再ログインさせて、そのテナントのトークンを取得します。

2.  次に、サイトは、ユーザーが選択されているテナントと関連付けているサブスクリプションを Azure から検索します。ユーザーが新しい構成済みソリューションを作成するときに、使用できるサブスクリプションが表示されます。

3.  最後に、サイトはサブスクリプション内および構成済みソリューションというタグが付いているリソース グループ内のすべてのリソースを取得して、ホーム ページのタイトルを設定します。

以下のセクションでは、構成済みソリューションへのアクセスを制御するロールについて説明します。

## AAD のロール

AAD のロールは、構成済みソリューションをプロビジョニングし、構成済みソリューション内のユーザーを管理する権限を制御します。

AAD での管理者ロールの詳細については、「[Azure AD での管理者ロールの割り当て][lnk-aad-admin]」を参照してください。この記事では主として、構成済みソリューションによって使用される**グローバル管理者**ロールと**ドメイン ユーザー/メンバー** ロールに焦点を当てます。

**グローバル管理者:** AAD テナントごとに多数のグローバル管理者がいてもかまいません。AAD テナントを作成したユーザーは、既定でそのテナントのグローバル管理者になります。グローバル管理者は、構成済みソリューションをプロビジョニングでき、AAD テナントの内部にあるアプリケーションに対する **ADMINISTRATOR** ロールを割り当てられます。ただし、同じ AAD テナントの別のユーザーがアプリケーションを作成した場合は、グローバル管理者に付与される既定のロールは **IMPLICIT READ ONLY** です。グローバル管理者は、[Azure クラシック ポータル][lnk-classic-portal]を使用してアプリケーションに対するロールを割り当てることができます。

**ドメイン ユーザー/メンバー:** AAD テナントごとに多数のドメイン ユーザー/メンバーがいてもかまいません。ドメイン ユーザーは [azureiotsuite.com][lnk-azureiotsuite] サイトを使用して構成済みソリューションをプロビジョニングできます。プロビジョニングするアプリケーションに対して付与される既定のロールは **ADMINISTRATOR** です。[azure-iot-solution][lnk-github-repo] リポジトリの build.cmd スクリプトを使用してアプリケーションを作成できますが、ロールを割り当てるアクセス許可はないので、既定で付与されるロールは **IMPLICIT READONLY** です。AAD テナントの別のユーザーが作成したアプリケーションに対して既定で割り当てられるロールは **IMPLICIT READONLY** です。アプリケーションのロールを割り当てることはできません。そのため、自分でプロビジョニングしたアプリケーションであっても、ユーザーまたはユーザーのロールを追加することはできません。

**ゲスト ユーザー/ゲスト:** AAD テナントごとに多数のゲスト ユーザー/ゲストがいてもかまいません。ゲスト ユーザーの AAD テナントでの権限セットは制限されています。したがって、ゲスト ユーザーは AAD テナントで構成済みソリューションをプロビジョニングできません。

詳細については、次のリソースを参照してください。

- [Azure AD でのユーザーの作成または編集][lnk-create-edit-users]
- [AAD でのアプリ ロールの割り当て][lnk-assign-app-roles]

## Azure サブスクリプション管理者ロール

Azure 管理者ロールは、Azure サブスクリプションを AD テナントにマップする機能を制御します。

Azure 共同管理者、サービス管理者、アカウント管理者のロールの詳細については、「[Azure 共同管理者、サービス管理者、アカウント管理者を追加または変更する方法][lnk-admin-roles]」を参照してください。

## アプリケーション ロール

アプリケーション ロールは、構成済みソリューション内のデバイスへのアクセスを制御します。

構成済みソリューションをプロビジョニングすると作成されるアプリケーションには、2 つの定義済みロールと 1 つの暗黙ロールが定義されます。

-   **ADMINISTRATOR:** デバイスの追加、管理、削除を完全に制御できます

-   **READ ONLY:** デバイスを表示できます

-   **IMPLICIT READ ONLY:** READ ONLY と同じですが、AAD テナントのすべてのユーザーに付与されます。これは、開発中の利便性のためのものです。このロールは、[RolePermissions.cs][lnk-resource-cs] ソース ファイルを変更することによって削除できます。

### アプリケーション ロールの変更

ユーザーのロールを変更できるのは、AAD グローバル管理者だけです。

1. [Azure クラシック ポータル][lnk-classic-portal]に移動します。

2. **[Active Directory]** を選択します。

3. AAD テナントの名前をクリックします。

4. **[アプリケーション]** をクリックします。

5. 一覧にアプリケーションが表示されない場合は、**[表示]** ドロップダウンを **[自分の会社が所有するアプリケーション]** に切り替えて、チェック マークをクリックします。

6. 構成済みソリューション名と一致するアプリケーションの名前をクリックします。

7. **[ユーザー]** をクリックします。

8. ロールを切り替えるユーザーを選択します。

9. 割り当てボタンをクリックして割り当てるロールをクリックし、チェック マークをクリックします。

## FAQ

### サービス管理者が自分のサブスクリプションと特定の AAD テナントの間のディレクトリ マッピングを変更する必要がある場合は、どうすればよいですか

1. [Azure クラシック ポータル][lnk-classic-portal]に移動し、左側のサービスの一覧で **[設定]** をクリックします。

2. ディレクトリ マッピングを変更するサブスクリプションを選択します。

3. **[ディレクトリの編集]** をクリックします。

4. ドロップダウン リストで使用する**ディレクトリ**を選択します。右向きの矢印をクリックします。

5. ディレクトリのマッピングと影響を受ける共同管理者を確認します。別のディレクトリから移動する場合、元のディレクトリのすべての共同管理者が削除されることに注意してください。

### AAD テナントのドメイン ユーザー/メンバーが構成済みソリューションを作成した場合、アプリケーションに対するロールを割り当ててもらうにはどうすればよいですか

グローバル管理者に依頼して AAD テナントでのグローバル管理者を割り当ててもらい、自分でユーザーにロールを割り当てるアクセス許可を取得するか、またはグローバル管理者にロールの割り当てを依頼します。構成済みソリューションをデプロイした AAD テナントを変更したい場合は、次の質問を参照してください。

### リモート監視構成済みソリューションおよびアプリケーションが割り当てられている AAD テナントを切り替えるにはどうすればよいですか

<https://github.com/Azure/azure-iot-remote-monitoring> からクラウドのデプロイを実行し、新しく作成された AAD テナントで再デプロイできます。新しい AAD テナントを作成したユーザーは既定でグローバル管理者になるので、ユーザーを追加し、ユーザーにロールを割り当てることができます。

1. [Microsoft Azure 管理ポータル][lnk-classic-portal]で新しい AAD ディレクトリを作成します。

2. <https://github.com/Azure/azure-iot-remote-monitoring> にアクセスします。クラウドのデプロイの詳細は、[クラウドのデプロイ][lnk-wiki-clouddeployment]に関するページを参照してください。

3. `build.cmd cloud [debug | release] {name of previously deployed remote monitoring solution}` を実行します (例: `build.cmd cloud debug myRMSolution`)

4. プロンプトが表示されたら、古いテナントの代わりに新しく作成したテナントの **tenantid** を設定します。


### 組織アカウントでログインするときにサービス管理者または共同管理者を変更するにはどうすればよいですか

「[組織アカウントを利用してログインしたときのサービス管理者および共同管理者の変更][lnk-service-admins]」を参照してください。

### 次のエラーが表示されるのはなぜですか。 「お使いのアカウントにはソリューションを作成する適切なアクセス許可がありません。アカウント管理者に確認するか、別のアカウントを使用してください。」

次の図を見てください。

![][img-flowchart]

**Azure サブスクリプションがあるのに次のエラーが表示されるのはなぜですか。** *「構成済みソリューションを作成するには Azure サブスクリプションが必要です。数分で無料試用版のアカウントを作成することができます。」*

Azure サブスクリプションが確かにある場合は、サブスクリプションのテナント マッピングを調べ、ドロップダウンで正しいテナントが選択されていることを確認してください。目的のテナントが正しいことを確認できた場合は、上の図に従って、サブスクリプションとこの AAD テナントのマッピングを確認してください。

[img-flowchart]: media/iot-suite-permissions/flowchart.png

[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-github-repo]: https://github.com/Azure/azure-iot-solution
[lnk-aad-admin]: https://azure.microsoft.com/documentation/articles/active-directory-assign-admin-roles/
[lnk-classic-portal]: https://manage.windowsazure.com/
[lnk-create-edit-users]: https://azure.microsoft.com/documentation/articles/active-directory-create-users/
[lnk-assign-app-roles]: https://github.com/Azure/azure-iot-remote-monitoring/wiki/Manually-setting-up-roles-and-assigning-permissions-in-Azure-Active-Directory-(AAD)#assigning-users-to-the-roles
[lnk-service-admins]: https://azure.microsoft.com/support/changing-service-admin-and-co-admin/
[lnk-admin-roles]: https://azure.microsoft.com/documentation/articles/billing-add-change-azure-subscription-administrator/
[lnk-resource-cs]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/DeviceAdministration/Web/Security/RolePermissions.cs
[lnk-wiki-clouddeployment]: https://github.com/Azure/azure-iot-remote-monitoring/wiki/Cloud-deployment

<!---HONumber=AcomDC_0218_2016-->