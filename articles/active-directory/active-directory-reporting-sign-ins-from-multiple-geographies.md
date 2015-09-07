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
<p>次のレポートには、1 人のユーザーの成功したサインインで、2 回のサインインが異なる地域と時間で行われ、サインインとサインインの間にユーザーが地域から地域へ移動することは不可能であると思われるサインインが含まれています。考えられる原因: </p><ul><li>ユーザーが自分のパスワードを他のユーザーと共有している</li><li>ユーザーがリモート デスクトップを使用して、Web ブラウザーを起動してサインインしている</li><li>ハッカーが他の国からユーザーのアカウントにサインインしている</li><li>ユーザーが VPN またはプロキシを使用している</li><li>ユーザーがデスクトップや携帯電話などの複数のデバイスから同時にサインインしているが、携帯電話の IP アドレスが通常とは異なっている</li></ul><p>このレポートの結果には、成功したサインイン イベント、サインイン間の時間、サインインが実行されたとみなされる地域、各地域間の推定移動時間が表示されます。</p><p>表示される移動時間は推定値にすぎず、地域間の実際の移動時間とは異なる可能性があります。</p>


![複数の地域からのサインイン](./media/active-directory-reporting-sign-ins-from-multiple-geographies/signInsFromMultipleGeographies.PNG)

<!---HONumber=August15_HO9-->