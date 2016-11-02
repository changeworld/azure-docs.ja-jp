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
	ms.date="03/04/2016"
	ms.author="saah;kenhoff"/>

# 複数の地域からのサインイン

このレポートには、1 人のユーザーの成功したサインインで、2 回のサインインが異なる地域と時間で行われ、サインインとサインインの間にユーザーが地域から地域へ移動することは不可能であると思われるサインインが含まれます。考えられる原因は次のとおりです。

- ユーザーが他のユーザーとパスワードを共有している

- ユーザーがリモート デスクトップを使用してサインイン用の Web ブラウザーを起動している

- ハッカーが別の国からユーザーのアカウントにサインインした

- ユーザーが VPN またはプロキシを使用している

- ユーザーが同時に複数のデバイス (デスクトップと携帯電話など) からサインインしており、携帯電話の IP アドレスが異常である

このレポートの結果では、成功したサインイン イベントが、サインインの間隔、サインインが開始されたと思われる地域、および地域間の推定移動時間とともに示されます。示される移動時間は単なる推定値であり、各場所間の実際の移動時間とは異なる可能性があります。


![複数の地域からのサインイン](./media/active-directory-reporting-sign-ins-from-multiple-geographies/signInsFromMultipleGeographies.PNG)

<!---HONumber=AcomDC_0309_2016-->