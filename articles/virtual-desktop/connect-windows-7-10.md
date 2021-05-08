---
title: Windows Virtual Desktop に接続する Windows 10 または 7 - Azure
description: Windows デスクトップ クライアントを使用して Windows Virtual Desktop に接続する方法。
author: Heidilohr
ms.topic: how-to
ms.date: 09/22/2020
ms.author: helohr
manager: femila
ms.custom: template-how-to
ms.openlocfilehash: 625662a6b67e7d30e6320fe7831e4fa7793b9c30
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2021
ms.locfileid: "106447881"
---
# <a name="connect-with-the-windows-desktop-client"></a>Windows デスクトップ クライアントを使用して接続する

Windows デスクトップ クライアントを使用して、Windows 10、Windows 10 IoT Enterprise、Windows 7 を使用しているデバイス上の Windows Virtual Desktop リソースにアクセスできます。 

> [!IMPORTANT]
> これは、Windows 8 または Windows 8.1 をサポートしていません。
> 
> Azure Resource Manager オブジェクトのみをサポートしています。Azure Resource Manager を使用せずにオブジェクトをサポートする場合は、「[Windows デスクトップ (クラシック) クライアントを使用して接続する](./virtual-desktop-fall-2019/connect-windows-7-10-2019.md)」を参照してください。
> 
> これは、RemoteApp とデスクトップ接続 (RADC) クライアントまたはリモート デスクトップ接続 (MSTSC) クライアントをサポートしていません。

## <a name="install-the-windows-desktop-client"></a>Windows デスクトップ クライアントをインストールする

お使いの Windows のバージョンに基づいてクライアントをダウンロードします。

- [Windows (64 ビット)](https://go.microsoft.com/fwlink/?linkid=2068602)
- [Windows 32 ビット](https://go.microsoft.com/fwlink/?linkid=2098960)
- [Windows ARM64](https://go.microsoft.com/fwlink/?linkid=2098961)

インストール中にアクセス権を指定するには、次のいずれかを選択します。

- **[Install just for you]\(自分専用にインストール\)**
- **[Install for all users of this machine]\(このマシンのすべてのユーザー用にインストール\)** (管理者権限が必要です)

インストール後にクライアントを起動するには、 **[スタート]** メニューを使用して、「**リモート デスクトップ**」を検索します。

## <a name="subscribe-to-a-workspace"></a>ワークスペースをサブスクライブする

ワークスペースをサブスクライブするには、次のいずれかを選択します。

- 職場または学校アカウントを使用して、利用可能なリソースをクライアントに検出させる
- 特定のリソース URL を使用する

サブスクライブしたリソースを起動するには、**接続センター** に移動し、リソースをダブルクリックします。

> [!TIP]
> **[スタート]** メニューからリソースを起動するには、ワークスペース名のフォルダーを検索するか、検索バーにリソース名を入力します。

### <a name="use-a-user-account"></a>ユーザー アカウントを使用する

1. メイン ページで **[サブスクライブ]** を選択します。
1. メッセージが表示されたら、自分のユーザー アカウントでサインインします。

ワークスペース別にグループ化されたリソースが、**接続センター** に表示されます。

   > [!NOTE]
   > Windows クライアントは自動的に既定の Windows Virtual Desktop (クラシック) になります。 
   > 
   > ただし、クライアントがその他の Azure Resource Manager リソースを検出した場合、そのリソースが自動的に追加されるか、それらが利用可能であることがユーザーに通知されます。

### <a name="use-a-specific-url"></a>特定の URL を使用する

1. メイン ページで **[Subscribe with URL]\(URL を使用してサブスクライブする\)** を選択します。
1. "*ワークスペース URL*" または "*メール アドレス*" を入力してください。
   - **ワークスペース URL** の場合は、管理者から提供された URL を使用します。

   |使用可能なリソース|URL|
   |-|-|
   |Windows Virtual Desktop (クラシック)|`https://rdweb.wvd.microsoft.com/api/feeddiscovery/webfeeddiscovery.aspx`|
   |Windows Virtual Desktop|`https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery`|
   |Windows Virtual Desktop (US Gov)|`https://rdweb.wvd.azure.us/api/arm/feeddiscovery`|
   
   - **メール アドレス** の場合は、自分のメール アドレスを使用します。 
      
   管理者が[メール検出](/windows-server/remote/remote-desktop-services/rds-email-discovery)を有効にしている場合、クライアントはそのメール アドレスに関連付けられている URL を検索します。

1. **[次へ]** を選択します。
1. メッセージが表示されたら、自分のユーザー アカウントでサインインします。

ワークスペース別にグループ化されたリソースが、**接続センター** に表示されます。

## <a name="next-steps"></a>次の手順

クライアントの使用方法の詳細については、「[Windows デスクトップ クライアントの概要](/windows-server/remote/remote-desktop-services/clients/windowsdesktop/)」を参照してください。

管理者の方で、クライアントの機能についてより詳細な情報を知りたい場合は、「[管理者のための Windows デスクトップ クライアント](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-admin)」を参照してください。
