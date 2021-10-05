---
title: Azure Service Bus 名前空間の移行 - Standard から Premium
description: 既存の Azure Service Bus Standard 名前空間を Premium に移行するためのガイドです
ms.topic: article
ms.date: 09/20/2021
ms.openlocfilehash: eea34edddf641e3ee1c07bea92b20364e7aeaf34
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128602136"
---
# <a name="migrate-existing-azure-service-bus-standard-namespaces-to-the-premium-tier"></a>既存の Azure Service Bus Standard 名前空間を Premium レベルに移行する

以前は、Azure Service Bus では Standard レベルでのみ名前空間が提供されていました。 名前空間は、低スループット環境と開発者環境用に最適化されたマルチテナントの設定です。 Premium レベルでは、名前空間ごとに専用のリソースが提供され、予測可能な待機時間と固定価格でのスループット向上が実現します。 Premium レベルは、追加のエンタープライズ機能が必要な高スループット環境および実稼働環境向けに最適化されています。

この記事では、既存の Standard レベルの名前空間を Premium レベルに移行する方法について説明します。  

>[!WARNING]
> 移行は、Service Bus の Standard 名前空間を Premium レベルにアップグレードするためのものです。 移行ツールでは、ダウングレードはサポートされていません。

いくつかの点に注意してください。

- この移行はインプレースで行われるよう設計されており、既存の送信側および受信側アプリケーションで **コードまたは構成の変更は必要ありません**。 既存の接続文字列は、新しい Premium 名前空間を自動的に指すようになります。
- 移行を成功させるためには、**Premium** 名前空間に **エンティティが含まれない** ようにしてください。
- Standard 名前空間のすべての **エンティティ** は、移行プロセスの間に Premium 名前空間に **コピー** されます。
- 移行では、Premium レベルで **メッセージング ユニットあたり 1,000 エンティティ** がサポートされます。 必要なメッセージング ユニットの数を明らかにするには、現在の Standard 名前空間に存在するエンティティの数から始めます。
- **Basic レベル** から **Premium レベル** に直接移行することはできませんが、まず Basic から Standard に移行し、次のステップで Standard から Premium に移行することによって間接的に移行できます。

## <a name="migration-steps"></a>移行の手順

いくつかの条件が移行プロセスに関連します。 エラーの可能性を減らすために、以下の手順をよく理解してください。 以下の手順は移行プロセスの概要であり、以降のセクションで詳細な手順について説明しています。

1. 新しい Premium 名前空間を作成します。
1. Standard 名前空間と Premium 名前空間を相互にペアにします。
1. Standard 名前空間から Premium 名前空間にエンティティを同期 (コピー) します。
1. 移行をコミットします。
1. 名前空間の移行後の名前を使用して、Standard 名前空間内のエンティティをドレインします。
1. Standard 名前空間を削除します。

>[!IMPORTANT]
> 移行をコミットした後、古い Standard 名前空間にアクセスして、キューとサブスクリプションをドレインします。 メッセージをドレインすると、メッセージを新しい Premium 名前空間に送信し、受信側アプリケーションで処理できるようになります。 キューとサブスクリプションをドレインした後は、古い Standard 名前空間を削除することをお勧めします。

### <a name="migrate-by-using-the-azure-cli-or-powershell"></a>Azure CLI または PowerShell を使用して移行する

Azure CLI または PowerShell ツールを使用して Service Bus の Standard 名前空間を Premium に移行するには、次の手順に従います。

1. 新しい Service Bus Premium 名前空間を作成します。 [Azure Resource Manager テンプレート](service-bus-resource-manager-namespace.md)に関する記事、または [Azure portal の使用](service-bus-create-namespace-portal.md)に関する記事をご覧ください。 **serviceBusSku** パラメーターに対して必ず **premium** を選択してください。

1. 次の環境変数を設定して、移行コマンドを簡略化します。

   ```
   resourceGroup = <resource group for the standard namespace>
   standardNamespace = <standard namespace to migrate>
   premiumNamespaceArmId = <Azure Resource Manager ID of the premium namespace to migrate to>
   postMigrationDnsName = <post migration DNS name entry to access the standard namespace>
   ```

    >[!IMPORTANT]
    > 移行後に古い Standard 名前空間にアクセスするには、移行後の別名/名前 (post_migration_dns_name) を使用します。 これを使用してキューとサブスクリプションをドレインした後、名前空間を削除します。

1. Standard 名前空間と Premium 名前空間をペアにし、次のコマンドを使用して同期を開始します。

    ```azurecli-interactive
    az servicebus migration start --resource-group $resourceGroup --name $standardNamespace --target-namespace $premiumNamespaceArmId --post-migration-name $postMigrationDnsName
    ```

1. 次のコマンドを使用して、移行の状態を確認します。

    ```azurecli-interactive
    az servicebus migration show --resource-group $resourceGroup --name $standardNamespace
    ```

    次の値が表示されたら、移行は完了したものとみなされます。

    * MigrationState = "Active"
    * pendingReplicationsOperationsCount = 0
    * provisioningState = "Succeeded"

    このコマンドでは、移行の構成も表示されます。 値が正しく設定されていることを確認します。 また、ポータルで Premium 名前空間を調べて、すべてのキューとトピックが作成されていること、およびそれらが Standard 名前空間に存在していたものと一致することを確認します。

1. 次の complete コマンドを実行して、移行をコミットします。

   ```azurecli-interactive
   az servicebus migration complete --resource-group $resourceGroup --name $standardNamespace
   ```

### <a name="migrate-by-using-the-azure-portal"></a>Azure portal を使用した移行

Azure portal を使用した移行の論理フローは、コマンドを使用した移行と同じです。 Azure portal を使用して移行するには、次の手順に従います。

1. 左側のウィンドウの **[ナビゲーション]** メニューで、 **[Premium へ移行]** を選択します。 **[作業を開始する]** ボタンをクリックして、次のページに進みます。
    :::image type="content" source="./media/service-bus-standard-premium-migration/migrate-premium-page.png" alt-text="[Premium へ移行] ページを示す画像。":::
1. 次の **[名前空間の設定]** ページが表示されます。

    :::image type="content" source="./media/service-bus-standard-premium-migration/setup-namespaces-page.png" alt-text="[名前空間の設定] ページを示す画像。":::
1. **[名前空間の設定]** ページで、次のいずれかの手順のようにします。 
    1. **[新しい Premium 名前空間を作成する]** を選択した場合:
        1. **[名前空間の作成]** ページで、名前空間の名前を入力して、 **[確認および作成]** を選択します。
        1. **[確認および作成]** ページで、 **[作成]** を選択します。

            :::image type="content" source="./media/service-bus-standard-premium-migration/create-premium-namespace.png" alt-text="[名前空間の作成] ページを示す画像。":::
    1. **[既存の空の Premium 名前空間を選択します]** を選択した場合:
        1. その名前空間を使用している Azure サブスクリプションとリソース グループを選択します。
        1. 次に、Premium 名前空間を選択します。
        1. **[選択]** をクリックします。
        
            :::image type="content" source="./media/service-bus-standard-premium-migration/select-existing-namespace.png" alt-text="既存の Premium 名前空間の選択を示す画像。":::
1. **[移行後の名前]** を入力してから、 **[次へ]** を選択します。 この名前は、移行が完了した後で Standard 名前空間にアクセスするために使用します。

    :::image type="content" source="./media/service-bus-standard-premium-migration/enter-post-migration-name.png" alt-text="Standard 名前空間の移行後の名前を示す画像。":::
1. **[同期の開始]** を選択し、Standard と Premium の名前空間の間でエンティティを同期します。

    :::image type="content" source="./media/service-bus-standard-premium-migration/start-sync-button.png" alt-text="[同期の開始] ボタンを示す画像。":::
1. ダイアログ ボックス内の **[はい]** を選択し、同期を確認して開始します。同期が完了するまで待ちます。 **[次へ]** を選択します。

    >[!IMPORTANT]
    > 何らかの理由で移行を中止する必要がある場合は、このドキュメントの FAQ セクションにある中止のフローを確認してください。    
1. **[切り替え]** ページで **[移行の完了]** を選択します。 

    :::image type="content" source="./media/service-bus-standard-premium-migration/complete-migration.png" alt-text="移行ウィザードの **[切り替え]** ページを示す画像。":::
1. **[はい]** を選択して、Standard 名前空間から Premium への切り替えを確認します。 切り替えが完了すると、Standard 名前空間の DNS 名が Premium 名前空間を指すようになります。 この操作は元に戻すことができません。 移行が完了すると、 **[成功]** ページが表示されます。

    :::image type="content" source="./media/service-bus-standard-premium-migration/success-page.png" alt-text="[成功] ページを示す画像。":::

## <a name="caveats"></a>注意事項

Azure Service Bus の Standard レベルで提供される機能の一部は、Azure Service Bus の Premium レベルではサポートされません。 Premium レベルでは予測可能なスループットと待機時間の専用リソースが提供されるため、これらは仕様です。

以下は、Premium とそれらのリスク軽減でサポートされない機能のリストです。

### <a name="express-entities"></a>エクスプレス エンティティ

   すべてのメッセージ データをストレージにコミットしないエクスプレス エンティティは、Premium ではサポートされません。 専用リソースでは、エンタープライズ メッセージング システムからの予想どおり、確実にデータを保持しながら、スループットが大幅に向上しました。

   移行中に、Standard 名前空間のいずれかのエクスプレス エンティティが、エクスプレス エンティティ以外のものとして Premium 名前空間で作成されます。

   Azure Resource Manager (ARM) テンプレートを利用する場合は、エラーが発生することなく自動ワークフローが実行されるように、必ず、デプロイ構成から 'enableExpress' フラグを削除するようにしてください。

### <a name="partitioned-entities"></a>パーティション分割されたエンティティ

   パーティション分割されたエンティティは、マルチテナント設定でより優れた可用性を提供するために、Standard レベルでサポートされていました。 Premium レベルでの名前空間ごとの利用可能な専用リソースのプロビジョニングでは、これは必要なくなりました。

   移行中に、Standard 名前空間のパーティション分割されたエンティティが、パーティション分割されていないエンティティとして Premium 名前空間に作成されます。

   ARM テンプレートで、特定のキューまたはトピックに対して 'enablePartitioning' が 'true' に設定された場合、それはブローカーによって無視されます。

## <a name="faqs"></a>FAQ

### <a name="what-happens-when-the-migration-is-committed"></a>移行をコミットするとどうなりますか?

移行をコミットした後、Standard 名前空間を指していた接続文字列は、Premium 名前空間を指すようになります。

送信側と受信側のアプリケーションは Standard 名前空間から切断され、Premium 名前空間に自動的に再接続されます。

### <a name="what-do-i-do-after-the-standard-to-premium-migration-is-complete"></a>Standard から Premium への移行が完了した後は、どうすればよいですか?

Standard から Premium への移行では、トピック、サブスクリプション、フィルターなどのエンティティ メタデータが Standard 名前空間から Premium 名前空間にコピーされることが保証されています。 Standard 名前空間にコミットされたメッセージ データは、Standard 名前空間から Premium 名前空間にコピーされません。

Standard 名前空間には、移行の進行中に送信されてコミットされたメッセージが存在する可能性があります。 これらのメッセージを Standard 名前空間から手動でドレインし、Premium 名前空間に手動で送信します。 メッセージを手動でドレインするには、移行コマンドで指定した移行後の DNS 名を使用して Standard 名前空間のエンティティをドレインするコンソール アプリまたはスクリプトを使用します。 これらのメッセージを Premium 名前空間に送信し、受信側で処理できるようにします。

メッセージがドレインされた後に、Standard 名前空間を削除します。

>[!IMPORTANT]
> Standard 名前空間からのメッセージがドレインされた後に、Standard 名前空間を削除します。 最初は Standard 名前空間を参照していた接続文字列が、Premium 名前空間を参照するようになっているため、これは重要なことです。 Standard 名前空間が必要になることはもうありません。 移行した Standard 名前空間を削除しておくと、後々の混乱を減らすために役立ちます。

### <a name="how-much-downtime-do-i-expect"></a>どの程度のダウンタイムが想定されますか?

移行プロセスは、アプリケーションに予想されるダウンタイムを短縮するためのものです。 送信側と受信側のアプリケーションが新しい Premium 名前空間を指すために使用する接続文字列を使用することによって、ダウンタイムが短縮されます。

アプリケーションで発生するダウンタイムは、Premium 名前空間を指すように DNS エントリを更新するのにかかる時間に限定されます。 ダウンタイムは約 5 分です。

### <a name="do-i-have-to-make-any-configuration-changes-while-doing-the-migration"></a>移行を行う間に構成を変更する必要はありますか?

いいえ、移行を行うために、コードまたは構成の変更は必要ありません。 送信側と受信側のアプリケーションが Standard 名前空間にアクセスするために使用する接続文字列は、Premium 名前空間に対する別名として機能するように自動的にマップされます。

### <a name="what-happens-when-i-abort-the-migration"></a>移行を中止するとどうなりますか?

移行は、`Abort` コマンドを使用して、または Azure portal を使用して中止することができます。

#### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az servicebus migration abort --resource-group $resourceGroup --name $standardNamespace
```

#### <a name="azure-portal"></a>Azure portal

**[エンティティの同期]** ページで **[中止]** を選択します。 

:::image type="content" source="./media/service-bus-standard-premium-migration/abort1.png" alt-text="[中止] ページを示す画像。":::

完了すると、次のページが表示されます。 

:::image type="content" source="./media/service-bus-standard-premium-migration/abort3.png" alt-text="中止完了ページを示す画像。":::

移行プロセスを中止すると、Standard 名前空間から Premium 名前空間にエンティティ (トピック、サブスクリプション、フィルター) をコピーするプロセスが中止され、ペアリングが解除されます。

接続文字列は、Premium 名前空間を指すように更新されません。 既存のアプリケーションは、移行を始める前と同様に機能し続けます。

ただし、Premium 名前空間のエンティティまたは Premium 名前空間が削除されることはありません。 移行を中止することにした場合は、エンティティを手動で削除してください。

>[!IMPORTANT]
> 移行を中止する場合は、移行用にプロビジョニングした Premium 名前空間を削除し、リソースに課金されないようにします。

#### <a name="i-dont-want-to-have-to-drain-the-messages-what-do-i-do"></a>メッセージのドレインを必要にしたくありません。 どうすればよいですか。

移行が行われている間の、移行がコミットされる直前に、送信側アプリケーションによって送信されて、Standard 名前空間のストレージにコミットされたメッセージが存在する可能性があります。

移行中、実際のメッセージ データ/ペイロードは Standard 名前空間から Premium 名前空間にコピーされません。 メッセージは、手動でドレインしてから Premium 名前空間に送信する必要があります。

ただし、計画的なメンテナンス/ハウスキープ処理期間中に移行でき、メッセージを手動でドレインして送信したくない場合は、次の手順に従います。

1. 送信側のアプリケーションを停止します。 受信側アプリケーションは、現在 Standard 名前空間にあるメッセージを処理してキューをドレインします。
1. Standard 名前空間のキューとサブスクリプションが空になった後、前に説明した手順に従い、Standard 名前空間から Premium 名前空間への移行を実行します。
1. 移行が完了した後、送信側アプリケーションを再起動できます。
1. 送信側と受信側は、Premium 名前空間と自動的に接続します。

    >[!NOTE]
    > 移行のために受信側アプリケーションを停止する必要はありません。
    >
    > 移行が完了した後、受信側アプリケーションは Standard 名前空間から切断され、Premium 名前空間に自動的に接続されます。

## <a name="next-steps"></a>次のステップ

* [Standard と Premium のメッセージングの違い](./service-bus-premium-messaging.md)についてさらに詳しく学習してください。
* [Service Bus Premium の高可用性と geo ディザスター リカバリーの側面](service-bus-outages-disasters.md#protecting-against-outages-and-disasters---service-bus-premium)について学びます。

