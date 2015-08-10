<properties
	pageTitle="複数のエラー後のサインイン"
	description="サインイン試行に何度も連続して失敗した後に正常にサインインしたユーザーを示すレポート。"
	services="active-directory"
	documentationCenter=""
	authors="kenhoff"
	manager="ilanas"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/01/2015"
	ms.author="kenhoff"/>

# 複数のエラー後のサインイン

| 説明 | レポートの場所 |
| :-------------     | :-------        |
| このレポートは、サインイン試行に何度も連続して失敗した後に、正常にサインインしたユーザーを示します。考えられる原因: <ul><li>ユーザーがパスワードを忘れた</li><li>ユーザーが、パスワードの推測に成功したブルート フォース攻撃の被害を受けた</li></ul><p>このレポートの結果には、サインインに成功するまでに失敗したサインイン試行の連続回数と、最初に成功したサインインに関連付けられているタイムスタンプが表示されます。</p><p><b>レポートの設定: </b> レポートに表示する失敗したサインイン試行の最小連続回数を構成できます。この設定を変更する場合、その変更は、既存のレポートに現在表示されている失敗した既存のサインインには適用されないことに注意してください。変更は、すべての今後のサインインに適用されます。このレポートの変更は、ライセンスを付与された管理者がのみ行うことができます。 | [ディレクトリ] > [レポート] タブ |

![複数のエラー後のサインイン](./media/active-directory-reporting-sign-ins-after-multiple-failures/signInsAfterMultipleFailures.PNG)

<!---HONumber=July15_HO5-->