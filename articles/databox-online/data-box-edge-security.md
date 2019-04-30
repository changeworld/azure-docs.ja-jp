---
title: Azure Data Box Edge のセキュリティ | Microsoft Docs
description: Azure Data Box Edge のデバイス、サービス、オンプレミスまたはクラウド上のデータを保護するセキュリティ機能とプライバシー機能について説明します。
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 5316ddf9d456731f2789241434926366f732993a
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2019
ms.locfileid: "59682102"
---
# <a name="azure-data-box-edge-security-and-data-protection"></a>Azure Data Box Edge のセキュリティとデータ保護

新しいテクノロジを採用する際に、セキュリティは大きな懸案事項となります。そのテクノロジで機密データや財産的価値のあるデータを扱うとなれば、なおさらです。 Microsoft Azure Data Box Edge ソリューションは、承認済みのエンティティ以外データの閲覧、変更、削除ができないよう徹底するうえで役立ちます。

この記事では、Data Box Edge のセキュリティ機能に注目し、このソリューションの各コンポーネントとそこに格納されるデータを保護する働きを持った機能について説明します。

Azure Data Box Edge ソリューションは、互いに連携し合う 4 つのメイン コンポーネントで構成されています。

- **Azure でホストされる Data Box Edge サービス** - デバイスを発注して構成し、その注文を最後まで追跡するための管理リソース。
- **Data Box Edge デバイス** - オンプレミスのデータを Azure にインポートするためにお客様に発送される転送デバイス。
- **デバイスに接続されるクライアント/ホスト** - お客様のインフラストラクチャにおいて保護の対象となるデータを格納しているクライアント。Data Box Edge デバイスに接続されます。
- **クラウド ストレージ** – Azure クラウド内のデータの保存先となる場所。 通常、この場所は、お客様が作成した Data Box Edge リソースにリンクされたストレージ アカウントになります。

## <a name="data-box-edge-service-protection"></a>Data Box Edge サービスの保護

Data Box Edge サービスは、Microsoft Azure にホストされている管理サービスです。 このサービスは、デバイスを構成および管理するために使用します。

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-edge-device-protection"></a>Data Box Edge デバイスの保護

Data Box Edge デバイスは、データをローカルで処理して、そのデータを Azure に送信することで、データを変換できるオンプレミス デバイスです。 お客様のデバイスは...

- Data Box Edge サービスにアクセスするためのアクティブ化キーが必要です。
- デバイスのパスワードで常に保護されます。
- ロックダウンされます。 デバイスの BMC と BIOS は、パスワードで保護され、BIOS に対するユーザー アクセスは制限されます。
- セキュア ブートが有効です。
- Windows Defender Device Guard を実行します。 Device Guard によって、コード整合性ポリシーで定義する信頼されたアプリケーションのみを実行することができます。

### <a name="protect-the-device-via-activation-key"></a>アクティブ化キーでデバイスを保護する

Azure サブスクリプションで作成した Data Box Edge サービスに参加できるのは、承認されている Data Box Edge デバイスだけです。 デバイスを承認するには、アクティブ化キーを使用して、Data Box Edge サービスを使用するデバイスをアクティブにする必要があります。

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

詳細については、「[アクティブ化キーの取得](data-box-edge-deploy-prep.md#get-the-activation-key)」をご覧ください。

### <a name="protect-the-device-via-password"></a>パスワードでデバイスを保護する

パスワードによって、承認されたユーザーだけがお客様のデータにアクセスできるようにします。 Data Box Edge デバイスは、ロックされた状態で起動されます。

次のようにすることができます。

- ブラウザーを介してデバイスのローカル Web UI に接続し、パスワードを指定して、デバイスにサインインします。
- HTTP 経由でデバイスの PowerShell インターフェイスにリモートで接続します。 既定ではリモート管理がオンになっています。 デバイスのパスワードを入力して、デバイスにサインインできます。 詳細については、[Data Box Edge デバイスにリモートで接続する方法](data-box-edge-connect-powershell-interface.md#connect-to-the-powershell-interface)に関する記事をご覧ください。

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- ローカル Web UI を使用して[パスワードを変更します](data-box-edge-manage-access-power-connectivity-mode.md#manage-device-access)。 パスワードを変更する場合は、サインインできなくなることを防ぐために、すべてのリモート アクセス ユーザーにその旨を通知してください。

## <a name="protect-the-data"></a>データの保護

ここでは、移動中のデータと保管されているデータを保護する Data Box Edge のセキュリティ機能について説明します。

### <a name="protect-data-at-rest"></a>保存データの保護

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>移動中のデータの保護

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>ストレージ アカウントによるデータの保護

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- 承認されていないユーザーがストレージ アカウントにアクセスできないよう[ストレージ アカウント キーの同期](data-box-edge-manage-shares.md#sync-storage-keys)とローテーションは日常的に行う。

## <a name="manage-personal-information"></a>個人情報の管理

Data Box Edge サービスは、次の主要なインスタンスの個人情報を収集します。

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

共有にアクセスできる、または削除できるユーザーの一覧を表示するには、[Data Box Edge での共有の管理](data-box-edge-manage-shares.md)に関するページにある手順に従ってください。

詳細については、[セキュリティ センター](https://www.microsoft.com/trustcenter)にある Microsoft のプライバシー ポリシーを確認してください。

## <a name="next-steps"></a>次の手順

[Data Box Edge デバイスをデプロイします](data-box-edge-deploy-prep.md)。
