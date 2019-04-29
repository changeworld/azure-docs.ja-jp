---
title: Azure Data Box Gateway のセキュリティ | Microsoft Docs
description: Azure Data Box Gateway の仮想デバイス、サービス、オンプレミスまたはクラウド上のデータを保護するセキュリティ機能とプライバシー機能について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: b27b712128ddfbb07a7a7f68f616c20ec3fb53d3
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2019
ms.locfileid: "59686159"
---
# <a name="azure-data-box-gateway-security-and-data-protection"></a>Azure Data Box Gateway のセキュリティとデータ保護

新しいテクノロジを採用する際に、セキュリティは大きな懸案事項となります。そのテクノロジで機密データや財産的価値のあるデータを扱うとなれば、なおさらです。 Microsoft Azure Data Box Gateway ソリューションは、承認済みのエンティティ以外データの閲覧、変更、削除ができないよう徹底するうえで役立ちます。

この記事では、Azure Data Box Gateway のセキュリティに注目し、各ソリューション コンポーネントとそこに格納されるデータを保護する働きを持った機能について説明します。

Data Box Gateway ソリューションは、互いに連携し合う 4 つのメイン コンポーネントで構成されています。

- **Azure でホストされる Data Box Gateway サービス** - デバイスを発注して構成し、その注文を最後まで追跡するための管理リソース。
- **Data Box Gateway デバイス** - お客様が用意するシステムのハイパーバイザー内にプロビジョニングする仮想デバイスです。 この仮想デバイスは、オンプレミス データを Azure にインポートするために使用されます。
- **デバイスに接続されるクライアント/ホスト** - お客様のインフラストラクチャにおいて保護の対象となるデータを格納しているクライアント。Data Box Gateway デバイスに接続されます。
- **クラウド ストレージ** – Azure クラウド内のデータの保存先となる場所。 通常、この場所は、お客様が作成した Data Box Gateway リソースにリンクされたストレージ アカウントになります。


## <a name="data-box-gateway-service-protection"></a>Data Box Gateway サービスの保護

Data Box Gateway サービスは、Microsoft Azure にホストされている管理サービスです。 このサービスは、デバイスを構成および管理するために使用します。

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-gateway-device-protection"></a>Data Box Gateway デバイスの保護

Data Box Gateway デバイスは、お客様が用意するオンプレミス システムのハイパーバイザー内にプロビジョニングする仮想デバイスです。 このデバイスは、データを Azure に送信するために役立ちます。 お客様のデバイスは...

- Data Box Edge/Data Box Gateway のサービスにアクセスするためのアクティブ化キーが必要です。
- デバイスのパスワードで常に保護されます。
<!---  secure boot enabled.
- Runs Windows Defender Device Guard. Device Guard allows you to run only trusted applications that you define in your code integrity policies.-->

### <a name="protect-the-device-via-activation-key"></a>アクティブ化キーでデバイスを保護する

Azure サブスクリプションで作成した Data Box Gateway サービスに参加できるのは、承認されている Data Box Gateway デバイスだけです。 デバイスを承認するには、アクティブ化キーを使用して、Data Box Gateway サービスを使用するデバイスをアクティブにする必要があります。

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

詳細については、「[アクティブ化キーの取得](data-box-gateway-deploy-prep.md#get-the-activation-key)」をご覧ください。

### <a name="protect-the-device-via-password"></a>パスワードでデバイスを保護する

パスワードによって、承認されたユーザーだけがお客様のデータにアクセスできるようにします。 Data Box Gateway デバイスは、ロックされた状態で起動されます。

次のようにすることができます。

- ブラウザーを介してデバイスのローカル Web UI に接続し、パスワードを指定して、デバイスにサインインします。
- HTTP 経由でデバイスの PowerShell インターフェイスにリモートで接続します。 既定ではリモート管理がオンになっています。 デバイスのパスワードを入力して、デバイスにサインインできます。 詳細については、[Data Box Gateway デバイスにリモートで接続する方法](data-box-gateway-connect-powershell-interface.md#connect-to-the-powershell-interface)に関する記事を参照してください。

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- ローカル Web UI を使用して[パスワードを変更します](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access)。 パスワードを変更する場合は、サインインできなくなることを防ぐために、すべてのリモート アクセス ユーザーにその旨を通知してください。


## <a name="protect-the-data"></a>データの保護

ここでは、移動中のデータと保管されているデータを保護する Data Box Gateway のセキュリティ機能について説明します。

### <a name="protect-data-at-rest"></a>保存データの保護

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>移動中のデータの保護

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>ストレージ アカウントによるデータの保護

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- 承認されていないユーザーがストレージ アカウントにアクセスできないよう[ストレージ アカウント キーの同期](data-box-gateway-manage-shares.md#sync-storage-keys)とローテーションは日常的に行う。

## <a name="manage-personal-information"></a>個人情報の管理

Data Box Gateway サービスは、次の主要なインスタンスの個人情報を収集します。

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

共有にアクセスできる、または削除できるユーザーの一覧を表示するには、[Data Box Gateway での共有の管理](data-box-gateway-manage-shares.md)に関するページにある手順に従ってください。

詳細については、[セキュリティ センター](https://www.microsoft.com/trustcenter)にある Microsoft のプライバシー ポリシーを確認してください。

## <a name="next-steps"></a>次の手順

[Data Box Gateway デバイスをデプロイする](data-box-gateway-deploy-prep.md)。
