<properties 
	pageTitle="既知のネットワーク |　Microsoft Azure" 
	description="既知のネットワークを構成すると、";複数の地域からのサインイン"; レポートおよび ";疑わしいアクティビティを示す IP アドレスからのサインイン"; レポートに、組織が所有する IP アドレスが含まれないようにすることができます。" 
	services="active-directory" 
	documentationCenter="" 
	authors="markusvi" 
	manager="stevenpo"  
	editor=""/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/03/2016" 
	ms.author="markvi"/>

# 既知のネットワーク


Azure Active Directory のアクセスおよび使用状況レポートを使用すると、組織のディレクトリの整合性とセキュリティを表示できます。ディレクトリ管理者は、この情報を使用して、リスクを軽減するために適切に計画できるように、セキュリティ上のリスクがある箇所をより適切に確認できます。

"*複数の地域からのサインイン*" レポートと "*疑わしいアクティビティを示す IP アドレスからのサインイン*" レポートで、組織で実際に所有している IP アドレスに対してフラグが不適切に設定される場合が考えられます。

これは、たとえば次の場合に起こります。

- ボストン支社のユーザーがサンフランシスコのデータ センターにリモートでサインインすると、"複数の地域からのサインイン" レポートがトリガーされます。 

- 組織のユーザーが間違ったパスワードで複数回サインインしようとすると、"疑わしいアクティビティを示す IP アドレスからのサインイン" レポートがトリガーされます。

こうした場合に誤解を招くセキュリティ レポートが生成されないようにするには、組織の パブリック IP アドレスの一覧に既知の IP アドレス範囲を追加する必要があります。


###組織のパブリック IP アドレス範囲を追加するには、次の手順に従います。 

1.	[Microsoft Azure 管理ポータル](https://manage.windowsazure.com)にサインインします。

2.	左側のウィンドウで、**[Active Directory]** をクリックします。<br><br>![Cloud App Discovery のしくみ](./media/active-directory-known-networks/known-netwoks-01.png)

3.	**[ディレクトリ]** タブでディレクトリを選択します。

4.	上部のメニューで **[構成]** をクリックします。<br><br>![Cloud App Discovery のしくみ](./media/active-directory-known-networks/known-netwoks-02.png)

5.	[構成] タブで、**[組織のパブリック IP アドレス範囲]** に移動します。<br><br>![Cloud App Discovery のしくみ](./media/active-directory-known-networks/known-netwoks-03.png)

6.	**[既知の IP アドレス範囲の追加]** をクリックします。

7.	表示されるダイアログ ボックスでアドレス範囲を追加し、完了したらチェック ボタンをクリックします。<br><br>![Cloud App Discovery のしくみ](./media/active-directory-known-networks/known-netwoks-04.png)


**その他のリソース**


* [アクセスおよび使用状況レポートの表示](active-directory-view-access-usage-reports.md)
* [不審なアクティビティのある IP アドレスからのサインイン](active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity.md)
* [複数の地域からのサインイン](active-directory-reporting-sign-ins-from-multiple-geographies.md)

<!---HONumber=AcomDC_0504_2016-->