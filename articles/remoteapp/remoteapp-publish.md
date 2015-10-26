<properties
    pageTitle="Azure RemoteApp のアプリの発行 | Microsoft Azure"
    description="Azure RemoteApp のアプリとリソースを発行する方法について説明します。"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/12/2015"
    ms.author="elizapo" />


# RemoteApp のアプリを発行する方法

RemoteApp コレクションの作成後、ユーザーが使用できるアプリまたはリソースを発行する必要があります。サブスクリプションで提供されるテンプレート イメージには、既定で発行されているいくつかのアプリしか含まれていません。その他のアプリを共有するにはアプリを公開する必要があります。

> [AZURE.NOTE]アプリを更新する場合には、 まず [イメージを更新する](remoteapp-update.md)必要があります。

ポータルの **[発行しています]** タブをクリックして、**[発行]** をクリックします。**[スタート]** メニューのテンプレート イメージからアプリを追加することも、テンプレート イメージにインストールされているアプリの場所の指定することもできます。**[スタート]** メニューから追加する場合は、一覧から発行するアプリケーションを選択します。アプリのパスを指定する場合は、アプリの名前とアプリのパスを入力します。パスには、"c:\" ではなく、たとえば "%systemdrive%" のような変数を使用します。

> [AZURE.NOTE]**[スタート]** メニューからアプリを追加する場合は、*テンプレート イメージの **[スタート]** メニューにそのアプリを追加しておく*必要があります。 そうしないと、RemoteApp は、**[スタート]** メニューに現在*存在する*もののみを表示するので、わかりにくくなる可能性があります。

>アプリを **[スタート]** メニューに表示するには、%systemdrive%\\ProgramData\\Microsoft\\Windows\\Start Menu\\Programs フォルダーの中にショートカット ファイル (**.lnk**) を作成します。

> テンプレートを作成するときに **[スタート]** メニューにアプリを追加しなかった場合は、アプリのパスを追加する方法を選択します(またはテンプレート イメージを再作成することもできますが、これは少し手間のかかる作業です)。


 

<!---HONumber=Oct15_HO3-->