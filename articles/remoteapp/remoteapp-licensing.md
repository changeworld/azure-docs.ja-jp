---
title: "Azure RemoteApp ライセンス | Microsoft Docs"
description: "Azure RemoteApp のライセンスの仕組みについて説明します。"
services: remoteapp
documentationcenter: 
author: lizap
manager: mbaldwin
ms.assetid: ff8ebd20-61a1-4f10-87a6-234a170534c9
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/15/2016
ms.author: elizapo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f4429461139a41644f87185bbb06b1f6a8011345


---
# <a name="how-does-licensing-work-in-azure-remoteapp"></a>RemoteApp のライセンスはどのような仕組みになっていますか。
> [!IMPORTANT]
> Azure RemoteApp の提供は終了しました。 詳細については、 [お知らせ](https://go.microsoft.com/fwlink/?linkid=821148) をご覧ください。
> 
> 

Azure RemoteApp サービスを設定し、テンプレートを作成したので、ユーザーにアプリケーションを発行する準備ができました。 最後にもうひとつ解決することがあります: ライセンスです。 RemoteApp のライセンスはどのような仕組みになっていますか。また、RemoteApp を通して共有するアプリケーションについてはどうですか。

RemoteApp は、Windows ライセンスもリモート デスクトップ CAL も必要としません。 サブスクリプションにより、RemoteApp 側自体のライセンス処理が行われます  (価格プランの詳細については、[こちら](https://azure.microsoft.com/pricing/details/remoteapp)を参照してください)。

サブスクリプションに含まれているイメージのいずれかを使用する場合、そのイメージにインストールされたアプリはすべて共有できます。その際、個別のライセンスは必要ありません。 たとえば、Windows Server 2012 R2 テンプレート イメージを使用してコレクションを作成すると、System Center Endpoint Protection をユーザーと共有できます。 このルールの例外として、個別のサブスクリプションを必要とする Office 365 ProPlus と、実稼働コレクションでは共有できない Office 2013 があります。

RemoteApp に同梱される Office 365 テンプレート イメージを使用する場合は、 *既に* Office 365 ProPlus プランを所有している必要があります。 カスタム テンプレートを使用して発行する Office 365 アプリも同様です。 自分のサブスクリプションでアプリを有効化する必要があります。 これは試用版でも有料サブスクリプションでも同様です。 試用期間中に Office 365 テンプレート イメージを使用する必要があり、" *サブスクリプションをまだ所有していない*" 場合は、Office 365 ページにアクセスして、試用版サブスクリプションに [サインアップ](https://go.microsoft.com/fwlink/p/?LinkID=403802) する必要があります。 詳細については、「 [Using Office 365 with Azure RemoteApp](remoteapp-o365.md) 」を参照してください。

試用期間中に、Office 365 試用版のサブスクリプションが必要ない場合は、RemoteApp に付属の Office 2013 Professional Plus テンプレート イメージを使用します。 このテンプレート イメージは 30 日間のみ使用でき、有料のコレクションに変換することはできません。

その他のアプリケーションについては、そのアプリケーションを共有するためのライセンスを所有していることを確認します。

ご理解いただけたでしょうか。 法的に共有が可能なすべてのアプリを発行することができます。 またプログラムを共有する資格を確認する必要があります。

クラウド コレクションでは CAL またはボリューム ライセンス契約を使用できないことに注意してください。 ボリューム ライセンス契約を使用して、ハイブリッド コレクションのアプリケーションをライセンス認証することが *できます* (Office を除く)。 ボリューム ライセンス メディアから、テンプレート イメージにインストールする必要があります。 アプリケーションのベンダの情報に従ってリモート デスクトップ環境にライセンスをインストールしてください。




<!--HONumber=Nov16_HO2-->


