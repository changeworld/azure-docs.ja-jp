---
title: カスタム アプリをホストする Azure Virtual Desktop - Azure
description: Azure Virtual Desktop を使用してカスタム アプリを提供する方法。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 07/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 35e07fad22760e6c8c87e60f77cd6d98e5db42a2
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/14/2021
ms.locfileid: "113798952"
---
# <a name="how-to-host-custom-apps-with-azure-virtual-desktop"></a>Azure Virtual Desktop を使用してカスタム アプリをホストする方法

Azure Virtual Desktop では、複数の種類の Windows アプリケーションをホストできます。 アプリをデプロイする予定のアプリ パッケージの種類に応じてアプリを準備することをお勧めします。 この記事では、アプリ パッケージの種類ごとに行う必要がある操作について説明します。 

>[!NOTE]
>アプリはマルチセッション ホスト上でホストすることをお勧めします。 さらに、アプリをテストして、マルチセッション ホストでの実行中にアプリが期待どおりに動作することを確認することをお勧めします。 たとえば、同じセッション ホスト上の 2 人以上のユーザーがアプリを同時に正常に実行できるかどうかを確認するためのテストを実行します。

## <a name="msix"></a>MSIX

MSIX は、Azure Virtual Desktop のカスタム アプリに推奨されます。それは、このパッケージの種類ではサービスの組み込み [MSIX アプリのアタッチ機能](../app-attach-glossary.md)を利用できるためです。 既存の Win32 アプリケーションを MSIX 形式で再パッケージ化する方法については、「[既存の Win32 アプリケーションを MSIX 形式に再パッケージ化する](/windows/application-management/msix-app-packaging-tool)」を参照してください。

対象のアプリを MSIX 形式でパッケージ化すると、Azure Virtual Desktop の MSIX アプリのアタッチ機能を使用して、アプリを顧客に提供できます。 MSIX アプリのアタッチを使用してアプリをパッケージ化およびデプロイする方法については、「[MSIX アプリのアタッチを使用してアプリをデプロイする](msix-app-attach.md)」を参照してください。

## <a name="other-options-for-win32-applications"></a>Win32 アプリケーションのその他のオプション

次のオプションを使用して、MSIX 形式で再パッケージ化せずに Win32 アプリケーションをユーザーに提供することもできます。

### <a name="include-the-application-manually-on-session-hosts"></a>セッション ホストにアプリケーションを手動で含める

「[マスター VHD イメージを準備してカスタマイズする](../set-up-customize-master-image.md)」の手順に従って、仮想マシンに使用する Windows イメージの一部としてアプリを含めます。 具体的には、「[その他のアプリケーションとレジストリを構成する](../set-up-customize-master-image.md#other-applications-and-registry-configuration)」セクションの指示に従って、すべてのユーザー向けにアプリケーションをインストールします。

### <a name="use-microsoft-endpoint-manager-to-deploy-the-application-at-scale"></a>Microsoft エンドポイント マネージャーを使用してアプリケーションを大規模にデプロイする

Microsoft エンドポイント マネージャーを使用してセッション ホストを管理する場合は、「[Windows 10 デバイスにアプリをインストールする](/mem/intune/apps/apps-windows-10-app-deploy#install-apps-on-windows-10-devices)」の手順に従ってアプリケーションをデプロイできます。 デプロイ内のすべてのユーザーがアプリケーションにアクセスできるようにするために、すべてのセッション ホストにアプリを "デバイス コンテキスト" モードでデプロイしてください。

### <a name="manual-installation"></a>手動インストール

セッション ホストごとにプロセスを繰り返す必要があるため、アプリを手動でインストールすることはお勧めしません。 これは、IT プロフェッショナルがテスト目的でよく使用する方法です。

アプリを手動でインストールする必要がある場合は、Azure Virtual Desktop ホスト プールを設定した後、管理者アカウントを使用してセッション ホストにリモート接続する必要があります。 その後、物理 PC の場合と同じようにアプリケーションをインストールします。 このプロセスを繰り返して、ホスト プール内の各セッション ホストにアプリケーションをインストールする必要があります。

>[!NOTE]
>設定プロセスですべてのユーザー向けにアプリケーションをインストールするオプションが表示される場合は、そのオプションを選択します。

## <a name="microsoft-store-applications"></a>Microsoft Store アプリケーション

現時点では、Azure Virtual Desktop でのリモート アプリのストリーミングに Microsoft Store アプリを使用することはお勧めしません。

## <a name="next-steps"></a>次のステップ

MSIX アプリのアタッチを使用してアプリをパッケージ化およびデプロイする方法については、「[MSIX アプリのアタッチを使用してアプリをデプロイする](msix-app-attach.md)」を参照してください。