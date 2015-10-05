<properties
	pageTitle="感染している可能性があるデバイスからのサインイン"
	description="マルウェア (悪意のあるソフトウェア) が実行されている可能性があるデバイスから実行されたサインイン試行が含まれるレポート。"
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


# 感染している可能性があるデバイスからのサインイン
このレポートは、ユーザーのデバイスがウイルスに感染してボットネットの一部 (ゾンビ コンピューターとも呼ばれます) になっていることを識別しようとするものです。ユーザーがサインインした IP アドレスを、ボットネット サーバーにつながっていることがわかっている IP アドレスと関連付けます。

推奨事項: このレポートは、ユーザーのデバイスではなく、IP アドレスにフラグを設定します。ユーザーに連絡して、ユーザーのすべてのデバイスをスキャンして安全を確認することをお勧めします。ユーザー自身のデバイスが感染しているか、ユーザーと同じ IP アドレスを使用している他のユーザーがウイルスに感染したデバイスを使用している可能性があります。

マルウェア感染に対処する方法の詳細については、[マルウェア対策センター](http://go.microsoft.com/fwlink/?linkid=335773)を参照してください。

![感染している可能性があるデバイスからのサインイン](./media/active-directory-reporting-sign-ins-from-possibly-infected-devices/signInsFromPossiblyInfectedDevices.PNG)

<!---HONumber=Sept15_HO4-->