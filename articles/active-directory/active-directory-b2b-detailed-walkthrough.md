<properties
   pageTitle="Azure Active Directory B2B コラボレーション プレビューの使用方法に関する詳細なチュートリアル | Microsoft Azure"
   description="Azure Active Directory B2B コラボレーションは、会社のアプリケーションにビジネス パートナーが選択的にアクセスできるようにすることで会社間のリレーションシップをサポートします"
   services="active-directory"
   documentationCenter=""
   authors="viv-liu"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="identity"
   ms.date="05/09/2016"
   ms.author="viviali"/>

# Azure AD B2B コラボレーション プレビュー: 詳細なチュートリアル

このチュートリアルでは、Azure AD B2B コラボレーションの使用方法について説明します。Contoso の IT 管理者は、パートナー企業 3 社の従業員とアプリケーションを共有することを検討しています。パートナー企業側が Azure AD を導入する必要はありません。

- Simple Partner Org の Alice
- Medium Partner Org の Bob は、複数のアプリに対するアクセス権が必要です
- Complex Partner Org の Carol は、複数のアプリに対するアクセス権だけでなく、Contoso 社内のグループのメンバーシップも必要です

パートナー ユーザーに招待を送信した後に、Azure AD でそれらのユーザーを構成し、Azure ポータルを使用してアプリに対するアクセス権とグループのメンバーシップを付与することができます。まず Alice を追加してみましょう。

## Alice を Contoso 社のディレクトリに追加する
1. 次のような見出しの .csv ファイルを作成し、Alice の **[Email]**、**[DisplayName]**、**[InviteContactUsUrl]** のみを登録します。**[DisplayName]** は、招待に記載する名前です。Contoso 社の Azure AD ディレクトリに表示される名前にもなります。**[InviteContactUsUrl]** は、Alice から Contoso に連絡するときの窓口です。次の例では、[InviteContactUsUrl] に Contoso 社の LinkedIn プロファイルを指定しています。.csv ファイルの最初の行のラベルは、[CSV ファイル形式のリファレンス](active-directory-b2b-references-csv-file-format.md)で指定されているとおりに入力することが重要です。![Alice の CSV ファイルの例](./media/active-directory-b2b-detailed-walkthrough/AliceCSV.png)

2. Azure ポータルで、Contoso 社のディレクトリにユーザーを追加します ([Active Directory] > [Contoso] > [ユーザー] > [ユーザーの追加])。[ユーザーの種類] ドロップダウンで [パートナー会社のユーザー] を選択します。.csv ファイルをアップロードします。.csv ファイルを閉じてから、アップロードしてください。![Alice の CSV ファイルのアップロード](./media/active-directory-b2b-detailed-walkthrough/AliceUpload.png)

3. Alice は、Contoso 社の Azure AD ディレクトリに外部ユーザーとして表示されるようになりました。![Alice が Azure AD に表示されています](./media/active-directory-b2b-detailed-walkthrough/AliceInAD.png)

4. Alice は次の電子メールを受信します。![Alice の招待電子メール](./media/active-directory-b2b-detailed-walkthrough/AliceEmail.png)

5. Alice がリンクをクリックすると、招待を受け、会社の資格情報を使用してサインインするように求められます。Alice が Azure AD ディレクトリに登録されていない場合、サインアップするように求められます。![Alice の招待後にサインアップします](./media/active-directory-b2b-detailed-walkthrough/AliceSignUp.png)

6. Alice はアプリ アクセス パネルにリダイレクトされます。アプリへのアクセス権が付与されるまで、空のページが表示されます。![Alice のアクセス パネル](./media/active-directory-b2b-detailed-walkthrough/AliceAccessPanel.png)

この手順は、最も単純な形式の B2B コラボレーションです。Alice は Contoso 社の Azure AD ディレクトリのユーザーになったので、Azure ポータルでアプリケーションやグループに対するアクセス権を Alice に付与できます。次に Bob を追加してみましょう。Bob は、アプリケーション Moodle と Salesforce に対するアクセス権が必要です。

## Bob を Contoso 社のディレクトリに追加し、アプリへのアクセスを許可する
1. Azure AD モジュールがインストールされている Windows PowerShell を使用して、Moodle と Salesforce のアプリケーション ID を検索します。次のコマンドレットを使用して ID を取得できます。`Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId` このコマンドレットの結果、Contoso 社で使用できるすべてのアプリケーションと、その AppPrincialId の一覧が表示されます。![Bob の ID を取得します](./media/active-directory-b2b-detailed-walkthrough/BobPowerShell.png)

2. .csv ファイルを作成して、Bob の [Email]、[DisplayName]、**[InviteAppID]**、**[InviteAppResources]**、[InviteContactUsUrl] を登録します。**[InviteAppResources]** には、Moodle と Salesforce の AppPrincipalId (PowerShell で確認できます) をスペース区切りで入力します。**[InviteAppId]** には、Moodle ロゴを含む電子メールやサインイン ページのブランド設定に使用するのと同じ、Moodle の AppPrincipalId を入力します。![Example CSV file for Bob](./media/active-directory-b2b-detailed-walkthrough/BobCSV.png)

3. Alice の場合と同様に、Azure ポータルで .csv ファイルをアップロードします。Bob は Contoso 社の Azure AD ディレクトリの外部ユーザーになりました。

4. Bob は次の電子メールを受信します。![Invitation email for Bob](./media/active-directory-b2b-detailed-walkthrough/BobEmail.png)

5. Bob はリンクをクリックし、招待を受けるように求められます。サインインすると、アクセス パネルが表示され、Moodle と Salesforce 使用できる状態になります。![Access Panel for Bob](./media/active-directory-b2b-detailed-walkthrough/BobAccessPanel.png)

次に Carol を追加しましょう。Carol には、アプリケーションに対するアクセス権だけでなく、Contoso 社のディレクトリ内にあるグループのメンバーシップも必要です。

## Carol を Contoso 社のディレクトリに追加し、アプリケーションに対するアクセス権を付与し、グループのメンバーシップを与える

1. Azure AD モジュールがインストールされている Windows PowerShell を使用して、Contoso 社内のアプリケーション ID とグループ ID を検索します。
 - Bob の場合と同様に、`Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId` コマンドレットを使用して AppPrincipalId を取得します。
 - `Get-MsolGroup | fl DisplayName, ObjectId` コマンドレットを使用して、グループの ObjectId を取得します。その結果、Contoso 社内のすべてのグループとその ObjectId の一覧が表示されます。グループ ID を取得するには、Azure ポータルのグループの [プロパティ] タブで [オブジェクト ID] を確認する方法もあります。![Carol の ID とグループを取得します](./media/active-directory-b2b-detailed-walkthrough/CarolPowerShell.png)

2. .csv ファイルを作成して、Carol の [Email]、[DisplayName]、[InviteAppID]、[InviteAppResources]、**[InviteGroupResources]**、[InviteContactUsUrl] を登録します。**[InviteGroupResources]** には、グループ MyGroup1 と Externals の ObjectId をスペース区切りで入力します。![Example CSV file for Carol](./media/active-directory-b2b-detailed-walkthrough/CarolCSV.png)

3. Azure ポータルで .csv ファイルをアップロードします。

4. Carol は、Contoso 社のディレクトリのユーザーになり、次の Azure ポータルの図のように、グループ MyGroup1 と Externals のメンバーになりました。![Azure AD のグループに Carol が表示されます](./media/active-directory-b2b-detailed-walkthrough/CarolGroup.png)

5. Carol は、招待を受けるリンクが記載された電子メールを受信します。サインイン後は、アプリ アクセス パネルにリダイレクトされ、Moodle と Salesforce へのアクセス権が付与されます。

Azure AD B2B コラボレーションのパートナー企業のユーザーを追加する手順は以上です。このチュートリアルでは、3 つの独立した .csv ファイルを使用してユーザー Alice、Bob、および Carol を Contoso 社のディレクトリに追加する方法を紹介しました。このプロセスは、これらの .csv ファイルを 1 つのファイルにまとめることで、さらに簡単にできます。![Alice、Bob、Carol の CSV ファイルの例](./media/active-directory-b2b-detailed-walkthrough/CombinedCSV.png)

## 関連記事
Azure AD B2B コラボレーションに関する他の記事を参照してください。

- [Azure AD B2B コラボレーションとは](active-directory-b2b-what-is-azure-ad-b2b.md)
- [動作のしくみ](active-directory-b2b-how-it-works.md)
- [CSV ファイル形式リファレンス](active-directory-b2b-references-csv-file-format.md)
- [外部ユーザー トークンの形式](active-directory-b2b-references-external-user-token-format.md)
- [外部ユーザー オブジェクト属性の変更](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [現在のプレビューの制限事項](active-directory-b2b-current-preview-limitations.md)
- [Article Index for Application Management in Azure Active Directory](active-directory-apps-index.md)

<!---HONumber=AcomDC_0511_2016-->