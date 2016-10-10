<properties
	pageTitle="不明なソースからのサインイン"
	description="ユーザーが匿名のプロキシ IP アドレスからディレクトリに正常にサインインしたことを示すレポート。"
	services="active-directory"
	documentationCenter=""
	authors="SSalahAhmed"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/04/2016"
	ms.author="saah;kenhoff"/>

# 不明なソースからのサインイン
このレポートは、ディレクトリに正常にサインインしたユーザーで、割り当てられたクライアント IP アドレスがマイクロソフトによって匿名のプロキシ IP アドレス (Tor IP アドレスなど) として認識されているユーザーを示します。このようなプロキシは、自分のコンピューターの IP アドレスを隠したいユーザーによって使用されることが多く、悪意のある目的で使用される場合があります。

このレポートの結果は、そのアドレスとプロキシの IP アドレスから、ユーザーがディレクトリに正常にサインインした回数を示します。


![不明なソースからのサインイン](./media/active-directory-reporting-sign-ins-from-unknown-sources/signInsFromUnknownSources.PNG)

<!---HONumber=AcomDC_0928_2016-->