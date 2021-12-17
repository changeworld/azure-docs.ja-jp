---
title: Azure Key Vault と Azure Stack Edge リソースの統合とデバイスのアクティブ化
description: Azure Stack Edge Pro デバイスをアクティブ化するときに、Azure Key Vault がシークレットの管理にどのように関連するかを説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 09/08/2021
ms.author: alkohli
ms.openlocfilehash: 67cddd1839e666d4908706a1bbdaccbbd3565704
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129351438"
---
# <a name="manage-azure-stack-edge-secrets-using-azure-key-vault"></a>Azure Key Vault を使用して Azure Stack Edge シークレットを管理する 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Key Vault は、シークレット管理のために Azure Stack Edge リソースと統合されています。 この記事では、デバイスをアクティブ化するときに Azure Stack Edge リソース用に Azure キー コンテナーが作成されて、その後、シークレットの管理に使用される方法を詳しく説明します。 


## <a name="about-key-vault-and-azure-stack-edge"></a>Key Vault と Azure Stack Edge について

Azure Key Vault クラウド サービスは、トークン、パスワード、証明書、API キー、その他のシークレットを安全に格納し、それらへのアクセスを制御するために使用されます。 また、Key Vault により、データの暗号化に使用される暗号化キーの作成と制御が簡単になります。 

Azure Stack Edge サービスの場合、キー コンテナーとの統合には次の利点があります。

- 顧客シークレットを格納します。 Azure Stack Edge サービスに使用されるシークレットの 1 つは、チャネル整合性キー (CIK) です。 このキーを使用すると、シークレットを暗号化し、キー コンテナーに安全に格納できます。 BitLocker 回復キーやベースボード管理コントローラー (BMC) ユーザー パスワードなどのデバイス シークレットも、キー コンテナーに格納されます。 

    詳細については、「[シークレットとキーを安全に保存](../key-vault/general/overview.md#securely-store-secrets-and-keys)」を参照してください。
- 暗号化された顧客シークレットをデバイスに渡します。
- デバイスがダウンしている場合に、簡単にアクセスできるようにデバイス シークレットを表示します。


## <a name="generate-activation-key-and-create-key-vault"></a>アクティブ化キーを生成してキー コンテナーを作成する

アクティブ化キーの生成処理中に、Azure Stack Edge リソース用のキー コンテナーが作成されます。 キー コンテナーは、Azure Stack Edge リソースが存在するのと同じリソース グループ内に作成されます。 キー コンテナーに対する共同作成者のアクセス許可が必要です。 

### <a name="prerequisites-for-key-vault"></a>キー コンテナーの前提条件

アクティブ化中にキー コンテナーを作成する前に、次の前提条件を満たす必要があります。

- Azure Stack Edge リソースを作成する前に、*Microsoft.KeyVault* リソース プロバイダーを登録します。 サブスクリプションへの所有者または共同作成者のアクセス権をお持ちの場合、リソース プロバイダーの登録は自動的に行われます。 キー コンテナーは、Azure Stack Edge リソースと同じサブスクリプションとリソース グループに作成されます。 

- Azure Stack Edge リソースを作成すると、システム割り当てマネージド ID も作成されます。これはリソースの有効期間にわたって保持され、これとクラウド上のリソース プロバイダーとの間で通信が行われます。 

    マネージド ID が有効になると、Azure によって Azure Stack Edge リソース用の信頼できる ID が作成されます。

### <a name="key-vault-creation"></a>キー コンテナーの作成

リソースを作成したら、デバイスでリソースをアクティブ化する必要があります。 これを行うには、アクティブ化キーを Azure portal から生成します。 

アクティブ化キーを生成すると、次のイベントが発生します。 

![アクティブ化キー生成フロー](media/azure-stack-edge-gpu-activation-key-vault/activation-key-generation-flow-1.png)

- アクティブ化キーは、Azure portal で要求します。 その後、要求がキー コンテナー リソース プロバイダーに送信されます。 
- アクセス ポリシーを持つ Standard レベルのキー コンテナーが作成され、既定でロックされます。 
    - このキー コンテナーでは、指定した既定の名前または 3 から 24 文字の長いカスタム名が使用されます。 既に使用されているキー コンテナーを使用することはできません。 
    - キー コンテナーの詳細はサービスに格納されます。 このキー コンテナーは、シークレットの管理に使用され、Azure Stack Edge リソースが存在する限り保持されます。 

        ![アクティブ化キーの生成時に作成されたキー コンテナー](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-3.png)
- キー コンテナーでは、誤って削除されることを防ぐため、リソース ロックが有効になっています。 また、キー コンテナーでは論理的な削除が有効になっており、誤って削除された場合は 90 日以内にキー コンテナーを復元することができます。 詳細については、「[Azure Key Vault の論理的な削除の概要](../key-vault/general/soft-delete-overview.md)」を参照してください。
- Azure Stack Edge リソースの作成時に作成されたシステム割り当てマネージド ID が、ここで有効になります。
- チャネル整合性キー (CIK) が生成され、キー コンテナーに配置されます。 CIK の詳細が、サービスに表示されます。
- ゾーン冗長ストレージ アカウント (ZRS) も、Azure Stack Edge リソースと同じスコープで作成され、アカウントにロックが設定されます。 
    - このアカウントは、監査ログを格納するために使用されます。 
    - ストレージ アカウントの作成は長期プロセスであり、数分かかります。
    - ストレージ アカウントには、キー コンテナー名がタグ付けされます。
- 診断設定がキー コンテナーに追加され、ログ記録が有効になります。 
- デバイスがキー コンテナーを使用してシークレットを格納および取得すると、マネージド ID がキー コンテナー アクセス ポリシーに追加され、キー コンテナーへのアクセスが許可されます。 
- キー コンテナーは、マネージド ID を使用して要求を認証し、アクティブ化キーを生成します。 アクティブ化キーは、Azure portal に返されます。 その後、このキーをコピーし、ローカル UI で使用してデバイスをアクティブ化できます。

> [!NOTE]
> - Azure キー コンテナーと Azure Stack Edge リソースが統合される前に、既存の Azure Stack Edge リソースがあった場合は、影響を受けません。 既存の Azure Stack Edge リソースを引き続き使用できます。
> -  キー コンテナーとストレージ アカウントを作成すると、全体的なリソース コストは増加します。 許可されるトランザクションと対応する料金の詳細については、[Azure Key Vault の価格](https://azure.microsoft.com/pricing/details/key-vault/)と[ストレージ アカウントの価格](https://azure.microsoft.com/pricing/details/storage/blobs/)に関するページを参照してください。

キー コンテナーとデバイスのアクティブ化に関連する問題が発生した場合は、[デバイスのアクティブ化の問題のトラブルシューティング](azure-stack-edge-gpu-troubleshoot-activation.md)に関する記事をご覧ください。


## <a name="view-key-vault-properties"></a>キー コンテナーのプロパティを表示する

アクティブ化キーが生成され、キー コンテナーが作成された後、キー コンテナーにアクセスして、シークレット、アクセス ポリシー、診断、分析情報を表示することができます。 次の手順で、これらの各操作について説明します。

### <a name="view-secrets"></a>シークレットを表示する

アクティブ化キーが生成され、キー コンテナーが作成されたら、キー コンテナーにアクセスできます。 

キー コンテナーにアクセスしてシークレットを表示するには、次の手順に従います。

1. Azure portal で Azure Stack Edge リソースの **[セキュリティ]** にアクセスします。 
1. 右側のウィンドウの **[セキュリティ]** で、 **[シークレット]** を表示できます。 
1. Azure Stack Edge リソースに関連付けられているキー コンテナーに移動することもできます。 **[キー コンテナー名]** を選択します。 

    ![デバイス キー コンテナーに移動する](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-name-1.png)

1. キー コンテナーに格納されているシークレットを表示するには、 **[シークレット]** に移動します。 チャネル整合性キー、BitLocker 回復キー、ベースボード管理コントローラー (BMC) ユーザー パスワードが、キー コンテナーに格納されます。 デバイスがダウンした場合、ポータルは BitLocker 回復キーと BMC ユーザー パスワードに簡単にアクセスできる方法を提供します。
    
    ![キー コンテナーでデバイス シークレットを表示する](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-secrets-1.png)

### <a name="view-managed-identity-access-policies"></a>マネージド ID アクセス ポリシーを表示する

キー コンテナーとマネージド ID のアクセス ポリシーにアクセスするには、次の手順に従います。

1. Azure portal で Azure Stack Edge リソースの **[セキュリティ]** にアクセスします。 
1. **キー コンテナー名** に対応するリンクを選択して、Azure Stack Edge リソースに関連付けられているキー コンテナーに移動します。 

    ![デバイス キー コンテナーに移動する](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-name-1.png)

1. キー コンテナーに関連付けられているアクセス ポリシーを表示するには、 **[アクセス ポリシー]** に移動します。 マネージド ID にアクセス権が与えられたのを確認できます。 **[シークレットのアクセス許可]** を選択します。 マネージド ID アクセスがシークレットの **Get** と **Set** のみに制限されているのを確認できます。 
    
    ![キー コンテナーのアクセス ポリシーを表示する](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-access-policies-1.png)

    
### <a name="view-audit-logs"></a>監査ログの表示

キー コンテナーにアクセスし、診断設定と監査ログを表示するには、次の手順に従います。

1. Azure portal で Azure Stack Edge リソースの **[セキュリティ]** にアクセスします。 
1. **キー コンテナー名** に対応するリンクを選択して、Azure Stack Edge リソースに関連付けられているキー コンテナーに移動します。 

    ![デバイス キー コンテナーに移動する](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-name-1.png)

1. キー コンテナーに関連付けられている診断設定を表示するには、 **[診断設定]** に移動します。 この設定では、キー コンテナーがどのような方法で、いつ、だれによってアクセスされたかを監視できます。 診断設定が作成されているのを確認できます。 ログは、作成されたストレージ アカウントに流れ込みます。 監査イベントもキー コンテナーに作成されます。
    
    ![キー コンテナーの診断設定を表示する](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-diagnostics-settings-1.png)

キー コンテナー上のログ用に別のストレージ ターゲットを構成している場合は、そのストレージ アカウントでログを直接表示できます。

### <a name="view-insights"></a>分析情報の表示 

キー コンテナーに対して実行された操作を含むキー コンテナーの分析情報にアクセスするには、次の手順に従います。

1. Azure portal で Azure Stack Edge リソースの **[セキュリティ]** にアクセスします。 
1. **キー コンテナーの診断** に対応するリンクを選択します。 

    ![デバイス キー コンテナーに移動する](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-name-1.png)

1. **[分析情報]** ブレードには、キー コンテナーで実行される操作の概要が表示されます。

    ![キー コンテナーの分析情報を表示する](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-insights-1.png) 

### <a name="view-managed-identity-status"></a>マネージド ID 状態を表示する

Azure Stack Edge リソースに関連付けられているシステム割り当てマネージド ID の状態を表示するには、これらの手順に従います。

1. Azure portal で Azure Stack Edge リソースの **[セキュリティ]** にアクセスします。 
1. 右側のウィンドウで、**システム割り当てマネージド ID** に移動して、システム割り当てマネージド ID が有効になっているか無効になっているか確認します。

    ![デバイス キー コンテナーに移動する](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-name-1.png)

### <a name="view-key-vault-locks"></a>キー コンテナーのロックを表示する

キー コンテナーにアクセスしてロックを表示するには、次の手順に従います。

1. Azure portal で Azure Stack Edge リソースの **[セキュリティ]** にアクセスします。 
1. **キー コンテナー名** に対応するリンクを選択して、Azure Stack Edge リソースに関連付けられているキー コンテナーに移動します。 

    ![デバイス キー コンテナーに移動する](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-name-1.png)
1. キー コンテナーのロックを表示するには、 **[ロック]** に移動します。 誤って削除されないように、キー コンテナーではリソース ロックが有効になっています。 
    
    ![キー コンテナーのロックを表示する](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-locks-1.png)


## <a name="regenerate-activation-key"></a>アクティブ化キーを再生成する

特定のインスタンスでは、アクティブ化キーの再生成が必要な場合があります。 アクティブ化キーを再生成すると、次のイベントが発生します。

1. アクティブ化キーは、Azure portal で再生成を要求します。 
1. アクティブ化キーは、Azure portal に返されます。 その後、このキーをコピーして使用できます。 

アクティブ化キーを再生成するときに、キー コンテナーにはアクセスされません。 

## <a name="recover-device-secrets"></a>デバイス シークレットを回復する

CIK が誤って削除された場合、またはキー コンテナー内のシークレット (BMC ユーザー パスワードなど) が古くなった場合は、デバイスからシークレットをプッシュして、キー コンテナーのシークレットを更新する必要があります。 

デバイス シークレットを同期するには、次の手順に従います。  

1. Azure portal で Azure Stack Edge リソースに移動し、 **[セキュリティ]** に移動します。
1. 右側のウィンドウで、上部のコマンド バーから **[Sync device secrets]\(デバイス シークレットの同期\)** を選択します。
1. デバイス シークレットはキー コンテナーにプッシュされ、キー コンテナー内のシークレットが復元または更新されます。 同期が完了すると、通知が表示されます。

    ![キー コンテナーでデバイス シークレットを同期する](media/azure-stack-edge-gpu-activation-key-vault/sync-device-secrets-1.png)

## <a name="delete-key-vault"></a>キー コンテナーを削除する

Azure Stack Edge リソースに関連付けられているキー コンテナーを削除するには、次の 2 つの方法があります。

- Azure Stack Edge リソースを削除し、これに関連付けられているキー コンテナーを同時に削除することを選択します。
- キー コンテナーを誤って直接削除しました。

Azure Stack Edge リソースが削除されると、そのリソースと共にキー コンテナーも削除されます。 確認を求められます。 このキー コンテナーに他のキーを格納しており、このキー コンテナーを削除しない場合は、同意しないことを選択できます。 キー コンテナーはそのまま残り、Azure Stack Edge リソースのみが削除されます。 

次の手順に従って、Azure Stack Edge リソースと、これに関連付けられているキー コンテナーを削除します。

1. Azure portal で Azure Stack Edge リソースに移動し、 **[概要]** に移動します。
1. 右側のウィンドウで **[削除]** を選択します。 このアクションにより、Azure Stack Edge リソースが削除されます。

   ![Azure Stack Edge リソースと、これに関連付けられているキー コンテナーを削除する](media/azure-stack-edge-gpu-activation-key-vault/delete-azure-stack-edge-resource-1.png)  

1. 確認ブレードが表示されます。 Azure Stack Edge のリソース名を入力します。 関連付けられているキー コンテナーの削除を確認するには、「**はい**」と入力します。

    ![Azure Stack Edge リソースと、関連付けられているキー コンテナーの削除を確認する](media/azure-stack-edge-gpu-activation-key-vault/confirm-delete-azure-stack-edge-resource-1.png)   
1. **[削除]** を選択します。

Azure Stack Edge リソースとキー コンテナーが削除されます。

Azure Stack Edge リソースが使用中である場合、キー コンテナーが誤って削除される可能性があります。 このような場合は、Azure Stack Edge リソースの **[セキュリティ]** ページに、重大なアラートが発生します。 このページに移動して、キー コンテナーを回復できます。 


## <a name="recover-key-vault"></a>キー コンテナーを回復する

誤って削除または消去された場合は、Azure Stack Edge リソースに関連付けられているキー コンテナーを回復できます。 このキー コンテナーが、他のキーの格納に使用されていた場合は、キー コンテナーを復元してそれらのキーを回復する必要があります。

- 削除から 90 日以内は、削除されたキー コンテナーを復元できます。
- 消去保護期間の 90 日が既に経過している場合は、キー コンテナーを復元することはできません。 代わりに、新しいキー コンテナーを作成する必要があります。

削除から 90 日以内に、次の手順に従ってキー コンテナーを回復します。

- Azure portal で Azure Stack Edge リソースの **[セキュリティ]** ページに移動します。 リソースに関連付けられているキー コンテナーが削除された結果に対する通知が表示されます。 通知を選択するか、 **[セキュリティ設定]** の下にある **[再構成]** をキー コンテナー名に対して選択し、キー コンテナーを回復できます。

    ![セキュリティ ページに移動する](media/azure-stack-edge-gpu-activation-key-vault/security-page-1.png) 

- **[Recover key vault]\(キー コンテナーの回復\)** ブレードを選択し、 **[構成]** を選択します。 回復の一環として、次の操作が実行されます。  

    ![回復後の手順](media/azure-stack-edge-gpu-activation-key-vault/recover-key-vault-2.png) 

    - キー コンテナーは同じ名前で回復され、キー コンテナー リソースにロックが設定されます。 
    
        > [!NOTE]
        > キー コンテナーが削除され、90 日間の消去保護期間が経過していない場合、その期間にそのキー コンテナー名を使用して新しいキー コンテナーを作成することはできません。
    - 監査ログを格納するストレージ アカウントが作成されます。 
    - システム割り当てマネージド ID には、キー コンテナーへのアクセス権が付与されます。
    - デバイス シークレットは、キー コンテナーにプッシュされます。 
    
    **[構成]** をクリックします。 
 
    ![[Recover key vault]\(キー コンテナーの回復\) ブレード](media/azure-stack-edge-gpu-activation-key-vault/recover-key-vault-1.png)  

    キー コンテナーが回復され、回復が完了すると、その結果に通知が表示されます。

キー コンテナーが削除され、消去保護期間の 90 日が経過した場合は、前述の[キーの回復手順](#recover-key-vault)を使用して新しいキー コンテナーを作成できます。 この場合は、キー コンテナーに新しい名前を指定します。 新しいストレージ アカウントが作成され、マネージド ID にこのキー コンテナーへのアクセスが許可され、デバイス シークレットがこのキー コンテナーにプッシュされます。
  
<!--To recover the key vault using the follow these steps to [Recover your key vault](../key-vault/general/key-vault-recovery.md#list-recover-or-purge-soft-deleted-secrets-keys-and-certificates).-->

## <a name="recover-managed-identity-access"></a>マネージド ID アクセスを回復する

システム割り当てマネージド ID アクセス ポリシーが削除された場合、デバイスがキー コンテナーのシークレットを再同期できないときにアラートが発生します。 マネージド ID がキー コンテナーにアクセス権を持っていない場合、デバイス アラートが再度発生します。 各ケースでアラートを選択して **[Recover key vault]\(キー コンテナーの回復\)** ブレードを開き、再構成します。 このプロセスでは、マネージド ID アクセス権を復元する必要があります。 

![マネージド ID にキー コンテナーへのアクセス権を付与するフロー](media/azure-stack-edge-gpu-activation-key-vault/activation-key-generation-flow-2.png)


## <a name="next-steps"></a>次のステップ

- [アクティブ化キーの生成](azure-stack-edge-gpu-deploy-prep.md#get-the-activation-key)方法に関する詳細をご覧ください。
- Azure Stack Edge デバイスで[キー コンテナー エラーのトラブルシューティング](azure-stack-edge-gpu-troubleshoot-activation.md)を行います。
