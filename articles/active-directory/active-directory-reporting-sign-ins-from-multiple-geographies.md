<properties
	pageTitle="複数の地域からのサインイン"
	description="2 回のサインインが異なるリージョンから実行されたと思われ、サインイン間の時間からユーザーがそれらのリージョン間を移動することは不可能と判断される場合に、そのユーザーを示すレポート。"
	services="active-directory"
	documentationCenter=""
	authors="SSalahAhmed"
	manager="gchander"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/17/2015"
	ms.author="saah;kenhoff"/>

# 複数の地域からのサインイン

| 説明 | レポートの場所 |
| :-------------     | :-------        |
| <p>このレポートには、2 回のサインインが異なる地域から実行されたと見なされ、サインイン間の時間により、ユーザーが 2 カ所の地域間で移動することが不可能である場合に、そのユーザーの成功したサインイン アクティビティが含まれます。考えられる原因: </p><ul><li>ユーザーが自分のパスワードを共有している</li><li>ユーザーがリモート デスクトップを使用して、サインインのために Web ブラウザーを起動している</li><li>ハッカーが他の国からユーザーのアカウントにサインインした</li></ul><p>このレポートの結果には、成功したサインイン イベント、サインイン間の時間、サインインが実行されたと見なされる地域、各地域間の推定移動時間が表示されます。</p><p>表示される移動時間は推定値にすぎず、地域間の実際の移動時間とは異なる可能性があります。また、近隣地域間のサインインに対してイベントは生成されません。</p> | [ディレクトリ] > [レポート] タブ |

![複数の地域からのサインイン](./media/active-directory-reporting-sign-ins-from-multiple-geographies/signInsFromMultipleGeographies.PNG)

<!---HONumber=August15_HO8-->