---
title: Azure Kubernetes Service に関する一般的な問題のトラブルシューティング
description: Azure Kubernetes Service (AKS) を使用するときに発生する一般的な問題をトラブルシューティングおよび解決する方法について説明します
services: container-service
author: sauryadas
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: saudas
ms.openlocfilehash: 8460f4f2a66a1f545bea767cccf3aa77c9d3bff3
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82778959"
---
# <a name="aks-troubleshooting"></a>AKS のトラブルシューティング

Azure Kubernetes Service (AKS) クラスターを作成または管理するときに、問題が発生することがあります。 この記事では、よくある問題とトラブルシューティングの手順について詳しく説明します。

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>通常、Kubernetes の問題のデバッグに関する情報はどこにありますか。

[Kubernetes クラスターのトラブルシューティングに関する公式ガイド](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/)をご覧ください。
Microsoft のエンジニアによって公開された、ポッド、ノード、クラスター、他の機能のトラブルシューティングに関する[トラブルシューティング ガイド](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md)もあります。

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>作成またはアップグレード中に、"クォータ超過" エラーが発生します。 どうすればよいですか。 

[コアを要求する](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)必要があります。

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>AKS におけるノードあたりの最大ポッド数はいくつに設定されていますか。

Azure portal で AKS クラスターをデプロイする場合、ノードあたりの最大ポッド数は既定で 30 に設定されます。
Azure CLI で AKS クラスターをデプロイする場合、ノードあたりの最大ポッド数は既定で 110 に設定されます。 (最新バージョンの Azure CLI を使用していることを確認してください)。 この既定の設定は、`az aks create` コマンドで `–-max-pods` フラグを使用して変更することができます。

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>高度なネットワークで AKS クラスターをデプロイしているときに、insufficientSubnetSize エラーが発生します。 どうすればよいですか。

Azure CNI (高度なネットワーク) を使用した場合、構成された 1 ノードあたりの "最大ポッド数" に基づいて、AKS によって IP アドレスが割り当てられます。 ノードあたりの構成された最大ポッド数に基づくと、サブネットのサイズは、ノード数とノード セットあたりの最大ポッド数の積より大きくする必要があります。 次の式は、その概要を示します。

サブネットのサイズ > クラスター内のノードの数 (今後のスケーリングの要件を考慮して) * ノード セットあたりの最大ポッド数。

詳しくは、「[クラスターの IP アドレス指定を計画する](configure-azure-cni.md#plan-ip-addressing-for-your-cluster)」を参照してください。

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>ポッドが CrashLoopBackOff モードでスタックします。 どうすればよいですか。

このモードでポッドがスタックする理由は複数あります。 以下を確認します。

* ポッド自体。`kubectl describe pod <pod-name>` を使用します。
* ログ。`kubectl logs <pod-name>` を使用します。

ポッドの問題のトラブルシューティング方法について詳しくは、「[Debug applications (アプリケーションをデバッグする)](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods)」をご覧ください。

## <a name="im-trying-to-enable-rbac-on-an-existing-cluster-how-can-i-do-that"></a>既存のクラスターで RBAC を有効にしようとしています。 どうすればいいですか。

残念ながら、現時点では、既存のクラスターでロールベースのアクセス制御 (RBAC) を有効にすることはできません。 新しいクラスターを明示的に作成する必要があります。 CLI を使用する場合、RBAC は既定で有効になります。 AKS ポータルを使用する場合は、RBAC を有効にするトグル ボタンを作成ワークフローで使用できます。

## <a name="i-created-a-cluster-with-rbac-enabled-by-using-either-the-azure-cli-with-defaults-or-the-azure-portal-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>Azure CLI で既定の設定を使用して、または Azure portal を使用して、RBAC を有効にしたクラスターを作成しましたが、Kubernetes ダッシュボードに多数の警告が表示されます。 以前ダッシュボードは警告なしで動作していました。 どうすればよいですか。

ダッシュボードに警告が表示されるのは、クラスターで RBAC が有効になっており、クラスターへのアクセスが既定で無効になっているためです。 ダッシュボードを、クラスターのすべてのユーザーに既定で公開すると、セキュリティの脅威につながる可能性があるため、一般的にこのアプローチは適切な方法です。 それでもダッシュボードを有効にする場合は、[こちらのブログ投稿](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/)の手順に従ってください。

## <a name="i-cant-connect-to-the-dashboard-what-should-i-do"></a>ダッシュボードに接続できません。 どうすればよいですか。

クラスター外にあるサービスにアクセスする最も簡単な方法は、`kubectl proxy` を実行することです。これにより、自分の localhost ポート 8001 に送信された要求が Kubernetes API サーバーにプロキシされます。 そこからは、API サーバーがユーザーのサービス `http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/` にプロキシできます。

Kubernetes ダッシュボードが表示されない場合は、`kube-proxy` ポッドが `kube-system` 名前空間で実行されているかどうか確認します。 実行状態でない場合は、ポッドを削除します。これにより再起動されます。

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>kubectl logs を使用してログを取得できません。または、API サーバーに接続できません。 "Error from server: error dialing backend: dial tcp…" (サーバーからのエラー: バックエンドへのダイヤルでのエラー: tcp にダイヤル...) と表示されます。 どうすればよいですか。

既定のネットワーク セキュリティ グループが変更されていないこと、および API サーバーへの接続用にポート 22 とポート 9000 の両方が開放されていることを確認します。 `kubectl get pods --namespace kube-system` コマンドを使用して、`tunnelfront` ポッドが *kube-system* 名前空間で実行されているかどうかを確認します。 そうでない場合は、ポッドを強制的に削除すると、再起動されます。

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-message-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>アップグレードまたはスケーリングを行おうとすると、"メッセージ:プロパティ 'imageReference' は変更できませ" というエラーが発生します。 この問題を解決するにはどうすればよいですか。

AKS クラスター内のエージェント ノードのタグを変更したことが原因で、このエラーが発生している可能性があります。 MC_* リソース グループのリソースのタグやその他のプロパティを変更または削除すると、予期しない結果につながる可能性があります。 AKS クラスターの MC_* グループでリソースを変更すると、サービス レベル目標 (SLO) が中断されます。

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>クラスターがエラー状態であり、状態が修正されるまで、アップグレードもスケーリングも機能しないというエラーが表示されます。

*このトラブルシューティングの支援は、 https://aka.ms/aks-cluster-failed に基づいています。*

このエラーは、複数の理由でクラスターがエラー状態になったときに発生します。 以前に失敗した操作を再試行する前に、次の手順に従ってクラスターのエラー状態を解決してください。

1. クラスターが `failed` 状態から回復するまで、`upgrade` 操作と `scale` 操作は成功しません。 一般的な根本問題と解決策は次のとおりです。
    * **計算 (CRP) クォータが不足**している状態でのスケーリング。 これを解決するには、まず、クォータの範囲内で安定した目標状態にクラスターをスケールバックします。 次に、最初のクォータ制限を超えて再度スケールアップを試みる前に、[こちらの手順](../azure-portal/supportability/resource-manager-core-quotas-request.md)に従って計算クォータの引き上げを依頼します。
    * 高度なネットワーク リソースと**不十分なサブネット (ネットワーク) リソース**を使用したクラスターのスケーリング。 これを解決するには、まず、クォータの範囲内で安定した目標状態にクラスターをスケールバックします。 次に、最初のクォータ制限を超えて再度スケールアップを試みる前に、[こちらの手順](../azure-resource-manager/templates/error-resource-quota.md#solution)に従ってリソース クォータの引き上げを依頼します。
2. アップグレードの失敗の根本原因が解決されると、クラスターは成功状態になるはずです。 成功状態が確認されたら、元の操作を再試行します。

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-currently-being-upgraded-or-has-failed-upgrade"></a>クラスターをアップグレードまたはスケーリングしようとしたときに、クラスターが現在アップグレード中であるか、アップグレードに失敗したというエラーが表示されます。

*このトラブルシューティングの支援は、 https://aka.ms/aks-pending-upgrade に基づいています。*

1 つのノード プールを持つクラスターまたは[複数のノード プール](use-multiple-node-pools.md)を持つクラスターでのアップグレード操作とスケール操作は、相互に排他的です。 クラスターまたはノード プールを同時にアップグレードやスケーリングすることはできません。 代わりに、ターゲット リソースに対する次の要求を行う前に、各操作の種類をその同じリソースで完了する必要があります。 その結果、アクティブなアップグレード操作またはスケール操作の実行中、または操作が試行されたが失敗した場合、操作は制限されます。 

問題の診断に役立てるには、`az aks show -g myResourceGroup -n myAKSCluster -o table` を実行してクラスターの詳細な状態を取得します。 結果に基づいて、次のことを行います。

* クラスターのアップグレードを実行中の場合は、操作が終了するまで待ちます。 成功した場合は、もう一度、以前に失敗した操作を再試行します。
* クラスターのアップグレードに失敗した場合は、上記のセクションで概説された手順に従います。

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>自分のクラスターを別のサブスクリプションに移したり、自分のクラスターが含まれる自分のサブスクリプションを新しいテナントに移したりできますか?

AKS クラスターを別のサブスクリプションに移したり、サブスクリプションを所有するクラスターを新しいテナントに移したりした場合は、ロールの割り当てやサービス プリンシパルの権限が失われるため、クラスターが機能を失います。 この制約により、**AKS はサブスクリプションまたはテナント間でのクラスターの移動をサポートしていません**。

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>仮想マシン スケール セットを必要とする機能を使用しようとするとエラーが発生します

*このトラブルシューティングの支援は、aka.ms/aks-vmss-enablement に基づいています*

次の例など、AKS クラスターが仮想マシン スケール セット上にないことを示すエラーが表示されます。

**AgentPool 'agentpool' は自動スケールが有効に設定されていますが、Microsoft Azure Virtual Machine Scale Sets 上にありません**

クラスター オートスケーラーや複数ノード プールなどの機能を使用するには、仮想マシン スケール セットを使用する AKS クラスターを作成する必要があります。 仮想マシン スケール セットに依存する機能を使用しようとして、通常の非仮想マシン スケール セットの AKS クラスターを対象とする場合、エラーが返されます。

適切なドキュメントの「*開始する前に*」のステップに従い、AKS クラスターを正しく作成します。

* [クラスター オートスケーラーを使用する](cluster-autoscaler.md)
* [複数のノード プールを作成し使用する](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>AKS リソースおよびパラメーターにはどのような名前付けの制約が適用されますか。

*このトラブルシューティングの支援は、aka.ms/aks-naming-rules に基づいています*

名前付けの制約は、Azure プラットフォームと AKS の両方によって実装されます。 リソース名またはパラメーターがこれらのいずれかの制約に違反した場合、別の入力を行うように求めるエラーが返されます。 次の一般的な名前付けのガイドラインが適用されます。

* クラスター名は 1 ～ 63 文字にする必要があります。 使用できる文字は、文字、数字、ダッシュ、およびアンダースコアのみです。 先頭と末尾の文字は、文字または数字にしてください。
* AKS *MC_* リソース グループ名は、リソース グループ名とリソース名を結合します。 `MC_resourceGroupName_resourceName_AzureRegion` の自動生成された構文は、80 文字以内にする必要があります。 必要な場合は、リソース グループ名または AKS クラスター名の長さを短くします。
* *dnsPrefix* の最初と最後は英数字の値にする必要があり、1 から 54 文字の間にする必要があります。 有効な文字には英数字の値とハイフン (-) が含まれます。 *dnsPrefix* にはピリオド (.) などの特殊文字を含めることはできません。

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>クラスターを作成、更新、スケーリング、削除、またはアップグレードしようとすると、別の操作が進行中のためその操作は許可されませんというエラーを受け取ります。

*このトラブルシューティングの支援は、aka.ms/aks-pending-operation に基づいています*

クラスター操作は、前の操作がまだ進行中のときには制限されます。 クラスターの詳細な状態を取得するには、`az aks show -g myResourceGroup -n myAKSCluster -o table` コマンドを使用します。 必要に応じて、独自のリソース グループと AKS クラスター名を使用します。

次のクラスター状態の出力に基づきます。

* クラスターが、 *[成功]* または *[失敗]* 以外のプロビジョニング状態の場合は、操作 (*アップグレード / 更新 / 作成 / スケーリング / 削除 / 移行*) が終了するまで待ってください。 前の操作が完了したら、最新のクラスターの操作を再試行します。

* クラスターでアップグレードが失敗している場合は、 「[クラスターがエラー状態であり、状態が修正されるまで、アップグレードもスケーリングも機能しないというエラーが表示されます](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed)」で概説されている手順に従ってください。

## <a name="im-receiving-errors-that-my-service-principal-was-not-found-when-i-try-to-create-a-new-cluster-without-passing-in-an-existing-one"></a>既存のクラスターを渡さずに新しいクラスターを作成しようとすると、サービス プリンシパルが見つからなかったというエラーが発生します。

AKS クラスターの作成時には、ユーザーに代わってリソースを作成するためのサービス プリンシパルが必要です。 AKS は、クラスターの作成時に新しいクラスターを作成する機能を提供しますが、クラスターの作成を成功させるには、Azure Active Directory で新しいサービス プリンシパルを適切な時間内に完全に伝達する必要があります。 この伝達に時間がかかりすぎると、クラスターは使用可能なサービス プリンシパルを見つけることができないため、作成の検証に失敗します。 

これについては次の回避策を使用してください。
1. リージョン間で既に伝達されていて、クラスターの作成時に AKS に渡すために存在する既存のサービス プリンシパルを使用します。
2. 自動化スクリプトを使用する場合は、サービス プリンシパルの作成と AKS クラスターの作成の間に遅延時間を追加します。
3. Azure portal を使用する場合は、作成中にクラスター設定に戻り、数分後に検証ページを再試行します。

## <a name="im-receiving-errors-after-restricting-my-egress-traffic"></a>エグレス トラフィックを制限した後、エラーが表示されました

AKS クラスターからのエグレス トラフィックを制限するときには、AKS 向けの[必須および任意の推奨される](limit-egress-traffic.md)送信ポート、ネットワーク規則と FQDN、アプリケーション規則があります。 ご利用の設定がこのような規則と競合するとき、一部の `kubectl` コマンドを実行できないことがあります。 また、AKS クラスターの作成時にエラーが表示されることがあります。

必須または任意の推奨される送信ポート、ネットワーク規則と FQDN、およびアプリケーション規則のいずれにもご利用の設定が競合していないことを確認します。

## <a name="azure-storage-and-aks-troubleshooting"></a>Azure Storage および ASK のトラブルシューティング

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-disk"></a>推奨される Kubernetes for Azure Disk の安定したバージョンは何ですか。 

| Kubernetes バージョン | 推奨されるバージョン |
| -- | :--: |
| 1.12 | 1.12.9 以上 |
| 1.13 | 1.13.6 以上 |
| 1.14 | 1.14.2 以上 |


### <a name="what-versions-of-kubernetes-have-azure-disk-support-on-the-sovereign-cloud"></a>ソブリン クラウドで Azure Disk をサポートしている Kubernetes のバージョンは何ですか。

| Kubernetes バージョン | 推奨されるバージョン |
| -- | :--: |
| 1.12 | 1.12.0 以上 |
| 1.13 | 1.13.0 以上 |
| 1.14 | 1.14.0 以上 |


### <a name="waitforattach-failed-for-azure-disk-parsing-devdiskazurescsi1lun1-invalid-syntax"></a>Azure Disk の WaitForAttach に失敗しました: "/dev/disk/azure/scsi1/lun1" を解析しています: 構文が無効です

Kubernetes バージョン1.10 では、Azure Disk の再マウントによって MountVolume が失敗する場合があります。

Linux では、正しくない DevicePath フォーマット エラーが表示されることがあります。 次に例を示します。

```console
MountVolume.WaitForAttach failed for volume "pvc-f1562ecb-3e5f-11e8-ab6b-000d3af9f967" : azureDisk - Wait for attach expect device path as a lun number, instead got: /dev/disk/azure/scsi1/lun1 (strconv.Atoi: parsing "/dev/disk/azure/scsi1/lun1": invalid syntax)
  Warning  FailedMount             1m (x10 over 21m)   kubelet, k8s-agentpool-66825246-0  Unable to mount volumes for pod
```

Windows では、間違った DevicePath (LUN) 番号のエラーが表示されることがあります。 次に例を示します。

```console
Warning  FailedMount             1m    kubelet, 15282k8s9010    MountVolume.WaitForAttach failed for volume "disk01" : azureDisk - WaitForAttach failed within timeout node (15282k8s9010) diskId:(andy-mghyb
1102-dynamic-pvc-6c526c51-4a18-11e8-ab5c-000d3af7b38e) lun:(4)
```

この問題は、次のバージョンの Kubernetes で修正されました。

| Kubernetes バージョン | 修正済みのバージョン |
| -- | :--: |
| 1.10 | 1.10.2 以上 |
| 1.11 | 1.11.0 以上 |
| 1.12 以上 | 該当なし |

### <a name="failure-when-setting-uid-and-gid-in-mountoptions-for-azure-disk"></a>Azure Disk の MountOptions で UID と GID を設定するときにエラーが発生する

既定では、Azure ディスクは ext4、xfs ファイル システムを使用します。uid=x、gid=x などの mountOptions はマウント時に設定できません。 たとえば、mountOptions uid=999、gid=999 を設定しようとすると、次のようなエラーが表示されます。

```console
Warning  FailedMount             63s                  kubelet, aks-nodepool1-29460110-0  MountVolume.MountDevice failed for volume "pvc-d783d0e4-85a1-11e9-8a90-369885447933" : azureDisk - mountDevice:FormatAndMount failed with mount failed: exit status 32
Mounting command: systemd-run
Mounting arguments: --description=Kubernetes transient mount for /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985 --scope -- mount -t xfs -o dir_mode=0777,file_mode=0777,uid=1000,gid=1000,defaults /dev/disk/azure/scsi1/lun2 /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985
Output: Running scope as unit run-rb21966413ab449b3a242ae9b0fbc9398.scope.
mount: wrong fs type, bad option, bad superblock on /dev/sde,
       missing codepage or helper program, or other error
```

問題を軽減するには、次のいずれかの操作を行います。

* fsGroup で GID、runAsUser で UID を設定して、[ポッドのセキュリティ コンテキストを構成](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/)します。 たとえば、次の設定ではポッドを root として実行するよう設定し、任意のファイルからアクセスできるようにします。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  securityContext:
    runAsUser: 0
    fsGroup: 0
```

  >[!NOTE]
  > 既定では、GID と UID は root または 0 としてマウントされるためです。 GID または UID が非 root (1000 など) として設定されている場合、Kubernetes は `chown` を使用して、そのディスクにあるすべてのディレクトリとファイルを変更します。 この操作には時間がかかることがあり、ディスクのマウントが非常に遅くなる可能性があります。

* initContainers で `chown` を使用して、GID と UID を設定します。 次に例を示します。

```yaml
initContainers:
- name: volume-mount
  image: busybox
  command: ["sh", "-c", "chown -R 100:100 /data"]
  volumeMounts:
  - name: <your data volume>
    mountPath: /data
```

### <a name="error-when-deleting-azure-disk-persistentvolumeclaim-in-use-by-a-pod"></a>ポッドによって使用されている Azure Disk PersistentVolumeClaim の削除により発生したエラー

ポッドによって使用されている Azure Disk PersistentVolumeClaim を削除しようとすると、エラーが表示される場合があります。 次に例を示します。

```console
$ kubectl describe pv pvc-d8eebc1d-74d3-11e8-902b-e22b71bb1c06
...
Message:         disk.DisksClient#Delete: Failure responding to request: StatusCode=409 -- Original Error: autorest/azure: Service returned an error. Status=409 Code="OperationNotAllowed" Message="Disk kubernetes-dynamic-pvc-d8eebc1d-74d3-11e8-902b-e22b71bb1c06 is attached to VM /subscriptions/{subs-id}/resourceGroups/MC_markito-aks-pvc_markito-aks-pvc_westus/providers/Microsoft.Compute/virtualMachines/aks-agentpool-25259074-0."
```

Kubernetes バージョン 1.10 以降では、このエラーを防ぐために既定で PersistentVolumeClaim 保護機能が有効になっています。 この問題の修正プログラムがインストールされていないバージョンの Kubernetes を使用している場合は、PersistentVolumeClaim を削除する前に PersistentVolumeClaim を使用してポッドを削除することで、この問題を軽減できます。


### <a name="error-cannot-find-lun-for-disk-when-attaching-a-disk-to-a-node"></a>ディスクをノードに接続すると、"Cannot find Lun for disk" (ディスクの Lun が見つかりませんでした) というエラーが発生する

ディスクをノードに接続すると、次のエラーが表示される場合があります。

```console
MountVolume.WaitForAttach failed for volume "pvc-12b458f4-c23f-11e8-8d27-46799c22b7c6" : Cannot find Lun for disk kubernetes-dynamic-pvc-12b458f4-c23f-11e8-8d27-46799c22b7c6
```

この問題は、次のバージョンの Kubernetes で修正されました。

| Kubernetes バージョン | 修正済みのバージョン |
| -- | :--: |
| 1.10 | 1.10.10 以上 |
| 1.11 | 1.11.5 以上 |
| 1.12 | 1.12.3 以上 |
| 1.13 | 1.13.0 以上 |
| 1.14 以上 | 該当なし |

この問題の修正プログラムがインストールされていないバージョンの Kubernetes を使用している場合は、数分間待ってから再試行することで、問題を軽減できます。

### <a name="azure-disk-attachdetach-failure-mount-issues-or-io-errors-during-multiple-attachdetach-operations"></a>Azure ディスクのアタッチ/デタッチの失敗、マウントの問題、または複数のアタッチ/デタッチ操作中の I/O エラー

Kubernetes バージョン 1.9.2 以降では、複数のアタッチ/デタッチ操作を並行して実行すると、VM キャッシュがダーティであるため、次のディスクの問題が発生することがあります。

* Diskのアタッチ/デタッチの失敗
* Diskの I/O エラー
* 予期しない VM からのディスクのデタッチ
* 存在しないディスクのアタッチによる VM のエラー状態

この問題は、次のバージョンの Kubernetes で修正されました。

| Kubernetes バージョン | 修正済みのバージョン |
| -- | :--: |
| 1.10 | 1.10.12 以上 |
| 1.11 | 1.11.6 以上 |
| 1.12 | 1.12.4 以上 |
| 1.13 | 1.13.0 以上 |
| 1.14 以上 | 該当なし |

この問題の修正プログラムがインストールされていないバージョンの Kubernetes を使用している場合は、次のようにして問題を軽減できます。

* 長時間デタッチを待機しているディスクがある場合は、ディスクを手動でデタッチしてください。

### <a name="azure-disk-waiting-to-detach-indefinitely"></a>デタッチを無期限で待機している Azure Disk

一部のケースでは、Azure Disk のデタッチ操作が最初の試行で失敗した場合、デタッチ操作は再試行されず、元のノード VM にアタッチされたままになります。 このエラーは、ディスクを 1 つのノードから別のノードに移動するときに発生する可能性があります。 次に例を示します。

```console
[Warning] AttachVolume.Attach failed for volume "pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9" : Attach volume "kubernetes-dynamic-pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9" to instance "/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/virtualMachines/aks-agentpool-57634498-0" failed with compute.VirtualMachinesClient#CreateOrUpdate: Failure sending request: StatusCode=0 -- Original Error: autorest/azure: Service returned an error. Status= Code="ConflictingUserInput" Message="Disk '/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/disks/kubernetes-dynamic-pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9' cannot be attached as the disk is already owned by VM '/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/virtualMachines/aks-agentpool-57634498-1'."
```

この問題は、次のバージョンの Kubernetes で修正されました。

| Kubernetes バージョン | 修正済みのバージョン |
| -- | :--: |
| 1.11 | 1.11.9 以上 |
| 1.12 | 1.12.7 以上 |
| 1.13 | 1.13.4 以上 |
| 1.14 以上 | 該当なし |

この問題の修正プログラムがインストールされていないバージョンの Kubernetes を使用している場合は、ディスクを手動でデタッチしてください。

### <a name="azure-disk-detach-failure-leading-to-potential-race-condition-issue-and-invalid-data-disk-list"></a>競合状態の問題および無効なデータ ディスク リストにつながるAzure ディスクのデタッチの失敗

Azure Disk のデタッチに失敗すると、エクスポネンシャル バックオフを使用してディスクのデタッチを最大 6 回再試行します。 また、データ ディスク リストにノード レベルのロックを約 3 分間保持します。 手動によるアタッチやデタッチ操作など、この期間中にディスク リストを手動で更新すると、ノード レベルのロックによって保持されていたディスクの一覧が互換性のために残され、ノード VM が不安定になることがあります。

この問題は、次のバージョンの Kubernetes で修正されました。

| Kubernetes バージョン | 修正済みのバージョン |
| -- | :--: |
| 1.12 | 1.12.9 以上 |
| 1.13 | 1.13.6 以上 |
| 1.14 | 1.14.2 以上 |
| 1.15 以上 | 該当なし |

この問題の修正プログラムがインストールされていないバージョンの Kubernetes を使用し、かつノード VM のディスク リストが古くなっている場合は、単一の一括操作として VM からすべての存在しないディスクを切断することで問題を軽減できます。 **存在しないディスクを個別にデタッチすると失敗する場合があります。**


### <a name="large-number-of-azure-disks-causes-slow-attachdetach"></a>多数の Azure ディスクによってアタッチ/デタッチが遅くなる

ノード VM に接続されている Azure ディスクの数が 10 を超えると、アタッチとデタッチの操作が遅くなる場合があります。 この問題は既知の問題であり、現時点では回避策はありません。

### <a name="azure-disk-detach-failure-leading-to-potential-node-vm-in-failed-state"></a>エラー状態のノード VM につながる Azure Disk デタッチ エラー

一部のエッジ ケースでは、Azure Disk のデタッチが部分的に失敗し、ノード VM がエラー状態のままになる場合があります。

この問題は、次のバージョンの Kubernetes で修正されました。

| Kubernetes バージョン | 修正済みのバージョン |
| -- | :--: |
| 1.12 | 1.12.10 以上 |
| 1.13 | 1.13.8 以上 |
| 1.14 | 1.14.4 以上 |
| 1.15 以上 | 該当なし |

この問題の修正プログラムがインストールされていないバージョンの Kubernetes を使用し、かつノード VM がエラー状態である場合は、次のいずれかを使用して VM の状態を手動で更新することで、この問題を軽減できます。

* 可用性セットベースのクラスターの場合:
    ```azurecli
    az vm update -n <VM_NAME> -g <RESOURCE_GROUP_NAME>
    ```

* VMSS ベースのクラスターの場合:
    ```azurecli
    az vmss update-instances -g <RESOURCE_GROUP_NAME> --name <VMSS_NAME> --instance-id <ID>
    ```

## <a name="azure-files-and-aks-troubleshooting"></a>Azure Files および AKS のトラブルシューティング

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-files"></a>推奨される Kubernetes for Azure File の安定したバージョンは何ですか。
 
| Kubernetes バージョン | 推奨されるバージョン |
| -- | :--: |
| 1.12 | 1.12.6 以上 |
| 1.13 | 1.13.4 以上 |
| 1.14 | 1.14.0 以上 |

### <a name="what-versions-of-kubernetes-have-azure-files-support-on-the-sovereign-cloud"></a>ソブリン クラウドで Azure Files をサポートしている Kubernetes のバージョンは何ですか。

| Kubernetes バージョン | 推奨されるバージョン |
| -- | :--: |
| 1.12 | 1.12.0 以上 |
| 1.13 | 1.13.0 以上 |
| 1.14 | 1.14.0 以上 |

### <a name="what-are-the-default-mountoptions-when-using-azure-files"></a>Azure Files を使用する場合の既定の mountOptions は何ですか。

推奨設定

| Kubernetes バージョン | fileMode および dirMode の値|
| -- | :--: |
| 1.12.0 - 1.12.1 | 0755 |
| 1.12.2 以上 | 0777 |

Kuberetes バージョン 1.8.5 以降のクラスターを使い、ストレージ クラスを使用して永続ボリュームを動的に作成している場合は、ストレージ クラスのオブジェクトに対してマウント オプションを指定できます。 次の例では、*0777* が設定されます。

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
  - cache=none
parameters:
  skuName: Standard_LRS
```

その他の便利な *mountOptions* 設定を次に示します。

* *mfsymlinks* は、Azure Files mount (cifs) をサポートするシンボリック リンクを作成します
* *nobrl* は、サーバーにバイト範囲のロック要求が送信されるのを防止します。 この設定は、cifs 形式の必須のバイト範囲のロックにより中断する特定のアプリケーションに必要です。 ほとんどの cifs サーバーでは、アドバイザリ バイト範囲のロック要求はまだサポートされていません。 *nobrl* を使用しない場合、cifs 形式の必須なバイト範囲のロックで中断されたアプリケーションでは、次のようなエラー メッセージが発生する可能性があります。
    ```console
    Error: SQLITE_BUSY: database is locked
    ```

### <a name="error-could-not-change-permissions-when-using-azure-files"></a>Azure Files を使用すると、"could not change permissions" (アクセス許可を変更できませんでした) というエラーが発生する

Azure Files プラグインで PostgreSQL を実行すると、次のようなエラーが表示されることがあります。

```console
initdb: could not change permissions of directory "/var/lib/postgresql/data": Operation not permitted
fixing permissions on existing directory /var/lib/postgresql/data
```

このエラーは、cifs/SMB プロトコルを使用する Azure Files プラグインにより発生します。 cifs/SMB プロトコルを使用している場合、ファイルとディレクトリのアクセス許可をマウント後に変更することはできません。

この問題を解決するには、*subPath* を Azure Disk プラグインと一緒に使用します。 

> [!NOTE] 
> ext3/4 ディスクの種類の場合、ディスクのフォーマット後に lost+found ディレクトリがあります。

### <a name="azure-files-has-high-latency-compared-to-azure-disk-when-handling-many-small-files"></a>多数の小さなファイルを処理する際、Azure ディスクと比べて Azure Files の待機時間が長くなる

多数の小さなファイルを処理する場合など、Azure ディスクと比較して Azure Files を使用すると待機時間が長くなることがあります。

### <a name="error-when-enabling-allow-access-allow-access-from-selected-network-setting-on-storage-account"></a>ストレージアカウントの [Allow access from selected network]/(選択したネットワークからのアクセスを許可する/) 設定を有効にすると、エラーが発生する

AKS で動的プロビジョニングに使用されるストレージアカウントの *[Allow access from selected network]/(選択したネットワークからのアクセスを許可する/)* 設定を有効にすると、AKS がファイル共有を作成するときに次のエラーが表示されます。

```console
persistentvolume-controller (combined from similar events): Failed to provision volume with StorageClass "azurefile": failed to create share kubernetes-dynamic-pvc-xxx in account xxx: failed to create file share, err: storage: service returned error: StatusCode=403, ErrorCode=AuthorizationFailure, ErrorMessage=This request is not authorized to perform this operation.
```

このエラーは、 *[Allow access from selected network]/(選択したネットワークからのアクセスを許可する/)* の設定時に *Kubernetes persistentvolume-controller* が選択したネットワーク上に存在しないことが原因で発生します。

[Azure Files で静的プロビジョニング](azure-files-volume.md)を使用することによって、この問題を軽減できます。

### <a name="azure-files-fails-to-remount-in-windows-pod"></a>Windows ポッドで Azure Files を再マウントできない

Azure Files がマウントされている Windows ポッドが削除され、同じノードで再作成されるようにスケジュールされている場合、マウントは失敗します。 このエラーは、Azure Files マウントが既にノードにマウントされているために `New-SmbGlobalMapping` コマンドが失敗することが原因で発生します。

例えば、次のようなエラー メッセージが表示される場合があります。

```console
E0118 08:15:52.041014    2112 nestedpendingoperations.go:267] Operation for "\"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (\"42c0ea39-1af9-11e9-8941-000d3af95268\")" failed. No retries permitted until 2019-01-18 08:15:53.0410149 +0000 GMT m=+732.446642701 (durationBeforeRetry 1s). Error: "MountVolume.SetUp failed for volume \"pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (UniqueName: \"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\") pod \"deployment-azurefile-697f98d559-6zrlf\" (UID: \"42c0ea39-1af9-11e9-8941-000d3af95268\") : azureMount: SmbGlobalMapping failed: exit status 1, only SMB mount is supported now, output: \"New-SmbGlobalMapping : Generic failure \\r\\nAt line:1 char:190\\r\\n+ ... ser, $PWord;New-SmbGlobalMapping -RemotePath $Env:smbremotepath -Cred ...\\r\\n+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\\r\\n    + CategoryInfo          : NotSpecified: (MSFT_SmbGlobalMapping:ROOT/Microsoft/...mbGlobalMapping) [New-SmbGlobalMa \\r\\n   pping], CimException\\r\\n    + FullyQualifiedErrorId : HRESULT 0x80041001,New-SmbGlobalMapping\\r\\n \\r\\n\""
```

この問題は、次のバージョンの Kubernetes で修正されました。

| Kubernetes バージョン | 修正済みのバージョン |
| -- | :--: |
| 1.12 | 1.12.6 以上 |
| 1.13 | 1.13.4 以上 |
| 1.14 以上 | 該当なし |

### <a name="azure-files-mount-fails-due-to-storage-account-key-changed"></a>ストレージ アカウント キーが変更されたため Azure Files マウントが失敗する

ストレージ アカウント キーが変更されている場合、Azure Files マウント エラーが発生することがあります。

この問題は、base64 でエンコードされたストレージ アカウント キーを使用して Azure シークレット ファイルの *azurestorageaccountkey* フィールドを手動で更新することで軽減できます。

Base64 でストレージ アカウント キーをエンコードするには、`base64` を使用します。 次に例を示します。

```console
echo X+ALAAUgMhWHL7QmQ87E1kSfIqLKfgC03Guy7/xk9MyIg2w4Jzqeu60CVw2r/dm6v6E0DWHTnJUEJGVQAoPaBc== | base64
```

Azure シークレット ファイルを更新するには、`kubectl edit secret` を使用します。 次に例を示します。

```console
kubectl edit secret azure-storage-account-{storage-account-name}-secret
```

数分後、エージェント ノードは更新されたストレージ キーを使用して Azure File のマウントを再試行します。

### <a name="cluster-autoscaler-fails-to-scale-with-error-failed-to-fix-node-group-sizes"></a>ノード グループ サイズの修正に失敗したため、クラスター オートスケーラーのスケールに失敗しました

クラスター オートスケーラーがスケールアップまたはスケールダウンされない場合、次のようなエラーが[クラスター オートスケーラー ログ][view-master-logs]に表示されます。

```console
E1114 09:58:55.367731 1 static_autoscaler.go:239] Failed to fix node group sizes: failed to decrease aks-default-35246781-vmss: attempt to delete existing nodes
```

このエラーは、上流クラスターのオートスケーラー競合状態が原因で発生します。この場合、クラスター オートスケーラーは、クラスター内に実際にある値とは異なる値で終了しています。 この状態を修正するには、単純に[クラスター オートスケーラー][cluster-autoscaler]を無効にしてから再度有効にします。

### <a name="slow-disk-attachment-getazuredisklun-takes-10-to-15-minutes-and-you-receive-an-error"></a>低速のディスクの接続では、GetAzureDiskLun の実行に 10 分から 15 分かかり、エラーが発生します

Kubernetes の **1.15.0 より古いバージョン**では、**WaitForAttach でディスクの LUN を見つけることができない**などのエラーが発生する場合があります。  これを回避するには、約 15 分間待機してから再試行します。

<!-- LINKS - internal -->
[view-master-logs]: view-master-logs.md
[cluster-autoscaler]: cluster-autoscaler.md
