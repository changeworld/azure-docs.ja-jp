---
title: Azure Peering Service 接続を作成する - Azure portal
description: Azure portal を使用して Azure Peering Service を作成する方法について説明します
services: peering-service
author: gthareja
ms.service: peering-service
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 04/07/2021
ms.author: gatharej
ms.openlocfilehash: b75c0ec5be4ac26189a0e847a58cbe81461c5ff2
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111952462"
---
# <a name="create-peering-service-connection-using-the-azure-portal"></a>Azure portal を使用して Peering Service 接続を作成する

Azure Peering Service は、Microsoft 365、Dynamics 365、サービスとしてのソフトウェア (SaaS) サービス、Azure、パブリック インターネット経由でアクセス可能な Microsoft サービスなど、Microsoft パブリック クラウド サービスへのお客様の接続を強化するネットワーク サービスです。

この記事では、Azure portal を使用して Peering Service 接続を作成する方法について説明します。

Azure サブスクリプションをお持ちでない場合は、すぐに[アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

> 

## <a name="prerequisites"></a>前提条件

次のものが必要です。

### <a name="azure-account"></a>Azure アカウント

有効かつアクティブな Microsoft Azure アカウントが必要です。 Peering Service 接続を設定するには、このアカウントが必須です。 Peering Service 接続は、Azure サブスクリプション内のリソースです。 

### <a name="connectivity-provider"></a>接続プロバイダー

任意の [Azure Peering Service プロバイダー](./location-partners.md)と協力して Peering Service を取得することで、お使いのネットワークを最適な方法で Microsoft ネットワークに接続することができます。




## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

ブラウザーから Azure portal に移動し、Azure アカウントでサインインします。

## <a name="create-a-peering-service-connection"></a>Peering Service 接続を作成する

1. Peering Service 接続を作成するには、 **[リソースの作成]**  > **[Peering Service]** を選択します。

    ![Peering Service を作成する](./media/peering-service-portal/peering-servicecreate.png)

1. **[Create a peering service connection]\(Peering Service 接続の作成\)** ページの **[基本]** タブで、次の詳細を入力します。

 
1. サブスクリプションと、サブスクリプションに関連付けられているリソース グループを選択します。

   ![Peering 作成の基本タブ](./media/peering-service-portal/peering-servicebasics.png)

1. Peering Service インスタンスを登録する名前を **[名前]** に入力します。
 
1. 次に、ページの下部にある **[次へ: 構成]** ボタンを選択します。 **[構成]** ページが表示されます。

## <a name="configure-the-peering-service-connection"></a>Peering Service 接続を構成する

1. **[構成]** ページで、 **[Peering service location]\(ピアリング サービスの場所\)** ボックスの一覧から Peering Service を有効にするカスタマー ネットワークの場所を選択します。 

1. Peering Service を取得するサービス プロバイダーを選び、 **[Peering service provider]\(ピアリング サービス プロバイダー\)** ボックスの一覧からプロバイダー名を選択します。

1. カスタマー ネットワークの場所に最も近い、**プロバイダーのプライマリ ピアリングの場所** を選択します。 これは、Microsoft とパートナーの間のピアリング サービスの場所です。

1. カスタマー ネットワークの場所に次に最も近い、**プロバイダーのバックアップ ピアリングの場所** を選択します。 ピアリング サービスがバックアップの場所を介してアクティブになるのは、ディザスター リカバリーのためにプライマリ ピアリング サービスの場所で障害が発生した場合に限られます。 [なし] が選択された場合、プライマリ ピアリング サービスの場所に障害が発生した場合の既定のフェールオーバー ルートはインターネットになります。

 
1. **[プレフィックス]** セクションの下部にある **[Create new prefix]\(新しいプレフィックスを作成する\)** を選択すると、テキスト ボックスが表示されます。 ここで、サービス プロバイダーに関連付けられているプレフィックス リソースの名前とプレフィックスを入力します。

1. **[Prefix key]\(プレフィックス キー\)** を選択し、プロバイダー (ISP または IXP) から付与されているプレフィックス キーを追加します。 MS は、このキーを使用して、IP プレフィックスとこのプレフィックスを割り当てたプロバイダーを検証できます。
   > ![スクリーンショットは、[ピアリング サービス接続の作成] ページの [構成] タブを示しています。ここでプレフィック スキーを入力できます。](./media/peering-service-portal/peering-serviceconfiguration.png)

1. ページの左下にある **[Review + create]\(確認と作成\)** ボタンを選択します。 **[Review + create]\(確認と作成\)** ページが表示され、Azure によって構成が検証されます。
    

1. 示されているように、 **[検証に成功しました]** というメッセージが表示されたら、 **[作成]** を選択します。

   > ![スクリーンショットは、[ピアリング サービス接続の作成] ページの [確認と作成] タブを示しています。](./media/peering-service-portal/peering-service-prefix.png)


1. Peering Service 接続を作成すると、含まれているプレフィックスに対して追加の検証が実行されます。 検証の状態は、リソース名の **[プレフィックス]** セクションで確認できます。 検証が失敗した場合は、次のいずれかのエラー メッセージが表示されます。

   - Invalid Peering Service prefix, the prefix should be valid format, only IPv4 prefix is supported currently. (Peering Service のプレフィックスが無効です。プレフィックスは有効な形式である必要があります。現在 IPv4 プレフィックスのみがサポートされています。)
   - Prefix was not received from Peering Service provider, contact Peering Service provider. (Peering Service プロバイダーからプレフィックスを受信しませんでした。Peering Service プロバイダーにお問い合わせください。)
   - Prefix announcement does not have a valid BGP community, contact Peering Service provider. (プレフィックスのアナウンスに有効な BGP コミュニティが含まれていません。Peering Service プロバイダーにお問い合わせください。)
   - Prefix received with longer AS path(>3), contact Peering Service provider.(より長い AS パス (>3) を含むプレフィックスを受信しました。Peering Service プロバイダーにお問い合わせください。)
   - Prefix received with private AS in the path, contact Peering Service provider. (パスにプライベート AS を含むプレフィックスを受信しました、Peering Service プロバイダーにお問い合わせください。)

### <a name="add-or-remove-a-prefix"></a>プレフィックスを追加または削除する

プレフィックスを追加するには、 **[プレフィックス]** ページで **[プレフィックスの追加]** を選択します。

表示されているプレフィックスの横にある省略記号 ([...]) を選択し、 **[削除]** を選択します。

### <a name="delete-a-peering-service-connection"></a>Peering Service 接続を削除する

**[すべてのリソース]** ページで、Peering Service のチェック ボックスをオンにし、ページの上部にある **[削除]** を選択します。

> [!NOTE]
> 既存のプレフィックスを変更することはできません。
>

## <a name="next-steps"></a>次のステップ

- Peering Service 接続の詳細については、「[Peering Service 接続](connection.md)」を参照してください。
- Peering Service 接続のテレメトリの詳細については、「[Peering Service 接続のテレメトリ](connection-telemetry.md)」を参照してください。
- テレメトリを測定する方法については、[接続のテレメトリの測定](measure-connection-telemetry.md)に関するページを参照してください。
- Azure PowerShell を使用して Peering Service 接続を作成する方法については、[Azure PowerShell を使用した Peering Service 接続の作成](powershell.md)に関するページを参照してください。
- Azure CLI を使用して接続を作成する方法については、[Azure CLI を使用した Peering Service 接続の作成](cli.md)に関するページを参照してください。