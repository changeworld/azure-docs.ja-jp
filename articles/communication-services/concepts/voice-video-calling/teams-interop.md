---
title: Teams の会議の相互運用性
titleSuffix: An Azure Communication Services concept document
description: Teams の会議に参加する
author: chpalm
manager: chpalm
services: azure-communication-services
ms.author: chpalm
ms.date: 10/10/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 028e7f9764cbf75263736471f434e04bc6daca0f
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/19/2020
ms.locfileid: "94886823"
---
# <a name="teams-interoperability"></a>Teams の相互運用性

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Azure Communication Services を使用すると、Microsoft Teams と対話するカスタム会議エクスペリエンスを作成できます。 Communication Services ソリューションのユーザーは、音声、ビデオ、画面共有を通じて、Teams の参加者と対話することができます。

この相互運用性によって、ユーザーと Teams の会議とをつなぐカスタムの Azure アプリケーションを作成することができます。 カスタム アプリケーションのユーザーがこの機能を利用するために Azure Active Directory の ID や Teams のライセンスを所有している必要はありません。 これは、(Teams を使い慣れている) 従業員と (カスタム アプリケーションのエクスペリエンスを利用する) 外部ユーザーとを 1 つのシームレスな会議エクスペリエンスに参加させる手段として理想的です。 作成できるエクスペリエンスには次のようなものがあります。

1. 従業員が Teams を使用して会議の予約をする
2. カスタム Communication Services アプリケーションで Microsoft Graph API を使用して会議の詳細にアクセスする
3. カスタム アプリケーションを通じて外部ユーザーと会議の詳細を共有する
4. 外部ユーザーがカスタム アプリケーションを使用し、(Communication Services 通話クライアント ライブラリを通じて) Teams の会議に参加する

このユースケースのアーキテクチャは、おおよそ次のようになります。 

![Teams の相互運用のアーキテクチャ](..//media/call-flows/teams-interop.png)

Teams の会議に備わっている特定の機能 (挙手、集合モード、ブレイクアウト ルームなど) は Teams ユーザーしか利用できませんが、オーディオ、ビデオ、画面共有という会議の主要な機能には、カスタム アプリケーションからアクセスできます。

Communication Services ユーザーが Teams の会議に参加すると、通話クライアント ライブラリを通じて指定された表示名が Teams のユーザーに表示されます。 その他の点では、Communication Services ユーザーと Teams の匿名ユーザーが同様に扱われます。 カスタム アプリケーションでは、Teams の会議を保護するためにユーザー認証などのセキュリティ対策について考慮する必要があります。 匿名ユーザーを会議に参加させることによって生じるセキュリティへの影響に留意し、[Teams のセキュリティ ガイド](/microsoftteams/teams-security-guide#addressing-threats-to-teams-meetings)を利用して、匿名ユーザーに提供する機能を構成してください。

予定されている Teams の会議に Communication Services ユーザーが参加できるのは、匿名での参加が[会議の設定](/microsoftteams/meeting-settings-in-teams)で有効にされている場合に限られます。



## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [通話アプリを Teams の会議に参加させる](../../quickstarts/voice-video-calling/get-started-teams-interop.md)