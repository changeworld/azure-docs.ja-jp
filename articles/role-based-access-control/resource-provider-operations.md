---
title: Azure Resource Manager のプロバイダー操作 | Microsoft Docs
description: Microsoft Azure Resource Manager の各リソース プロバイダーで使用できる操作について詳しく説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/06/2018
ms.author: rolyon
ms.openlocfilehash: 80724a24fe7cf2565334a5212a0877589eb1eecf
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2018
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Azure Resource Manager のリソース プロバイダー操作

このドキュメントでは、Microsoft Azure Resource Manager の各リソース プロバイダーで使用できる操作を示します。 これらの操作をカスタム ロールで使用することで、Azure のリソースにロールベースのアクセス制御 (RBAC) の詳細なアクセス許可を提供できます。 これは包括的な一覧ではないことに注意してください。各プロバイダーの更新に伴って、操作が追加または削除される場合があります。 操作文字列は、`Microsoft.<ProviderName>/<ChildResourceType>/<action>` の形式に従います。 

> [!NOTE]
> 最新の包括的な一覧については、`Get-AzureRmProviderOperation` (PowerShell の場合) または `az provider operation list` (Azure CLI v2 の場合) を使って、Azure リソース プロバイダーの操作の一覧を表示してください。

## <a name="microsoftaad"></a>Microsoft.AAD

| 操作 | [説明] |
|---|---|
|/domainServices/delete|Domain Services を削除します。|
|/domainServices/read|Domain Services を読み取ります。|
|/domainServices/write|Domain Services を書き込みます。|
|/locations/operationresults/read|非同期操作の状態を読み取ります。|
|/Operations/read|ユーザーに対して表示される必要がある、操作のローカライズされたわかりやすい説明です。|

## <a name="microsoftaadiam"></a>microsoft.aadiam

| 操作 | [説明] |
|---|---|
|/diagnosticsettings/delete|診断設定を削除します。|
|/diagnosticsettings/read|診断設定を読み取ります。|
|/diagnosticsettings/write|診断設定を書き込みます。|
|/diagnosticsettingscategories/read|診断設定のカテゴリを読み取ります。|
|/tenants/providers/Microsoft.Insights/diagnosticSettings/read|リソースの診断設定を取得します。|
|/tenants/providers/Microsoft.Insights/diagnosticSettings/write|リソースの診断設定を作成または更新します。|
|/tenants/providers/Microsoft.Insights/logDefinitions/read|テナントの利用可能なログを取得します。|

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

| 操作 | [説明] |
|---|---|
|/configuration/action|テナント構成を更新します。|
|/configuration/read|テナント構成を読み取ります。|
|/configuration/write|テナント構成を作成します。|
|/services/action|テナントのサービス インスタンスを更新します。|
|/services/alerts/read|サービスのアラートを読み取ります。|
|/services/alerts/read|サービスのアラートを読み取ります。|
|/services/delete|テナントのサービス インスタンスを削除します。|
|/services/read|テナントのサービス インスタンスを読み取ります。|
|/services/servicemembers/action|サービスにサービス メンバー インスタンスを作成します。|
|/services/servicemembers/alerts/read|サービス メンバーのアラートを読み取ります。|
|/services/servicemembers/delete|サービスのサービス メンバー インスタンスを削除します。|
|/services/servicemembers/read|サービスのサービス メンバー インスタンスを読み取ります。|
|/services/write|テナントにサービス インスタンスを作成します。|

## <a name="microsoftadvisor"></a>Microsoft.Advisor

| 操作 | [説明] |
|---|---|
|/configurations/read|構成の取得|
|/configurations/write|構成を作成/更新します。|
|/generateRecommendations/action|推奨事項を生成します。|
|/generateRecommendations/read|推奨事項の生成の状態を取得します。|
|/operations/read|Microsoft Advisor の操作を取得します。|
|/recommendations/read|推奨事項を読み取ります。|
|/recommendations/suppressions/delete|抑制を削除します。|
|/recommendations/suppressions/read|抑制を取得します。|
|/recommendations/suppressions/write|抑制を作成または更新します。|
|/register/action|Microsoft Advisor にサブスクリプションを登録します。|
|/suppressions/delete|抑制を削除します。|
|/suppressions/read|抑制を取得します。|
|/suppressions/write|抑制を作成または更新します。|
|/unregister/action|Microsoft Advisor のサブスクリプションを登録解除します。|

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

| 操作 | [説明] |
|---|---|
|/alerts/read|入力フィルターのすべてのアラートを取得します。|
|/alerts/resolve/action|アラートの状態を "解決" に変更します。|
|/alertsSummary/read|アラートの概要を取得します。|
|/Operations/read|提供された操作を読み取ります。|

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

| 操作 | [説明] |
|---|---|
|/locations/checkNameAvailability/action|指定された分析サーバー名が有効であり、使用されていないことを確認します。|
|/locations/operationresults/read|指定された操作の結果の情報を取得します。|
|/locations/operationstatuses/read|指定された操作の状態の情報を取得します。|
|/operations/read|操作の情報を取得します。|
|/register/action|Analysis Services リソース プロバイダーに登録します。|
|/servers/delete|分析サーバーを削除します。|
|/servers/listGatewayStatus/action|サーバーに関連付けられているゲートウェイの状態を一覧表示します。|
|/servers/providers/Microsoft.Insights/diagnosticSettings/read|分析サーバーの診断の設定を取得します。|
|/servers/providers/Microsoft.Insights/diagnosticSettings/write|分析サーバーの診断の設定を作成または更新します。|
|/servers/providers/Microsoft.Insights/logDefinitions/read|サーバーの利用可能なログを取得します。|
|/servers/providers/Microsoft.Insights/metricDefinitions/read|分析サーバーの利用可能なメトリックを取得します。|
|/servers/read|指定された分析サーバーの情報を取得します。|
|/servers/resume/action|分析サーバーを再開します。|
|/servers/skus/read|サーバーの利用可能な SKU 情報を取得します。|
|/servers/suspend/action|分析サーバーを中断します。|
|/servers/write|指定された分析サーバーを作成または更新します。|
|/skus/read|SKU の情報を取得します。|

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

| 操作 | [説明] |
|---|---|
|/checkNameAvailability/read|指定されたサービス名を使用できるかどうかを確認します。|
|/operations/read|Microsoft.ApiManagement リソースで使用可能なすべての API 操作を読み取ります。|
|/register/action|Microsoft.ApiManagement リソース プロバイダーにサブスクリプションを登録します。|
|/reports/read|期間、地理的地域、開発者、製品、API、操作、サブスクリプション、要求ごとに集計されたレポートを取得します。|
|/service/apis/delete|既存の API を削除します。|
|/service/apis/diagnostics/delete|既存の診断を削除します。|
|/service/apis/diagnostics/loggers/delete|ロガーと診断設定のマッピングを削除します。|
|/service/apis/diagnostics/loggers/read|既存の診断ロガーの一覧を取得します。|
|/service/apis/diagnostics/loggers/write|ロガーを診断設定にマップします。|
|/service/apis/diagnostics/read|診断の一覧または診断の詳細を取得します。|
|/service/apis/diagnostics/write|新しい診断を追加するか、既存の診断の詳細を更新します。|
|/service/apis/operations/delete|既存の API 操作を削除します。|
|/service/apis/operations/policies/delete|API 操作ポリシーからポリシー構成を削除します。|
|/service/apis/operations/policies/read|API 操作のポリシーを取得するか、API 操作のポリシー構成の詳細を取得します。|
|/service/apis/operations/policies/write|API 操作のポリシー構成の詳細を設定します。|
|/service/apis/operations/policy/delete|操作からポリシーの構成を削除します。|
|/service/apis/operations/policy/read|操作のポリシーの構成の詳細を取得します。|
|/service/apis/operations/policy/write|操作のポリシーの構成の詳細を設定します。|
|/service/apis/operations/read|既存の API 操作の一覧を取得するか、API 操作の詳細を取得します。|
|/service/apis/operations/tags/delete|既存のタグと既存の操作の関連付けを削除します。|
|/service/apis/operations/tags/read|操作に関連付けられているタグを取得するか、タグの詳細を取得します。|
|/service/apis/operations/tags/write|既存のタグと既存の操作を関連付けます。|
|/service/apis/operations/write|新しい API 操作を作成するか、既存の API 操作を更新します。|
|/service/apis/operationsByTags/read|操作/タグの関連付けの一覧を取得します。|
|/service/apis/policies/delete|API ポリシーからポリシーの構成を削除します。|
|/service/apis/policies/read|API のポリシーを取得するか、API のポリシー構成の詳細を取得します。|
|/service/apis/policies/write|API のポリシーの構成の詳細を設定します。|
|/service/apis/policy/delete|API からポリシーの構成を削除します。|
|/service/apis/policy/read|API のポリシーの構成の詳細を取得します。|
|/service/apis/policy/write|API のポリシーの構成の詳細を設定します。|
|/service/apis/products/read|API が含まれるすべての製品を取得します。|
|/service/apis/read|すべての登録済み API の一覧を取得するか、API の詳細を取得します。|
|/service/apis/releases/delete|API のすべてのリリースを削除するか、API の 1 つのリリースを削除します。|
|/service/apis/releases/read|API のリリースを取得するか、API のリリースの詳細を取得します。|
|/service/apis/releases/write|新しい API のリリースを作成するか、既存の API のリリースを更新します。|
|/service/apis/revisions/delete|API のすべてのリビジョンを削除します。|
|/service/apis/revisions/read|API に属するリビジョンを取得します。|
|/service/apis/schemas/delete|既存のスキーマを削除します。|
|/service/apis/schemas/document/read|スキーマを記述しているドキュメントを取得します。|
|/service/apis/schemas/document/write|スキーマを記述しているドキュメントを更新|
|/service/apis/schemas/read|指定された API のすべてのスキーマを取得するか、API によって使用されているスキーマを取得します。|
|/service/apis/schemas/write|API によって使用されるスキーマを設定します。|
|/service/apis/tagDescriptions/delete|API からタグの記述を削除します。|
|/service/apis/tagDescriptions/read|API のスコープ内の複数または 1 つのタグの記述を取得します。|
|/service/apis/tagDescriptions/write|API のスコープ内のタグの記述を作成または変更します。|
|/service/apis/tags/delete|既存の API/タグの関連付けを削除します。|
|/service/apis/tags/read|API のすべての API/タグの関連付けを取得するか、API/タグの関連付けの詳細を取得します。|
|/service/apis/tags/write|新しい API/タグの関連付けを追加します。|
|/service/apis/write|新しい API を作成するか、既存の API の詳細を更新します。|
|/service/apisByTags/read|API/タグの関連付けの一覧を取得します。|
|/service/api-version-sets/delete|既存の VersionSet を削除します。|
|/service/api-version-sets/read|バージョン グループ エンティティの一覧を取得するか、VersionSet の詳細を取得します。|
|/service/api-version-sets/versions/read|バージョン エンティティの一覧を取得します。|
|/service/api-version-sets/write|新しい VersionSet を作成するか、既存の VersionSet の詳細を更新します。|
|/service/applynetworkconfigurationupdates/action|更新済みのネットワーク設定を選択するために、仮想ネットワークで実行されている Microsoft.ApiManagement リソースを更新します。|
|/service/authorizationServers/delete|既存の承認サーバーを削除します。|
|/service/authorizationServers/read|承認サーバーの一覧を取得するか、承認サーバーの詳細を取得します。|
|/service/authorizationServers/write|新しい承認サーバーを作成するか、既存の承認サーバーの詳細を更新します。|
|/service/backends/delete|既存のバックエンドを削除します。|
|/service/backends/read|バックエンドの一覧を取得するか、バックエンドの詳細を取得します。|
|/service/backends/reconnect/action|再接続要求を作成します。|
|/service/backends/write|新しいバックエンドを追加するか、既存のバックエンドの詳細を更新します。|
|/service/backup/action|ユーザーが指定したストレージ アカウント内の指定されたコンテナーに API Management サービスをバックアップします。|
|/service/certificates/delete|既存の証明書を削除します。|
|/service/certificates/read|証明書の一覧を取得するか、証明書の詳細を取得します。|
|/service/certificates/write|新しい証明書を追加します。|
|/service/delete|API Management サービス インスタンスを削除します。|
|/service/diagnostics/delete|既存の診断を削除します。|
|/service/diagnostics/loggers/delete|ロガーと診断設定のマッピングを削除します。|
|/service/diagnostics/loggers/read|既存の診断ロガーの一覧を取得します。|
|/service/diagnostics/loggers/write|ロガーを診断設定にマップします。|
|/service/diagnostics/read|診断の一覧または診断の詳細を取得します。|
|/service/diagnostics/write|新しい診断を追加するか、既存の診断の詳細を更新します。|
|/service/getssotoken/action|API Management サービスの従来のポータルに管理者としてログインする際に使用できる SSO トークンを取得します。|
|/service/groups/delete|既存のグループを削除します。|
|/service/groups/read|グループの一覧を取得するか、グループの詳細を取得します。|
|/service/groups/users/delete|既存のグループから既存のユーザーを削除します。|
|/service/groups/users/read|グループのユーザーの一覧を取得します。|
|/service/groups/users/write|既存のユーザーを既存のグループに追加します。|
|/service/groups/write|新しいグループを作成するか、既存のグループの詳細を更新します。|
|/service/identityProviders/delete|既存の ID プロバイダーを削除します。|
|/service/identityProviders/read|ID プロバイダーの一覧を取得するか、ID プロバイダーの詳細を取得します。|
|/service/identityProviders/write|新しい ID プロバイダーを作成するか、既存の ID プロバイダーの詳細を更新します。|
|/service/locations/networkstatus/read|その場所でサービスが依存しているリソースのネットワーク アクセスの状態を取得します。|
|/service/loggers/delete|既存のロガーを削除します。|
|/service/loggers/read|ロガーの一覧を取得するか、ロガーの詳細を取得します。|
|/service/loggers/write|新しいロガーを追加するか、既存のロガーの詳細を更新します。|
|/service/managedeployments/action|SKU/ユニット数を変更し、API Management サービスのリージョン デプロイを追加または削除します。|
|/service/networkstatus/read|サービスが依存しているリソースのネットワーク アクセスの状態を取得します。|
|/service/notifications/action|指定されたユーザーに通知を送信します。|
|/service/notifications/read|すべての API Management 発行者通知を取得するか、API Management 発行者通知の詳細を取得します。|
|/service/notifications/recipientEmails/delete|通知に関連付けられている既存のメールを削除します。|
|/service/notifications/recipientEmails/read|API Management 発行者通知に関連付けられているメール受信者を取得します。|
|/service/notifications/recipientEmails/write|通知の新しいメール受信者を作成します。|
|/service/notifications/recipientUsers/delete|通知受信者に関連付けられているユーザーを削除します。|
|/service/notifications/recipientUsers/read|通知に関連付けられている受信者ユーザーを取得します。|
|/service/notifications/recipientUsers/write|通知の受信者にユーザーを追加します。|
|/service/notifications/write|API Management 発行者通知を作成または更新します。|
|/service/openidConnectProviders/delete|既存の OpenID Connect プロバイダーを削除します。|
|/service/openidConnectProviders/read|OpenID Connect プロバイダーの一覧を取得するか、OpenID Connect プロバイダーの詳細を取得します。|
|/service/openidConnectProviders/write|新しい OpenID Connect プロバイダーを作成するか、既存の OpenID Connect プロバイダーの詳細を更新します。|
|/service/operationresults/read|実行時間の長い操作の現在の状態を取得します。|
|/service/policies/delete|テナント ポリシーからポリシーの構成を削除します。|
|/service/policies/read|テナントのポリシーを取得するか、テナントのポリシー構成の詳細を取得します。|
|/service/policies/write|テナントのポリシー構成の詳細を設定します。|
|/service/policySnippets/read|すべてのポリシーのスニペットを取得します。|
|/service/portalsettings/read|ポータルのサインアップ、サインイン、または委任の設定を取得します。|
|/service/portalsettings/write|サインアップ、サインイン、または委任の設定を更新します。|
|/service/products/apis/delete|既存の製品から既存の API を削除します。|
|/service/products/apis/read|既存の製品に追加された API の一覧を取得します。|
|/service/products/apis/write|既存の API を既存の製品に追加します。|
|/service/products/delete|既存の製品を削除します。|
|/service/products/groups/delete|既存の製品への既存の開発者グループの関連付けを削除します。|
|/service/products/groups/read|製品に関連付けられている開発者グループの一覧を取得します。|
|/service/products/groups/write|既存の開発者グループを既存の製品に関連付けます。|
|/service/products/policies/delete|製品ポリシーからポリシーの構成を削除します。|
|/service/products/policies/read|製品のポリシーまたはポリシー構成の詳細を取得します。|
|/service/products/policies/write|製品のポリシー構成の詳細を設定します。|
|/service/products/policy/delete|既存の製品からポリシーの構成を削除します。|
|/service/products/policy/read|既存の製品のポリシーの構成を取得します。|
|/service/products/policy/write|既存の製品のポリシーの構成を設定します。|
|/service/products/read|製品の一覧を取得するか、製品の詳細を取得します。|
|/service/products/subscriptions/read|製品サブスクリプションの一覧を取得します。|
|/service/products/tags/delete|既存のタグと既存の製品の関連付けを削除します。|
|/service/products/tags/read|製品に関連付けられているタグまたはタグの詳細を取得します。|
|/service/products/tags/write|既存のタグと既存の製品を関連付けます。|
|/service/products/write|新しい製品を作成するか、既存の製品の詳細を更新します。|
|/service/properties/delete|既存のプロパティを削除します。|
|/service/properties/read|すべてのプロパティの一覧を取得するか、指定されたプロパティの詳細を取得します。|
|/service/properties/write|新しいプロパティを作成するか、指定されたプロパティの値を更新します。|
|/service/providers/Microsoft.Insights/diagnosticSettings/read|API Management サービスの診断設定を取得します。|
|/service/providers/Microsoft.Insights/diagnosticSettings/write|API Management サービスの診断設定を作成または更新します。|
|/service/providers/Microsoft.Insights/logDefinitions/read|API Management サービスで使用可能なログを取得します。|
|/service/providers/Microsoft.Insights/metricDefinitions/read|API Management サービスで使用可能なメトリックを取得します。|
|/service/quotas/periods/read|期間のクォータ カウンターの値を取得します。|
|/service/quotas/periods/write|クォータ カウンターの現在の値を設定します。|
|/service/quotas/read|クォータの値を取得します。|
|/service/quotas/write|クォータ カウンターの現在の値を設定します。|
|/service/read|API Management サービス インスタンスのメタデータの読み取り|
|/service/reports/read|期間、地理的地域、または開発者ごとに集計されたレポートを取得します。 または、製品ごとに集計されたレポートを取得します。 または、API、操作、サブスクリプションごとに集計されたレポートを取得します。 または、要求のレポート データを取得します。|
|/service/restore/action|ユーザーが指定したストレージ アカウント内の指定されたコンテナーからの API Management サービスの復元|
|/service/subscriptions/delete|サブスクリプションを削除します。 この操作を使用してサブスクリプションを削除できます。|
|/service/subscriptions/read|製品サブスクリプションの一覧を取得するか、製品サブスクリプションの詳細を取得します。|
|/service/subscriptions/regeneratePrimaryKey/action|サブスクリプションのプライマリ キーを再生成します。|
|/service/subscriptions/regenerateSecondaryKey/action|サブスクリプションのセカンダリ キーを再生成します。|
|/service/subscriptions/write|既存のユーザーを既存の製品にサブスクライブするか、既存のサブスクリプションの詳細を更新します。 この操作を使用してサブスクリプションを更新できます。|
|/service/tagResources/read|タグおよび関連付けられているリソースの一覧を取得します。|
|/service/tags/delete|既存のタグを削除します。|
|/service/tags/read|タグの一覧またはタグの詳細を取得します。|
|/service/tags/write|新しいタグを追加するか、既存のタグの詳細を更新します。|
|/service/templates/delete|既定の API Management メール テンプレートをリセットします。|
|/service/templates/read|すべてのメール テンプレートまたは API Management メール テンプレートの詳細を取得します。|
|/service/templates/write|API Management メール テンプレートを作成または更新します。|
|/service/tenant/delete|テナントのポリシーの構成を削除します。|
|/service/tenant/deploy/action|指定された Git ブランチの変更をデータベース内の構成に適用するデプロイ タスクを実行します。|
|/service/tenant/operationResults/read|操作の結果の一覧を取得するか、特定の操作の結果を取得します。|
|/service/tenant/read|テナントのポリシー構成またはテナントのアクセス情報の詳細を取得します。|
|/service/tenant/regeneratePrimaryKey/action|プライマリ アクセス キーを再生成します。|
|/service/tenant/regenerateSecondaryKey/action|セカンダリ アクセス キーを再生成します。|
|/service/tenant/save/action|レポジトリ内の指定されたブランチの構成のスナップショットを使用してコミットを作成します。|
|/service/tenant/syncState/read|前回の Git 同期の状態を取得します。|
|/service/tenant/validate/action|指定された Git ブランチの変更を検証します。|
|/service/tenant/write|テナントのポリシー構成を設定するか、テナントのアクセス情報の詳細を更新します。|
|/service/updatecertificate/action|API Management サービスの SSL 証明書をアップロードします。|
|/service/updatehostname/action|API Management サービスのカスタム ドメイン名を設定、更新、または削除します。|
|/service/users/action|新しいユーザーを登録します。|
|/service/users/applications/attachments/delete|添付ファイルを削除します。|
|/service/users/applications/attachments/read|アプリケーションの 1 つまたは複数の添付ファイルを取得します。|
|/service/users/applications/attachments/write|アプリケーションに添付ファイルを追加します。|
|/service/users/applications/delete|既存のアプリケーションを削除します。|
|/service/users/applications/read|すべてのユーザー アプリケーションの一覧または API Management アプリケーションの詳細を取得します。|
|/service/users/applications/write|アプリケーションを API Management に登録するか、アプリケーションの詳細を更新します。|
|/service/users/delete|ユーザー アカウントを削除します。|
|/service/users/generateSsoUrl/action|SSO URL を生成します。 この URL を使用して、管理ポータルにアクセスできます。|
|/service/users/groups/read|ユーザー グループの一覧を取得します。|
|/service/users/keys/read|ユーザー キーの一覧を取得します。|
|/service/users/read|登録済みユーザーの一覧を取得するか、ユーザーのアカウントの詳細を取得します。|
|/service/users/subscriptions/read|ユーザー サブスクリプションの一覧を取得します。|
|/service/users/token/action|ユーザーのトークン アクセス トークンを取得します。|
|/service/users/write|新しいユーザーを登録するか、既存のユーザーのアカウントの詳細を更新します。|
|/service/write|API Management サービスの新しいインスタンスの作成|
|/unregister/action|Microsoft.ApiManagement リソース プロバイダーへのサブスクリプションの登録を解除します。|

## <a name="microsoftauthorization"></a>Microsoft.Authorization

| 操作 | [説明] |
|---|---|
|/checkAccess/action|呼び出し元が特定のアクションの実行を承認されているかどうかを確認します。|
|/classicAdministrators/delete|サブスクリプションから管理者を削除します。|
|/classicAdministrators/read|サブスクリプションの管理者を読み取ります。|
|/classicAdministrators/write|サブスクリプションの管理者を追加または変更しします。|
|/elevateAccess/action|テナント スコープで、ユーザー アクセス管理者のアクセス権を呼び出し元に付与します。|
|/locks/delete|指定されたスコープのロックを削除します。|
|/locks/read|指定されたスコープのロックを取得します。|
|/locks/write|指定されたスコープのロックを追加します。|
|/permissions/read|指定されたスコープで呼び出し元が持つすべてのアクセス許可を一覧表示します。|
|/policyAssignments/delete|指定されたスコープのポリシー割り当てを削除します。|
|/policyAssignments/read|ポリシー割り当てに関する情報を取得します。|
|/policyAssignments/write|指定されたスコープのポリシー割り当てを作成します。|
|/policyDefinitions/delete|ポリシー定義を削除します。|
|/policyDefinitions/read|ポリシー定義に関する情報を取得します。|
|/policyDefinitions/write|カスタムのポリシー定義を作成します。|
|/policySetDefinitions/delete|ポリシー セットの定義を削除します。|
|/policySetDefinitions/read|ポリシー セットの定義に関する情報を取得します。|
|/policySetDefinitions/write|カスタム ポリシー セットの定義を作成します。|
|/providerOperations/read|ロール定義で使用できる、すべてのリソース プロバイダーのための操作を取得します。|
|/roleAssignments/delete|指定したスコープにおけるロールの割り当てを削除します。|
|/roleAssignments/read|ロールの割り当てに関する情報を取得します。|
|/roleAssignments/write|指定されたスコープのロールの割り当てを作成します。|
|/roleDefinitions/delete|指定されたカスタムのロール定義を削除します。|
|/roleDefinitions/read|ロール定義に関する情報を取得します。|
|/roleDefinitions/write|指定されたアクセス許可と割り当て可能なスコープで、カスタムのロール定義を作成または更新します。|

## <a name="microsoftautomation"></a>Microsoft.Automation

| 操作 | [説明] |
|---|---|
|/automationAccounts/agentRegistrationInformation/read|Azure Automation DSC の登録情報を読み取ります。|
|/automationAccounts/agentRegistrationInformation/regenerateKey/action|Azure Automation DSC のキーを再生成する要求を書き込みます。|
|/automationAccounts/certificates/delete|Azure Automation 証明書資産を削除します。|
|/automationAccounts/certificates/read|Azure Automation 証明書資産を取得します。|
|/automationAccounts/certificates/write|Azure Automation 証明書資産を作成または更新します。|
|/automationAccounts/compilationjobs/read|Azure Automation DSC のコンパイルを読み取ります。|
|/automationAccounts/compilationjobs/write|Azure Automation DSC のコンパイルを書き込みます。|
|/automationAccounts/configurations/delete|Azure Automation DSC のコンテンツを削除します。|
|/automationAccounts/configurations/getCount/action|Azure Automation DSC のコンテンツの数を読み取ります。|
|/automationAccounts/configurations/read|Azure Automation DSC のコンテンツを取得します。|
|/automationAccounts/configurations/write|Azure Automation DSC のコンテンツを書き込みます。|
|/automationAccounts/connections/delete|Azure Automation 接続資産を削除します。|
|/automationAccounts/connections/read|Azure Automation 接続資産を取得します。|
|/automationAccounts/connections/write|Azure Automation 接続資産を作成または更新します。|
|/automationAccounts/connectionTypes/delete|Azure Automation 接続の種類の資産を削除します。|
|/automationAccounts/connectionTypes/read|Azure Automation 接続の種類の資産を取得します。|
|/automationAccounts/connectionTypes/write|Azure Automation 接続の種類の資産を作成します。|
|/automationAccounts/credentials/delete|Azure Automation 資格情報資産を削除します。|
|/automationAccounts/credentials/read|Azure Automation 資格情報資産を取得します。|
|/automationAccounts/credentials/write|Azure Automation 資格情報資産を作成または更新します。|
|/automationAccounts/delete|Azure Automation アカウントを削除します。|
|/automationAccounts/diagnosticSettings/read|リソースの診断設定を取得します。|
|/automationAccounts/diagnosticSettings/write|リソースの診断設定を設定します。|
|/automationAccounts/hybridRunbookWorkerGroups/delete|Hybrid Runbook Worker リソースを削除します。|
|/automationAccounts/hybridRunbookWorkerGroups/read|Hybrid Runbook Worker リソースを読み取ります。|
|/automationAccounts/jobs/output/action|ジョブの出力を取得します。|
|/automationAccounts/jobs/output/action|ジョブの出力を取得します。|
|/automationAccounts/jobs/read|Azure Automation ジョブを取得します。|
|/automationAccounts/jobs/read|Azure Automation ジョブを取得します。|
|/automationAccounts/jobs/resume/action|Azure Automation ジョブを再開します。|
|/automationAccounts/jobs/resume/action|Azure Automation ジョブを再開します。|
|/automationAccounts/jobs/runbookContent/action|ジョブ実行時に Azure Automation Runbook のコンテンツを取得します。|
|/automationAccounts/jobs/runbookContent/action|ジョブ実行時に Azure Automation Runbook のコンテンツを取得します。|
|/automationAccounts/jobs/stop/action|Azure Automation ジョブを停止します。|
|/automationAccounts/jobs/stop/action|Azure Automation ジョブを停止します。|
|/automationAccounts/jobs/streams/read|Azure Automation ジョブ ストリームを取得します。|
|/automationAccounts/jobs/streams/read|Azure Automation ジョブ ストリームを取得します。|
|/automationAccounts/jobs/suspend/action|Azure Automation ジョブを中断します。|
|/automationAccounts/jobs/suspend/action|Azure Automation ジョブを中断します。|
|/automationAccounts/jobs/write|Azure Automation ジョブを作成します。|
|/automationAccounts/jobs/write|Azure Automation ジョブを作成します。|
|/automationAccounts/jobSchedules/delete|Azure Automation ジョブ スケジュールを削除します。|
|/automationAccounts/jobSchedules/read|Azure Automation ジョブ スケジュールを取得します。|
|/automationAccounts/jobSchedules/write|Azure Automation ジョブ スケジュールを作成します。|
|/automationAccounts/linkedWorkspace/read|Automation アカウントにリンクされているワークスペースを取得します。|
|/automationAccounts/logDefinitions/read|Automation アカウントで使用可能なログを取得します。|
|/automationAccounts/modules/activities/read|Azure Automation のアクティビティを取得します。|
|/automationAccounts/modules/delete|Azure Automation モジュールを削除します。|
|/automationAccounts/modules/read|Azure Automation モジュールを取得します。|
|/automationAccounts/modules/write|Azure Automation モジュールを作成または更新します。|
|/automationAccounts/nodeConfigurations/delete|Azure Automation DSC のノード構成を削除します。|
|/automationAccounts/nodeConfigurations/read|Azure Automation DSC のノード構成を読み取ります。|
|/automationAccounts/nodeConfigurations/readContent/action|Azure Automation DSC のノード構成の内容を読み取ります。|
|/automationAccounts/nodeConfigurations/write|Azure Automation DSC のノード構成を書き込みます。|
|/automationAccounts/nodes/delete|Azure Automation DSC のノードを削除します。|
|/automationAccounts/nodes/read|Azure Automation DSC のノードを読み取ります。|
|/automationAccounts/nodes/reports/read|Azure Automation DSC のレポートの内容を読み取ります。|
|/automationAccounts/nodes/reports/read|Azure Automation DSC のレポートを読み取ります。|
|/automationAccounts/objectDataTypes/fields/read|Azure Automation の TypeFields を取得します。|
|/automationAccounts/providers/Microsoft.Insights/metricDefinitions/read|Automation のメトリックの定義を取得します。|
|/automationAccounts/read|Azure Automation アカウントを取得します。|
|/automationAccounts/runbooks/delete|Azure Automation Runbook を削除します。|
|/automationAccounts/runbooks/draft/publish/action|Azure Automation Runbook の下書きを発行します。|
|/automationAccounts/runbooks/draft/read|Azure Automation Runbook の下書きを取得します。|
|/automationAccounts/runbooks/draft/readContent/action|Azure Automation Runbook の下書きのコンテンツを取得します。|
|/automationAccounts/runbooks/draft/testJob/read|Azure Automation Runbook の下書きのテスト ジョブを取得します。|
|/automationAccounts/runbooks/draft/testJob/resume/action|Azure Automation Runbook の下書きのテスト ジョブを再開します。|
|/automationAccounts/runbooks/draft/testJob/stop/action|Azure Automation Runbook の下書きのテスト ジョブを停止します。|
|/automationAccounts/runbooks/draft/testJob/suspend/action|Azure Automation Runbook の下書きのテスト ジョブを中断します。|
|/automationAccounts/runbooks/draft/testJob/write|Azure Automation Runbook の下書きのテスト ジョブを作成します。|
|/automationAccounts/runbooks/draft/undoEdit/action|Azure Automation Runbook の下書きの編集を元に戻します。|
|/automationAccounts/runbooks/draft/writeContent/action|Azure Automation Runbook の下書きのコンテンツを作成します。|
|/automationAccounts/runbooks/read|Azure Automation Runbook を取得します。|
|/automationAccounts/runbooks/readContent/action|Azure Automation Runbook のコンテンツを取得します。|
|/automationAccounts/runbooks/write|Azure Automation Runbook を作成または更新します。|
|/automationAccounts/schedules/delete|Azure Automation スケジュール資産を削除します。|
|/automationAccounts/schedules/read|Azure Automation スケジュール資産を取得します。|
|/automationAccounts/schedules/write|Azure Automation スケジュール資産を作成または更新します。|
|/automationAccounts/statistics/read|Azure Automation の統計情報を取得します。|
|/automationAccounts/usages/read|Azure Automation の使用状況を取得します。|
|/automationAccounts/variables/delete|Azure Automation 変数資産を削除します。|
|/automationAccounts/variables/read|Azure Automation 変数資産を読み取ります。|
|/automationAccounts/variables/write|Azure Automation 変数資産を作成または更新します。|
|/automationAccounts/watchers/streams/read|Azure Automation Watcher のジョブ ストリームを取得します。|
|/automationAccounts/webhooks/delete|Azure Automation Webhook を削除します。 |
|/automationAccounts/webhooks/generateUri/action|Azure Automation Webhook の URI を生成します。|
|/automationAccounts/webhooks/read|Azure Automation Webhook を読み取ります。|
|/automationAccounts/webhooks/write|Azure Automation Webhook を作成または更新します。|
|/automationAccounts/write|Azure Automation アカウントを作成または更新します。|
|/automationAccounts/write|Azure Automation アカウントを作成または更新します。|

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

| 操作 | [説明] |
|---|---|
|/b2cDirectories/delete|B2C ディレクトリ リソースを削除します。|
|/b2cDirectories/read|B2C ディレクトリ リソースを表示します。|
|/b2cDirectories/write|B2C ディレクトリ リソースを作成または更新します。|
|/operations/read|Microsoft.AzureActiveDirectory リソース プロバイダーで使用可能なすべての API 操作を読み取ります。|
|/register/action|Microsoft.AzureActiveDirectory リソース プロバイダーにサブスクリプションを登録します。|

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

| 操作 | [説明] |
|---|---|
|/Operations/read|リソース プロバイダーの操作のプロパティを取得します。|
|/register/action|Microsoft.AzureStack リソース プロバイダーにサブスクリプションを登録します。|
|/registrations/customerSubscriptions/delete|Azure Stack の顧客サブスクリプションを削除します。|
|/registrations/customerSubscriptions/read|Azure Stack の顧客サブスクリプションのプロパティを取得します。|
|/registrations/customerSubscriptions/write|Azure Stack の顧客サブスクリプションを作成または更新します。|
|/registrations/delete|Azure Stack の登録を削除します。|
|/registrations/getActivationKey/action|Azure Stack の最新のライセンス認証キーを取得します。|
|/registrations/products/listDetails/action|Azure Stack Marketplace の製品の拡張詳細を取得します。|
|/registrations/products/read|Azure Stack Marketplace の製品のプロパティを取得します。|
|/registrations/read|Azure Stack の登録のプロパティを取得します。|
|/registrations/write|Azure Stack の登録を作成または更新します。|

## <a name="microsoftbatch"></a>Microsoft.Batch

| 操作 | [説明] |
|---|---|
|/batchAccounts/applications/delete|アプリケーションを削除します。|
|/batchAccounts/applications/read|アプリケーションを一覧表示するか、アプリケーションのプロパティを取得します。|
|/batchAccounts/applications/versions/activate/action|アプリケーション パッケージをアクティブにします。|
|/batchAccounts/applications/versions/delete|アプリケーション パッケージを削除します。|
|/batchAccounts/applications/versions/read|アプリケーション パッケージのプロパティを取得します。|
|/batchAccounts/applications/versions/write|新しいアプリケーション パッケージを作成するか、既存のアプリケーション パッケージを更新します。|
|/batchAccounts/applications/write|新しいアプリケーションを作成するか、既存のアプリケーションを更新します。|
|/batchAccounts/certificateOperationResults/read|Batch アカウントでの実行時間の長い証明書操作の結果を取得します。|
|/batchAccounts/certificates/cancelDelete/action|Batch アカウントでの失敗した証明書削除を取り消します|
|/batchAccounts/certificates/delete|Batch アカウントから証明書を削除します。|
|/batchAccounts/certificates/read|Batch アカウントの証明書を一覧表示するか、証明書のプロパティを取得します。|
|/batchAccounts/certificates/write|Batch アカウントで新しい証明書を作成するか、既存の証明書を更新します。|
|/batchAccounts/delete|Batch アカウントを削除します。|
|/batchAccounts/listkeys/action|Batch アカウントのアクセス キーを一覧表示します。|
|/batchAccounts/operationResults/read|Batch アカウントの実行時間の長い操作の結果を取得します。|
|/batchAccounts/poolOperationResults/read|Batch アカウントでの実行時間の長いプール操作の結果を取得します。|
|/batchAccounts/pools/delete|Batch アカウントからプールを削除します。|
|/batchAccounts/pools/disableAutoscale/action|Batch アカウント プールの自動スケーリングを無効にします。|
|/batchAccounts/pools/read|Batch アカウントのプールを一覧表示するか、プールのプロパティを取得します。|
|/batchAccounts/pools/stopResize/action|Batch アカウント プールで実行中のサイズ変更操作を停止します。|
|/batchAccounts/pools/upgradeOs/action|Batch アカウント プールのオペレーティング システムをアップグレードします。|
|/batchAccounts/pools/write|Batch アカウントに新しいプールを作成するか、既存のプールを更新します。|
|/batchAccounts/providers/Microsoft.Insights/diagnosticSettings/read|リソースの診断設定を取得します。|
|/batchAccounts/providers/Microsoft.Insights/diagnosticSettings/write|リソースの診断設定を作成または更新します。|
|/batchAccounts/providers/Microsoft.Insights/logDefinitions/read|Batch サービスで使用可能なログを取得します。|
|/batchAccounts/providers/Microsoft.Insights/metricDefinitions/read|Batch サービスで使用可能なメトリックを取得します。|
|/batchAccounts/read|Batch アカウントを一覧表示するか、Batch アカウントのプロパティを取得します。|
|/batchAccounts/regeneratekeys/action|Batch アカウントのアクセス キーを再生成します。|
|/batchAccounts/syncAutoStorageKeys/action|Batch アカウント用に構成された自動ストレージ アカウントのアクセス キーを同期します。|
|/batchAccounts/write|新しい Batch アカウントを作成するか、既存の Batch アカウントを更新します。|
|/locations/checkNameAvailability/action|アカウント名が有効であり、使用されていないことを確認します。|
|/locations/quotas/read|指定された Azure リージョンの指定されたサブスクリプションの Batch クォータを取得します。|
|/register/action|Batch リソース プロバイダーにサブスクリプションを登録し、Batch アカウントを作成できるようにします。|
|/unregister/action|Batch リソース プロバイダーのサブスクリプションを登録解除し、Batch アカウントを作成できないようにします。|

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

| 操作 | [説明] |
|---|---|
|/clusters/delete|Batch AI クラスターを削除します。|
|/clusters/read|Batch AI クラスターを一覧表示するか、Batch AI クラスターのプロパティを取得します。|
|/clusters/remoteLoginInformation/action|Batch AI クラスターのリモート ログイン情報を一覧表示します。|
|/clusters/write|新しい Batch AI クラスターを作成するか、既存の Batch AI クラスターを更新します。|
|/fileservers/delete|Batch AI ファイル サーバーを削除します。|
|/fileservers/read|Batch AI ファイル サーバーを一覧表示するか、Batch AI ファイル サーバーのプロパティを取得します。|
|/fileservers/resume/action|Batch AI ファイル サーバーを再開します。|
|/fileservers/suspend/action|Batch AI ファイル サーバーを一時停止します。|
|/fileservers/write|新しい Batch AI ファイル サーバーを作成するか、既存の Batch AI ファイル サーバーを更新します。|
|/jobs/delete|Batch AI ジョブを削除します。|
|/jobs/read|Batch AI ジョブを一覧表示するか、Batch AI ジョブのプロパティを取得します。|
|/jobs/remoteLoginInformation/action|Batch AI ジョブのリモート ログイン情報を一覧表示します。|
|/jobs/terminate/action|Batch AI ジョブを終了します。|
|/jobs/write|新しい Batch AI ジョブを作成するか、既存の Batch AI ジョブを更新します。|
|/register/action|Batch AI リソース プロバイダーにサブスクリプションを登録し、Batch AI リソースを作成できるようにします。|

## <a name="microsoftbilling"></a>Microsoft.Billing

| 操作 | [説明] |
|---|---|
|/billingPeriods/read|使用可能な請求期間を一覧表示します。|
|/invoices/read|使用可能な請求書を一覧表示します。|

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

| 操作 | [説明] |
|---|---|
|/mapApis/Delete|削除操作。|
|/mapApis/listSecrets/action|シークレットを一覧表示します。|
|/mapApis/listSingleSignOnToken/action|リソースのシングル サインオン認証トークンを読み取ります。|
|/mapApis/Read|読み取り操作。|
|/mapApis/regenerateKey/action|キーを再生成します。|
|/mapApis/Write|書き込み操作。|
|/Operations/read|操作の説明。|

## <a name="microsoftcache"></a>Microsoft.Cache

| 操作 | [説明] |
|---|---|
|/checknameavailability/action|新しい Redis Cache で名前を使用できるかどうかを確認します。|
|/operations/read|"Microsoft.Cache" プロバイダーがサポートする操作を一覧表示します。|
|/redis/delete|Redis Cache 全体を削除します。|
|/redis/export/action|指定された形式のプレフィックス付きストレージ BLOB に Redis データをエクスポートします。|
|/redis/firewallRules/delete|Redis Cache の IP ファイアウォール規則を削除します。|
|/redis/firewallRules/read|Redis Cache の IP ファイアウォール規則を取得します。|
|/redis/firewallRules/write|Redis Cache の IP ファイアウォール規則を編集します。|
|/redis/forceReboot/action|キャッシュ インスタンスを強制的に再起動します。データが失われる可能性があります。|
|/redis/import/action|指定された形式のデータを複数の BLOB から Redis にインポートします。|
|/redis/linkedservers/delete|Redis Cache からリンク サーバーを削除します。|
|/redis/linkedservers/read|Redis Cache に関連付けられているリンク サーバーを取得します。|
|/redis/linkedservers/write|Redis Cache にリンク サーバーを追加します。|
|/redis/listKeys/action|管理ポータルで Redis Cache のアクセス キーの値を表示します。|
|/redis/listUpgradeNotifications/read|キャッシュのテナントに対する最新のアップグレード通知を一覧表示します。|
|/redis/locations/operationresults/read|前に "Location" ヘッダーがクライアントに返された、実行時間の長い操作の結果を取得します。|
|/redis/metricDefinitions/read|Redis Cache の利用可能なメトリックを取得します。|
|/redis/patchSchedules/delete|Redis Cache のパッチ スケジュールを削除します。|
|/redis/patchSchedules/read|Redis Cache のパッチ スケジュールを取得します。|
|/redis/patchSchedules/write|Redis Cache のパッチ スケジュールを変更します。|
|/redis/read|管理ポータルで Redis Cache の設定と構成を表示します。|
|/redis/regenerateKey/action|管理ポータルで Redis Cache のアクセス キーの値を変更します。|
|/redis/start/action|キャッシュ インスタンスを開始します。|
|/redis/stop/action|キャッシュ インスタンスを停止します。|
|/redis/write|管理ポータルで Redis Cache の設定と構成を変更します。|
|/register/action|"Microsoft.Cache" リソース プロバイダーをサブスクリプションに登録します。|
|/unregister/action|サブスクリプションへの "Microsoft.Cache" リソース プロバイダーの登録を解除します。|

## <a name="microsoftcapacity"></a>Microsoft.Capacity

| 操作 | [説明] |
|---|---|
|/register/action|キャパシティ リソース プロバイダーに登録し、キャパシティ リソースを作成できるようにします。|
|/reservationorders/action|予約を更新します。|
|/reservationorders/delete|予約を削除します。|
|/reservationorders/read|すべての予約を読み取ります。|
|/reservationorders/reservations/action|予約を更新します。|
|/reservationorders/reservations/delete|予約を削除します。|
|/reservationorders/reservations/read|すべての予約を読み取ります。|
|/reservationorders/reservations/revisions/read|すべての予約を読み取ります。|
|/reservationorders/reservations/write|予約を作成します。|
|/reservationorders/write|予約を作成します。|

## <a name="microsoftcdn"></a>Microsoft.Cdn

| 操作 | [説明] |
|---|---|
|/CheckNameAvailability/action||
|/CheckResourceUsage/action||
|/edgenodes/delete||
|/edgenodes/read||
|/edgenodes/write||
|/operationresults/delete||
|/operationresults/profileresults/CheckResourceUsage/action||
|/operationresults/profileresults/delete||
|/operationresults/profileresults/endpointresults/CheckResourceUsage/action||
|/operationresults/profileresults/endpointresults/customdomainresults/delete||
|/operationresults/profileresults/endpointresults/customdomainresults/ DisableCustomHttps/action||
|/operationresults/profileresults/endpointresults/customdomainresults/ EnableCustomHttps/action||
|/operationresults/profileresults/endpointresults/customdomainresults/read||
|/operationresults/profileresults/endpointresults/customdomainresults/write||
|/operationresults/profileresults/endpointresults/delete||
|/operationresults/profileresults/endpointresults/Load/action||
|/operationresults/profileresults/endpointresults/originresults/delete||
|/operationresults/profileresults/endpointresults/originresults/read||
|/operationresults/profileresults/endpointresults/originresults/write||
|/operationresults/profileresults/endpointresults/Purge/action||
|/operationresults/profileresults/endpointresults/read||
|/operationresults/profileresults/endpointresults/Start/action||
|/operationresults/profileresults/endpointresults/Stop/action||
|/operationresults/profileresults/endpointresults/ValidateCustomDomain/action||
|/operationresults/profileresults/endpointresults/write||
|/operationresults/profileresults/GenerateSsoUri/action||
|/operationresults/profileresults/GetSupportedOptimizationTypes/action||
|/operationresults/profileresults/read||
|/operationresults/profileresults/write||
|/operationresults/read||
|/operationresults/write||
|/operations/read||
|/profiles/CheckResourceUsage/action||
|/profiles/delete||
|/profiles/endpoints/CheckResourceUsage/action||
|/profiles/endpoints/customdomains/delete||
|/profiles/endpoints/customdomains/DisableCustomHttps/action||
|/profiles/endpoints/customdomains/EnableCustomHttps/action||
|/profiles/endpoints/customdomains/read||
|/profiles/endpoints/customdomains/write||
|/profiles/endpoints/delete||
|/profiles/endpoints/Load/action||
|/profiles/endpoints/origins/delete||
|/profiles/endpoints/origins/read||
|/profiles/endpoints/origins/write||
|/profiles/endpoints/providers/Microsoft.Insights/diagnosticSettings/read|リソースの診断設定を取得します。|
|/profiles/endpoints/providers/Microsoft.Insights/diagnosticSettings/write|リソースの診断設定を作成または更新します。|
|/profiles/endpoints/providers/Microsoft.Insights/logDefinitions/read|Microsoft.Cdn で使用可能なログを取得します。|
|/profiles/endpoints/Purge/action||
|/profiles/endpoints/read||
|/profiles/endpoints/Start/action||
|/profiles/endpoints/Stop/action||
|/profiles/endpoints/ValidateCustomDomain/action||
|/profiles/endpoints/write||
|/profiles/GenerateSsoUri/action||
|/profiles/GetSupportedOptimizationTypes/action||
|/profiles/read||
|/profiles/write||
|/register/action|CDN リソース プロバイダーにサブスクリプションを登録し、CDN プロファイルを作成できるようにします。|
|/ValidateProbe/action||

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

| 操作 | [説明] |
|---|---|
|/certificateOrders/certificates/Delete|既存の証明書を削除します。|
|/certificateOrders/certificates/Read|証明書の一覧を取得します。|
|/certificateOrders/certificates/Write|新しい証明書を追加するか、既存の証明書を更新します。|
|/certificateOrders/Delete|既存の AppServiceCertificate を削除します。|
|/certificateOrders/operations/Read|アプリ サービス証明書登録のすべての操作を一覧表示します。|
|/certificateOrders/Read|CertificateOrders の一覧を取得します。|
|/certificateOrders/reissue/Action|既存の certificateorder を再発行します。|
|/certificateOrders/renew/Action|既存の certificateorder を更新します。|
|/certificateOrders/resendEmail/Action|証明書の電子メールを再送信します。|
|/certificateOrders/resendRequestEmails/Action|要求の電子メールを別の電子メール アドレスに再送信します。|
|/certificateOrders/resendRequestEmails/Action|発行済みの App Service 証明書のサイト シールを取得します。|
|/certificateOrders/retrieveCertificateActions/Action|証明書のアクションの一覧を取得します。|
|/certificateOrders/retrieveEmailHistory/Action|証明書の電子メールの履歴を取得します。|
|/certificateOrders/verifyDomainOwnership/Action|ドメインの所有権を検証する|
|/certificateOrders/Write|新しい certificateOrder を追加するか、既存の certificateOrder を更新します。|
|/provisionGlobalAppServicePrincipalInUserTenant/Action|サービス アプリ プリンシパルのサービス プリンシパルをプロビジョニングします。|
|/register/action|Microsoft Certificates リソース プロバイダーをサブスクリプションに登録します。|
|/validateCertificateRegistrationInformation/Action|証明書購入オブジェクトを送信せずに検証します。|

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

| 操作 | [説明] |
|---|---|
|/capabilities/read|機能を表示します。|
|/checkDomainNameAvailability/action|指定されたドメイン名を使用できるかどうかを確認します。|
|/domainNames/active/write|アクティブなドメイン名を設定します。|
|/domainNames/availabilitySets/read|リソースの可用性セットを表示します。|
|/domainNames/capabilities/read|ドメイン名機能を表示します。|
|/domainNames/delete|リソースのドメイン名を削除します。|
|/domainNames/extensions/delete|ドメイン名拡張子を削除します。|
|/domainNames/extensions/operationStatuses/read|ドメイン名拡張子の操作の状態を読み取ります。|
|/domainNames/extensions/read|ドメイン名拡張子を返します。|
|/domainNames/extensions/write|ドメイン名拡張子を追加します。|
|/domainNames/internalLoadBalancers/delete|新しい内部負荷分散を削除します。|
|/domainNames/internalLoadBalancers/operationStatuses/read|ドメイン名の内部ロード バランサーの操作の状態を読み取ります。|
|/domainNames/internalLoadBalancers/read|内部ロード バランサーを取得します。|
|/domainNames/internalLoadBalancers/write|新しい内部負荷分散を作成します。|
|/domainNames/loadBalancedEndpointSets/operationStatuses/read|ドメイン名の負荷分散エンドポイント セットの操作の状態を読み取ります。|
|/domainNames/loadBalancedEndpointSets/read|負荷分散エンドポイント セットを表示します。|
|/domainNames/read|リソースのドメイン名を返します。|
|/domainNames/serviceCertificates/delete|使用するサービス証明書を削除します。|
|/domainNames/serviceCertificates/operationStatuses/read|ドメイン名のサービス証明書の操作の状態を読み取ります。|
|/domainNames/serviceCertificates/read|使用するサービス証明書を返します。|
|/domainNames/serviceCertificates/write|使用するサービス証明書を追加または変更します。|
|/domainNames/slots/delete|指定されたデプロイ スロットを削除します。|
|/domainNames/slots/operationStatuses/read|ドメイン名スロットの操作の状態を読み取ります。|
|/domainNames/slots/read|デプロイ スロットを表示します。|
|/domainNames/slots/roles/extensionReferences/delete|デプロイ スロット ロールの拡張子参照を削除します。|
|/domainNames/slots/roles/extensionReferences/operationStatuses/read|ドメイン名スロット ロールの拡張子参照の操作の状態を読み取ります。|
|/domainNames/slots/roles/extensionReferences/read|デプロイ スロット ロールの拡張子参照を返します。|
|/domainNames/slots/roles/extensionReferences/write|デプロイ スロット ロールの拡張子参照を追加または変更します。|
|/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/read|診断設定を取得します。|
|/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/write|診断設定を追加または変更します。|
|/domainNames/slots/roles/providers/Microsoft.Insights/metricDefinitions/read|メトリック定義を取得します。|
|/domainNames/slots/roles/read|デプロイ スロットのロールを取得します。|
|/domainNames/slots/roles/roleInstances/operationStatuses/read|ドメイン名スロット ロールのロール インスタンスの操作の状態を読み取ります。|
|/domainNames/slots/roles/roleInstances/read|ロール インスタンスを取得します。|
|/domainNames/slots/roles/roleInstances/rebuild/action|ロール インスタンスを再構築します。|
|/domainNames/slots/roles/roleInstances/reimage/action|ロール インスタンスを再イメージ化します。|
|/domainNames/slots/roles/roleInstances/restart/action|ロール インスタンスを再起動します。|
|/domainNames/slots/start/action|デプロイ スロットを開始します。|
|/domainNames/slots/state/start/write|デプロイ スロットを停止状態に変更します。|
|/domainNames/slots/state/stop/write|デプロイ スロットを開始状態に変更します。|
|/domainNames/slots/stop/action|デプロイ スロットを中断します。|
|/domainNames/slots/upgradeDomain/write|ドメインのウォーク アップグレードを行います。|
|/domainNames/slots/write|デプロイを作成または更新します。|
|/domainNames/swap/action|ステージング スロットを運用スロットに切り替えます。|
|/domainNames/write|リソースのドメイン名を追加または変更します。|
|/moveSubscriptionResources/action|すべてのクラシック リソースを別のサブスクリプションに移動します。|
|/operatingSystemFamilies/read|Microsoft Azure で利用できるゲスト オペレーティング システムのファミリと、それぞれのファミリで利用できるオペレーティング システムのバージョンを一覧表示します。|
|/operatingSystems/read|Microsoft Azure で現在利用できるゲスト オペレーティング システムのバージョンを一覧表示します。|
|/quotas/read|サブスクリプションのクォータを取得します。|
|/register/action|従来のコンピューティングに登録します。|
|/resourceTypes/skus/read|サポートされているリソースの種類の SKU の一覧を取得します。|
|/validateSubscriptionMoveAvailability/action|従来の移動操作でサブスクリプションが利用可能かどうかを検証します。|
|/virtualMachines/associatedNetworkSecurityGroups/delete|仮想マシンに関連付けられたネットワーク セキュリティ グループを削除します。|
|/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read|仮想マシンに関連付けられたネットワーク セキュリティ グループの操作の状態を読み取ります。|
|/virtualMachines/associatedNetworkSecurityGroups/read|仮想マシンに関連付けられたネットワーク セキュリティ グループを取得します。|
|/virtualMachines/associatedNetworkSecurityGroups/write|仮想マシンに関連付けられたネットワーク セキュリティ グループを追加します。|
|/virtualMachines/asyncOperations/read|可能な非同期操作を取得します。|
|/virtualMachines/attachDisk/action|仮想マシンにデータ ディスクを接続します。|
|/virtualMachines/delete|仮想マシンを削除します。|
|/virtualMachines/detachDisk/action|仮想マシンからデータ ディスクを切断します。|
|/virtualMachines/disks/read|データ ディスクの一覧を取得します。|
|/virtualMachines/downloadRemoteDesktopConnectionFile/action|仮想マシンの RDP ファイルをダウンロードします。|
|/virtualMachines/extensions/operationStatuses/read|仮想マシン拡張機能の操作の状態を読み取ります。|
|/virtualMachines/extensions/read|仮想マシン拡張機能を取得します。|
|/virtualMachines/extensions/write|仮想マシン拡張機能を追加します。|
|/virtualMachines/metrics/read|メトリックを取得します。|
|/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/delete|ネットワーク インターフェイスに関連付けられたネットワーク セキュリティ グループを削除します。|
|/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/ operationStatuses/read|仮想マシンに関連付けられたネットワーク セキュリティ グループの操作の状態を読み取ります。|
|/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/read|ネットワーク インターフェイスに関連付けられたネットワーク セキュリティ グループを取得します。|
|/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/write|ネットワーク インターフェイスに関連付けられたネットワーク セキュリティ グループを追加します。|
|/virtualMachines/operationStatuses/read|仮想マシンの操作の状態を読み取ります。|
|/virtualMachines/performMaintenance/action|仮想マシンに対してメンテナンスを実行します。|
|/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read|診断設定を取得します。|
|/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/write|診断設定を追加または変更します。|
|/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read|メトリック定義を取得します。|
|/virtualMachines/read|仮想マシンの一覧を取得します。|
|/virtualMachines/redeploy/action|仮想マシンを再デプロイします。|
|/virtualMachines/restart/action|仮想マシンを再起動します。|
|/virtualMachines/shutdown/action|仮想マシンをシャットダウンします。|
|/virtualMachines/start/action|仮想マシンを開始します。|
|/virtualMachines/stop/action|仮想マシンを停止します。|
|/virtualMachines/write|仮想マシンを追加または変更します。|

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

| 操作 | [説明] |
|---|---|
|/gatewaySupportedDevices/read|サポート対象デバイスの一覧を取得します。|
|/networkSecurityGroups/delete|ネットワーク セキュリティ グループを削除します。|
|/networkSecurityGroups/operationStatuses/read|ネットワーク セキュリティ グループの操作の状態を読み取ります。|
|/networkSecurityGroups/read|ネットワーク セキュリティ グループを取得します。|
|/networkSecurityGroups/securityRules/delete|セキュリティ規則を削除します。|
|/networkSecurityGroups/securityRules/operationStatuses/read|ネットワーク セキュリティ グループのセキュリティ規則の操作の状態を読み取ります。|
|/networkSecurityGroups/securityRules/read|セキュリティ規則を取得します。|
|/networkSecurityGroups/securityRules/write|セキュリティ規則を追加または更新します。|
|/networkSecurityGroups/write|新しいネットワーク セキュリティ グループを追加します。|
|/quotas/read|サブスクリプションのクォータを取得します。|
|/register/action|従来のネットワークに登録します。|
|/reservedIps/delete|予約済み IP を削除します。|
|/reservedIps/join/action|予約済み IP を結合します。|
|/reservedIps/link/action|予約済み IP をリンクします。|
|/reservedIps/operationStatuses/read|予約済み IP の操作の状態を読み取ります。|
|/reservedIps/read|予約済み IP を取得します。|
|/reservedIps/write|新しい予約済み IP を追加します。|
|/virtualNetworks/capabilities/read|機能を表示します。|
|/virtualNetworks/checkIPAddressAvailability/action|指定された IP アドレスを仮想ネットワーク内で使用できるかどうかを確認します。|
|/virtualNetworks/delete|仮想ネットワークを削除します。|
|/virtualNetworks/gateways/clientRevokedCertificates/delete|クライアント証明書の失効を取り消します。|
|/virtualNetworks/gateways/clientRevokedCertificates/read|失効したクライアント証明書を読み取ります。|
|/virtualNetworks/gateways/clientRevokedCertificates/write|クライアント証明書を失効させます。|
|/virtualNetworks/gateways/clientRootCertificates/delete|仮想ネットワーク ゲートウェイ クライアント証明書を削除します。|
|/virtualNetworks/gateways/clientRootCertificates/download/action|拇印によって証明書をダウンロードします。|
|/virtualNetworks/gateways/clientRootCertificates/listPackage/action|仮想ネットワーク ゲートウェイ証明書パッケージを一覧表示します。|
|/virtualNetworks/gateways/clientRootCertificates/read|クライアント ルート証明書を検索します。|
|/virtualNetworks/gateways/clientRootCertificates/write|新しいクライアント ルート証明書をアップロードします。|
|/virtualNetworks/gateways/connections/connect/action|サイト間ゲートウェイ接続を行います。|
|/virtualNetworks/gateways/connections/disconnect/action|サイト間ゲートウェイ接続を切断します。|
|/virtualNetworks/gateways/connections/read|接続の一覧を取得します。|
|/virtualNetworks/gateways/connections/test/action|サイト間ゲートウェイ接続をテストします。|
|/virtualNetworks/gateways/delete|仮想ネットワーク ゲートウェイを削除します。|
|/virtualNetworks/gateways/downloadDeviceConfigurationScript/action|デバイス構成スクリプトをダウンロードします。|
|/virtualNetworks/gateways/downloadDiagnostics/action|ゲートウェイ診断をダウンロードします。|
|/virtualNetworks/gateways/listCircuitServiceKey/action|回線サービス キーを取得します。|
|/virtualNetworks/gateways/listPackage/action|仮想ネットワーク ゲートウェイ パッケージを一覧表示します。|
|/virtualNetworks/gateways/operationStatuses/read|仮想ネットワーク ゲートウェイの操作の状態を読み取ります。|
|/virtualNetworks/gateways/packages/read|仮想ネットワーク ゲートウェイ パッケージを取得します。|
|/virtualNetworks/gateways/read|仮想ネットワーク ゲートウェイを取得します。|
|/virtualNetworks/gateways/startDiagnostics/action|仮想ネットワーク ゲートウェイの診断を開始します。|
|/virtualNetworks/gateways/stopDiagnostics/action|仮想ネットワーク ゲートウェイの診断を停止します。|
|/virtualNetworks/gateways/write|仮想ネットワーク ゲートウェイを追加します。|
|/virtualNetworks/join/action|仮想ネットワークに参加します。|
|/virtualNetworks/operationStatuses/read|仮想ネットワークの操作の状態を読み取ります。|
|/virtualNetworks/peer/action|仮想ネットワークを別の仮想ネットワークとピアリングします。|
|/virtualNetworks/read|仮想ネットワークを取得します。|
|/virtualNetworks/subnets/associatedNetworkSecurityGroups/delete|サブネットに関連付けられたネットワーク セキュリティ グループを削除します。|
|/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/read|仮想ネットワーク サブネットに関連付けられたネットワーク セキュリティ グループの操作の状態を読み取ります。|
|/virtualNetworks/subnets/associatedNetworkSecurityGroups/read|サブネットに関連付けられたネットワーク セキュリティ グループを取得します。|
|/virtualNetworks/subnets/associatedNetworkSecurityGroups/write|サブネットに関連付けられたネットワーク セキュリティ グループを追加します。|
|/virtualNetworks/write|新しい仮想ネットワークを追加します。|

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

| 操作 | [説明] |
|---|---|
|/capabilities/read|機能を表示します。|
|/checkStorageAccountAvailability/action|ストレージ アカウントを使用できるかどうかを確認します。|
|/disks/read|ストレージ アカウント ディスクを返します。|
|/images/read|イメージを返します。|
|/osImages/read|オペレーティング システム イメージを返します。|
|/publicImages/read|仮想マシンのパブリック イメージを取得します。|
|/quotas/read|サブスクリプションのクォータを取得します。|
|/register/action|従来のストレージに登録します。|
|/storageAccounts/delete|ストレージ アカウントを削除します。|
|/storageAccounts/disks/delete|指定されたストレージ アカウント ディスクを削除します。|
|/storageAccounts/disks/operationStatuses/read|リソースの操作の状態を読み取ります。|
|/storageAccounts/disks/read|ストレージ アカウント ディスクを返します。|
|/storageAccounts/disks/write|ストレージ アカウント ディスクを追加します。|
|/storageAccounts/images/delete|指定されたストレージ アカウント イメージを削除します。|
|/storageAccounts/images/read|ストレージ アカウント イメージを返します。|
|/storageAccounts/listKeys/action|ストレージ アカウントのアクセス キーを一覧表示します。|
|/storageAccounts/operationStatuses/read|リソースの操作の状態を読み取ります。|
|/storageAccounts/osImages/delete|指定されたストレージ アカウント オペレーティング システム イメージを削除します。|
|/storageAccounts/osImages/read|ストレージ アカウント オペレーティング システム イメージを返します。|
|/storageAccounts/read|特定のアカウントのストレージ アカウントを返します。|
|/storageAccounts/regenerateKey/action|ストレージ アカウントの既存のアクセス キーを再生成します。|
|/storageAccounts/services/diagnosticSettings/read|診断設定を取得します。|
|/storageAccounts/services/diagnosticSettings/write|診断設定を追加または変更します。|
|/storageAccounts/services/metricDefinitions/read|メトリック定義を取得します。|
|/storageAccounts/services/metrics/read|メトリックを取得します。|
|/storageAccounts/services/read|利用可能なサービスを取得します。|
|/storageAccounts/write|新しいストレージ アカウントを追加します。|

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

| 操作 | [説明] |
|---|---|
|/accounts/delete|API アカウントを削除します。|
|/accounts/listKeys/action|キーを一覧表示します。|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/read|リソースの診断設定を取得します。|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/write|リソースの診断設定を作成または更新します。|
|/accounts/providers/Microsoft.Insights/metricDefinitions/read|Cognitive Services で使用可能なメトリックを取得します。|
|/accounts/read|API アカウントを読み取ります。|
|/accounts/regenerateKey/action|キーを再生成します。|
|/accounts/skus/read|既存のリソースの使用可能な SKU を読み取ります。|
|/accounts/usages/read|既存のリソースのクォータ使用率を取得します。|
|/accounts/write|API アカウントを書き込みます。|
|/locations/checkSkuAvailability/action|サブスクリプションで使用可能な SKU を読み取ります。|
|/Operations/read|使用可能なすべての操作を一覧表示します。|
|/register/action|Cognitive Services にサブスクリプションを登録します。|
|/skus/read|Cognitive Services で使用可能な SKU を読み取ります。|

## <a name="microsoftcommerce"></a>Microsoft.Commerce

| 操作 | [説明] |
|---|---|
|/RateCard/read|特定のサブスクリプションのプラン データ、リソース/測定メタデータ、料金を返します。|
|/UsageAggregates/read|サブスクリプションによる Microsoft Azure の利用状況を取得します。 結果には、特定の時間の範囲における利用状況データ、サブスクリプション、およびリソースに関連する情報の集計が含まれます。|

## <a name="microsoftcompute"></a>Microsoft.Compute

| 操作 | [説明] |
|---|---|
|/availabilitySets/delete|可用性セットを削除します。|
|/availabilitySets/read|可用性セットのプロパティを取得します。|
|/availabilitySets/vmSizes/read|可用性セットの仮想マシンを作成または更新する際に使用できるサイズを一覧表示します。|
|/availabilitySets/write|新しい可用性セットを作成するか、既存の可用性セットを更新します。|
|/disks/beginGetAccess/action|BLOB へのアクセス用にディスクの SAS URI を取得します。|
|/disks/delete|ディスクを削除します。|
|/disks/endGetAccess/action|ディスクの SAS URI を取り消します。|
|/disks/read|ディスクのプロパティを取得します。|
|/disks/write|新しいディスクを作成するか、既存のディスクを更新します。|
|/images/delete|イメージを削除します。|
|/images/read|イメージのプロパティを取得します。|
|/images/write|新しいイメージを作成するか、既存のイメージを更新します。|
|/locations/capsOperations/read|非同期のキャップ操作の状態を取得します。|
|/locations/diskOperations/read|非同期のディスク操作の状態を取得します。|
|/locations/operations/read|非同期操作の状態を取得します。|
|/locations/publishers/artifacttypes/offers/read|プラットフォーム イメージ プランのプロパティを取得します。|
|/locations/publishers/artifacttypes/offers/skus/read|プラットフォーム イメージ SKU のプロパティを取得します。|
|/locations/publishers/artifacttypes/offers/skus/versions/read|プラットフォーム イメージ バージョンのプロパティを取得します。|
|/locations/publishers/artifacttypes/types/read|VMExtension 型のプロパティを取得します。|
|/locations/publishers/artifacttypes/types/versions/read|VMExtension バージョンのプロパティを取得します。|
|/locations/publishers/read|パブリッシャーのプロパティを取得します。|
|/locations/runCommands/read|場所で使用可能な実行コマンドを一覧表示します。|
|/locations/usages/read|その場所のサブスクリプションのコンピューティング リソースについて、サービスの上限と現在の使用容量を取得します。|
|/locations/vmSizes/read|その場所で使用できる仮想マシンのサイズを一覧表示します。|
|/operations/read|Microsoft.Compute リソース プロバイダーで使用できる操作を一覧表示します。|
|/register/action|Microsoft.Compute リソース プロバイダーにサブスクリプションを登録します。|
|/restorePointCollections/delete|復元ポイント コレクションと、コレクションに含まれている復元ポイントを削除します。|
|/restorePointCollections/read|復元ポイント コレクションのプロパティを取得します。|
|/restorePointCollections/restorePoints/delete|復元ポイントを削除します。|
|/restorePointCollections/restorePoints/read|復元ポイントのプロパティを取得します。|
|/restorePointCollections/restorePoints/retrieveSasUris/action|復元ポイントのプロパティを BLOB SAS URI と共に取得します。|
|/restorePointCollections/restorePoints/write|新しい復元ポイントを作成します。|
|/restorePointCollections/write|新しい復元ポイント コレクションを作成するか、既存の復元ポイント コレクションを更新します。|
|/sharedVMImages/delete|SharedVMImage を削除します。|
|/sharedVMImages/read|SharedVMImage のプロパティを取得します。|
|/sharedVMImages/versions/delete|SharedVMImageVersion を削除します。|
|/sharedVMImages/versions/read|SharedVMImageVersion のプロパティを取得します。|
|/sharedVMImages/versions/replicate/action|ターゲット リージョンに SharedVMImageVersion をレプリケートします。|
|/sharedVMImages/versions/write|新しい SharedVMImageVersion を作成するか、既存のものを更新します。|
|/sharedVMImages/write|新しい SharedVMImage を作成するか、既存のものを更新します。|
|/skus/read|サブスクリプションで使用可能な Microsoft.Compute SKU の一覧を取得します。|
|/snapshots/beginGetAccess/action|BLOB アクセス用のスナップショットの SAS URI を取得します。|
|/snapshots/delete|スナップショットを削除します。|
|/snapshots/endGetAccess/action|スナップショットの SAS URI を取り消します。|
|/snapshots/read|スナップショットのプロパティを取得します。|
|/snapshots/write|新しいスナップショットを作成するか、既存のスナップショットを更新します。|
|/virtualMachines/capture/action|仮想ハード ディスクをコピーして仮想マシンをキャプチャし、テンプレートを生成します。このテンプレートを使用して、類似する仮想マシンを作成できます。|
|/virtualMachines/convertToManagedDisks/action|仮想マシンの BLOB ベースのディスクを管理ディスクに変換します。|
|/virtualMachines/deallocate/action|仮想マシンを電源オフにし、コンピューティング リソースを解放します。|
|/virtualMachines/delete|仮想マシンを削除します。|
|/virtualMachines/extensions/delete|仮想マシン拡張機能を削除します。|
|/virtualMachines/extensions/read|仮想マシン拡張機能のプロパティを取得します。|
|/virtualMachines/extensions/write|新しい仮想マシン拡張機能を作成するか、既存の仮想マシン拡張機能を更新します。|
|/virtualMachines/generalize/action|仮想マシンの状態を [一般化] に設定し、仮想マシンのキャプチャを準備します。|
|/virtualMachines/instanceView/read|仮想マシンとそのリソースの詳細なランタイムの状態を取得します。|
|/virtualMachines/performMaintenance/action|VM でメンテナンス操作を実行します。|
|/virtualMachines/powerOff/action|仮想マシンを電源オフにします。 仮想マシンの料金は引き続き課金されます。|
|/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read|仮想マシンのメトリック定義を読み取ります。|
|/virtualMachines/read|仮想マシンのプロパティを取得します。|
|/virtualMachines/redeploy/action|仮想マシンを再デプロイします。|
|/virtualMachines/reimage/action|差分ディスクを使用する仮想マシンを再イメージ化します。|
|/virtualMachines/restart/action|仮想マシンを再起動します。|
|/virtualMachines/runCommand/action|仮想マシンで定義済みのスクリプトを実行します。|
|/virtualMachines/start/action|仮想マシンを起動します。|
|/virtualMachines/vmSizes/read|仮想マシンを更新する際に使用できるサイズを一覧表示します。|
|/virtualMachines/write|新しい仮想マシンを作成するか、既存の仮想マシンを更新します。|
|/virtualMachineScaleSets/deallocate/action|仮想マシン スケール セットのインスタンスのコンピューティング リソースを電源オフにして解放します。 |
|/virtualMachineScaleSets/delete|仮想マシン スケール セットを削除します。|
|/virtualMachineScaleSets/delete/action|仮想マシン スケール セットのインスタンスを削除します。|
|/virtualMachineScaleSets/extensions/delete|仮想マシン スケール セット拡張機能を削除します。|
|/virtualMachineScaleSets/extensions/read|仮想マシン スケール セット拡張機能のプロパティを取得します。|
|/virtualMachineScaleSets/extensions/write|新しい仮想マシン スケール セット拡張機能を作成するか、既存のものを更新します。|
|/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/action|サービス ファブリック仮想マシン スケール セットのプラットフォーム更新ドメインを手動で移動して、スタックしている保留中の更新を終了します。|
|/virtualMachineScaleSets/instanceView/read|仮想マシン スケール セットのインスタンス ビューを取得します。|
|/virtualMachineScaleSets/manualUpgrade/action|インスタンスを仮想マシン スケール セットの最新モデルに手動で更新します。|
|/virtualMachineScaleSets/networkInterfaces/read|仮想マシン スケール セットのすべてのネットワーク インターフェイスのプロパティを取得します。|
|/virtualMachineScaleSets/osUpgradeHistory/read|仮想マシン スケール セットの OS アップグレードの履歴を取得します。|
|/virtualMachineScaleSets/performMaintenance/action|仮想マシン スケール セットのインスタンスで計画的なメンテナンスを実行します。|
|/virtualMachineScaleSets/powerOff/action|仮想マシン スケール セットのインスタンスを電源オフにします。|
|/virtualMachineScaleSets/providers/Microsoft.Insights/metricDefinitions/read|仮想マシン スケール セットのメトリック定義を読み取ります。|
|/virtualMachineScaleSets/publicIPAddresses/read|仮想マシン スケール セットのすべてのパブリック IP アドレスのプロパティを取得します。|
|/virtualMachineScaleSets/read|仮想マシン スケール セットのプロパティを取得します。|
|/virtualMachineScaleSets/redeploy/action|仮想マシン スケール セットのインスタンスを再デプロイします。|
|/virtualMachineScaleSets/reimage/action|仮想マシン スケール セットのインスタンスを再イメージ化します。|
|/virtualMachineScaleSets/restart/action|仮想マシン スケール セットのインスタンスを再起動します。|
|/virtualMachineScaleSets/rollingUpgrades/cancel/action|仮想マシン スケール セットのローリング アップグレードを取り消します。|
|/virtualMachineScaleSets/rollingUpgrades/read|仮想マシン スケール セットの最新のローリング アップグレードの状態を取得します。|
|/virtualMachineScaleSets/scale/action|既存の仮想マシン スケール セットを、指定されたインスタンス数にスケールイン/スケールアウトできるかどうかを確認します。|
|/virtualMachineScaleSets/skus/read|既存の仮想マシン スケール セットの有効な SKU を一覧表示します。|
|/virtualMachineScaleSets/start/action|仮想マシン スケール セットのインスタンスを起動します。|
|/virtualMachineScaleSets/virtualMachines/deallocate/action|VM スケール セット内の仮想マシンのコンピューティング リソースを電源オフにして解放します。|
|/virtualMachineScaleSets/virtualMachines/delete|VM スケール セット内の特定の仮想マシンを削除します。|
|/virtualMachineScaleSets/virtualMachines/instanceView/read|VM スケール セット内の仮想マシンのインスタンス ビューを取得します。|
|/virtualMachineScaleSets/virtualMachines/networkInterfaces/ ipConfigurations/publicIPAddresses/read|仮想マシン スケール セットを使って作成されたパブリック IP アドレスのプロパティを取得します。 仮想マシン スケール セットは、ipconfiguration (プライベート IP) あたり最大で 1 つのパブリック IP を作成できます。|
|/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/read|仮想マシン スケール セットを使って作成されたネットワーク インターフェイスの 1 つまたはすべての IP 構成のプロパティを取得します。 IP 構成は、プライベート IP を表します。|
|/virtualMachineScaleSets/virtualMachines/networkInterfaces/read|仮想マシン スケール セットを使って作成された仮想マシンの 1 つまたはすべてのネットワーク インターフェイスのプロパティを取得します。|
|/virtualMachineScaleSets/virtualMachines/performMaintenance/action|仮想マシン スケール セットの仮想マシン インスタンスで計画的なメンテナンスを実行します。|
|/virtualMachineScaleSets/virtualMachines/powerOff/action|VM スケール セット内の仮想マシン インスタンスを電源オフにします。|
|/virtualMachineScaleSets/virtualMachines/providers/ Microsoft.Insights/metricDefinitions/read|スケール セットの仮想マシンのメトリック定義を読み取ります。|
|/virtualMachineScaleSets/virtualMachines/read|VM スケール セット内の仮想マシンのプロパティを取得します。|
|/virtualMachineScaleSets/virtualMachines/redeploy/action|仮想マシン スケール セット内の仮想マシン インスタンスを再デプロイします。|
|/virtualMachineScaleSets/virtualMachines/reimage/action|仮想マシン スケール セット内の仮想マシン インスタンスを再イメージ化します。|
|/virtualMachineScaleSets/virtualMachines/restart/action|VM スケール セット内の仮想マシン インスタンスを再起動します。|
|/virtualMachineScaleSets/virtualMachines/start/action|VM スケール セット内の仮想マシン インスタンスを起動します。|
|/virtualMachineScaleSets/virtualMachines/write|VM スケール セット内の仮想マシンのプロパティを更新します。|
|/virtualMachineScaleSets/write|新しい仮想マシン スケール セットを作成するか、既存のものを更新します。|

## <a name="microsoftconsumption"></a>Microsoft.Consumption

| 操作 | [説明] |
|---|---|
|/balances/read|管理グループの請求期間の使用状況の概要を一覧表示します。|
|/budgets/read|サブスクリプションまたは管理グループごとに予算を一覧表示します。|
|/budgets/write|サブスクリプションまたは管理グループで予算を作成、更新、削除します。|
|/marketplaces/read|EA および WebDirect サブスクリプションのスコープに対する Marketplace リソース使用状況の詳細を一覧表示します。|
|/operations/read|Microsoft.Consumption リソース プロバイダーによってサポートされるすべての操作を一覧表示します。|
|/pricesheets/read|サブスクリプションまたは管理グループの価格シート データを一覧表示します。|
|/reservationDetails/read|予約注文または管理グループごとに予約インスタンスの使用状況の詳細を一覧表示します。 詳細データは、インスタンス別の日レベル別です。|
|/reservationSummaries/read|予約注文または管理グループごとに予約インスタンスの使用状況の概要を一覧表示します。 概要データは、月レベル別または日レベル別です。|
|/reservationTransactions/read|管理グループ別に予約インスタンスのトランザクション履歴を一覧表示します。|
|/terms/read|サブスクリプションまたは管理グループの条件を一覧表示します。|
|/usageDetails/read|EA および WebDirect サブスクリプションのスコープに対する使用状況の詳細を一覧表示します。|

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

| 操作 | [説明] |
|---|---|
|/containerGroups/containers/logs/read|特定のコンテナーのログを取得します。|
|/containerGroups/delete|特定のコンテナー グループを削除します。|
|/containerGroups/providers/Microsoft.Insights/diagnosticSettings/read|コンテナー グループの診断設定を取得します。|
|/containerGroups/providers/Microsoft.Insights/diagnosticSettings/write|コンテナー グループの診断設定を作成または更新します。|
|/containerGroups/providers/Microsoft.Insights/metricDefinitions/read|コンテナー グループで使用可能なメトリックを取得します。|
|/containerGroups/read|すべてのコンテナー グループを取得します。|
|/containerGroups/write|特定のコンテナー グループを作成または更新します。|

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

| 操作 | [説明] |
|---|---|
|/checkNameAvailability/read|コンテナー レジストリ名が使用可能かどうかを確認します。|
|/locations/operationResults/read|非同期操作の結果を取得します。|
|/operations/read|使用可能なすべての Azure Container Registry REST API 操作を一覧表示します。|
|/register/action|コンテナー レジストリ リソース プロバイダーにサブスクリプションを登録し、コンテナー レジストリを作成できるようにします。|
|/registries/delete|コンテナー レジストリを削除します。|
|/registries/eventGridFilters/delete|コンテナー レジストリからイベント グリッド フィルターを削除します。|
|/registries/eventGridFilters/read|指定されたイベント グリッド フィルターのプロパティを取得するか、指定されたコンテナー レジストリのすべてのイベント グリッド フィルターを一覧表示します。|
|/registries/eventGridFilters/write|指定されたパラメーターでコンテナー レジストリのイベント グリッド フィルターを作成または更新します。|
|/registries/listCredentials/action|指定されたコンテナー レジストリのログイン資格情報を一覧表示します。|
|/registries/listUsages/read|指定されたコンテナー レジストリのクォータ使用状況を一覧表示します。|
|/registries/operationStatuses/read|レジストリの非同期操作の状態を取得します。|
|/registries/read|指定されたコンテナー レジストリのプロパティを取得するか、指定されたリソース グループまたはサブスクリプションのすべてのコンテナー レジストリを一覧表示します。|
|/registries/regenerateCredential/action|指定されたコンテナー レジストリのログイン資格情報の 1 つを再生成します。|
|/registries/replications/delete|コンテナー レジストリからレプリケーションを削除します。|
|/registries/replications/operationStatuses/read|レプリケーションの非同期操作の状態を取得します。|
|/registries/replications/read|指定されたレプリケーションのプロパティを取得するか、指定されたコンテナー レジストリのすべてのレプリケーションを一覧表示します。|
|/registries/replications/write|指定されたパラメーターでコンテナー レジストリのレプリケーションを作成または更新します。|
|/registries/webhooks/delete|コンテナー レジストリから webhook を削除します。|
|/registries/webhooks/getCallbackConfig/action|webhook のサービス URI とカスタム ヘッダーの構成を取得します。|
|/registries/webhooks/listEvents/action|指定された webhook の最近のイベントを一覧表示します。|
|/registries/webhooks/operationStatuses/read|webhook の非同期操作の状態を取得します。|
|/registries/webhooks/ping/action|webhook に送信される ping イベントをトリガーします。|
|/registries/webhooks/read|指定された webhook のプロパティを取得するか、指定されたコンテナー レジストリのすべての webhook を一覧表示します。|
|/registries/webhooks/write|指定されたパラメーターでコンテナー レジストリの webhook を作成または更新します。|
|/registries/write|指定されたパラメーターでコンテナー レジストリを作成または更新します。|

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

| 操作 | [説明] |
|---|---|
|/containerServices/delete|指定されたコンテナー サービスを削除します。|
|/containerServices/read|指定されたコンテナー サービスを取得します。|
|/containerServices/write|指定されたコンテナー サービスを追加または更新します。|

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

| 操作 | [説明] |
|---|---|
|/applications/delete|削除操作。|
|/applications/listSecrets/action|シークレットを一覧表示します。|
|/applications/listSingleSignOnToken/action|シングル サインオン トークンを読み取ります。|
|/applications/read|読み取り操作。|
|/applications/write|書き込み操作。|
|/applications/write|書き込み操作。|
|/listCommunicationPreference/action|通信設定を一覧表示します。|
|/operations/read|操作を読み取ります。|
|/updateCommunicationPreference/action|通信設定を更新します。|

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

| 操作 | [説明] |
|---|---|
|/hubs/adobemetadata/action|Azure Customer Insights の Adobe メタデータを作成または更新します。|
|/hubs/adobemetadata/read|Azure Customer Insights の Adobe メタデータを読み取ります。|
|/hubs/authorizationPolicies/delete|Azure Customer Insights の Shared Access Signature ポリシーを削除します。|
|/hubs/authorizationPolicies/read|Azure Customer Insights の Shared Access Signature ポリシーを読み取ります。|
|/hubs/authorizationPolicies/regeneratePrimaryKey/action|Azure Customer Insights の Shared Access Signature ポリシーのプライマリ キーを再生成します。|
|/hubs/authorizationPolicies/regenerateSecondaryKey/action|Azure Customer Insights の Shared Access Signature ポリシーのセカンダリ キーを再生成します。|
|/hubs/authorizationPolicies/write|Azure Customer Insights の Shared Access Signature ポリシーを作成または更新します。|
|/hubs/connectors/activate/action|Azure Customer Insights コネクタをアクティブにします。|
|/hubs/connectors/activate/action|Azure Customer Insights コネクタをアクティブにします。|
|/hubs/connectors/delete|Azure Customer Insights コネクタを削除します。|
|/hubs/connectors/getruntimestatus/action|Azure Customer Insights コネクタのランタイムの状態を取得します。|
|/hubs/connectors/mappings/activate/action|Azure Customer Insights コネクタ マッピングをアクティブにします。|
|/hubs/connectors/mappings/delete|Azure Customer Insights コネクタ マッピングを削除します。|
|/hubs/connectors/mappings/operations/read|Azure Customer Insights コネクタ マッピングの操作結果を読み取ります。|
|/hubs/connectors/mappings/read|Azure Customer Insights コネクタ マッピングを読み取ります。|
|/hubs/connectors/mappings/write|Azure Customer Insights コネクタ マッピングを作成または更新します。|
|/hubs/connectors/operations/read|Azure Customer Insights コネクタの操作結果を読み取ります。|
|/hubs/connectors/read|Azure Customer Insights コネクタを読み取ります。|
|/hubs/connectors/saveauthinfo/action|Azure Customer Insights コネクタの認証情報を作成または更新します。|
|/hubs/connectors/update/action|Azure Customer Insights コネクタを更新します。|
|/hubs/connectors/write|Azure Customer Insights コネクタを作成または更新します。|
|/hubs/crmmetadata/action|Azure Customer Insights の Crm メタデータを作成または更新します。|
|/hubs/crmmetadata/read|Azure Customer Insights の Crm メタデータを読み取ります。|
|/hubs/delete|Azure Customer Insights Hub を削除します。|
|/hubs/gdpr/delete|Azure Customer Insights Gdpr を削除します。|
|/hubs/gdpr/read|Azure Customer Insights Gdpr を読み取ります。|
|/hubs/gdpr/write|Azure Customer Insights Gdpr を作成または更新します。|
|/hubs/getbillingcredits/read|Azure Customer Insights Hub の請求先クレジットを取得します。|
|/hubs/getbillinghistory/read|Azure Customer Insights Hub の請求履歴を取得します。|
|/hubs/images/delete|Azure Customer Insights イメージを削除します。|
|/hubs/images/read|Azure Customer Insights イメージを読み取ります。|
|/hubs/images/write|Azure Customer Insights イメージを作成または更新します。|
|/hubs/interactions/delete|Azure Customer Insights の対話を削除します。|
|/hubs/interactions/operations/read|Azure Customer Insights の対話の操作結果を読み取ります。|
|/hubs/interactions/read|Azure Customer Insights の対話を読み取ります。|
|/hubs/interactions/suggestrelationshiplinks/action|Azure Customer Insights の対話に対するリレーションシップ リンクを提案します。|
|/hubs/interactions/write|Azure Customer Insights の対話を作成または更新します。|
|/hubs/kpi/delete|Azure Customer Insights の主要業績評価指標を削除します。|
|/hubs/kpi/operations/read|Azure Customer Insights の主要業績評価指標の操作結果を読み取ります。|
|/hubs/kpi/read|Azure Customer Insights の主要業績評価指標を読み取ります。|
|/hubs/kpi/reprocess/action|Azure Customer Insights の主要業績評価指標を再処理します。|
|/hubs/kpi/write|Azure Customer Insights の主要業績評価指標を作成または更新します。|
|/hubs/links/delete|Azure Customer Insights のリンクを削除します。|
|/hubs/links/operations/read|Azure Customer Insights のリンクの操作結果を読み取ります。|
|/hubs/links/read|Azure Customer Insights のリンクを読み取ります。|
|/hubs/links/write|Azure Customer Insights のリンクを作成または更新します。|
|/hubs/msemetadata/action|Azure Customer Insights の Mse メタデータを作成または更新します。|
|/hubs/msemetadata/read|Azure Customer Insights の Mse メタデータを読み取ります。|
|/hubs/operationresults/read|Azure Customer Insights Hub の操作結果を取得します。|
|/hubs/predictions/delete|Azure Customer Insights の予測を削除します。|
|/hubs/predictions/operations/read|Azure Customer Insights の予測の操作結果を読み取ります。|
|/hubs/predictions/read|Azure Customer Insights の予測を読み取ります。|
|/hubs/predictions/write|Azure Customer Insights の予測を作成または更新します。|
|/hubs/predictivematchpolicies/delete|Azure Customer Insights の予測一致ポリシーを削除します。|
|/hubs/predictivematchpolicies/operations/read|Azure Customer Insights の予測一致ポリシーの操作結果を読み取ります。|
|/hubs/predictivematchpolicies/read|Azure Customer Insights の予測一致ポリシーを読み取ります。|
|/hubs/predictivematchpolicies/write|Azure Customer Insights の予測一致ポリシーを作成または更新します。|
|/hubs/profiles/delete|Azure Customer Insights プロファイルを削除します。|
|/hubs/profiles/operations/read|Azure Customer Insights プロファイルの操作結果を読み取ります。|
|/hubs/profiles/read|Azure Customer Insights プロファイルを読み取ります。|
|/hubs/profiles/write|Azure Customer Insights プロファイルを書き込みます。|
|/hubs/providers/Microsoft.Insights/diagnosticSettings/read|リソースの診断設定を取得します。|
|/hubs/providers/Microsoft.Insights/diagnosticSettings/write|リソースの診断設定を作成または更新します。|
|/hubs/providers/Microsoft.Insights/logDefinitions/read|リソースの利用可能なログを取得します。|
|/hubs/providers/Microsoft.Insights/metricDefinitions/read|リソースの利用可能なメトリックを取得します。|
|/hubs/read|Azure Customer Insights Hub を読み取ります。|
|/hubs/relationshiplinks/delete|Azure Customer Insights のリレーションシップ リンクを削除します。|
|/hubs/relationshiplinks/operations/read|Azure Customer Insights のリレーションシップ リンクの操作結果を読み取ります。|
|/hubs/relationshiplinks/read|Azure Customer Insights のリレーションシップ リンクを読み取ります。|
|/hubs/relationshiplinks/write|Azure Customer Insights のリレーションシップ リンクを作成または更新します。|
|/hubs/relationships/delete|Azure Customer Insights のリレーションシップを削除します。|
|/hubs/relationships/operations/read|Azure Customer Insights のリレーションシップの操作結果を読み取ります。|
|/hubs/relationships/read|Azure Customer Insights のリレーションシップを読み取ります。|
|/hubs/relationships/write|Azure Customer Insights のリレーションシップを作成または更新します。|
|/hubs/roleAssignments/delete|Azure Customer Insights の RBAC の割り当てを削除します。|
|/hubs/roleAssignments/operations/read|Azure Customer Insights の RBAC 割り当ての操作結果を読み取ります。|
|/hubs/roleAssignments/read|Azure Customer Insights の RBAC の割り当てを読み取ります。|
|/hubs/roleAssignments/write|Azure Customer Insights の RBAC の割り当てを作成または更新します。|
|/hubs/roles/read|Azure Customer Insights の RBAC ロールを読み取ります。|
|/hubs/salesforcemetadata/action|Azure Customer Insights の SalesForce メタデータを作成または更新します。|
|/hubs/salesforcemetadata/read|Azure Customer Insights の SalesForce メタデータを読み取ります。|
|/hubs/segments/delete|Azure Customer Insights のセグメントを削除します。|
|/hubs/segments/dynamic/action|Azure Customer Insights の動的セグメントを管理します。|
|/hubs/segments/read|Azure Customer Insights のセグメントを読み取ります。|
|/hubs/segments/static/action|Azure Customer Insights の静的セグメントを管理します。|
|/hubs/segments/write|Azure Customer Insights のセグメントを作成または更新します。|
|/hubs/sqlconnectionstrings/delete|Azure Customer Insights の SqlConnectionStrings を削除します。|
|/hubs/sqlconnectionstrings/read|Azure Customer Insights の SqlConnectionStrings を読み取ります。|
|/hubs/sqlconnectionstrings/write|Azure Customer Insights の SqlConnectionStrings を作成または更新します。|
|/hubs/suggesttypeschema/action|サンプル データから提案タイプ スキーマを生成します。|
|/hubs/tenantmanagement/read|Azure Customer Insights Hub の設定を管理します。|
|/hubs/views/delete|Azure Customer Insights のアプリ ビューを削除します。|
|/hubs/views/read|Azure Customer Insights のアプリ ビューを読み取ります。|
|/hubs/views/write|Azure Customer Insights のアプリ ビューを作成または更新します。|
|/hubs/widgettypes/read|Azure Customer Insights のアプリ ウィジェット タイプを読み取ります。|
|/hubs/write|Azure Customer Insights Hub を作成または更新します。|
|/operations/read|Azure Customer Insights の API メタデータを読み取ります。|
|/register/action|Customer Insights リソース プロバイダーにサブスクリプションを登録し、Customer Insights リソースを作成できるようにします。|
|/unregister/action|Customer Insights リソース プロバイダーにサブスクリプションの登録を解除します。|

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

| 操作 | [説明] |
|---|---|
|/catalogs/delete|カタログを削除します。|
|/catalogs/read|サブスクリプションまたはリソース グループでカタログのプロパティを取得します。|
|/catalogs/write|カタログを作成するか、カタログのタグとプロパティを更新します。|
|/checkNameAvailability/action|テナントのカタログ名を使用できるかどうかを確認します。|
|/operations/read|Microsoft.DataCatalog リソース プロバイダーで使用できる操作を一覧表示します。|
|/register/action|Microsoft.DataCatalog リソース プロバイダーにサブスクリプションを登録します。|
|/unregister/action|Microsoft.DataCatalog リソース プロバイダーにサブスクリプションを登録解除します。|

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

| 操作 | [説明] |
|---|---|
|/datafactories/providers/Microsoft.Insights/diagnosticSettings/read|リソースの診断設定を取得します。|
|/datafactories/providers/Microsoft.Insights/diagnosticSettings/write|リソースの診断設定を作成または更新します。|
|/datafactories/providers/Microsoft.Insights/metricDefinitions/read|datafactories で使用可能なメトリックを取得します。|
|/factories/providers/Microsoft.Insights/diagnosticSettings/read|リソースの診断設定を取得します。|
|/factories/providers/Microsoft.Insights/diagnosticSettings/write|リソースの診断設定を作成または更新します。|
|/factories/providers/Microsoft.Insights/logDefinitions/read|ファクトリで使用可能なログを取得します。|
|/factories/providers/Microsoft.Insights/metricDefinitions/read|ファクトリで使用可能なメトリックを取得します。|

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

| 操作 | [説明] |
|---|---|
|/accounts/computePolicies/delete|コンピューティング ポリシーを削除します。|
|/accounts/computePolicies/read|コンピューティング ポリシーに関する情報を取得します。|
|/accounts/computePolicies/write|コンピューティング ポリシーを作成または更新します。|
|/accounts/dataLakeStoreAccounts/delete|DataLakeAnalytics アカウントから DataLakeStore アカウントのリンクを解除します。|
|/accounts/dataLakeStoreAccounts/read|DataLakeAnalytics アカウントのリンクされた DataLakeStore アカウントに関する情報を取得します。|
|/accounts/dataLakeStoreAccounts/write|DataLakeAnalytics アカウントのリンクされた DataLakeStore アカウントを作成または更新します。|
|/accounts/delete|DataLakeAnalytics アカウントを削除します。|
|/accounts/firewallRules/delete|ファイアウォール規則を削除します。|
|/accounts/firewallRules/read|ファイアウォール規則に関する情報を取得します。|
|/accounts/firewallRules/write|ファイアウォール規則を作成または更新します。|
|/accounts/operationResults/read|DataLakeAnalytics アカウントの操作の結果を取得します。|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/read|DataLakeAnalytics アカウントの診断設定を取得します。|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/write|DataLakeAnalytics アカウントの診断設定を作成または更新します。|
|/accounts/providers/Microsoft.Insights/logDefinitions/read|DataLakeAnalytics アカウントで使用可能なログを取得します。|
|/accounts/providers/Microsoft.Insights/metricDefinitions/read|DataLakeAnalytics アカウントで使用可能なメトリックを取得します。|
|/accounts/read|既存の DataLakeAnalytics アカウントに関する情報を取得します。|
|/accounts/storageAccounts/Containers/listSasTokens/action|DataLakeAnalytics アカウントのリンクされたストレージ アカウントのストレージ コンテナーの SAS トークンを一覧表示します。|
|/accounts/storageAccounts/Containers/read|DataLakeAnalytics アカウントのリンクされたストレージ アカウントのコンテナーを取得します。|
|/accounts/storageAccounts/delete|DataLakeAnalytics アカウントからストレージ アカウントをリンク解除します。|
|/accounts/storageAccounts/read|DataLakeAnalytics アカウントのリンクされたストレージ アカウントに関する情報を取得します。|
|/accounts/storageAccounts/write|DataLakeAnalytics アカウントのリンクされたストレージ アカウントを作成または更新します。|
|/accounts/TakeOwnership/action|他のユーザーによって送信されたジョブを取り消すアクセス許可を付与します。|
|/accounts/write|DataLakeAnalytics アカウントを作成または更新します。|
|/locations/capability/read|DataLakeAnalytics の使用に関するサブスクリプションの機能の情報を取得します。|
|/locations/checkNameAvailability/action|DataLakeAnalytics アカウントの名前を使用できるかどうかを確認します。|
|/locations/operationResults/read|DataLakeAnalytics アカウントの操作の結果を取得します。|
|/operations/read|DataLakeAnalytics で使用可能な操作を取得します。|
|/register/action|DataLakeAnalytics にサブスクリプションを登録します。|

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

| 操作 | [説明] |
|---|---|
|/accounts/delete|DataLakeStore アカウントを削除します。|
|/accounts/enableKeyVault/action|DataLakeStore アカウントの KeyVault を有効にします。|
|/accounts/firewallRules/delete|ファイアウォール規則を削除します。|
|/accounts/firewallRules/read|ファイアウォール規則に関する情報を取得します。|
|/accounts/firewallRules/write|ファイアウォール規則を作成または更新します。|
|/accounts/operationResults/read|DataLakeStore アカウントの操作の結果を取得します。|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/read|DataLakeStore アカウントの診断設定を取得します。|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/write|DataLakeStore アカウントの診断設定を作成または更新します。|
|/accounts/providers/Microsoft.Insights/logDefinitions/read|DataLakeStore アカウントで使用可能なログを取得します。|
|/accounts/providers/Microsoft.Insights/metricDefinitions/read|DataLakeStore アカウントで使用可能なメトリックを取得します。|
|/accounts/read|既存の DataLakeStore アカウントに関する情報を取得します。|
|/accounts/Superuser/action|Microsoft.Authorization/roleAssignments/write で許可されているときに、Data Lake Store でのスーパーユーザーを許可します。|
|/accounts/trustedIdProviders/delete|信頼できる ID プロバイダーを削除します。|
|/accounts/trustedIdProviders/read|信頼できる ID プロバイダーに関する情報を取得します。|
|/accounts/trustedIdProviders/write|信頼できる ID プロバイダーを作成または更新します。|
|/accounts/write|DataLakeStore アカウントを作成または更新します。|
|/locations/capability/read|DataLakeStore の使用に関するサブスクリプションの機能の情報を取得します。|
|/locations/checkNameAvailability/action|DataLakeStore アカウントの名前を使用できるかどうかを確認します。|
|/locations/operationResults/read|DataLakeStore アカウントの操作の結果を取得します。|
|/operations/read|DataLakeStore で使用可能な操作を取得します。|
|/register/action|DataLakeStore にサブスクリプションを登録します。|

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

| 操作 | [説明] |
|---|---|
|/locations/performanceTiers/read|使用可能なパフォーマンス レベルの一覧を返します。|
|/performanceTiers/read|使用可能なパフォーマンス レベルの一覧を返します。|
|/servers/delete|既存のサーバーを削除します。|
|/servers/firewallRules/delete|既存のファイアウォール規則を削除します。|
|/servers/firewallRules/read|サーバーのファイアウォール規則の一覧を返すか、指定されたファイアウォール規則のプロパティを取得します。|
|/servers/firewallRules/write|指定されたパラメーターでファイアウォール規則を作成するか、既存の規則を更新します。|
|/servers/providers/Microsoft.Insights/diagnosticSettings/read|リソースの診断設定を取得します。|
|/servers/providers/Microsoft.Insights/diagnosticSettings/write|リソースの診断設定を作成または更新します。|
|/servers/providers/Microsoft.Insights/metricDefinitions/read|データベースの利用可能なメトリックの種類を返します。|
|/servers/read|サーバーの一覧を返すか、指定されたサーバーのプロパティを取得します。|
|/servers/recoverableServers/read|復旧可能な MySQL サーバーの情報を返します|
|/servers/virtualNetworkRules/delete|既存の仮想ネットワーク ルールを削除します。|
|/servers/virtualNetworkRules/read|仮想ネットワーク ルールの一覧を返すか、指定された仮想ネットワーク ルールのプロパティを取得します。|
|/servers/virtualNetworkRules/write|指定されたパラメーターで仮想ネットワーク ルールを作成するか、指定された仮想ネットワーク ルールのプロパティまたはタグを更新します。|
|/servers/write|指定されたパラメーターでサーバーを作成するか、指定されたサーバーのプロパティまたはタグを更新します。|

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

| 操作 | [説明] |
|---|---|
|/locations/performanceTiers/read|使用可能なパフォーマンス レベルの一覧を返します。|
|/performanceTiers/read|使用可能なパフォーマンス レベルの一覧を返します。|
|/servers/delete|既存のサーバーを削除します。|
|/servers/firewallRules/delete|既存のファイアウォール規則を削除します。|
|/servers/firewallRules/read|サーバーのファイアウォール規則の一覧を返すか、指定されたファイアウォール規則のプロパティを取得します。|
|/servers/firewallRules/write|指定されたパラメーターでファイアウォール規則を作成するか、既存の規則を更新します。|
|/servers/providers/Microsoft.Insights/diagnosticSettings/read|リソースの診断設定を取得します。|
|/servers/providers/Microsoft.Insights/diagnosticSettings/write|リソースの診断設定を作成または更新します。|
|/servers/providers/Microsoft.Insights/logDefinitions/read|データベースで使用可能なジョブの種類を返します。|
|/servers/providers/Microsoft.Insights/metricDefinitions/read|データベースの利用可能なメトリックの種類を返します。|
|/servers/read|サーバーの一覧を返すか、指定されたサーバーのプロパティを取得します。|
|/servers/recoverableServers/read|復旧可能な PostgreSQL サーバーの情報を返します|
|/servers/virtualNetworkRules/delete|既存の仮想ネットワーク ルールを削除します。|
|/servers/virtualNetworkRules/read|仮想ネットワーク ルールの一覧を返すか、指定された仮想ネットワーク ルールのプロパティを取得します。|
|/servers/virtualNetworkRules/write|指定されたパラメーターで仮想ネットワーク ルールを作成するか、指定された仮想ネットワーク ルールのプロパティまたはタグを更新します。|
|/servers/write|指定されたパラメーターでサーバーを作成するか、指定されたサーバーのプロパティまたはタグを更新します。|

## <a name="microsoftdevices"></a>Microsoft.Devices

| 操作 | [説明] |
|---|---|
|/checkNameAvailability/Action|IotHub 名を使用できるかどうかを確認します。|
|/checkProvisioningServiceNameAvailability/Action|IotHub 名を使用できるかどうかを確認します。|
|/ElasticPools/diagnosticSettings/read|リソースの診断設定を取得します。|
|/ElasticPools/diagnosticSettings/write|リソースの診断設定を作成または更新します。|
|/elasticPools/iotHubTenants/Delete|IotHub テナントのリソースを削除します。|
|/ElasticPools/IotHubTenants/diagnosticSettings/read|リソースの診断設定を取得します。|
|/ElasticPools/IotHubTenants/diagnosticSettings/write|リソースの診断設定を作成または更新します。|
|/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete|EventHub コンシューマー グループを削除します。|
|/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Read|EventHub コンシューマー グループを取得します。|
|/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write|EventHub コンシューマー グループを作成します。|
|/elasticPools/iotHubTenants/exportDevices/Action|デバイスをエクスポートします。|
|/elasticPools/iotHubTenants/getStats/Read|IotHub テナントの統計リソースを取得します|
|/elasticPools/iotHubTenants/importDevices/Action|デバイスをインポートします。|
|/elasticPools/iotHubTenants/iotHubKeys/listkeys/Action|IotHub テナントのキーを取得します。|
|/elasticPools/iotHubTenants/jobs/Read|特定の IotHub で送信されたジョブの詳細を取得します。|
|/elasticPools/iotHubTenants/listKeys/Action|IotHub テナントのキーを取得します。|
|/ElasticPools/IotHubTenants/logDefinitions/read|IotHub サービスで使用可能なログ定義を取得します。|
|/ElasticPools/IotHubTenants/metricDefinitions/read|IotHub サービスの利用可能なメトリックを取得します。|
|/elasticPools/iotHubTenants/quotaMetrics/Read|クォータ メトリックを取得します。|
|/elasticPools/iotHubTenants/Read|IotHub テナントのリソースを取得します。|
|/elasticPools/iotHubTenants/routing/routes/$testall/Action|既存のすべてのルートに対してメッセージをテストします。|
|/elasticPools/iotHubTenants/routing/routes/$testnew/Action|指定されたテスト ルートに対してメッセージをテストします。|
|/elasticPools/iotHubTenants/routingEndpointsHealth/Read|IotHub のすべてのルーティング エンドポイントの正常性を取得します。|
|/elasticPools/iotHubTenants/Write|IotHub テナントのリソースを作成または更新します。|
|/ElasticPools/metricDefinitions/read|IotHub サービスの利用可能なメトリックを取得します。|
|/iotHubs/certificates/generateVerificationCode/Action|確認コードを生成します。|
|/iotHubs/certificates/verify/Action|証明書リソースを確認します。|
|/iotHubs/Delete|IotHub リソースを削除します。|
|/IotHubs/diagnosticSettings/read|リソースの診断設定を取得します。|
|/IotHubs/diagnosticSettings/write|リソースの診断設定を作成または更新します。|
|/iotHubs/eventGridFilters/Delete|Event Grid フィルターを削除します。|
|/iotHubs/eventGridFilters/Read|Event Grid フィルターを取得します。|
|/iotHubs/eventGridFilters/Write|新しい Event Grid フィルターを作成するか、既存の Event Grid フィルターを更新します。|
|/iotHubs/eventHubEndpoints/consumerGroups/Delete|EventHub コンシューマー グループを削除します。|
|/iotHubs/eventHubEndpoints/consumerGroups/Read|EventHub コンシューマー グループを取得します。|
|/iotHubs/eventHubEndpoints/consumerGroups/Write|EventHub コンシューマー グループを作成します。|
|/iotHubs/exportDevices/Action|デバイスをエクスポートします。|
|/iotHubs/importDevices/Action|デバイスをインポートします。|
|/iotHubs/iotHubKeys/listkeys/Action|特定の名前の IotHub キーを取得します。|
|/iotHubs/iotHubStats/Read|IotHub の統計を取得します。|
|/iotHubs/jobs/Read|特定の IotHub で送信されたジョブの詳細を取得します。|
|/iotHubs/listkeys/Action|すべての IotHub キーを取得します。|
|/IotHubs/logDefinitions/read|IotHub サービスで使用可能なログ定義を取得します。|
|/IotHubs/metricDefinitions/read|IotHub サービスの利用可能なメトリックを取得します。|
|/iotHubs/quotaMetrics/Read|クォータ メトリックを取得します。|
|/iotHubs/Read|IotHub リソースを取得します。|
|/iotHubs/routing/$testall/Action|既存のすべてのルートに対してメッセージをテストします。|
|/iotHubs/routing/$testnew/Action|指定されたテスト ルートに対してメッセージをテストします。|
|/iotHubs/routingEndpointsHealth/Read|IotHub のすべてのルーティング エンドポイントの正常性を取得します。|
|/iotHubs/skus/Read|有効な IotHub SKU を取得します。|
|/iotHubs/Write|IotHub リソースを作成または更新します。|
|/operations/Read|ResourceProvider のすべての操作を取得します。|
|/provisioningServices/certificates/generateVerificationCode/Action|確認コードを生成します。|
|/provisioningServices/certificates/verify/Action|証明書リソースを確認します。|
|/provisioningServices/Delete|IotDps リソースを削除します。|
|/provisioningServices/diagnosticSettings/read|リソースの診断設定を取得します。|
|/provisioningServices/diagnosticSettings/write|リソースの診断設定を作成または更新します。|
|/provisioningServices/listkeys/Action|すべての IotDps キーを取得します。|
|/provisioningServices/logDefinitions/read|プロビジョニング サービスで使用可能なログ定義を取得します。|
|/provisioningServices/metricDefinitions/read|プロビジョニング サービスで使用可能なメトリックを取得します。|
|/provisioningServices/ProvisioningServiceKeys/listkeys/Action|キー名の IotDps キーを取得します。|
|/provisioningServices/Read|IotDps リソースを取得します。|
|/provisioningServices/skus/Read|有効な IotDps SKU を取得します。|
|/provisioningServices/Write|IotDps リソースを作成します。|
|/register/action|IotHub リソース プロバイダーにサブスクリプションを登録し、IotHub リソースを作成できるようにします。|
|/register/action|IotHub リソース プロバイダーにサブスクリプションを登録し、IotHub リソースを作成できるようにします。|
|/usages/Read|このプロバイダーのサブスクリプションの使用状況の詳細を取得します。|

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

| 操作 | [説明] |
|---|---|
|/labCenters/delete|ラボ センターを削除します。|
|/labCenters/read|ラボ センターを読み取ります。|
|/labCenters/write|ラボ センターを追加または変更します。|
|/labs/artifactSources/armTemplates/read|Azure Resource Manager テンプレートを読み取ります。|
|/labs/artifactSources/artifacts/GenerateArmTemplate/action|特定のアーティファクトの ARM テンプレートを生成し、必要なファイルをストレージ アカウントにアップロードして、生成されたアーティファクトを検証します。|
|/labs/artifactSources/artifacts/read|アーティファクトを読み取ります。|
|/labs/artifactSources/delete|アーティファクト ソースを削除します。|
|/labs/artifactSources/read|アーティファクト ソースを読み取ります。|
|/labs/artifactSources/write|アーティファクト ソースを追加または変更します。|
|/labs/ClaimAnyVm/action|ラボ内のランダムに要求可能な仮想マシンを要求します。|
|/labs/costs/read|コストを読み取ります。|
|/labs/costs/write|コストを追加または変更します。|
|/labs/CreateEnvironment/action|ラボで仮想マシンを作成します。|
|/labs/customImages/delete|カスタム イメージを削除します。|
|/labs/customImages/read|カスタム イメージを読み取ります。|
|/labs/customImages/write|カスタム イメージを追加または変更します。|
|/labs/delete|ラボを削除します。|
|/labs/ExportResourceUsage/action|ラボのリソース使用量をストレージ アカウントにエクスポートします。|
|/labs/formulas/delete|数式を削除します。|
|/labs/formulas/read|数式を読み取ります。|
|/labs/formulas/write|数式を追加または変更します。|
|/labs/galleryImages/read|ギャラリー イメージを読み取ります。|
|/labs/GenerateUploadUri/action|カスタム ディスク イメージをラボにアップロードするための URI を生成します。|
|/labs/ImportVirtualMachine/action|別のラボに仮想マシンをインポートします。|
|/labs/ListVhds/action|カスタム イメージの作成に使用できるディスク イメージを一覧表示します。|
|/labs/notificationChannels/delete|notificationchannels を削除します。|
|/labs/notificationChannels/Notify/action|指定されたチャンネルに通知を送信します。|
|/labs/notificationChannels/read|notificationchannels を読み取ります。|
|/labs/notificationChannels/write|notificationchannels を追加または変更します。|
|/labs/policySets/EvaluatePolicies/action|ラボ ポリシーを評価します。|
|/labs/policySets/policies/delete|ポリシーを削除します。|
|/labs/policySets/policies/read|ポリシーを読み取ります。|
|/labs/policySets/policies/write|ポリシーを追加または変更します。|
|/labs/read|ラボを読み取ります。|
|/labs/schedules/delete|スケジュールを削除します。|
|/labs/schedules/Execute/action|スケジュールを実行します。|
|/labs/schedules/ListApplicable/action|適用可能なすべてのスケジュールを一覧表示します。|
|/labs/schedules/read|スケジュールを読み取ります。|
|/labs/schedules/write|スケジュールを追加または変更します。|
|/labs/serviceRunners/delete|サービス ランナーを削除します。|
|/labs/serviceRunners/read|サービス ランナーを読み取ります。|
|/labs/serviceRunners/write|サービス ランナーを追加または変更します。|
|/labs/users/delete|ユーザー プロファイルを削除します。|
|/labs/users/disks/Attach/action|仮想マシンにディスクを接続し、ディスクのリースを作成します。|
|/labs/users/disks/delete|ディスクを削除します。|
|/labs/users/disks/Detach/action|仮想マシンに接続されているディスクを切断し、ディスクのリースを解除します。|
|/labs/users/disks/read|ディスクを読み取ります。|
|/labs/users/disks/write|ディスクを追加または変更します。|
|/labs/users/environments/delete|環境を削除します。|
|/labs/users/environments/read|環境を読み取ります。|
|/labs/users/environments/write|環境を追加または変更します。|
|/labs/users/read|ユーザー プロファイルを読み取ります。|
|/labs/users/secrets/delete|シークレットを削除します。|
|/labs/users/secrets/read|シークレットを読み取ります。|
|/labs/users/secrets/write|シークレットを追加または変更します。|
|/labs/users/serviceFabrics/delete|サービス ファブリックを削除します。|
|/labs/users/serviceFabrics/ListApplicableSchedules/action|適用可能なすべてのスケジュールを一覧表示します。|
|/labs/users/serviceFabrics/read|サービス ファブリックを読み取ります。|
|/labs/users/serviceFabrics/schedules/delete|スケジュールを削除します。|
|/labs/users/serviceFabrics/schedules/Execute/action|スケジュールを実行します。|
|/labs/users/serviceFabrics/schedules/read|スケジュールを読み取ります。|
|/labs/users/serviceFabrics/schedules/write|スケジュールを追加または変更します。|
|/labs/users/serviceFabrics/Start/action|サービス ファブリックを開始します。|
|/labs/users/serviceFabrics/Stop/action|サービス ファブリックを停止します。|
|/labs/users/serviceFabrics/write|サービス ファブリックを追加または変更します。|
|/labs/users/write|ユーザー プロファイルを追加または変更します。|
|/labs/virtualMachines/AddDataDisk/action|新規または既存のデータ ディスクを仮想マシンに接続します。|
|/labs/virtualMachines/ApplyArtifacts/action|仮想マシンにアーティファクトを適用します。|
|/labs/virtualMachines/Claim/action|既存の仮想マシンの所有権を取得します。|
|/labs/virtualMachines/delete|仮想マシンを削除します。|
|/labs/virtualMachines/DetachDataDisk/action|指定されたディスクを仮想マシンから切断します。|
|/labs/virtualMachines/ListApplicableSchedules/action|適用可能なすべてのスケジュールを一覧表示します。|
|/labs/virtualMachines/read|仮想マシンを読み取ります。|
|/labs/virtualMachines/Restart/action|仮想マシンを再起動します。|
|/labs/virtualMachines/schedules/delete|スケジュールを削除します。|
|/labs/virtualMachines/schedules/Execute/action|スケジュールを実行します。|
|/labs/virtualMachines/schedules/read|スケジュールを読み取ります。|
|/labs/virtualMachines/schedules/write|スケジュールを追加または変更します。|
|/labs/virtualMachines/Start/action|仮想マシンを起動します。|
|/labs/virtualMachines/Stop/action|仮想マシンの停止|
|/labs/virtualMachines/TransferDisks/action|仮想マシン データ ディスクの所有権を自分に譲渡します。|
|/labs/virtualMachines/UnClaim/action|既存の仮想マシンの所有権を放棄します。|
|/labs/virtualMachines/write|仮想マシンを追加または変更します。|
|/labs/virtualNetworks/delete|仮想ネットワークを削除します。|
|/labs/virtualNetworks/read|仮想ネットワークを読み取ります。|
|/labs/virtualNetworks/write|仮想ネットワークを追加または変更します。|
|/labs/write|ラボを追加または変更します。|
|/locations/operations/read|操作を読み取ります。|
|/register/action|サブスクリプションを登録します。|
|/schedules/delete|スケジュールを削除します。|
|/schedules/Execute/action|スケジュールを実行します。|
|/schedules/read|スケジュールを読み取ります。|
|/schedules/Retarget/action|スケジュールのターゲット リソース ID を更新します。|
|/schedules/write|スケジュールを追加または変更します。|

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

| 操作 | [説明] |
|---|---|
|/databaseAccountNames/read|名前を使用できるかどうかを確認します。|
|/databaseAccounts/changeResourceGroup/action|データベース アカウントのリソース グループを変更します。|
|/databaseAccounts/databases/collections/metricDefinitions/read|コレクションのメトリック定義を読み取ります。|
|/databaseAccounts/databases/collections/metrics/read|コレクションのメトリックを読み取ります。|
|/databaseAccounts/databases/collections/partitionKeyRangeId/metrics/read|データベース アカウントのパーティション キー レベルのメトリックを読み取ります。|
|/databaseAccounts/databases/collections/partitions/metrics/read|データベース アカウントのパーティション レベルのメトリックを読み取ります。|
|/databaseAccounts/databases/collections/partitions/usages/read|データベース アカウントのパーティション レベルの使用状況を読み取ります。|
|/databaseAccounts/databases/collections/usages/read|コレクションの使用状況を読み取ります。|
|/databaseAccounts/databases/metricDefinitions/read|データベースのメトリック定義を読み取ります。|
|/databaseAccounts/databases/metrics/read|データベースのメトリックを読み取ります。|
|/databaseAccounts/databases/usages/read|データベースの使用状況を読み取ります。|
|/databaseAccounts/delete|データベース アカウントを削除します。|
|/databaseAccounts/failoverPriorityChange/action|データベース アカウントのリージョンのフェールオーバーの優先度を変更します。 これは手動フェールオーバー操作を実行する際に使用します。|
|/databaseAccounts/listConnectionStrings/action|データベース アカウントの接続文字列を取得します。|
|/databaseAccounts/listKeys/action|データベース アカウントのキーを一覧表示します。|
|/databaseAccounts/metricDefinitions/read|データベース アカウントのメトリック定義を読み取ります。|
|/databaseAccounts/metrics/read|データベース アカウントのメトリックを読み取ります。|
|/databaseAccounts/operationResults/read|非同期操作の状態を読み取ります。|
|/databaseAccounts/percentile/metrics/read|待機時間メトリックを読み取ります。|
|/databaseAccounts/percentile/sourceRegion/targetRegion/metrics/read|特定のソースおよびターゲット リージョンの待機時間メトリックを読み取ります。|
|/databaseAccounts/percentile/targetRegion/metrics/read|特定のターゲット リージョンの待機時間メトリックを読み取ります。|
|/databaseAccounts/providers/Microsoft.Insights/diagnosticSettings/read|リソースの診断設定を取得します。|
|/databaseAccounts/providers/Microsoft.Insights/diagnosticSettings/write|リソースの診断設定を作成または更新します。|
|/databaseAccounts/providers/Microsoft.Insights/logDefinitions/read|データベース アカウントで使用可能なログ カテゴリを取得します。|
|/databaseAccounts/providers/Microsoft.Insights/metricDefinitions/read|データベース アカウントで使用可能なメトリックを取得します。|
|/databaseAccounts/read|データベース アカウントを読み取ります。|
|/databaseAccounts/readonlykeys/action|データベース アカウントの読み取り専用キーを読み取ります。|
|/databaseAccounts/readonlykeys/read|データベース アカウントの読み取り専用キーを読み取ります。|
|/databaseAccounts/regenerateKey/action|データベース アカウントのキーをローテーションします。|
|/databaseAccounts/region/databases/collections/metrics/read|リージョンのコレクション メトリックを読み取ります。|
|/databaseAccounts/region/databases/collections/partitionKeyRangeId/metrics/read|リージョンのデータベース アカウントのパーティション キー レベルのメトリックを読み取ります。|
|/databaseAccounts/region/databases/collections/partitions/metrics/read|リージョンのデータベース アカウントのパーティション レベルのメトリックを読み取ります。|
|/databaseAccounts/region/databases/collections/partitions/read|コレクション内のデータベース アカウント パーティションを読み取ります。|
|/databaseAccounts/region/metrics/read|リージョンとデータベース アカウントのメトリックを読み取ります。|
|/databaseAccounts/usages/read|データベース アカウントの使用状況を読み取ります。|
|/databaseAccounts/write|データベース アカウントを更新します。|
|/locations/deleteVirtualNetworkOrSubnets/action|仮想ネットワークまたはサブネットが削除されていることを Microsoft.DocumentDB に通知します。|
|/operationResults/read|非同期操作の状態を読み取ります。|
|/operations/read|Microsoft DocumentDB で使用可能な操作を読み取ります。 |
|/register/action| Microsoft DocumentDB リソース プロバイダーにサブスクリプションを登録します。|

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

| 操作 | [説明] |
|---|---|
|/checkDomainAvailability/Action|ドメインを購入できるかどうかを確認します。|
|/domains/Delete|既存のドメインを削除します。|
|/domains/domainownershipidentifiers/Delete|所有権の識別子を削除します。|
|/domains/domainownershipidentifiers/Read|所有権の識別子を一覧表示します。|
|/domains/domainownershipidentifiers/Read|所有権の識別子を取得します。|
|/domains/domainownershipidentifiers/Write|識別子を作成または更新します。|
|/domains/operationresults/Read|ドメイン操作を取得します。|
|/domains/operations/Read|アプリ サービス ドメイン登録のすべての操作を一覧表示します。|
|/domains/Read|ドメインの一覧を取得します。|
|/domains/Read|ドメインを取得します。|
|/domains/renew/Action|既存のドメインを更新します。|
|/domains/Write|新しいドメインを追加するか、既存のドメインを更新します。|
|/generateSsoRequest/Action|ドメイン コントロール センターへのサインイン要求を生成します。|
|/listDomainRecommendations/Action|キーワードに基づいて、ドメインの推奨事項の一覧を取得します。|
|/register/action|Microsoft Domains リソース プロバイダーをサブスクリプションに登録します。|
|/topLevelDomains/listAgreements/Action|契約のアクションを一覧表示します。|
|/topLevelDomains/Read|最上位ドメインを取得します。|
|/topLevelDomains/Read|最上位ドメインを取得します。|
|/validateDomainRegistrationInformation/Action|ドメイン購入オブジェクトを送信せずに検証します。|

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

| 操作 | [説明] |
|---|---|
|/lcsprojects/clouddeployments/read|ユーザーに属する Microsoft Dynamics Lifecycle Services プロジェクトの Microsoft Dynamics AX 2012 R3 Evaluation デプロイを表示します。|
|/lcsprojects/clouddeployments/write|ユーザーに属する Microsoft Dynamics Lifecycle Services プロジェクトの Microsoft Dynamics AX 2012 R3 Evaluation デプロイを作成します。 デプロイは Azure 管理ポータルから管理できます。|
|/lcsprojects/connectors/read|Microsoft Dynamics Lifecycle Services プロジェクトに属するコネクタを読み取ります。|
|/lcsprojects/connectors/write|Microsoft Dynamics Lifecycle Services プロジェクトに属するコネクタを作成および更新します。|
|/lcsprojects/delete|ユーザーに属する Microsoft Dynamics Lifecycle Services プロジェクトを削除します。|
|/lcsprojects/read|ユーザーに属する Microsoft Dynamics Lifecycle Services プロジェクトを表示します。|
|/lcsprojects/write|ユーザーに属する Microsoft Dynamics Lifecycle Services プロジェクトを作成および更新します。 更新できるのは、名前プロパティと説明プロパティだけです。 プロジェクトに関連付けられているサブスクリプションと場所は、プロジェクトの作成後に更新することはできません。|

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

| 操作 | [説明] |
|---|---|
|/eventSubscriptions/delete|eventSubscription を削除します。|
|/eventSubscriptions/getFullUrl/action|イベント サブスクリプションの完全な URL を取得します。|
|/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/read|イベント サブスクリプションの診断設定を取得します。|
|/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/write|イベント サブスクリプションの診断設定を作成または更新します。|
|/eventSubscriptions/providers/Microsoft.Insights/metricDefinitions/read|eventSubscription で使用可能なメトリックを取得します。|
|/eventSubscriptions/read|eventSubscription を読み取ります。|
|/eventSubscriptions/write|eventSubscription を作成または更新します。|
|/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/read|トピックの診断設定を取得します。|
|/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/write|トピックの診断設定を作成または更新します。|
|/extensionTopics/providers/Microsoft.Insights/metricDefinitions/read|トピックで使用可能なメトリックを取得します。|
|/register/action|EventGrid リソース プロバイダーに eventSubscription を登録し、Event Grid サブスクリプションを作成できるようにします。|
|/topics/delete|トピックを削除する|
|/topics/listKeys/action|トピックのキーを一覧表示します|
|/topics/providers/Microsoft.Insights/diagnosticSettings/read|トピックの診断設定を取得します。|
|/topics/providers/Microsoft.Insights/diagnosticSettings/write|トピックの診断設定を作成または更新します。|
|/topics/providers/Microsoft.Insights/metricDefinitions/read|トピックで使用可能なメトリックを取得します。|
|/topics/read|トピックを読み取ります。|
|/topics/regenerateKey/action|トピックのキーを再生成します。|
|/topics/write|トピックを作成または更新します。|

## <a name="microsofteventhub"></a>Microsoft.EventHub

| 操作 | [説明] |
|---|---|
|/checkNameAvailability/action|特定のサブスクリプションで名前空間を使用できるかどうかを確認します。|
|/checkNamespaceAvailability/action|特定のサブスクリプションで名前空間を使用できるかどうかを確認します。 この API は使用されていません。代わりに CheckNameAvailabiltiy を使用してください。|
|/namespaces/authorizationRules/action|名前空間の承認規則を更新します。 この API は使用されていません。 名前空間の承認規則を更新するには、代わりに PUT 呼び出しを使用してください。 この操作は、API バージョン 2017-04-01 ではサポートされていません。|
|/namespaces/authorizationRules/delete|名前空間の承認規則を削除します。 名前空間の既定の承認規則は削除できません。 |
|/namespaces/authorizationRules/listkeys/action|名前空間への接続文字列を取得します。|
|/namespaces/authorizationRules/read|名前空間の承認規則の説明の一覧を取得します。|
|/namespaces/authorizationRules/regenerateKeys/action|リソースのプライマリ キーまたはセカンダリ キーを再生成します。|
|/namespaces/authorizationRules/write|名前空間レベルの承認規則を作成し、そのプロパティを更新します。 承認規則のアクセス権、プライマリ キー、セカンダリ キーを更新できます。|
|/namespaces/Delete|名前空間リソースを削除します。|
|/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action|ディザスター リカバリーのプライマリ名前空間の承認規則キーを取得します。|
|/namespaces/disasterRecoveryConfigs/authorizationRules/read|ディザスター リカバリーのプライマリ名前空間の承認規則を取得します。|
|/namespaces/disasterRecoveryConfigs/breakPairing/action|ディザスター リカバリーを無効にし、プライマリ名前空間からセカンダリ名前空間への変更の複製を停止します。|
|/namespaces/disasterrecoveryconfigs/checkNameAvailability/action|指定されたサブスクリプションで名前空間別名を使用できるかどうかを確認します。|
|/namespaces/disasterRecoveryConfigs/delete|名前空間に関連付けられているディザスター リカバリー構成を削除します。 この操作は、プライマリ名前空間からのみ呼び出すことができます。|
|/namespaces/disasterRecoveryConfigs/failover/action|geo DR フェールオーバーを呼び出し、セカンダリ名前空間を指すように名前空間の別名を再構成します。|
|/namespaces/disasterRecoveryConfigs/read|名前空間に関連付けられているディザスター リカバリーの構成を取得します。|
|/namespaces/disasterRecoveryConfigs/write|名前空間に関連付けられているディザスター リカバリーの構成を作成または更新します。|
|/namespaces/eventhubs/authorizationRules/action|EventHub を更新する操作です。 この操作は、API バージョン 2017-04-01 ではサポートされていません。 承認規則。 承認規則を更新するには、PUT 呼び出しを使用してください。|
|/namespaces/eventhubs/authorizationRules/delete|EventHub の承認規則を削除する操作。|
|/namespaces/eventhubs/authorizationRules/listkeys/action|EventHub への接続文字列を取得します。|
|/namespaces/eventhubs/authorizationRules/read| EventHub の承認規則の一覧を取得します。|
|/namespaces/eventhubs/authorizationRules/regenerateKeys/action|リソースのプライマリ キーまたはセカンダリ キーを再生成します。|
|/namespaces/eventhubs/authorizationRules/write|EventHub の承認規則を作成し、そのプロパティを更新します。 承認規則のアクセス権を更新できます。|
|/namespaces/eventHubs/consumergroups/Delete|ConsumerGroup リソースを削除する操作。|
|/namespaces/eventHubs/consumergroups/read|ConsumerGroup リソースの説明の一覧を取得します。|
|/namespaces/eventHubs/consumergroups/write|ConsumerGroup のプロパティを作成または更新します。|
|/namespaces/eventhubs/Delete|EventHub リソースを削除する操作。|
|/namespaces/eventhubs/read|EventHub リソースの説明の一覧を取得します。|
|/namespaces/eventhubs/write|EventHub のプロパティを作成または更新します。|
|/namespaces/messagingPlan/read|名前空間のメッセージング プランを取得します。 この API は使用されていません。 より新しい API バージョンでは、MessagingPlan リソースにより公開されるプロパティは (親) 名前空間リソースに移動されています。 この操作は、API バージョン 2017-04-01 ではサポートされていません。|
|/namespaces/messagingPlan/write|名前空間のメッセージング プランを更新します。 この API は使用されていません。 より新しい API バージョンでは、MessagingPlan リソースにより公開されるプロパティは (親) 名前空間リソースに移動されています。 この操作は、API バージョン 2017-04-01 ではサポートされていません。|
|/namespaces/operationresults/read|名前空間の操作の状態を取得します。|
|/namespaces/providers/Microsoft.Insights/diagnosticSettings/read|名前空間の診断設定リソースの説明の一覧を取得します。|
|/namespaces/providers/Microsoft.Insights/diagnosticSettings/write|名前空間の診断設定リソースの説明の一覧を取得します。|
|/namespaces/providers/Microsoft.Insights/logDefinitions/read|名前空間のログ リソースの説明の一覧を取得します。|
|/namespaces/providers/Microsoft.Insights/metricDefinitions/read|名前空間のメトリック リソースの説明の一覧を取得します。|
|/namespaces/read|名前空間リソースの説明の一覧を取得します。|
|/namespaces/write|名前空間リソースを作成し、そのプロパティを更新します。 名前空間のタグとキャパシティは、更新できるプロパティです。|
|/operations/read|操作を取得します。|
|/register/action|EventHub リソース プロバイダーにサブスクリプションを登録し、EventHub リソースを作成できるようにします。|
|/sku/read|SKU リソースの説明の一覧を取得します。|
|/sku/regions/read|SkuRegions リソースの説明の一覧を取得します。|
|/unregister/action|EventHub リソース プロバイダーに登録します。|

## <a name="microsoftfeatures"></a>Microsoft.Features

| 操作 | [説明] |
|---|---|
|/features/read|サブスクリプションの機能を取得します。|
|/providers/features/read|指定されたリソース プロバイダーのサブスクリプションの機能を取得します。|
|/providers/features/register/action|指定されたリソース プロバイダーのサブスクリプションの機能を登録します。|
|/providers/features/unregister/action|指定されたリソース プロバイダーのサブスクリプションの機能を登録解除します。|

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

| 操作 | [説明] |
|---|---|
|/clusters/changerdpsetting/action|HDInsight クラスターの RDP 設定を変更します。|
|/clusters/configurations/action|HDInsight クラスターの構成を更新します。|
|/clusters/configurations/read|HDInsight クラスターの構成を取得します。|
|/clusters/delete|HDInsight クラスターを削除します。|
|/clusters/providers/Microsoft.Insights/diagnosticSettings/read|リソース HDInsight クラスターの診断設定を取得します。|
|/clusters/providers/Microsoft.Insights/diagnosticSettings/write|リソース HDInsight クラスターの診断設定を作成または更新します。|
|/clusters/providers/Microsoft.Insights/metricDefinitions/read|HDInsight クラスターで使用可能なメトリックを取得します。|
|/clusters/read|HDInsight クラスターの詳細を取得します。|
|/clusters/roles/resize/action|HDInsight クラスターのサイズを変更します。|
|/clusters/write|HDInsight クラスターを作成または更新します。|
|/locations/capabilities/read|サブスクリプション機能を取得します。|
|/locations/checkNameAvailability/read|名前を使用できるかどうかを確認します。|

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

| 操作 | [説明] |
|---|---|
|/jobs/delete|既存のジョブを削除します。|
|/jobs/listBitLockerKeys/action|指定されたジョブの BitLocker キーを取得します。|
|/jobs/read|指定されたジョブのプロパティを取得するか、ジョブの一覧を返します。|
|/jobs/write|指定されたパラメーターを使用してジョブを作成するか、指定されたジョブのプロパティまたはタグを更新します。|
|/locations/read|指定された場所のプロパティを取得するか、場所の一覧を返します。|
|/register/action|Import/Export リソース プロバイダーにサブスクリプションを登録し、インポート/エクスポート ジョブを作成できるようにします。|

## <a name="microsoftinsights"></a>Microsoft.Insights

| 操作 | [説明] |
|---|---|
|/ActionGroups/Delete|アクション グループを削除します。|
|/ActionGroups/Read|アクション グループを読み取ります。|
|/ActionGroups/Write|アクション グループを書き込みます。|
|/ActivityLogAlerts/Activated/Action|アクティビティ ログ アラートをトリガーしました。|
|/ActivityLogAlerts/Delete|アクティビティ ログ アラートを削除します。|
|/ActivityLogAlerts/Read|アクティビティ ログ アラートを読み取ります。|
|/ActivityLogAlerts/Write|アクティビティ ログ アラートを読み取ります。|
|/AlertRules/Activated/Action|アラート ルールがアクティブ化されました。|
|/AlertRules/Delete|アラート ルールの構成を削除します。|
|/AlertRules/Incidents/Read|アラート ルール インシデントの構成を読み取ります。|
|/AlertRules/Read|アラート ルールの構成を読み取ります。|
|/AlertRules/Resolved/Action|アラート ルールが解決されました。|
|/AlertRules/Throttled/Action|アラート ルールが調整されました。|
|/AlertRules/Write|アラート ルールの構成に書き込みます。|
|/AutoscaleSettings/Delete|自動スケール設定の構成を削除します。|
|/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read|メトリック定義を読み取ります。|
|/AutoscaleSettings/Read|自動スケール設定の構成を読み取ります。|
|/AutoscaleSettings/Scaledown/Action|自動スケールのスケールダウン操作。|
|/AutoscaleSettings/Scaleup/Action|自動スケールのスケールアップ操作。|
|/AutoscaleSettings/Write|自動スケール設定の構成に書き込みます。|
|/Components/AnalyticsItems/Delete|Application Insights の分析項目を削除します。|
|/Components/AnalyticsItems/Read|Application Insights の分析項目を読み取ります。|
|/Components/AnalyticsItems/Write|Application Insights の分析項目を書き込みます。|
|/Components/AnalyticsTables/Action|Application Insights の分析テーブル アクションです。|
|/Components/AnalyticsTables/Delete|Application Insights の分析テーブル スキーマを削除します。|
|/Components/AnalyticsTables/Read|Application Insights の分析テーブル スキーマを読み取ります。|
|/Components/AnalyticsTables/Write|Application Insights の分析テーブル スキーマを書き込みます。|
|/Components/Annotations/Delete|Application Insights の注釈を削除します。|
|/Components/Annotations/Read|Application Insights の注釈を読み取ります。|
|/Components/Annotations/Write|Application Insights の注釈を書き込みます。|
|/Components/Api/Read|Application Insights コンポーネントのデータ API を読み取ります。|
|/Components/ApiKeys/Action|Application Insights の API キーを生成します。|
|/Components/ApiKeys/Delete|Application Insights の API キーを削除します。|
|/Components/ApiKeys/Read|Application Insights の API キーを読み取ります。|
|/Components/BillingPlanForComponent/Read|Application Insights コンポーネントの料金プランを読み取ります。|
|/Components/CurrentBillingFeatures/Read|Application Insights コンポーネントの現在の料金機能を読み取ります。|
|/Components/CurrentBillingFeatures/Write|Application Insights コンポーネントの現在の料金機能を書き込みます。|
|/Components/DefaultWorkItemConfig/Read|Application Insights の既定の ALM 統合構成を読み取ります。|
|/Components/Delete|Application Insights コンポーネントの構成を削除します。|
|/Components/ExportConfiguration/Action|Application Insights のエクスポート設定アクションです。|
|/Components/ExportConfiguration/Delete|Application Insights のエクスポート設定を削除します。|
|/Components/ExportConfiguration/Read|Application Insights のエクスポート設定を読み取ります。|
|/Components/ExportConfiguration/Write|Application Insights のエクスポート設定を書き込みます。|
|/Components/ExtendQueries/Read|Application Insights コンポーネントの拡張クエリの結果を読み取ります。|
|/Components/Favorites/Delete|Application Insights のお気に入りを削除します。|
|/Components/Favorites/Read|Application Insights のお気に入りを読み取ります。|
|/Components/Favorites/Write|Application Insights のお気に入りを書き込みます。|
|/Components/FeatureCapabilities/Read|Application Insights コンポーネントの機能を読み取ります。|
|/Components/GetAvailableBillingFeatures/Read|Application Insights コンポーネントで使用可能な請求機能を読み取ります。|
|/Components/GetToken/Read|Application Insights コンポーネントのトークンを読み取ります。|
|/Components/ListMigrationDate/Action|サブスクリプション移行の日付を取得します。|
|/Components/ListMigrationDate/Read|サブスクリプション移行の日付を取得します。|
|/Components/MetricDefinitions/Read|Application Insights コンポーネントのメトリック定義を読み取ります。|
|/Components/Metrics/Read|Application Insights コンポーネントのメトリックを読み取ります。|
|/Components/MigrateToNewpricingModel/Action|新しい価格モデルにサブスクリプションを移行します。|
|/Components/Move/Action|Application Insights コンポーネントを別のリソース グループまたはサブスクリプションに移動します。|
|/Components/MyAnalyticsItems/Delete|Application Insights の個人分析項目を削除します。|
|/Components/MyAnalyticsItems/Read|Application Insights の個人分析項目を読み取ります。|
|/Components/MyAnalyticsItems/Write|Application Insights の個人分析項目を書き込みます。|
|/Components/MyFavorites/Read|Application Insights の個人のお気に入りを読み取ります。|
|/Components/PricingPlans/Read|Application Insights コンポーネントの価格プランを読み取ります。|
|/Components/PricingPlans/Write|Application Insights コンポーネントの価格プランを書き込みます。|
|/Components/ProactiveDetectionConfigs/Read|Application Insights のプロアクティブな検出構成を読み取ります。|
|/Components/ProactiveDetectionConfigs/Write|Application Insights のプロアクティブな検出構成を書き込みます。|
|/Components/providers/Microsoft.Insights/MetricDefinitions/Read|メトリック定義を読み取ります。|
|/Components/QuotaStatus/Read|Application Insights コンポーネントのクォータの状態を読み取ります。|
|/Components/Read|Application Insights コンポーネントの構成を読み取ります。|
|/Components/RollbackToLegacyPricingModel/Action|従来の価格モデルにサブスクリプションをロールバックします。|
|/Components/SyntheticMonitorLocations/Read|Application Insights の Web サイトの場所を読み取ります。|
|/Components/WorkItemConfigs/Delete|Application Insights の ALM 統合構成を削除します。|
|/Components/WorkItemConfigs/Read|Application Insights の ALM 統合構成を読み取ります。|
|/Components/WorkItemConfigs/Write|Application Insights の ALM 統合構成を書き込みます。|
|/Components/Write|Application Insights コンポーネントの構成を書き込みます。|
|/DiagnosticSettings/Delete|診断設定の構成を削除します。|
|/DiagnosticSettings/Read|診断設定の構成を読み取ります。|
|/DiagnosticSettings/Write|診断設定の構成に書き込みます。|
|/EventCategories/Read|イベント カテゴリを読み取ります。|
|/eventtypes/digestevents/Read|イベントの種類の管理のダイジェストを読み取ります。|
|/eventtypes/values/Read|イベントの種類の管理の値を読み取ります。|
|/ExtendedDiagnosticSettings/Delete|拡張診断設定の構成を削除します。|
|/ExtendedDiagnosticSettings/Read|拡張診断設定の構成を読み取ります。|
|/ExtendedDiagnosticSettings/Write|拡張診断設定の構成に書き込みます。|
|/LogDefinitions/Read|ログ定義を読み取ります。|
|/LogProfiles/Delete|ログ プロファイルの構成を削除します。|
|/LogProfiles/Read|ログ プロファイルを読み取ります。|
|/LogProfiles/Write|ログ プロファイルの構成に書き込みます。|
|/MetricAlerts/Delete|メトリック アラートを削除します。|
|/MetricAlerts/Read|メトリック アラートを読み取ります。|
|/MetricAlerts/Write|メトリック アラートを書き込みます。|
|/MetricDefinitions/Microsoft.Insights/Read|メトリック定義を読み取ります。|
|/MetricDefinitions/providers/Microsoft.Insights/Read|メトリック定義を読み取ります。|
|/MetricDefinitions/Read|メトリック定義を読み取ります。|
|/Metrics/providers/Metrics/Read|メトリックを読み取ります。|
|/Metrics/Read|メトリックを読み取ります。|
|/Metrics/Write|メトリックを書き込みます。|
|/Operations/Read|操作を読み取ります。|
|/Register/Action|Microsoft Insights プロバイダーを登録します。|
|/Tenants/Register/Action|Microsoft Insights プロバイダーを初期化します。|
|/Unregister/Action|Microsoft Insights プロバイダーを登録します。|
|/Webtests/Delete|Web テストの構成を削除します。|
|/Webtests/GetToken/Read|Web テストのトークンを読み取ります。|
|/Webtests/MetricDefinitions/Read|Web テストのメトリック定義を読み取ります。|
|/Webtests/Metrics/Read|Web テストのメトリックを読み取ります。|
|/Webtests/Read|Web テストの構成を読み取ります。|
|/Webtests/Write|Web テストの構成を書き込みます。|

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

| 操作 | [説明] |
|---|---|
|/checkNameAvailability/read|Key Vault 名が有効であり、使用されていないことを確認します。|
|/deletedVaults/read|論理的に削除された Key Vault のプロパティを表示します。|
|/hsmPools/delete|HSM プールを削除します。|
|/hsmPools/joinVault/action|HSM プールにキー コンテナーを結合します。|
|/hsmPools/read|HSM プールのプロパティを表示します。|
|/hsmPools/write|新しい HSM プールを作成するか、既存の HSM プールのプロパティを更新します。|
|/locations/deletedVaults/purge/action|論理的に削除された Key Vault を消去します。|
|/locations/deletedVaults/read|論理的に削除された Key Vault のプロパティを表示します。|
|/locations/deleteVirtualNetworkOrSubnets/action|仮想ネットワークまたはサブネットが削除されていることを Microsoft.KeyVault に通知します。|
|/locations/operationResults/read|長時間実行された操作の結果を確認します。|
|/operations/read|Microsoft.KeyVault リソース プロバイダーで使用できる操作を一覧表示します。|
|/register/action|サブスクリプションを登録します。|
|/unregister/action|サブスクリプションの登録を解除します。|
|/vaults/accessPolicies/write|既存のアクセス ポリシーをマージまたは置換して更新するか、資格情報コンテナーに新しいアクセス ポリシーを追加します。|
|/vaults/delete|Key Vault を削除します。|
|/vaults/deploy/action|Azure リソースのデプロイ時に、Key Vault 内のシークレットにアクセスできるようにします。|
|/vaults/providers/Microsoft.Insights/diagnosticSettings/Read|リソースの診断設定を取得します。|
|/vaults/providers/Microsoft.Insights/diagnosticSettings/Write|リソースの診断設定を作成または更新します。|
|/vaults/providers/Microsoft.Insights/logDefinitions/read|キー コンテナーで使用可能なログを取得します。|
|/vaults/providers/Microsoft.Insights/metricDefinitions/read|キー コンテナーで使用可能なメトリックを取得します。|
|/vaults/read|Key Vault のプロパティを表示します。|
|/vaults/secrets/read|シークレットの値ではなく、プロパティを表示します。|
|/vaults/secrets/write|新しいシークレットを作成するか、既存のシークレットの値を更新します。|
|/vaults/write|新しい Key Vault を作成するか、既存の Key Vault のプロパティを更新します。|

## <a name="microsoftlabservices"></a>Microsoft.LabServices

| 操作 | [説明] |
|---|---|
|/labAccounts/CreateLab/action|ラボ アカウントにラボを作成します。|
|/labAccounts/delete|ラボ アカウントを削除します。|
|/labAccounts/labs/delete|ラボを削除します。|
|/labAccounts/labs/environmentSettings/delete|環境の設定を削除します。|
|/labAccounts/labs/environmentSettings/environments/delete|環境を削除します。|
|/labAccounts/labs/environmentSettings/environments/read|環境を読み取ります。|
|/labAccounts/labs/environmentSettings/environments/write|環境を追加または変更します。|
|/labAccounts/labs/environmentSettings/Publish/action|ラボ/環境設定の現在の状態に基づいて環境設定に必要なリソースをプロビジョニング/プロビジョニング解除します。|
|/labAccounts/labs/environmentSettings/read|環境の設定を読み取ります。|
|/labAccounts/labs/environmentSettings/write|環境の設定を追加または変更します。|
|/labAccounts/labs/read|ラボを読み取ります。|
|/labAccounts/labs/users/delete|ユーザーを削除します。|
|/labAccounts/labs/users/read|ユーザーを読み取ります。|
|/labAccounts/labs/users/write|ユーザーを追加または変更します。|
|/labAccounts/labs/write|ラボを追加または変更します。|
|/labAccounts/read|ラボ アカウントを読み取ります。|
|/labAccounts/write|ラボ アカウントを追加または変更します。|
|/locations/operations/read|操作を読み取ります。|
|/register/action|サブスクリプションを登録します。|

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

| 操作 | [説明] |
|---|---|
|/accounts/delete|Location Based Services アカウントを削除します。|
|/accounts/listKeys/action|Location Based Services アカウントのキーを一覧表示します。|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/read|リソースの診断設定を取得します。|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/write|リソースの診断設定を作成または更新します。|
|/accounts/providers/Microsoft.Insights/metricDefinitions/read|Location Based Services アカウントで使用可能なメトリックを取得します。|
|/accounts/read|Location Based Services アカウントを取得します。|
|/accounts/regenerateKey/action|Location Based Services アカウントの新しいプライマリ キーまたはセカンダリ キーを生成します。|
|/accounts/write|Location Based Services アカウントを作成または更新します。|
|/register/action|プロバイダーに登録します。|

## <a name="microsoftlogic"></a>Microsoft.Logic

| 操作 | [説明] |
|---|---|
|/integrationAccounts/agreements/delete|統合アカウントの契約を削除します。|
|/integrationAccounts/agreements/listContentCallbackUrl/action|統合アカウントの契約内容のコールバック URL を取得します。|
|/integrationAccounts/agreements/read|統合アカウントの契約を読み取ります。|
|/integrationAccounts/agreements/write|統合アカウントの契約を作成または更新します。|
|/integrationAccounts/assemblies/delete|統合アカウントのアセンブリを削除します。|
|/integrationAccounts/assemblies/listContentCallbackUrl/action|統合アカウントのアセンブリ内容のコールバック URL を取得します。|
|/integrationAccounts/assemblies/read|統合アカウントのアセンブリを読み取ります。|
|/integrationAccounts/assemblies/write|統合アカウントのアセンブリを作成または更新します。|
|/integrationAccounts/batchConfigurations/delete|統合アカウントのバッチ構成を削除します。|
|/integrationAccounts/batchConfigurations/read|統合アカウントのバッチ構成を読み取ります。|
|/integrationAccounts/batchConfigurations/write|統合アカウントのバッチ構成を作成または更新します。|
|/integrationAccounts/certificates/delete|統合アカウントの証明書を削除します。|
|/integrationAccounts/certificates/read|統合アカウントの証明書を読み取ります。|
|/integrationAccounts/certificates/write|統合アカウントの証明書を作成または更新します。|
|/integrationAccounts/delete|統合アカウントを削除します。|
|/integrationAccounts/listCallbackUrl/action|統合アカウントのコールバック URL を取得します。|
|/integrationAccounts/listKeyVaultKeys/action|キー コンテナーのキーを取得します。|
|/integrationAccounts/logTrackingEvents/action|統合アカウントでの追跡イベントを記録します。|
|/integrationAccounts/maps/delete|統合アカウントのマップを削除します。|
|/integrationAccounts/maps/listContentCallbackUrl/action|統合アカウントのマップ内容のコールバック URL を取得します。|
|/integrationAccounts/maps/read|統合アカウントのマップを読み取ります。|
|/integrationAccounts/maps/write|統合アカウントのマップを作成または更新します。|
|/integrationAccounts/partners/delete|統合アカウントのパートナーを削除します。|
|/integrationAccounts/partners/listContentCallbackUrl/action|統合アカウントのパートナー内容のコールバック URL を取得します。|
|/integrationAccounts/partners/read|統合アカウントのパートナーを読み取ります。|
|/integrationAccounts/partners/write|統合アカウントのパートナーを作成または更新します。|
|/integrationAccounts/providers/Microsoft.Insights/logDefinitions/read|統合アカウントのログ定義を読み取ります。|
|/integrationAccounts/read|統合アカウントを読み取ります。|
|/integrationAccounts/regenerateAccessKey/action|アクセス キーのシークレットを再生成します。|
|/integrationAccounts/schemas/delete|統合アカウントのスキーマを削除します。|
|/integrationAccounts/schemas/listContentCallbackUrl/action|統合アカウントのスキーマ内容のコールバック URL を取得します。|
|/integrationAccounts/schemas/read|統合アカウントのスキーマを読み取ります。|
|/integrationAccounts/schemas/write|統合アカウントのスキーマを作成または更新します。|
|/integrationAccounts/sessions/delete|統合アカウントのセッションを削除します。|
|/integrationAccounts/sessions/read|統合アカウントのバッチ構成を読み取ります。|
|/integrationAccounts/sessions/write|統合アカウントのセッションを作成または更新します。|
|/integrationAccounts/write|統合アカウントを作成または更新します。|
|/locations/workflows/validate/action|ワークフローを検証します。|
|/operations/read|操作を取得します。|
|/register/action|指定されたサブスクリプションを Microsoft.Logic リソース プロバイダーに登録します。|
|/workflows/accessKeys/delete|アクセス キーを削除します。|
|/workflows/accessKeys/list/action|アクセス キーのシークレットを一覧表示します。|
|/workflows/accessKeys/read|アクセス キーを読み取ります。|
|/workflows/accessKeys/regenerate/action|アクセス キーのシークレットを再生成します。|
|/workflows/accessKeys/write|アクセス キーを作成または更新します。|
|/workflows/delete|ワークフローを削除します。|
|/workflows/disable/action|ワークフローを無効にします。|
|/workflows/enable/action|ワークフローを有効にします。|
|/workflows/listCallbackUrl/action|ワークフローのコールバック URL を取得します。|
|/workflows/listSwagger/action|ワークフローの Swagger 定義を取得します。|
|/workflows/move/action|ワークフローを既存のサブスクリプション ID、リソース グループ、または名前から、別のサブスクリプション ID、リソース グループ、または名前に移動します。|
|/workflows/providers/Microsoft.Insights/diagnosticSettings/read|ワークフローの診断設定を読み取ります。|
|/workflows/providers/Microsoft.Insights/diagnosticSettings/write|ワークフローの診断設定を作成または更新します。|
|/workflows/providers/Microsoft.Insights/logDefinitions/read|ワークフローのログ定義を読み取ります。|
|/workflows/providers/Microsoft.Insights/metricDefinitions/read|ワークフローのメトリック定義を読み取ります。|
|/workflows/read|ワークフローを読み取ります。|
|/workflows/regenerateAccessKey/action|アクセス キーのシークレットを再生成します。|
|/workflows/run/action|ワークフローの実行を開始します。|
|/workflows/runs/actions/listExpressionTraces/action|ワークフロー実行アクションの式のトレースを取得します。|
|/workflows/runs/actions/read|ワークフロー実行アクションを読み取ります。|
|/workflows/runs/actions/repetitions/listExpressionTraces/action|ワークフロー実行アクションの繰り返し式のトレースを取得します。|
|/workflows/runs/actions/repetitions/read|ワークフロー実行アクションの繰り返しを読み取ります。|
|/workflows/runs/actions/scoperepetitions/read|ワークフロー実行アクションのスコープの繰り返しを読み取ります。|
|/workflows/runs/cancel/action|ワークフローの実行を取り消します。|
|/workflows/runs/operations/read|ワークフロー実行操作の状態を読み取ります。|
|/workflows/runs/read|ワークフロー実行を読み取ります。|
|/workflows/suspend/action|ワークフローを一時停止します。|
|/workflows/triggers/histories/read|トリガーの履歴を読み取ります。|
|/workflows/triggers/histories/resubmit/action|ワークフロー トリガーを再送信します。|
|/workflows/triggers/listCallbackUrl/action|トリガーのコールバック URL を取得します。|
|/workflows/triggers/read|トリガーを読み取ります。|
|/workflows/triggers/reset/action|トリガーをリセットします。|
|/workflows/triggers/run/action|トリガーを実行します。|
|/workflows/triggers/setState/action|トリガーの状態を設定します。|
|/workflows/validate/action|ワークフローを検証します。|
|/workflows/versions/read|ワークフローのバージョンを読み取ります。|
|/workflows/versions/triggers/listCallbackUrl/action|トリガーのコールバック URL を取得します。|
|/workflows/write|ワークフローを作成または更新します。|

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

| 操作 | [説明] |
|---|---|
|/commitmentPlans/commitmentAssociations/move/action|Machine Learning コミットメント プランの関連付けを移動します。|
|/commitmentPlans/commitmentAssociations/read|Machine Learning コミットメント プランの関連付けを読み取ります。|
|/commitmentPlans/delete|Machine Learning コミットメント プランを削除します。|
|/commitmentPlans/join/action|Machine Learning コミットメント プランに加入します。|
|/commitmentPlans/read|Machine Learning コミットメント プランを読み取ります。|
|/commitmentPlans/write|Machine Learning コミットメント プランを作成または更新します。|
|/locations/operationresults/read|Machine Learning 操作の結果を取得します。|
|/locations/operationsstatus/read|実行中の Machine Learning 操作の状態を取得します。|
|/operations/read|Machine Learning 操作を取得します。|
|/register/action|Machine Learning Web サービス リソース プロバイダーにサブスクリプションを登録し、Web サービスを作成できるようにします。|
|/skus/read|Machine Learning コミットメント プラン SKU を取得します。|
|/webServices/action|サポートされているリージョンのリージョン固有の Web サービス プロパティを作成します。|
|/webServices/delete|Machine Learning Web サービスを削除します。|
|/webServices/read|Machine Learning Web サービスを読み取ります。|
|/webServices/write|Machine Learning Web サービスを作成または更新します。|
|/Workspaces/delete|Machine Learning ワークスペースを削除します。|
|/Workspaces/listworkspacekeys/action|Machine Learning ワークスペースのキーを一覧表示します。|
|/Workspaces/read|Machine Learning ワークスペースを読み取ります。|
|/Workspaces/resyncstoragekeys/action|Machine Learning ワークスペース用に構成されたストレージ アカウントのキーを再同期します。|
|/Workspaces/write|Machine Learning ワークスペースを作成または更新します。|

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

| 操作 | [説明] |
|---|---|
|/operationalizationClusters/checkUpdate/action|運用化クラスターのシステム サービスの更新プログラムが使用可能かどうか確認します。|
|/operationalizationClusters/delete|ホスティング アカウントを削除します。|
|/operationalizationClusters/listKeys/action|運用化クラスターに関連付けられているキーを一覧表示します。|
|/operationalizationClusters/read|ホスティング アカウントを読み取ります。|
|/operationalizationClusters/updateSystem/action|運用化クラスターのシステム サービスを更新します。|
|/operationalizationClusters/write|ホスティング アカウントを作成または更新します。|
|/register/action|リソース プロバイダーにサブスクリプション ID を登録し、機械学習コンピューティング リソースを作成できるようにします。|

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

| 操作 | [説明] |
|---|---|
|/accounts/delete|ホスティング アカウントを削除します。|
|/accounts/read|ホスティング アカウントを読み取ります。|
|/accounts/write|ホスティング アカウントを作成または更新します。|
|/register/action|リソース プロバイダーにサブスクリプション ID を登録し、ホスティング アカウントを作成できるようにします。|

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

| 操作 | [説明] |
|---|---|
|/userAssignedIdentities/assign/action|既存のユーザー割り当て ID をリソースに割り当てるための RBAC アクションです。|
|/userAssignedIdentities/delete|既存のユーザー割り当て ID を削除します。|
|/userAssignedIdentities/read|既存のユーザー割り当て ID を取得します。|
|/userAssignedIdentities/write|新しいユーザー割り当て ID を作成するか、既存のユーザー割り当て ID に関連付けられているタグを更新します。|

## <a name="microsoftmanagedlab"></a>Microsoft.ManagedLab

| 操作 | [説明] |
|---|---|
|/labAccounts/CreateLab/action|ラボ アカウントにラボを作成します。|
|/labAccounts/delete|ラボ アカウントを削除します。|
|/labAccounts/labs/delete|ラボを削除します。|
|/labAccounts/labs/environmentSettings/delete|環境の設定を削除します。|
|/labAccounts/labs/environmentSettings/environments/delete|環境を削除します。|
|/labAccounts/labs/environmentSettings/environments/read|環境を読み取ります。|
|/labAccounts/labs/environmentSettings/environments/write|環境を追加または変更します。|
|/labAccounts/labs/environmentSettings/read|環境の設定を読み取ります。|
|/labAccounts/labs/environmentSettings/write|環境の設定を追加または変更します。|
|/labAccounts/labs/labVms/delete|ラボ仮想マシンを削除します。|
|/labAccounts/labs/labVms/read|ラボ仮想マシンを読み取ります。|
|/labAccounts/labs/labVms/write|ラボ仮想マシンを追加または変更します。|
|/labAccounts/labs/read|ラボを読み取ります。|
|/labAccounts/labs/write|ラボを追加または変更します。|
|/labAccounts/read|ラボ アカウントを読み取ります。|
|/labAccounts/write|ラボ アカウントを追加または変更します。|
|/locations/operations/read|操作を読み取ります。|
|/register/action|サブスクリプションを登録します。|

## <a name="microsoftmanagement"></a>Microsoft.Management

| 操作 | [説明] |
|---|---|
|/checkNameAvailability/action|指定された管理グループ名が有効で一意かどうかを確認します。|
|/getEntities/action|認証済みユーザーのすべてのエンティティ (管理グループ、サブスクリプションなど) を一覧表示します。|
|/managementGroups/delete|管理グループを削除します。|
|/managementGroups/read|認証済みユーザーの管理グループを一覧表示します。|
|/managementGroups/subscriptions/delete|管理グループからサブスクリプションの関連付けを解除します。|
|/managementGroups/subscriptions/write|既存のサブスクリプションと管理グループを関連付けます。|
|/managementGroups/write|管理グループを作成または更新します。|

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

| 操作 | [説明] |
|---|---|
|/ClassicDevServices/delete|クラシック開発サービス リソースに対して DELETE 操作を行います。|
|/ClassicDevServices/listSecrets/action|クラシック開発サービス リソースの管理キーを取得します。|
|/ClassicDevServices/listSingleSignOnToken/action|クラシック開発サービスのシングル サインオン URL を取得します。|
|/ClassicDevServices/read|クラシック開発サービスに対して GET 操作を行います。|
|/ClassicDevServices/regenerateKey/action|クラシック開発サービス リソースの管理キーを生成します。|
|/Operations/read|すべてのリソースの種類の操作を読み取ります。|

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

| 操作 | [説明] |
|---|---|
|/agreements/offers/plans/cancel/action|指定された Marketplace の項目の契約を取り消します。|
|/agreements/offers/plans/read|指定された Marketplace の項目の契約を返します。|
|/agreements/offers/plans/sign/action|指定された Marketplace の項目の契約に署名します。|
|/agreements/read|指定されたサブスクリプションのすべての契約を返します。|
|/offertypes/publishers/offers/plans/agreements/read|指定された Marketplace 仮想マシン項目の契約を取得します。|
|/offertypes/publishers/offers/plans/agreements/write|指定された Marketplace 仮想マシン項目の契約を署名またはキャンセルします。|

## <a name="microsoftmedia"></a>Microsoft.Media

| 操作 | [説明] |
|---|---|
|/checknameavailability/action|Media Services のアカウント名が使用可能かどうかを確認します。|
|/mediaservices/delete|Media Services アカウントを削除します。|
|/mediaservices/listKeys/action|Media Services アカウントの ACS キーを一覧表示します。|
|/mediaservices/read|Media Services アカウントを読み取ります。|
|/mediaservices/regenerateKey/action|Media Services の ACS キーを再生成します。|
|/mediaservices/syncStorageKeys/action|アタッチされた Azure Storage アカウントのストレージ キーを同期します。|
|/mediaservices/write|Media Services アカウントを作成または更新します。|
|/operations/read|Media Services アカウントを読み取ります。|
|/register/action|Media Services リソース プロバイダーにサブスクリプションを登録し、Media Services アカウントを作成できるようにします。|

## <a name="microsoftmigrate"></a>Microsoft.Migrate

| 操作 | [説明] |
|---|---|
|/Operations/read|公開された操作を読み取ります。|

## <a name="microsoftnetwork"></a>Microsoft.Network

| 操作 | [説明] |
|---|---|
|/applicationGatewayAvailableSslOptions/predefinedPolicies/read|Application Gateway の SSL の定義済みポリシーです。|
|/applicationGatewayAvailableSslOptions/read|Application Gateway で使用可能な SSL オプションです。|
|/applicationGatewayAvailableWafRuleSets/read|アプリケーション ゲートウェイの利用可能な Waf ルール セットを取得します。|
|/applicationGateways/backendAddressPools/join/action|アプリケーション ゲートウェイのバックエンド アドレス プールを接続します。|
|/applicationGateways/backendhealth/action|アプリケーション ゲートウェイのバックエンドの正常性を取得します。|
|/applicationGateways/delete|アプリケーション ゲートウェイを削除します。|
|/applicationGateways/effectiveNetworkSecurityGroups/action|Application Gateway で構成されているルート テーブルを取得します。|
|/applicationGateways/effectiveRouteTable/action|Application Gateway で構成されているルート テーブルを取得します。|
|/applicationGateways/providers/Microsoft.Insights/logDefinitions/read|Application Gateway のイベントを取得します。|
|/applicationGateways/providers/Microsoft.Insights/metricDefinitions/read|Application Gateway で使用可能なメトリックを取得します。|
|/applicationGateways/read|アプリケーション ゲートウェイを取得します。|
|/applicationGateways/setSecurityCenterConfiguration/action|Application Gateway の Security Center の構成を設定します。|
|/applicationGateways/start/action|アプリケーション ゲートウェイを起動します。|
|/applicationGateways/stop/action|アプリケーション ゲートウェイを停止します。|
|/applicationGateways/write|アプリケーション ゲートウェイを作成するか、アプリケーション ゲートウェイを更新します。|
|/applicationSecurityGroups/delete|アプリケーション セキュリティ グループを削除します。|
|/applicationSecurityGroups/joinIpConfiguration/action|IP 構成をアプリケーション セキュリティ グループに結合します。|
|/applicationSecurityGroups/joinNetworkSecurityRule/action|セキュリティ ルールをアプリケーション セキュリティ グループに結合します。|
|/applicationSecurityGroups/read|アプリケーション セキュリティ グループ ID を取得します。|
|/applicationSecurityGroups/write|アプリケーション セキュリティ グループを作成するか、既存のアプリケーション セキュリティ グループを更新します。|
|/bgpServiceCommunities/read|BGP サービス コミュニティを取得します。|
|/checkTrafficManagerNameAvailability/action|Traffic Manager の相対 DNS 名を使用できるかどうかを確認します。|
|/connections/delete|VirtualNetworkGatewayConnection を削除します。|
|/connections/read|VirtualNetworkGatewayConnection を取得します。|
|/connections/sharedkey/action|VirtualNetworkGatewayConnection の SharedKey を取得します。|
|/connections/sharedKey/read|VirtualNetworkGatewayConnection の SharedKey を取得します。|
|/connections/sharedKey/write|VirtualNetworkGatewayConnection の SharedKey を作成するか、既存の SharedKey を更新します。|
|/connections/vpndeviceconfigurationscript/read|VirtualNetworkGatewayConnection の VPN デバイス構成を取得します。|
|/connections/write|VirtualNetworkGatewayConnection を作成するか、既存の VirtualNetworkGatewayConnection を更新します。|
|/ddosProtectionPlans/ddosProtectionPlanProxies/delete|DDoS Protection プラン プロキシを削除します。|
|/ddosProtectionPlans/ddosProtectionPlanProxies/read|DDoS Protection プラン プロキシの定義を取得します。|
|/ddosProtectionPlans/ddosProtectionPlanProxies/write|DDoS Protection プラン プロキシを作成するか、既存の DDoS Protection プラン プロキシを更新します。|
|/ddosProtectionPlans/delete|DDoS Protection プランを削除します。|
|/ddosProtectionPlans/join/action|DDoS Protection プランを結合します。|
|/ddosProtectionPlans/read|DDoS Protection プランを取得します。|
|/ddosProtectionPlans/write|DDoS Protection プランを作成するか、DDoS Protection プランを更新します。 |
|/dnsoperationresults/read|DNS 操作の結果を取得します。|
|/dnsoperationstatuses/read|DNS 操作の状態を取得します。 |
|/dnszones/A/delete|指定された名前の "A" タイプのレコード セットを DNS ゾーンから削除します。|
|/dnszones/A/read|"A" タイプのレコード セットを JSON 形式で取得します。 レコード セットには、レコードの一覧と、TTL、タグ、および ETag が含まれます。|
|/dnszones/A/write|DNS ゾーン内の "A" タイプのレコード セットを作成または更新します。 レコード セットの現在のレコードが指定されたレコードに置き換えられます。|
|/dnszones/AAAA/delete|指定された名前の "AAAA" タイプのレコード セットを DNS ゾーンから削除します。|
|/dnszones/AAAA/read|"AAAA" タイプのレコード セットを JSON 形式で取得します。 レコード セットには、レコードの一覧と、TTL、タグ、および ETag が含まれます。|
|/dnszones/AAAA/write|DNS ゾーン内の "AAAA" タイプのレコード セットを作成または更新します。 レコード セットの現在のレコードが指定されたレコードに置き換えられます。|
|/dnszones/all/read|各タイプの DNS レコード セットを取得します。|
|/dnszones/CAA/delete|指定された名前の "CAA" タイプのレコード セットを DNS ゾーンから削除します。|
|/dnszones/CAA/read|"CAA" タイプのレコード セットを JSON 形式で取得します。 レコード セットには、TTL、タグ、ETag が含まれます。|
|/dnszones/CAA/write|DNS ゾーン内の "CAA" タイプのレコード セットを作成または更新します。 レコード セットの現在のレコードが指定されたレコードに置き換えられます。|
|/dnszones/CNAME/delete|指定された名前の "CNAME" タイプのレコード セットを DNS ゾーンから削除します。|
|/dnszones/CNAME/read|"CNAME" タイプのレコード セットを JSON 形式で取得します。 レコード セットには、TTL、タグ、ETag が含まれます。|
|/dnszones/CNAME/write|DNS ゾーン内の "CNAME" タイプのレコード セットを作成または更新します。 レコード セットの現在のレコードが指定されたレコードに置き換えられます。|
|/dnszones/delete|JSON 形式の DNS ゾーンを削除します。 ゾーンのプロパティには、タグ、ETag、numberOfRecordSets、maxNumberOfRecordSets があります。|
|/dnszones/MX/delete|指定された名前の "MX" タイプのレコード セットを DNS ゾーンから削除します。|
|/dnszones/MX/read|"MX" タイプのレコード セットを JSON 形式で取得します。 レコード セットには、レコードの一覧と、TTL、タグ、および ETag が含まれます。|
|/dnszones/MX/write|DNS ゾーン内の "MX" タイプのレコード セットを作成または更新します。 レコード セットの現在のレコードが指定されたレコードに置き換えられます。|
|/dnszones/NS/delete|NS タイプの DNS レコード セットを削除します。|
|/dnszones/NS/read|NS タイプの DNS レコード セットを取得します。|
|/dnszones/NS/write|NS タイプの DNS レコード セットを作成または更新します。|
|/dnszones/providers/Microsoft.Insights/diagnosticSettings/read|DNS ゾーンの診断設定を取得します。|
|/dnszones/providers/Microsoft.Insights/diagnosticSettings/write|DNS ゾーンの診断設定を作成または更新します。|
|/dnszones/providers/Microsoft.Insights/metricDefinitions/read|DNS ゾーンのメトリック定義を取得します。|
|/dnszones/PTR/delete|指定された名前の "PTR" タイプのレコード セットを DNS ゾーンから削除します。|
|/dnszones/PTR/read|"PTR" タイプのレコード セットを JSON 形式で取得します。 レコード セットには、レコードの一覧と、TTL、タグ、および ETag が含まれます。|
|/dnszones/PTR/write|DNS ゾーン内の "PTR" タイプのレコード セットを作成または更新します。 レコード セットの現在のレコードが指定されたレコードに置き換えられます。|
|/dnszones/read|DNS ゾーンを JSON 形式で取得します。 ゾーンのプロパティには、タグ、ETag、numberOfRecordSets、maxNumberOfRecordSets があります。 このコマンドでは、ゾーン内のレコード セットは取得されません。|
|/dnszones/recordsets/read|各タイプの DNS レコード セットを取得します。|
|/dnszones/SOA/read|SOA タイプの DNS レコード セットを取得します。|
|/dnszones/SOA/write|SOA タイプの DNS レコード セットを作成または更新します。|
|/dnszones/SRV/delete|指定された名前の "SRV" タイプのレコード セットを DNS ゾーンから削除します。|
|/dnszones/SRV/read|"SRV" タイプのレコード セットを JSON 形式で取得します。 レコード セットには、レコードの一覧と、TTL、タグ、および ETag が含まれます。|
|/dnszones/SRV/write|SRV タイプのレコード セットを作成または更新します。|
|/dnszones/TXT/delete|指定された名前の "TXT" タイプのレコード セットを DNS ゾーンから削除します。|
|/dnszones/TXT/read|"TXT" タイプのレコード セットを JSON 形式で取得します。 レコード セットには、レコードの一覧と、TTL、タグ、および ETag が含まれます。|
|/dnszones/TXT/write|DNS ゾーン内の "TXT" タイプのレコード セットを作成または更新します。 レコード セットの現在のレコードが指定されたレコードに置き換えられます。|
|/dnszones/write|リソース グループ内の DNS ゾーンを作成または更新します。  DNS ゾーン リソースのタグを更新する際に使用します。 このコマンドを使用して、ゾーン内のレコード セットを作成または更新することはできません。|
|/expressRouteCircuits/authorizations/delete|ExpressRouteCircuit の承認を削除します。|
|/expressRouteCircuits/authorizations/read|ExpressRouteCircuit の承認を取得します。|
|/expressRouteCircuits/authorizations/write|ExpressRouteCircuit の承認を作成するか、既存の承認を更新します。|
|/expressRouteCircuits/delete|ExpressRouteCircuit を削除します。|
|/expressRouteCircuits/peerings/arpTables/action|ExpressRouteCircuit のピアリングの ArpTable を取得します。|
|/expressRouteCircuits/peerings/connections/delete|ExpressRouteCircuit の接続を削除します。|
|/expressRouteCircuits/peerings/connections/read|ExpressRouteCircuit の接続を取得します。|
|/expressRouteCircuits/peerings/connections/write|ExpressRouteCircuit の接続リソースを作成するか、既存の接続リソースを更新します。|
|/expressRouteCircuits/peerings/delete|ExpressRouteCircuit のピアリングを削除します。|
|/expressRouteCircuits/peerings/read|ExpressRouteCircuit のピアリングを取得します。|
|/expressRouteCircuits/peerings/routeTables/action|ExpressRouteCircuit のピアリングの RouteTable を取得します。|
|/expressRouteCircuits/peerings/routeTablesSummary/action|ExpressRouteCircuit のピアリングの RouteTable の概要を取得します。|
|/expressRouteCircuits/peerings/stats/read|ExpressRouteCircuit のピアリングの統計を取得します。|
|/expressRouteCircuits/peerings/write|ExpressRouteCircuit のピアリングを作成するか、既存のピアリングを更新します。|
|/expressRouteCircuits/providers/Microsoft.Insights/diagnosticSettings/read|ExpressRoute 回線の診断設定を取得します。|
|/expressRouteCircuits/providers/Microsoft.Insights/diagnosticSettings/write|ExpressRoute 回線の診断設定を作成または更新します。|
|/expressRouteCircuits/providers/Microsoft.Insights/logDefinitions/read|ExpressRoute 回線のイベントを取得します。|
|/expressRouteCircuits/providers/Microsoft.Insights/metricDefinitions/read|ExpressRoute 回線のメトリック定義を取得します。|
|/expressRouteCircuits/read|ExpressRouteCircuit を取得します。|
|/expressRouteCircuits/stats/read|ExpressRouteCircuit の統計を取得します。|
|/expressRouteCircuits/write|ExpressRouteCircuit を作成するか、既存の ExpressRouteCircuit を更新します。|
|/expressRouteCrossConnections/delete|ExpressRoute のクロス接続を削除します。|
|/expressRouteCrossConnections/join/action|ExpressRoute のクロス接続を結合します。|
|/expressRouteCrossConnections/peerings/arpTables/action|ExpressRoute のクロス接続のピアリング ARP テーブルを取得します。|
|/expressRouteCrossConnections/peerings/delete|ExpressRoute のクロス接続のピアリングを削除します。|
|/expressRouteCrossConnections/peerings/read|ExpressRoute のクロス接続のピアリングを取得します。|
|/expressRouteCrossConnections/peerings/routeTables/action|ExpressRoute のクロス接続のピアリング ルート テーブルを取得します。|
|/expressRouteCrossConnections/peerings/routeTableSummary/action|ExpressRoute のクロス接続のピアリング ルート テーブル概要を取得します。|
|/expressRouteCrossConnections/peerings/stats/read|ExpressRoute のクロス接続のピアリング統計を取得します。|
|/expressRouteCrossConnections/peerings/write|ExpressRoute のクロス接続ピアリングを作成するか、ExpressRoute の既存のクロス接続ピアリングを更新します。|
|/expressRouteCrossConnections/read|ExpressRoute のクロス接続を取得します。|
|/expressRouteCrossConnections/write|ExpressRoute のクロス接続を作成または更新します。|
|/expressRouteServiceProviders/read|Express Route サービス プロバイダーを取得します。|
|/loadBalancers/backendAddressPools/join/action|ロード バランサーのバックエンド アドレス プールを接続します。|
|/loadBalancers/backendAddressPools/read|ロード バランサーのバックエンド アドレス プールの定義を取得します。|
|/loadBalancers/delete|ロード バランサーを削除します。|
|/loadBalancers/frontendIPConfigurations/read|ロード バランサーのフロントエンド IP 構成定義を取得します。|
|/loadBalancers/inboundNatPools/join/action|ロード バランサーの受信 NAT プールを接続します。|
|/loadBalancers/inboundNatPools/read|ロード バランサーの受信 NAT プールの定義を取得します。|
|/loadBalancers/inboundNatRules/delete|ロード バランサーの受信 NAT 規則を削除します。|
|/loadBalancers/inboundNatRules/join/action|ロード バランサーの受信 NAT 規則を接続します。|
|/loadBalancers/inboundNatRules/read|ロード バランサーの受信 NAT 規則の定義を取得します。|
|/loadBalancers/inboundNatRules/write|ロード バランサーの受信 NAT 規則を作成するか、既存の受信 NAT 規則を更新します。|
|/loadBalancers/loadBalancingRules/read|ロード バランサーの負荷分散規則の定義を取得します。|
|/loadBalancers/networkInterfaces/read|ロード バランサーの下のすべてのネットワーク インターフェイスへの参照を取得します。|
|/loadBalancers/outboundNatRules/read|ロード バランサーの送信 NAT 規則の定義を取得します。|
|/loadBalancers/probes/join/action|ロード バランサーのプローブの使用を許可します。 たとえば、このアクセス許可では、VM スケール セットの healthProbe プロパティでプローブを参照できます。|
|/loadBalancers/probes/read|ロード バランサー プローブを取得します。|
|/loadBalancers/providers/Microsoft.Insights/diagnosticSettings/read|ロード バランサーの診断設定を取得します。|
|/loadBalancers/providers/Microsoft.Insights/diagnosticSettings/write|ロード バランサーの診断設定を作成または更新します。|
|/loadBalancers/providers/Microsoft.Insights/logDefinitions/read|ロード バランサーのイベントを取得します。|
|/loadBalancers/providers/Microsoft.Insights/metricDefinitions/read|ロード バランサーで使用可能なメトリックを取得します。|
|/loadBalancers/read|ロード バランサー定義を取得します。|
|/loadBalancers/virtualMachines/read|ロード バランサーの下のすべての仮想マシンへの参照を取得します。|
|/loadBalancers/write|ロード バランサーを作成するか、既存のロード バランサーを更新します。|
|/localnetworkgateways/delete|LocalNetworkGateway を削除します。|
|/localnetworkgateways/read|LocalNetworkGateway を取得します。|
|/localnetworkgateways/write|LocalNetworkGateway を作成するか、既存の LocalNetworkGateway を更新します。|
|/locations/checkDnsNameAvailability/read|指定された場所で DNS ラベルを使用できるかどうかを確認します。|
|/locations/operationResults/read|非同期の POST 操作または DELETE 操作の結果を取得します。|
|/locations/operations/read|非同期操作の状態を表す操作リソースを取得します。|
|/locations/usages/read|リソースの使用状況メトリックを取得します。|
|/locations/virtualNetworkAvailableEndpointServices/read|使用可能な仮想ネットワーク エンドポイント サービスの一覧を取得します。|
|/networkInterfaces/delete|ネットワーク インターフェイスを削除します。|
|/networkInterfaces/diagnosticIdentity/read|リソースの診断 ID を取得します。|
|/networkInterfaces/effectiveNetworkSecurityGroups/action|VM のネットワーク インターフェイスで構成されているネットワーク セキュリティ グループを取得します。|
|/networkInterfaces/effectiveRouteTable/action|VM のネットワーク インターフェイスで構成されているルート テーブルを取得します。|
|/networkInterfaces/ipconfigurations/read|ネットワーク インターフェイスの IP 構成定義を取得します。 |
|/networkInterfaces/join/action|仮想マシンをネットワーク インターフェイスに接続します。|
|/networkInterfaces/loadBalancers/read|ネットワーク インターフェイスが含まれているすべてのロード バランサーを取得します。|
|/networkInterfaces/providers/Microsoft.Insights/metricDefinitions/read|ネットワーク インターフェイスで使用可能なメトリックを取得します。|
|/networkInterfaces/read|ネットワーク インターフェイスの定義を取得します。 |
|/networkInterfaces/write|ネットワーク インターフェイスを作成するか、既存のネットワーク インターフェイスを更新します。 |
|/networkSecurityGroups/defaultSecurityRules/read|既定のセキュリティ規則の定義を取得します。|
|/networkSecurityGroups/delete|ネットワーク セキュリティ グループを削除します。|
|/networkSecurityGroups/join/action|ネットワーク セキュリティ グループに参加します。|
|/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/read|ネットワーク セキュリティ グループの診断設定を取得します。|
|/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/write|ネットワーク セキュリティ グループの診断設定を作成または更新します。この操作は、Insghts リソース プロバイダーによって補完されます。|
|/networksecuritygroups/providers/Microsoft.Insights/logDefinitions/read|ネットワーク セキュリティ グループのイベントを取得します。|
|/networkSecurityGroups/read|ネットワーク セキュリティ グループの定義を取得します。|
|/networkSecurityGroups/securityRules/delete|セキュリティ規則を削除します。|
|/networkSecurityGroups/securityRules/read|セキュリティ規則の定義を取得します。|
|/networkSecurityGroups/securityRules/write|セキュリティ規則を作成するか、既存のセキュリティ規則を更新します。|
|/networkSecurityGroups/write|ネットワーク セキュリティ グループを作成するか、既存のネットワーク セキュリティ グループを更新します。|
|/networkWatchers/availableProvidersList/action|指定された Azure リージョンで利用可能なすべてのインターネット サービス プロバイダーを返します。|
|/networkWatchers/azureReachabilityReport/action|指定された場所から Azure リージョンまでのインターネット サービス プロバイダーの相対待機時間スコアを返します。|
|/networkWatchers/configureFlowLog/action|ターゲット リソースのフロー ログを構成します。|
|/networkWatchers/connectionMonitors/delete|接続モニターを削除します。|
|/networkWatchers/connectionMonitors/providers/Microsoft.Insights/ diagnosticSettings/read|接続モニターの診断設定を取得します。|
|/networkWatchers/connectionMonitors/providers/Microsoft.Insights/ diagnosticSettings/write|接続モニターの診断設定を作成または更新します。|
|/networkWatchers/connectionMonitors/providers/Microsoft.Insights/ metricDefinitions/read|接続モニターで使用可能なメトリックを取得します。|
|/networkWatchers/connectionMonitors/query/action|指定されたエンドポイント間の接続の監視をクエリします。|
|/networkWatchers/connectionMonitors/read|接続モニターの詳細を取得します。|
|/networkWatchers/connectionMonitors/start/action|指定されたエンドポイント間の接続の監視を開始します。|
|/networkWatchers/connectionMonitors/stop/action|指定されたエンドポイント間の監視接続を停止/一時停止します。|
|/networkWatchers/connectionMonitors/write|接続モニターを作成します。|
|/networkWatchers/connectivityCheck/action|仮想マシンから、別の VM や任意のリモート サーバーなどの指定されたエンドポイントまでの、直接 TCP 接続が確立されたかどうかを確認します。|
|/networkWatchers/delete|Network Watcher を削除します。|
|/networkWatchers/ipFlowVerify/action|特定の宛先との間で送受信されるパケットを許可するか拒否するかを返します。|
|/networkWatchers/lenses/delete|レンズを削除します。|
|/networkWatchers/lenses/query/action|指定されたエンドポイントでのネットワーク トラフィックの監視をクエリします。|
|/networkWatchers/lenses/read|レンズの詳細を取得します。|
|/networkWatchers/lenses/start/action|指定されたエンドポイントでネットワーク トラフィックの監視を開始します。|
|/networkWatchers/lenses/stop/action|指定されたエンドポイントでのネットワーク トラフィックの監視を停止/一時停止します。|
|/networkWatchers/lenses/write|レンズを作成します。|
|/networkWatchers/nextHop/action|指定されたターゲットと宛先 IP アドレスについて、次ホップの種類と次ホップの IP アドレスを返します。|
|/networkWatchers/packetCaptures/delete|パケット キャプチャを削除します。|
|/networkWatchers/packetCaptures/queryStatus/action|パケット キャプチャ リソースのプロパティと状態に関する情報を取得します。|
|/networkWatchers/packetCaptures/read|パケット キャプチャ定義を取得します。|
|/networkWatchers/packetCaptures/stop/action|実行中のパケット キャプチャ セッションを停止します。|
|/networkWatchers/packetCaptures/write|パケット キャプチャを作成します。|
|/networkWatchers/queryFlowLogStatus/action|リソースのフロー ログの状態を取得します。|
|/networkWatchers/queryTroubleshootResult/action|実行済みまたは現在実行中のトラブルシューティング操作のトラブルシューティングの結果を取得します。|
|/networkWatchers/read|Network Watcher の定義を取得します。|
|/networkWatchers/securityGroupView/action|VM に適用されている構成済みの有効なネットワーク セキュリティ グループ規則を表示します。|
|/networkWatchers/topology/action|リソース グループ内のリソースとその関係のネットワーク レベル ビューを取得します。|
|/networkWatchers/troubleshoot/action|Azure のネットワーク リソースでトラブルシューティングを開始します。|
|/networkWatchers/write|Network Watcher を作成するか、既存の Network Watcher を更新します。|
|/operations/read|使用可能な操作を取得します。|
|/publicIPAddresses/delete|パブリック IP アドレスを削除します。|
|/publicIPAddresses/join/action|パブリック IP アドレスに接続します。|
|/publicIPAddresses/providers/Microsoft.Insights/diagnosticSettings/read|パブリック IP アドレスの診断設定を取得します。|
|/publicIPAddresses/providers/Microsoft.Insights/diagnosticSettings/write|パブリック IP アドレスの診断設定を作成または更新します。|
|/publicIPAddresses/providers/Microsoft.Insights/logDefinitions/read|パブリック IP アドレスのログ定義を取得します。|
|/publicIPAddresses/providers/Microsoft.Insights/metricDefinitions/read|パブリック IP アドレスのメトリック定義を取得します。|
|/publicIPAddresses/read|パブリック IP アドレス定義を取得します。|
|/publicIPAddresses/write|パブリック IP アドレスを作成するか、既存のパブリック IP アドレスを更新します。 |
|/register/action|サブスクリプションを登録します。|
|/routeFilters/delete|ルート フィルター定義を削除します。|
|/routeFilters/join/action|ルート フィルターを結合します。|
|/routeFilters/read|ルート フィルター定義を取得します。|
|/routeFilters/routeFilterRules/delete|ルート フィルター規則の定義を削除します。|
|/routeFilters/routeFilterRules/read|ルート フィルター規則の定義を取得します。|
|/routeFilters/routeFilterRules/write|ルート フィルター規則を作成するか、既存のルート フィルター規則を更新します。|
|/routeFilters/write|ルート フィルターを作成するか、既存のルート フィルターを更新します。|
|/routeTables/delete|ルート テーブルの定義を削除します。|
|/routeTables/join/action|ルート テーブルを結合します。|
|/routeTables/read|ルート テーブルの定義を取得します。|
|/routeTables/routes/delete|ルート定義を削除します。|
|/routeTables/routes/read|ルート定義を取得します。|
|/routeTables/routes/write|ルートを作成するか、既存のルートを更新します。|
|/routeTables/write|ルート テーブルを作成するか、既存のルート テーブルを更新します。|
|/securegateways/applicationRuleCollections/delete|安全なゲートウェイのアプリケーション ルール コレクションを削除します。|
|/securegateways/applicationRuleCollections/read|指定された安全なゲートウェイのアプリケーション ルール コレクションを取得します。|
|/securegateways/applicationRuleCollections/write|安全なゲートウェイのアプリケーション ルール コレクションを作成または更新します。|
|/securegateways/delete|安全なゲートウェイを削除します。|
|/securegateways/networkRuleCollections/delete|安全なゲートウェイのネットワーク ルール コレクションを削除します。|
|/securegateways/networkRuleCollections/read|指定された安全なゲートウェイのネットワーク ルール コレクションを取得します。|
|/securegateways/networkRuleCollections/write|安全なゲートウェイのネットワーク ルール コレクションを作成または更新します。|
|/securegateways/read|安全なゲートウェイを取得します。|
|/securegateways/write|安全なゲートウェイを作成または更新します。|
|/serviceEndpointPolicies/delete|サービス エンドポイント ポリシーを削除します。|
|/serviceEndpointPolicies/join/action|サービス エンドポイント ポリシーを結合します。|
|/serviceEndpointPolicies/joinSubnet/action|サブネットをサービス エンドポイント ポリシーに結合します。|
|/serviceEndpointPolicies/read|サービス エンドポイント ポリシーの説明を取得します。|
|/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/delete|サービス エンドポイント ポリシーの定義を削除します。|
|/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/read|サービス エンドポイント ポリシーの定義の説明を取得します。|
|/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/write|サービス エンドポイント ポリシーの定義を作成するか、既存のサービス エンドポイント ポリシーの定義を更新します。|
|/serviceEndpointPolicies/write|サービス エンドポイント ポリシーを作成するか、既存のサービス エンドポイント ポリシーを更新します。|
|/trafficManagerGeographicHierarchies/read|地理的トラフィック ルーティング方法で使用できるリージョンを含む、Traffic Manager の地理的階層を取得します。|
|/trafficManagerProfiles/azureEndpoints/delete|既存の Traffic Manager プロファイルから Azure エンドポイントを削除します。 Traffic Manager は、削除された Azure エンドポイントへのトラフィックのルーティングを停止します。|
|/trafficManagerProfiles/azureEndpoints/read|Traffic Manager プロファイルに属する Azure エンドポイントを取得します (その Azure エンドポイントのすべてのプロパティなど)。|
|/trafficManagerProfiles/azureEndpoints/write|既存の Traffic Manager プロファイルに新しい Azure エンドポイントを追加するか、その Traffic Manager プロファイルの既存の Azure エンドポイントのプロパティを更新します。|
|/trafficManagerProfiles/delete|Traffic Manager プロファイルを削除します。 Traffic Manager プロファイルに関連付けられているすべての設定が失われ、プロファイルを使用してトラフィックをルーティングすることはできなくなります。|
|/trafficManagerProfiles/externalEndpoints/delete|既存の Traffic Manager プロファイルから外部エンドポイントを削除します。 Traffic Manager は、削除された外部エンドポイントへのトラフィックのルーティングを停止します。|
|/trafficManagerProfiles/externalEndpoints/read|Traffic Manager プロファイルに属する外部エンドポイントを取得します (その外部エンドポイントのすべてのプロパティなど)。|
|/trafficManagerProfiles/externalEndpoints/write|既存の Traffic Manager プロファイルに新しい外部エンドポイントを追加するか、その Traffic Manager プロファイルの既存の外部エンドポイントのプロパティを更新します。|
|/trafficManagerProfiles/heatMaps/read|場所およびソース IP 別にクエリ数と待機時間のデータを含む指定された Traffic Manager プロファイルに対する Traffic Manager ヒート マップを取得します。|
|/trafficManagerProfiles/nestedEndpoints/delete|既存の Traffic Manager プロファイルから入れ子になったエンドポイントを削除します。 Traffic Manager は、削除された入れ子になったエンドポイントへのトラフィックのルーティングを停止します。|
|/trafficManagerProfiles/nestedEndpoints/read|Traffic Manager プロファイルに属する入れ子になったエンドポイントを取得します (その入れ子になったエンドポイントのすべてのプロパティなど)。|
|/trafficManagerProfiles/nestedEndpoints/write|既存の Traffic Manager プロファイルに新しい入れ子になったエンドポイントを追加するか、その Traffic Manager プロファイルの既存の入れ子になったエンドポイントのプロパティを更新します。|
|/trafficManagerProfiles/providers/Microsoft.Insights/diagnosticSettings/read|Traffic Manager の診断設定を取得します。|
|/trafficManagerProfiles/providers/Microsoft.Insights/diagnosticSettings/write|Traffic Manager の診断設定を作成または更新します。この操作は、Insghts リソース プロバイダーによって補完されます。|
|/trafficManagerProfiles/providers/Microsoft.Insights/logDefinitions/read|Traffic Manager のイベントを取得します。|
|/trafficManagerProfiles/providers/Microsoft.Insights/metricDefinitions/read|Traffic Manager で使用可能なメトリックを取得します。|
|/trafficManagerProfiles/read|Traffic Manager プロファイル構成を取得します。 これには、DNS 設定、トラフィック ルーティング設定、エンドポイント監視設定、この Traffic Manager プロファイルによってルーティングされるエンドポイントの一覧が含まれます。|
|/trafficManagerProfiles/write|Traffic Manager プロファイルを作成するか、既存の Traffic Manager プロファイルの構成を変更します。 これには、プロファイルの有効化または無効化と、DNS 設定、トラフィック ルーティング設定、またはエンドポイント監視設定の変更が含まれます。 Traffic Manager プロファイルによってルーティングされるエンドポイントを追加、削除、有効化、または無効化できます。|
|/trafficManagerUserMetricsKeys/delete|リアルタイムのユーザー メトリック収集に使用されるサブスクリプション レベルのキーを削除します。|
|/trafficManagerUserMetricsKeys/read|リアルタイムのユーザー メトリック収集に使用されるサブスクリプション レベルのキーを取得します。|
|/trafficManagerUserMetricsKeys/write|リアルタイムのユーザー メトリック収集に使用されるサブスクリプション レベルのキーを新しく作成します。|
|/unregister/action|サブスクリプションの登録を解除します。|
|/virtualHubs/delete|仮想ハブを削除します。|
|/virtualHubs/hubVirtualNetworkConnections/delete|HubVirtualNetworkConnection を削除します。|
|/virtualHubs/hubVirtualNetworkConnections/read|HubVirtualNetworkConnection を取得します。|
|/virtualHubs/hubVirtualNetworkConnections/write|HubVirtualNetworkConnection を作成または更新します。|
|/virtualHubs/read|仮想ハブを取得します。|
|/virtualHubs/write|仮想ハブを作成または更新します。|
|/virtualnetworkgateways/connections/read|VirtualNetworkGatewayConnection を取得します。|
|/virtualNetworkGateways/delete|virtualNetworkGateway を削除します。|
|/virtualnetworkgateways/generatevpnclientpackage/action|virtualNetworkGateway の VpnClient パッケージを生成します。|
|/virtualnetworkgateways/generatevpnprofile/action|VirtualNetworkGateway の VpnProfile パッケージを生成します。|
|/virtualnetworkgateways/getadvertisedroutes/action|virtualNetworkGateway でアドバタイズされたルートを取得します。|
|/virtualnetworkgateways/getbgppeerstatus/action|virtualNetworkGateway の BGP ピアの状態を取得します。|
|/virtualnetworkgateways/getlearnedroutes/action|virtualnetworkgateway で学習されたルートを取得します。|
|/virtualnetworkgateways/getvpnclientipsecparameters/action|VirtualNetworkGateway P2S クライアントの Vpnclient Ipsec パラメーターを取得します。|
|/virtualnetworkgateways/getvpnprofilepackageurl/action|事前生成済みの VPN クライアント プロファイル パッケージの URL を取得します。|
|/virtualNetworkGateways/providers/Microsoft.Insights/diagnosticSettings/read|Virtual Network ゲートウェイの診断設定を取得します。|
|/virtualNetworkGateways/providers/Microsoft.Insights/diagnosticSettings/write|Virtual Network ゲートウェイの診断設定を作成または更新します。この操作は、Insghts リソース プロバイダーによって補完されます。|
|/virtualNetworkGateways/providers/Microsoft.Insights/logDefinitions/read|Virtual Network ゲートウェイのイベントを取得します。|
|/virtualNetworkGateways/providers/Microsoft.Insights/metricDefinitions/read|Virtual Network ゲートウェイで使用可能なメトリックを取得します。|
|/virtualNetworkGateways/read|VirtualNetworkGateway を取得します。|
|/virtualnetworkgateways/reset/action|virtualNetworkGateway をリセットします。|
|/virtualnetworkgateways/setvpnclientipsecparameters/action|VirtualNetworkGateway P2S クライアントの Vpnclient Ipsec パラメーターを設定します。|
|/virtualnetworkgateways/supportedvpndevices/action|サポートされている VPN デバイスを一覧表示します。|
|/virtualNetworkGateways/write|VirtualNetworkGateway を作成または更新します。|
|/virtualNetworks/checkIpAddressAvailability/read|指定された仮想ネットワークで IP アドレスを使用できるかどうかを確認します。|
|/virtualNetworks/customViews/get/action|仮想ネットワークのカスタム ビューの内容を取得します。|
|/virtualNetworks/customViews/read|仮想ネットワークのカスタム ビューの定義を取得します。|
|/virtualNetworks/delete|仮想ネットワークを削除します。|
|/virtualNetworks/peer/action|仮想ネットワークを別の仮想ネットワークとピアリングします。|
|/virtualNetworks/providers/Microsoft.Insights/diagnosticSettings/read|仮想ネットワークの診断設定を取得します。|
|/virtualNetworks/providers/Microsoft.Insights/diagnosticSettings/write|仮想ネットワークの診断設定を作成または更新します。|
|/virtualNetworks/providers/Microsoft.Insights/logDefinitions/read|仮想ネットワークのログ定義を取得します。|
|/virtualNetworks/providers/Microsoft.Insights/metricDefinitions/read|仮想ネットワークのメトリック定義を取得します。|
|/virtualNetworks/read|仮想ネットワークの定義を取得します。|
|/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete|仮想ネットワークのピアリング プロキシを削除します。|
|/virtualNetworks/remoteVirtualNetworkPeeringProxies/read|仮想ネットワークのピアリング プロキシの定義を取得します。|
|/virtualNetworks/remoteVirtualNetworkPeeringProxies/write|仮想ネットワーク ピアリング プロキシを作成するか、既存の仮想ネットワーク ピアリング プロキシを更新します。|
|/virtualNetworks/subnets/delete|仮想ネットワーク サブネットを削除します。|
|/virtualNetworks/subnets/join/action|仮想ネットワークに参加します。|
|/virtualNetworks/subnets/joinViaServiceEndpoint/action|ストレージ アカウントや SQL Database などのリソースをサブネットに結合します。|
|/virtualNetworks/subnets/read|仮想ネットワーク サブネットの定義を取得します。|
|/virtualNetworks/subnets/resourceNavigationLinks/delete|リソース ナビゲーション リンクを削除します。|
|/virtualNetworks/subnets/resourceNavigationLinks/read|リソース ナビゲーション リンクの定義を取得します。|
|/virtualNetworks/subnets/resourceNavigationLinks/write|リソース ナビゲーション リンクを作成するか、既存のリソース ナビゲーション リンクを更新します。|
|/virtualNetworks/subnets/virtualMachines/read|仮想ネットワーク サブネットのすべての仮想マシンへの参照を取得します。|
|/virtualNetworks/subnets/write|仮想ネットワーク サブネットを作成するか、既存の仮想ネットワーク サブネットを更新します。|
|/virtualNetworks/taggedTrafficConsumers/delete|タグ付きのトラフィック コンシューマーを削除します。|
|/virtualNetworks/taggedTrafficConsumers/read|タグ付きのトラフィック コンシューマーの定義を取得します。|
|/virtualNetworks/taggedTrafficConsumers/validate/action|タグ付きのトラフィック コンシューマーを検証します。|
|/virtualNetworks/taggedTrafficConsumers/write|タグ付きのトラフィック コンシューマーを作成するか、既存のタグ付きのトラフィック コンシューマーを更新します|
|/virtualNetworks/usages/read|仮想ネットワークの各サブネットの IP の使用状況を取得します。|
|/virtualNetworks/virtualMachines/read|仮想ネットワークのすべての仮想マシンへの参照を取得します。|
|/virtualNetworks/virtualNetworkPeerings/delete|仮想ネットワーク ピアリングを削除します。|
|/virtualNetworks/virtualNetworkPeerings/read|仮想ネットワーク ピアリングの定義を取得します。|
|/virtualNetworks/virtualNetworkPeerings/write|仮想ネットワーク ピアリングを作成するか、既存の仮想ネットワーク ピアリングを更新します。|
|/virtualNetworks/write|仮想ネットワークを作成するか、既存の仮想ネットワークを更新します。|
|/virtualNetworkTaps/delete|仮想ネットワーク タップを削除します。|
|/virtualNetworkTaps/join/action|仮想ネットワーク タップに参加します。|
|/virtualNetworkTaps/read|仮想ネットワーク タップを取得します。|
|/virtualNetworkTaps/write|仮想ネットワーク タップを作成または更新します。|
|/virtualwans/delete|仮想 WAN を削除します。|
|/virtualwans/read|仮想 WAN を取得します。|
|/virtualWans/virtualHubProxies/delete|仮想ハブ プロキシを削除します。|
|/virtualWans/virtualHubProxies/read|仮想ハブ プロキシの定義を取得します。|
|/virtualWans/virtualHubProxies/write|仮想ハブ プロキシを作成または更新します。|
|/virtualwans/virtualHubs/read|仮想 WAN に関連付けられているすべての仮想ハブを取得します。|
|/virtualwans/vpnconfiguration/read|VPN 構成を取得します。|
|/virtualWans/vpnSiteProxies/delete|VPN サイト プロキシを削除します。|
|/virtualWans/vpnSiteProxies/read|VPN サイト プロキシの定義を取得します。|
|/virtualWans/vpnSiteProxies/write|VPN サイト プロキシを作成または更新します。|
|/virtualwans/vpnSites/read|仮想 WAN に関連付けられているすべての VPN サイトを取得します。|
|/virtualwans/write|仮想 WAN ハブを作成または更新します。|
|/vpnGateways/read|VpnGateway を取得します。|
|/vpnGateways/vpnConnections/read|VpnConnection を取得します。|
|/vpnGateways/vpnConnections/write|VpnConnection を設定します。|
|/vpnGateways/write|VpnGateway を設定します。|
|/vpnsites/delete|VPN サイト リソースを削除します。|
|/vpnsites/read|VPN サイト リソースを取得します。|
|/vpnsites/write|VPN サイト リソースを作成または更新します。|

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

| 操作 | [説明] |
|---|---|
|/CheckNamespaceAvailability/action|指定された名前空間リソース名を NotificationHub サービス内で使用できるかどうかを確認します。|
|/Namespaces/authorizationRules/action|名前空間の承認規則の説明の一覧を取得します。|
|/Namespaces/authorizationRules/delete|名前空間の承認規則を削除します。 名前空間の既定の承認規則は削除できません。 |
|/Namespaces/authorizationRules/listkeys/action|名前空間への接続文字列を取得します。|
|/Namespaces/authorizationRules/read|名前空間の承認規則の説明の一覧を取得します。|
|/Namespaces/authorizationRules/regenerateKeys/action|名前空間の承認規則では、Primary/SecondaryKey を再生成します。再生成する必要があるキーを指定します。|
|/Namespaces/authorizationRules/write|名前空間レベルの承認規則を作成し、そのプロパティを更新します。 承認規則のアクセス権、プライマリ キー、セカンダリ キーを更新できます。|
|/Namespaces/CheckNotificationHubAvailability/action|指定された NotificationHub 名を名前空間内で使用できるかどうかを確認します。|
|/Namespaces/Delete|名前空間リソースを削除します。|
|/Namespaces/NotificationHubs/authorizationRules/action|Notification Hub の承認規則の一覧を取得します。|
|/Namespaces/NotificationHubs/authorizationRules/delete|Notification Hub の承認規則を削除します。|
|/Namespaces/NotificationHubs/authorizationRules/listkeys/action|Notification Hub への接続文字列を取得します。|
|/Namespaces/NotificationHubs/authorizationRules/read|Notification Hub の承認規則の一覧を取得します。|
|/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action|Notification Hub の承認規則では、Primary/SecondaryKey を再生成します。再生成する必要があるキーを指定します。|
|/Namespaces/NotificationHubs/authorizationRules/write|Notification Hub の承認規則を作成し、そのプロパティを更新します。 承認規則のアクセス権、プライマリ キー、セカンダリ キーを更新できます。|
|/Namespaces/NotificationHubs/debugSend/action|テスト プッシュ通知を送信します。|
|/Namespaces/NotificationHubs/Delete|Notification Hub リソースを削除します。|
|/Namespaces/NotificationHubs/metricDefinitions/read|名前空間のメトリック リソースの説明の一覧を取得します。|
|/Namespaces/NotificationHubs/pnsCredentials/action|Notification Hub の PNS の資格情報をすべて取得します。 これには、WNS、MPNS、APNS、GCM、Baidu の各資格情報が含まれます。|
|/Namespaces/NotificationHubs/read|Notification Hub リソースの説明の一覧を取得します。|
|/Namespaces/NotificationHubs/write|Notification Hub を作成し、そのプロパティを更新します。 プロパティには、主に、PNS 資格情報、 承認規則、TTL が含まれます。|
|/Namespaces/read|名前空間リソースの説明の一覧を取得します。|
|/Namespaces/write|名前空間リソースを作成し、そのプロパティを更新します。 名前空間のタグとキャパシティは、更新できるプロパティです。|
|/register/action|NotifciationHubs リソース プロバイダーにサブスクリプションを登録し、名前空間と NotificationHubs を作成できるようにします。|

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

| 操作 | [説明] |
|---|---|
|/linkTargets/read|Azure サブスクリプションに関連付けられていない既存のアカウントを一覧表示します。 この Azure サブスクリプションをワークスペースにリンクするには、この操作で返される顧客 ID を、"ワークスペースの作成" 操作の顧客 ID プロパティで使用します。|
|/register/action|リソース プロバイダーにサブスクリプションを登録します。|
|/workspaces/analytics/query/action|新しいエンジンを使用して検索します。|
|/workspaces/analytics/query/schema/read|検索スキーマ V2 を取得します。|
|/workspaces/api/query/action|新しいエンジンを使用して検索します。|
|/workspaces/api/query/schema/read|検索スキーマ V2 を取得します。|
|/workspaces/configurationScopes/delete|構成スコープを削除します。|
|/workspaces/configurationScopes/read|構成スコープを取得します。|
|/workspaces/configurationScopes/write|構成スコープを設定します。|
|/workspaces/datasources/delete|ワークスペースのデータソースを削除します。|
|/workspaces/datasources/read|ワークスペースのデータソースを取得します。|
|/workspaces/datasources/write|ワークスペースのデータソースを作成または更新します。|
|/workspaces/delete|ワークスペースを削除します。 ワークスペースが、作成時に既存のワークスペースにリンクされていた場合、リンク先のワークスペースは削除されません。|
|/workspaces/generateregistrationcertificate/action|ワークスペースの登録証明書を生成します。 この証明書を使用して、Microsoft System Center Operation Manager をワークスペースに接続します。|
|/workspaces/intelligencepacks/disable/action|指定されたワークスペースのインテリジェンス パックを無効にします。|
|/workspaces/intelligencepacks/enable/action|指定されたワークスペースのインテリジェンス パックを有効にします。|
|/workspaces/intelligencepacks/read|指定されたワークスペースで表示されるすべてのインテリジェンス パックを一覧表示します。また、そのワークスペースでパックが有効になっているか無効になっているかも示します。|
|/workspaces/linkedServices/delete|指定されたワークスペースのリンクされたサービスを削除します。|
|/workspaces/linkedServices/read|指定されたワークスペースのリンクされたサービスを取得します。|
|/workspaces/linkedServices/write|指定されたワークスペースのリンクされたサービスを作成または更新します。|
|/workspaces/listKeys/action|ワークスペースの一覧キーを取得します。 これらのキーを使用して、Microsoft Operational Insights エージェントをワークスペースに接続します。|
|/workspaces/listKeys/read|ワークスペースの一覧キーを取得します。 これらのキーを使用して、Microsoft Operational Insights エージェントをワークスペースに接続します。|
|/workspaces/managementGroups/read|このワークスペースに接続されている System Center Operations Manager 管理グループの名前とメタデータを取得します。|
|/workspaces/metricDefinitions/read|ワークスペースのメトリック定義を取得します。|
|/workspaces/notificationSettings/delete|ワークスペースのユーザーの通知設定を削除します。|
|/workspaces/notificationSettings/read|ワークスペースのユーザーの通知設定を取得します。|
|/workspaces/notificationSettings/write|ワークスペースのユーザーの通知設定を設定します。|
|/workspaces/purge/action|ワークスペースから指定されたデータを削除します。|
|/workspaces/read|既存のワークスペースを取得します。|
|/workspaces/savedSearches/delete|保存された検索クエリを削除します。|
|/workspaces/savedSearches/read|保存された検索クエリを取得します。|
|/workspaces/savedSearches/write|保存された検索クエリを作成します。|
|/workspaces/schema/read|ワークスペースの検索スキーマを取得します。  検索スキーマには、公開されているフィールドとその種類が含まれます。|
|/workspaces/search/action|検索クエリを実行します。|
|/workspaces/sharedKeys/action|ワークスペースの共有キーを取得します。 これらのキーを使用して、Microsoft Operational Insights エージェントをワークスペースに接続します。|
|/workspaces/sharedKeys/read|ワークスペースの共有キーを取得します。 これらのキーを使用して、Microsoft Operational Insights エージェントをワークスペースに接続します。|
|/workspaces/storageinsightconfigs/delete|ストレージ構成を削除します。 これにより、Microsoft Operational Insights はストレージ アカウントからデータを読み取らなくなります。|
|/workspaces/storageinsightconfigs/read|ストレージ構成を取得します。|
|/workspaces/storageinsightconfigs/write|新しいストレージ構成を作成します。 これらの構成を使用して、既存のストレージ アカウント内の場所からデータを取得します。|
|/workspaces/usages/read|ワークスペースで読み取られたデータの量など、ワークスペースの使用状況データを取得します。|
|/workspaces/write|新しいワークスペースを作成するか、既存のワークスペースの顧客 ID を指定して既存のワークスペースにリンクします。|

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

| 操作 | [説明] |
|---|---|
|/managementAssociations/delete|既存の管理関連付けを削除します。|
|/managementAssociations/read|既存の管理関連付けを取得します。|
|/managementAssociations/write|新しい管理関連付けを作成します。|
|/managementConfigurations/delete|既存の管理構成を削除します。|
|/managementConfigurations/read|既存の管理構成を取得します。|
|/managementConfigurations/write|新しい管理構成を作成します。|
|/register/action|リソース プロバイダーにサブスクリプションを登録します。|
|/solutions/delete|既存の OMS ソリューションを削除します。|
|/solutions/read|既存の OMS ソリューションを取得します。|
|/solutions/write|新しい OMS ソリューションを作成します。|

## <a name="microsoftportal"></a>Microsoft.Portal

| 操作 | [説明] |
|---|---|
|/dashboards/delete|サブスクリプションからダッシュボードを削除します。|
|/dashboards/read|サブスクリプションのダッシュボードを読み取ります。|
|/dashboards/write|サブスクリプションのダッシュボードを追加または変更します。|

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

| 操作 | [説明] |
|---|---|
|/capacities/checkNameAvailability/action|指定された Power BI 専用容量の名前が有効であり使用されていないことを確認します。|
|/capacities/delete|Power BI 専用容量を削除します。|
|/capacities/providers/Microsoft.Insights/metricDefinitions/read|Power BI 専用容量で使用可能なメトリックを取得します。|
|/capacities/read|指定された Power BI 専用容量の情報を取得します。|
|/capacities/write|指定された Power BI 専用容量を作成または更新します。|

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

| 操作 | [説明] |
|---|---|
|/locations/allocatedStamp/read|GetAllocatedStamp は、サービスによって使用される内部操作です。|
|/locations/allocateStamp/action|AllocateStamp は、サービスによって使用される内部操作です。|
|/locations/backupPreValidateProtection/action||
|/locations/backupStatus/action|Recovery Services コンテナーのバックアップの状態を確認します。|
|/locations/backupValidateFeatures/action|機能を検証します。|
|/operations/read|リソース プロバイダーの操作の一覧を返します。|
|/register/action|指定されたリソース プロバイダーにサブスクリプションを登録します。|
|/Vaults/backupconfig/read|Recovery Services コンテナーの構成を返します。|
|/Vaults/backupconfig/write|Recovery Services コンテナーの構成を更新します。|
|/Vaults/backupEngines/read|コンテナーに登録されているすべてのバックアップ管理サーバーを返します。|
|/Vaults/backupFabrics/{fabricName}/protectionContainers/{containerName}/items/read|コンテナー内のすべての項目を取得します。|
|/Vaults/backupFabrics/backupProtectionIntent/write|バックアップの保護インテントを作成します|
|/Vaults/backupFabrics/operationResults/read|操作の状態を返します。|
|/Vaults/backupFabrics/protectableContainers/read|すべての保護可能なコンテナーを取得します。|
|/Vaults/backupFabrics/protectionContainers/inquire/action|コンテナー内のワークロードを照会します。|
|/Vaults/backupFabrics/protectionContainers/operationResults/read|保護コンテナーに対して実行された操作の結果を取得します。|
|/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action|保護された項目のバックアップを実行します。|
|/Vaults/backupFabrics/protectionContainers/protectedItems/delete|保護された項目を削除します。|
|/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read|保護された項目に対して実行された操作の結果を取得します。|
|/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read|保護された項目に対して実行された操作の状態を返します。|
|/Vaults/backupFabrics/protectionContainers/protectedItems/read|保護された項目のオブジェクトの詳細を返します。|
|/Vaults/backupFabrics/protectionContainers/protectedItems/ recoveryPoints/provisionInstantItemRecovery/action|保護された項目のインスタント項目回復をプロビジョニングします。|
|/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read|保護された項目の復旧ポイントを取得します。|
|/Vaults/backupFabrics/protectionContainers/protectedItems/ recoveryPoints/restore/action|保護された項目の復旧ポイントを復元します。|
|/Vaults/backupFabrics/protectionContainers/protectedItems/ recoveryPoints/revokeInstantItemRecovery/action|保護された項目のインスタント項目回復を取り消します。|
|/Vaults/backupFabrics/protectionContainers/protectedItems/write|バックアップ保護項目を作成します。|
|/Vaults/backupFabrics/protectionContainers/read|すべての登録済みコンテナーを返します。|
|/Vaults/backupFabrics/protectionContainers/write|登録済みコンテナーを作成します。|
|/Vaults/backupFabrics/refreshContainers/action|コンテナーの一覧を更新します。|
|/Vaults/backupJobs/cancel/action|ジョブを取り消します。|
|/Vaults/backupJobs/operationResults/read|ジョブ操作の結果を返します。|
|/Vaults/backupJobs/read|すべてのジョブ オブジェクトを返します。|
|/Vaults/backupJobsExport/action|ジョブをエクスポートします。|
|/Vaults/backupJobsExport/operationResults/read|"ジョブのエクスポート" 操作の結果を返します。|
|/Vaults/backupManagementMetaData/read|Recovery Services コンテナーのバックアップ管理メタデータを返します。|
|/Vaults/backupOperationResults/read|Recovery Services コンテナーに対するバックアップ操作の結果を返します。|
|/Vaults/backupOperations/read|Recovery Services コンテナーに対するバックアップ操作の状態を返します。|
|/Vaults/backupPolicies/delete|保護ポリシーを削除します。|
|/Vaults/backupPolicies/operationResults/read|ポリシー操作の結果を取得します。|
|/Vaults/backupPolicies/operations/read|ポリシー操作の状態を取得します。|
|/Vaults/backupPolicies/read|すべての保護ポリシーを返します。|
|/Vaults/backupPolicies/write|保護ポリシーを作成します。|
|/Vaults/backupProtectableItems/read|すべての保護可能な項目の一覧を返します。|
|/Vaults/backupProtectedItems/read|すべての保護された項目の一覧を返します。|
|/Vaults/backupProtectionContainers/read|サブスクリプションに属するすべてのコンテナーを返します。|
|/Vaults/backupSecurityPIN/action|Recovery Services コンテナーのセキュリティ PIN 情報を返します。|
|/Vaults/backupstorageconfig/read|Recovery Services コンテナーのストレージ構成を返します。|
|/Vaults/backupstorageconfig/write|Recovery Services コンテナーのストレージ構成を更新します。|
|/Vaults/backupUsageSummaries/read|Recovery Services の保護された項目と保護されたサーバーの概要を返します。|
|/Vaults/certificates/write|"リソース証明書を更新" 操作では、リソース/コンテナー資格情報証明書を更新します。|
|/Vaults/delete|"コンテナーの削除" 操作では、指定された "コンテナー" 型の Azure リソースを削除します。|
|/Vaults/extendedInformation/delete|"拡張情報の取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトの拡張情報を取得します。|
|/Vaults/extendedInformation/read|"拡張情報の取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトの拡張情報を取得します。|
|/Vaults/extendedInformation/write|"拡張情報の取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトの拡張情報を取得します。|
|/Vaults/monitoringAlerts/read|Recovery Services コンテナーのアラートを取得します。|
|/Vaults/monitoringAlerts/write|アラートを解決する。|
|/Vaults/monitoringConfigurations/read|Recovery Services コンテナーの通知構成を取得します。|
|/Vaults/monitoringConfigurations/write|Recovery Services コンテナーへのメール通知を構成します。|
|/Vaults/providers/Microsoft.Insights/diagnosticSettings/read|Azure Backup 診断です。|
|/Vaults/providers/Microsoft.Insights/diagnosticSettings/write|Azure Backup 診断です。|
|/Vaults/providers/Microsoft.Insights/logDefinitions/read|Azure Backup ログです。|
|/Vaults/providers/Microsoft.Insights/metricDefinitions/read|Azure Backup メトリックです。|
|/Vaults/read|"コンテナーの取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトを取得します。|
|/Vaults/registeredIdentities/delete|"コンテナーの登録を解除" 操作を使用すると、コンテナーの登録を解除できます。|
|/Vaults/registeredIdentities/operationResults/read|"操作結果を取得" 操作を使用すると、非同期で送信された操作の状態と結果を取得できます。|
|/Vaults/registeredIdentities/read|" コンテナーを取得" 操作を使用すると、リソースの登録済みコンテナーを取得できます。|
|/Vaults/registeredIdentities/write|"サービス コンテナーを登録" 操作を使用すると、コンテナーを Recovery Services に登録できます。|
|/vaults/replicationAlertSettings/read|アラート設定を読み取ります。|
|/vaults/replicationAlertSettings/write|アラート設定を作成または更新します。|
|/vaults/replicationEvents/read|イベントを読み取ります。|
|/vaults/replicationFabrics/checkConsistency/action|ファブリックの一貫性を確認します。|
|/vaults/replicationFabrics/delete|ファブリックをすべて削除します。|
|/vaults/replicationFabrics/deployProcessServerImage/action|プロセス サーバー イメージをデプロイします。|
|/vaults/replicationFabrics/read|ファブリックを読み取ります。|
|/vaults/replicationFabrics/reassociateGateway/action|ゲートウェイを再関連付けします。|
|/vaults/replicationFabrics/remove/action|ファブリックを削除します。|
|/vaults/replicationFabrics/renewcertificate/action|ファブリックの証明書を更新します。|
|/vaults/replicationFabrics/replicationNetworks/read|ネットワークを読み取ります。|
|/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/delete|ネットワーク マッピングをすべて削除します。|
|/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read|ネットワーク マッピングを読み取ります。|
|/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/write|ネットワーク マッピングを作成または更新します。|
|/vaults/replicationFabrics/replicationProtectionContainers/ discoverProtectableItem/action|保護可能な項目を検出します。|
|/vaults/replicationFabrics/replicationProtectionContainers/read|保護コンテナーを読み取ります。|
|/vaults/replicationFabrics/replicationProtectionContainers/remove/action|保護コンテナーを削除します。|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectableItems/read|保護可能な項目を読み取ります。|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/applyRecoveryPoint/action|復旧ポイントを適用します。|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/delete|保護された項目をすべて削除します。|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/failoverCommit/action|フェールオーバーのコミット。|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/plannedFailover/action|計画されたフェールオーバー。|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/read|保護された項目を読み取ります。|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/recoveryPoints/read|レプリケーションの復旧ポイントを読み取ります。|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/remove/action|保護された項目を削除します。|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/repairReplication/action|レプリケーションを修復します。|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/reProtect/action|保護された項目を再保護します。|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/testFailover/action|テスト フェールオーバー|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/testFailoverCleanup/action|テスト フェールオーバーのクリーンアップ。|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/unplannedFailover/action|フェールオーバー|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/updateMobilityService/action|モビリティ サービスを更新します。|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/write|保護された項目を作成または更新します。|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectionContainerMappings/delete|保護コンテナー マッピングをすべて削除します。|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectionContainerMappings/read|保護コンテナー マッピングを読み取ります。|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectionContainerMappings/remove/action|保護コンテナー マッピングを削除します。|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectionContainerMappings/write|保護コンテナー マッピングを作成または更新します。|
|/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action|保護コンテナーを切り替えます。|
|/vaults/replicationFabrics/replicationProtectionContainers/write|保護コンテナーを作成または更新します。|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/delete|Recovery Services プロバイダーをすべて削除します。|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/read|Recovery Services プロバイダーを読み取ります。|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/ refreshProvider/action|プロバイダーを更新します。|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/remove/action|Recovery Services プロバイダーを削除します。|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/write|Recovery Services プロバイダーを作成または更新します。|
|/vaults/replicationFabrics/replicationStorageClassifications/read|ストレージの分類を読み取ります。|
|/vaults/replicationFabrics/replicationStorageClassifications/ replicationStorageClassificationMappings/delete|ストレージ分類マッピングをすべて削除します。|
|/vaults/replicationFabrics/replicationStorageClassifications/ replicationStorageClassificationMappings/read|ストレージ分類マッピングを読み取ります。|
|/vaults/replicationFabrics/replicationStorageClassifications/ replicationStorageClassificationMappings/write|ストレージ分類マッピングを作成または更新します。|
|/vaults/replicationFabrics/replicationvCenters/delete|ジョブをすべて削除します。|
|/vaults/replicationFabrics/replicationvCenters/read|ジョブを読み取ります。|
|/vaults/replicationFabrics/replicationvCenters/write|ジョブを作成または更新します。|
|/vaults/replicationFabrics/write|ファブリックを作成または更新します。|
|/vaults/replicationJobs/cancel/action|ジョブを取り消します。|
|/vaults/replicationJobs/read|ジョブを読み取ります。|
|/vaults/replicationJobs/restart/action|ジョブを再実行します。|
|/vaults/replicationJobs/resume/action|ジョブを再開します。|
|/vaults/replicationPolicies/delete|ポリシーをすべて削除します。|
|/vaults/replicationPolicies/read|ポリシーを読み取ります。|
|/vaults/replicationPolicies/write|ポリシーを作成または更新します。|
|/vaults/replicationRecoveryPlans/delete|復旧計画をすべて削除します。|
|/vaults/replicationRecoveryPlans/failoverCommit/action|フェールオーバーのコミットの復旧計画。|
|/vaults/replicationRecoveryPlans/plannedFailover/action|計画されたフェールオーバーの復旧計画。|
|/vaults/replicationRecoveryPlans/read|復旧計画を読み取ります。|
|/vaults/replicationRecoveryPlans/reProtect/action|再保護の復旧計画。|
|/vaults/replicationRecoveryPlans/testFailover/action|テスト フェールオーバーの復旧計画。|
|/vaults/replicationRecoveryPlans/testFailoverCleanup/action|テスト フェールオーバーのクリーンアップの復旧計画。|
|/vaults/replicationRecoveryPlans/unplannedFailover/action|フェールオーバーの復旧計画。|
|/vaults/replicationRecoveryPlans/write|復旧計画を作成または更新します。|
|/Vaults/tokenInfo/read|Recovery Services コンテナーのトークン情報を返します。|
|/vaults/usages/read|コンテナーの使用状況を読み取ります。|
|/Vaults/usages/read|Recovery Services コンテナーの使用状況の詳細を返します。|
|/Vaults/vaultTokens/read|"コンテナー トークン" 操作を使用すると、コンテナー レベルのバックエンド操作のコンテナー トークンを取得できます。|
|/Vaults/write|"コンテナーの作成" 操作では、"コンテナー" 型の Azure リソースを作成します。|

## <a name="microsoftrelay"></a>Microsoft.Relay

| 操作 | [説明] |
|---|---|
|/checkNameAvailability/action|特定のサブスクリプションで名前空間を使用できるかどうかを確認します。|
|/checkNamespaceAvailability/action|特定のサブスクリプションで名前空間を使用できるかどうかを確認します。 この API は使用されていません。代わりに CheckNameAvailabiltiy を使用してください。|
|/namespaces/authorizationRules/action|名前空間の承認規則を更新します。 この API は使用されていません。 名前空間の承認規則を更新するには、代わりに PUT 呼び出しを使用してください。 この操作は、API バージョン 2017-04-01 ではサポートされていません。|
|/namespaces/authorizationRules/delete|名前空間の承認規則を削除します。 名前空間の既定の承認規則は削除できません。 |
|/namespaces/authorizationRules/listkeys/action|名前空間への接続文字列を取得します。|
|/namespaces/authorizationRules/read|名前空間の承認規則の説明の一覧を取得します。|
|/namespaces/authorizationRules/regenerateKeys/action|リソースのプライマリ キーまたはセカンダリ キーを再生成します。|
|/namespaces/authorizationRules/write|名前空間レベルの承認規則を作成し、そのプロパティを更新します。 承認規則のアクセス権、プライマリ キー、セカンダリ キーを更新できます。|
|/namespaces/Delete|名前空間リソースを削除します。|
|/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action|ディザスター リカバリーのプライマリ名前空間の承認規則キーを取得します。|
|/namespaces/disasterRecoveryConfigs/authorizationRules/read|ディザスター リカバリーのプライマリ名前空間の承認規則を取得します。|
|/namespaces/disasterRecoveryConfigs/breakPairing/action|ディザスター リカバリーを無効にし、プライマリ名前空間からセカンダリ名前空間への変更の複製を停止します。|
|/namespaces/disasterrecoveryconfigs/checkNameAvailability/action|指定されたサブスクリプションで名前空間別名を使用できるかどうかを確認します。|
|/namespaces/disasterRecoveryConfigs/delete|名前空間に関連付けられているディザスター リカバリー構成を削除します。 この操作は、プライマリ名前空間からのみ呼び出すことができます。|
|/namespaces/disasterRecoveryConfigs/failover/action|geo DR フェールオーバーを呼び出し、セカンダリ名前空間を指すように名前空間の別名を再構成します。|
|/namespaces/disasterRecoveryConfigs/read|名前空間に関連付けられているディザスター リカバリーの構成を取得します。|
|/namespaces/disasterRecoveryConfigs/write|名前空間に関連付けられているディザスター リカバリーの構成を作成または更新します。|
|/namespaces/HybridConnections/authorizationRules/action|HybridConnection を更新する操作です。 この操作は、API バージョン 2017-04-01 ではサポートされていません。 承認規則。 承認規則を更新するには、PUT 呼び出しを使用してください。|
|/namespaces/HybridConnections/authorizationRules/delete|HybridConnection の承認規則を削除する操作。|
|/namespaces/HybridConnections/authorizationRules/listkeys/action|HybridConnection への接続文字列を取得します。|
|/namespaces/HybridConnections/authorizationRules/read| HybridConnection 承認規則の一覧を取得します。|
|/namespaces/HybridConnections/authorizationRules/regeneratekeys/action|リソースのプライマリ キーまたはセカンダリ キーを再生成します。|
|/namespaces/HybridConnections/authorizationRules/write|HybridConnection の承認規則を作成し、そのプロパティを更新します。 承認規則のアクセス権を更新できます。|
|/namespaces/HybridConnections/Delete|HybridConnection リソースを削除する操作。|
|/namespaces/HybridConnections/read|HybridConnection リソースの説明の一覧を取得します。|
|/namespaces/HybridConnections/write|HybridConnection プロパティを作成または更新します。|
|/namespaces/messagingPlan/read|名前空間のメッセージング プランを取得します。 この API は使用されていません。 より新しい API バージョンでは、MessagingPlan リソースにより公開されるプロパティは (親) 名前空間リソースに移動されています。 この操作は、API バージョン 2017-04-01 ではサポートされていません。|
|/namespaces/messagingPlan/write|名前空間のメッセージング プランを更新します。 この API は使用されていません。 より新しい API バージョンでは、MessagingPlan リソースにより公開されるプロパティは (親) 名前空間リソースに移動されています。 この操作は、API バージョン 2017-04-01 ではサポートされていません。|
|/namespaces/operationresults/read|名前空間の操作の状態を取得します。|
|/namespaces/providers/Microsoft.Insights/metricDefinitions/read|名前空間のメトリック リソースの説明の一覧を取得します。|
|/namespaces/read|名前空間リソースの説明の一覧を取得します。|
|/namespaces/WcfRelays/authorizationRules/action|WcfRelay を更新する操作です。 この操作は、API バージョン 2017-04-01 ではサポートされていません。 承認規則。 承認規則を更新するには、PUT 呼び出しを使用してください。|
|/namespaces/WcfRelays/authorizationRules/delete|WcfRelay の承認規則を削除する操作。|
|/namespaces/WcfRelays/authorizationRules/listkeys/action|WcfRelay への接続文字列を取得します。|
|/namespaces/WcfRelays/authorizationRules/read| WcfRelay 承認規則の一覧を取得します。|
|/namespaces/WcfRelays/authorizationRules/regeneratekeys/action|リソースのプライマリ キーまたはセカンダリ キーを再生成します。|
|/namespaces/WcfRelays/authorizationRules/write|WcfRelay の承認規則を作成し、そのプロパティを更新します。 承認規則のアクセス権を更新できます。|
|/namespaces/WcfRelays/Delete|WcfRelay リソースを削除する操作。|
|/namespaces/WcfRelays/read|WcfRelay リソースの説明の一覧を取得します。|
|/namespaces/WcfRelays/write|WcfRelay のプロパティを作成または更新します。|
|/namespaces/write|名前空間リソースを作成し、そのプロパティを更新します。 名前空間のタグとキャパシティは、更新できるプロパティです。|
|/operations/read|操作を取得します。|
|/register/action|Relay リソース プロバイダーにサブスクリプションを登録し、リレー リソースを作成できるようにします。|
|/unregister/action|Relay リソース プロバイダーにサブスクリプションを登録し、リレー リソースを作成できるようにします。|

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

| 操作 | [説明] |
|---|---|
|/AvailabilityStatuses/current/read|指定されたリソースの利用状況を取得します。|
|/AvailabilityStatuses/read|指定されたスコープのすべてのリソースの利用状況を取得します。|
|/healthevent/action|指定されたリソースの正常性の状態の変更を表します。|
|/healthevent/Activated/action|指定されたリソースの正常性の状態の変更を表します。|
|/healthevent/InProgress/action|指定されたリソースの正常性の状態の変更を表します。|
|/healthevent/Pending/action|指定されたリソースの正常性の状態の変更を表します。|
|/healthevent/Resolved/action|指定されたリソースの正常性の状態の変更を表します。|
|/healthevent/Updated/action|指定されたリソースの正常性の状態の変更を表します。|
|/register/action|Microsoft ResourceHealth にサブスクリプションを登録します。|

## <a name="microsoftresources"></a>Microsoft.Resources

| 操作 | [説明] |
|---|---|
|/checkResourceName/action|リソース名の妥当性を確認します。|
|/deployments/cancel/action|デプロイを取り消します。|
|/deployments/delete|デプロイを削除します。|
|/deployments/operations/read|デプロイ操作を取得または一覧表示します。|
|/deployments/read|デプロイを取得または一覧表示します。|
|/deployments/validate/action|デプロイを検証します。|
|/deployments/write|デプロイを作成または更新します。|
|/links/delete|リソース リンクを削除します。|
|/links/read|リソース リンクを取得または一覧表示します。|
|/links/write|リソース リンクを作成または更新します。|
|/marketplace/purchase/action|Marketplace からリソースを購入します。|
|/providers/read|プロバイダーの一覧を取得します。|
|/resources/read|フィルターに基づいてリソースの一覧を取得します。|
|/subscriptions/locations/read|サポートされる場所の一覧を取得します。|
|/subscriptions/operationresults/read|サブスクリプション操作の結果を取得します。|
|/subscriptions/providers/read|リソース プロバイダーを取得または一覧表示します。|
|/subscriptions/read|サブスクリプションの一覧を取得します。|
|/subscriptions/resourceGroups/delete|リソース グループとそのすべてのリソースを削除します。|
|/subscriptions/resourcegroups/deployments/operations/read|デプロイ操作を取得または一覧表示します。|
|/subscriptions/resourcegroups/deployments/operationstatuses/read|デプロイ操作の状態を取得または一覧表示します。|
|/subscriptions/resourcegroups/deployments/read|デプロイを取得または一覧表示します。|
|/subscriptions/resourcegroups/deployments/write|デプロイを作成または更新します。|
|/subscriptions/resourceGroups/moveResources/action|リソース グループ間でリソースを移動します。|
|/subscriptions/resourceGroups/read|リソース グループを取得または一覧表示します。|
|/subscriptions/resourcegroups/resources/read|リソース グループのリソースを取得します。|
|/subscriptions/resourceGroups/validateMoveResources/action|リソース グループ間でのリソースの移動を検証します。|
|/subscriptions/resourceGroups/write|リソース グループを作成または更新します。|
|/subscriptions/resources/read|サブスクリプションのリソースを取得します。|
|/subscriptions/tagNames/delete|サブスクリプション タグを削除します。|
|/subscriptions/tagNames/read|サブスクリプション タグを取得または一覧表示します。|
|/subscriptions/tagNames/tagValues/delete|サブスクリプション タグの値を削除します。|
|/subscriptions/tagNames/tagValues/read|サブスクリプション タグの値を取得または一覧表示します。|
|/subscriptions/tagNames/tagValues/write|サブスクリプション タグの値を追加します。|
|/subscriptions/tagNames/write|サブスクリプション タグを追加します。|
|/tenants/read|テナントの一覧を取得します。|

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

| 操作 | [説明] |
|---|---|
|/jobcollections/delete|ジョブ コレクションを削除します。|
|/jobcollections/disable/action|ジョブ コレクションを無効にします。|
|/jobcollections/enable/action|ジョブ コレクションを有効にします。|
|/jobcollections/jobs/delete|ジョブを削除します。|
|/jobcollections/jobs/generateLogicAppDefinition/action|Scheduler ジョブに基づいてロジック アプリ定義を生成します。|
|/jobcollections/jobs/jobhistories/read|ジョブ履歴を取得します。|
|/jobcollections/jobs/read|ジョブを取得します。|
|/jobcollections/jobs/run/action|ジョブを実行します。|
|/jobcollections/jobs/write|ジョブを作成または更新します。|
|/jobcollections/read|ジョブ コレクションを取得します。|
|/jobcollections/write|ジョブ コレクションを作成または更新します。|

## <a name="microsoftsearch"></a>Microsoft.Search

| 操作 | [説明] |
|---|---|
|/checkNameAvailability/action|サービス名を使用できるかどうかを確認します。|
|/register/action|検索リソース プロバイダーにサブスクリプションを登録し、検索サービスを作成できるようにします。|
|/searchServices/createQueryKey/action|クエリ キーを作成します。|
|/searchServices/delete|検索サービスを削除します。|
|/searchServices/diagnosticSettings/read|リソースの診断設定の読み取りを取得します。|
|/searchServices/diagnosticSettings/write|リソースの診断設定を作成または更新します。|
|/searchServices/listAdminKeys/action|管理者キーを読み取ります。|
|/searchServices/logDefinitions/read|検索サービスで使用可能なログを取得します。|
|/searchServices/metricDefinitions/read|検索サービスで使用可能なメトリックを取得します。|
|/searchServices/queryKey/delete|クエリ キーを削除します。|
|/searchServices/queryKey/read|クエリ キーを読み取ります。|
|/searchServices/read|検索サービスを読み取ります。|
|/searchServices/regenerateAdminKey/action|管理者キーを再生成します。|
|/searchServices/start/action|検索サービスを開始します。|
|/searchServices/stop/action|検索サービスを停止します。|
|/searchServices/write|検索サービスを作成または更新します。|

## <a name="microsoftsecurity"></a>Microsoft.Security

| 操作 | [説明] |
|---|---|
|/alerts/read|すべての使用可能なセキュリティ アラートを取得します。|
|/applicationWhitelistings/read|アプリケーション ホワイトリストを取得します。|
|/applicationWhitelistings/write|新しいアプリケーション ホワイトリストを作成するか、既存のアプリケーション ホワイトリストを更新します。|
|/complianceResults/read|リソースのコンプライアンス結果を取得します。|
|/locations/alerts/activate/action|セキュリティ アラートをアクティブ化します。|
|/locations/alerts/dismiss/action|セキュリティ アラートを無視します。|
|/locations/alerts/read|すべての使用可能なセキュリティ アラートを取得します。|
|/locations/jitNetworkAccessPolicies/initiate/action|Just-In-Time ネットワーク アクセス ポリシーを開始します。|
|/locations/jitNetworkAccessPolicies/read|Just-In-Time ネットワーク アクセス ポリシーを取得します。|
|/locations/jitNetworkAccessPolicies/write|新しい Just-In-Time ネットワーク アクセス ポリシーを作成するか、既存のポリシーを更新します。|
|/locations/read|セキュリティ データの場所を取得します。|
|/locations/tasks/activate/action|セキュリティ推奨事項をアクティブにします。|
|/locations/tasks/dismiss/action|セキュリティ推奨事項を無視します。|
|/locations/tasks/read|利用可能なすべてのセキュリティ推奨事項を取得します。|
|/locations/tasks/resolve/action|セキュリティ推奨事項を解決します。|
|/locations/tasks/start/action|セキュリティ推奨事項を開始します。|
|/policies/read|セキュリティ ポリシーを取得します。|
|/policies/write|セキュリティ ポリシーを更新します。|
|/pricings/delete|スコープの価格設定を削除します。|
|/pricings/read|スコープの価格設定を取得します。|
|/pricings/write|スコープの価格設定を更新します。|
|/register/action|Azure Security Center にサブスクリプションを登録します。|
|/securityContacts/delete|セキュリティ連絡先を削除します。|
|/securityContacts/read|セキュリティ連絡先を取得します。|
|/securityContacts/write|セキュリティ連絡先を更新します。|
|/securitySolutions/delete|セキュリティ ソリューションを削除します。|
|/securitySolutions/read|セキュリティ ソリューションを取得します。|
|/securitySolutions/write|新しいセキュリティ ソリューションを作成するか、既存のセキュリティ ソリューションを更新します。|
|/securitySolutionsReferenceData/read|セキュリティ ソリューションの参照データを取得します。|
|/securityStatuses/read|Azure リソースのセキュリティ正常性状態を取得します。|
|/securityStatusesSummaries/read|スコープのセキュリティの状態の概要を取得します。|
|/tasks/read|利用可能なすべてのセキュリティ推奨事項を取得します。|
|/webApplicationFirewalls/delete|Web アプリケーション ファイアウォールを削除します。|
|/webApplicationFirewalls/read|Web アプリケーション ファイアウォールを取得します。|
|/webApplicationFirewalls/write|新しい Web アプリケーション ファイアウォールを作成するか、既存のWeb アプリケーション ファイアウォールを更新します。|
|/workspaceSettings/connect/action|ワークスペースの設定の再接続の設定を変更します。|
|/workspaceSettings/delete|ワークスペースの設定を削除します。|
|/workspaceSettings/read|ワークスペースの設定を取得します。|
|/workspaceSettings/write|ワークスペースの設定を更新します。|

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

| 操作 | [説明] |
|---|---|
|/checkNameAvailability/action|特定のサブスクリプションで名前空間を使用できるかどうかを確認します。|
|/checkNamespaceAvailability/action|特定のサブスクリプションで名前空間を使用できるかどうかを確認します。 この API は使用されていません。代わりに CheckNameAvailabiltiy を使用してください。|
|/namespaces/authorizationRules/action|名前空間の承認規則を更新します。 この API は使用されていません。 名前空間の承認規則を更新するには、代わりに PUT 呼び出しを使用してください。 この操作は、API バージョン 2017-04-01 ではサポートされていません。|
|/namespaces/authorizationRules/delete|名前空間の承認規則を削除します。 名前空間の既定の承認規則は削除できません。 |
|/namespaces/authorizationRules/listkeys/action|名前空間への接続文字列を取得します。|
|/namespaces/authorizationRules/read|名前空間の承認規則の説明の一覧を取得します。|
|/namespaces/authorizationRules/regenerateKeys/action|リソースのプライマリ キーまたはセカンダリ キーを再生成します。|
|/namespaces/authorizationRules/write|名前空間レベルの承認規則を作成し、そのプロパティを更新します。 承認規則のアクセス権、プライマリ キー、セカンダリ キーを更新できます。|
|/namespaces/Delete|名前空間リソースを削除します。|
|/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action|ディザスター リカバリーのプライマリ名前空間の承認規則キーを取得します。|
|/namespaces/disasterRecoveryConfigs/authorizationRules/read|ディザスター リカバリーのプライマリ名前空間の承認規則を取得します。|
|/namespaces/disasterRecoveryConfigs/breakPairing/action|ディザスター リカバリーを無効にし、プライマリ名前空間からセカンダリ名前空間への変更の複製を停止します。|
|/namespaces/disasterrecoveryconfigs/checkNameAvailability/action|指定されたサブスクリプションで名前空間別名を使用できるかどうかを確認します。|
|/namespaces/disasterRecoveryConfigs/delete|名前空間に関連付けられているディザスター リカバリー構成を削除します。 この操作は、プライマリ名前空間からのみ呼び出すことができます。|
|/namespaces/disasterRecoveryConfigs/failover/action|geo DR フェールオーバーを呼び出し、セカンダリ名前空間を指すように名前空間の別名を再構成します。|
|/namespaces/disasterRecoveryConfigs/read|名前空間に関連付けられているディザスター リカバリーの構成を取得します。|
|/namespaces/disasterRecoveryConfigs/write|名前空間に関連付けられているディザスター リカバリーの構成を作成または更新します。|
|/namespaces/eventGridFilters/delete|名前空間に関連付けられている Event Grid フィルターを削除します。|
|/namespaces/eventGridFilters/read|名前空間に関連付けられている Event Grid フィルターを取得します。|
|/namespaces/eventGridFilters/write|名前空間に関連付けられている Event Grid フィルターを作成または更新します。|
|/namespaces/eventhubs/read|EventHub リソースの説明の一覧を取得します。|
|/namespaces/messagingPlan/read|名前空間のメッセージング プランを取得します。 この API は使用されていません。 より新しい API バージョンでは、MessagingPlan リソースにより公開されるプロパティは (親) 名前空間リソースに移動されています。 この操作は、API バージョン 2017-04-01 ではサポートされていません。|
|/namespaces/messagingPlan/write|名前空間のメッセージング プランを更新します。 この API は使用されていません。 より新しい API バージョンでは、MessagingPlan リソースにより公開されるプロパティは (親) 名前空間リソースに移動されています。 この操作は、API バージョン 2017-04-01 ではサポートされていません。|
|/namespaces/migrate/action|名前空間の操作を移行します。|
|/namespaces/operationresults/read|名前空間の操作の状態を取得します。|
|/namespaces/providers/Microsoft.Insights/diagnosticSettings/read|名前空間の診断設定リソースの説明の一覧を取得します。|
|/namespaces/providers/Microsoft.Insights/diagnosticSettings/write|名前空間の診断設定リソースの説明の一覧を取得します。|
|/namespaces/providers/Microsoft.Insights/logDefinitions/read|名前空間のログ リソースの説明の一覧を取得します。|
|/namespaces/providers/Microsoft.Insights/metricDefinitions/read|名前空間のメトリック リソースの説明の一覧を取得します。|
|/namespaces/queues/authorizationRules/action|キューを更新する操作です。 この操作は、API バージョン 2017-04-01 ではサポートされていません。 承認規則。 承認規則を更新するには、PUT 呼び出しを使用してください。|
|/namespaces/queues/authorizationRules/delete|キューの承認規則を削除する操作。|
|/namespaces/queues/authorizationRules/listkeys/action|キューへの接続文字列を取得します。|
|/namespaces/queues/authorizationRules/read| キューの承認規則の一覧を取得します。|
|/namespaces/queues/authorizationRules/regenerateKeys/action|リソースのプライマリ キーまたはセカンダリ キーを再生成します。|
|/namespaces/queues/authorizationRules/write|キューの承認規則を作成し、そのプロパティを更新します。 承認規則のアクセス権を更新できます。|
|/namespaces/queues/Delete|キュー リソースを削除する操作。|
|/namespaces/queues/read|キュー リソースの説明の一覧を取得します。|
|/namespaces/queues/write|キューのプロパティを作成または更新します。|
|/namespaces/read|名前空間リソースの説明の一覧を取得します。|
|/namespaces/topics/authorizationRules/action|トピックを更新する操作です。 この操作は、API バージョン 2017-04-01 ではサポートされていません。 承認規則。 承認規則を更新するには、PUT 呼び出しを使用してください。|
|/namespaces/topics/authorizationRules/delete|トピックの承認規則を削除する操作。|
|/namespaces/topics/authorizationRules/listkeys/action|トピックへの接続文字列を取得します。|
|/namespaces/topics/authorizationRules/read| トピックの承認規則の一覧を取得します。|
|/namespaces/topics/authorizationRules/regenerateKeys/action|リソースのプライマリ キーまたはセカンダリ キーを再生成します。|
|/namespaces/topics/authorizationRules/write|トピックの承認規則を作成し、そのプロパティを更新します。 承認規則のアクセス権を更新できます。|
|/namespaces/topics/Delete|トピック リソースを削除する操作。|
|/namespaces/topics/read|トピック リソースの説明の一覧を取得します。|
|/namespaces/topics/subscriptions/Delete|TopicSubscription リソースを削除する操作。|
|/namespaces/topics/subscriptions/read|TopicSubscription リソースの説明の一覧を取得します。|
|/namespaces/topics/subscriptions/rules/Delete|規則リソースを削除する操作。|
|/namespaces/topics/subscriptions/rules/read|規則リソースの説明の一覧を取得します。|
|/namespaces/topics/subscriptions/rules/write|規則のプロパティを作成または更新します。|
|/namespaces/topics/subscriptions/write|TopicSubscription のプロパティを作成または更新します。|
|/namespaces/topics/write|トピックのプロパティを作成または更新します。|
|/namespaces/write|名前空間リソースを作成し、そのプロパティを更新します。 名前空間のタグとキャパシティは、更新できるプロパティです。|
|/operations/read|操作を取得します。|
|/register/action|ServiceBus リソース プロバイダーにサブスクリプションを登録し、ServiceBus リソースを作成できるようにします。|
|/sku/read|SKU リソースの説明の一覧を取得します。|
|/sku/regions/read|SkuRegions リソースの説明の一覧を取得します。|
|/unregister/action|ServiceBus リソース プロバイダーにサブスクリプションを登録し、ServiceBus リソースを作成できるようにします。|

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

| 操作 | [説明] |
|---|---|
|/clusters/applications/delete|アプリケーションを削除します。|
|/clusters/applications/read|アプリケーションを読み取ります。|
|/clusters/applications/services/delete|サービスを削除します。|
|/clusters/applications/services/partitions/read|パーティションを読み取ります。|
|/clusters/applications/services/partitions/replicas/read|レプリカを読み取ります。|
|/clusters/applications/services/read|サービスを読み取ります。|
|/clusters/applications/services/statuses/read|サービスの状態を読み取ります。|
|/clusters/applications/services/write|サービスを作成または更新します。|
|/clusters/applications/write|アプリケーションを作成または更新します。|
|/clusters/applicationTypes/delete|アプリケーションの種類を削除します。|
|/clusters/applicationTypes/read|アプリケーションの種類を読み取ります。|
|/clusters/applicationTypes/versions/delete|アプリケーションの種類のバージョンを削除します。|
|/clusters/applicationTypes/versions/read|アプリケーションの種類のバージョンを読み取ります。|
|/clusters/applicationTypes/versions/write|アプリケーションの種類のバージョンを作成または更新します。|
|/clusters/applicationTypes/write|アプリケーションの種類を作成または更新します。|
|/clusters/delete|クラスターを削除します。|
|/clusters/nodes/read|ノードを読み取ります。|
|/clusters/read|クラスターを読み取ります。|
|/clusters/statuses/read|クラスターの状態を読み取ります。|
|/clusters/write|クラスターを作成または更新します。|
|/locations/clusterVersions/read|クラスターのバージョンを読み取ります。|
|/locations/environments/clusterVersions/read|特定の環境のクラスターのバージョンを読み取ります。|
|/locations/operationresults/read|操作結果を読み取ります。|
|/locations/operations/read|場所別に操作を読み取ります。|
|/operations/read|使用可能な操作を読み取ります。|
|/register/action|アクションを登録します。|

## <a name="microsoftsolutions"></a>Microsoft.Solutions

| 操作 | [説明] |
|---|---|
|/applicationDefinitions/delete|アプリケーション定義を削除します。|
|/applicationDefinitions/read|アプリケーション定義の一覧を取得します。|
|/applicationDefinitions/write|アプリケーション定義を追加または変更します。|
|/applications/delete|アプリケーションを削除します。|
|/applications/read|アプリケーションの一覧を取得します。|
|/applications/write|アプリケーションを作成します。|
|/locations/operationStatuses/read|リソースの操作の状態を読み取ります。|
|/register/action|ソリューションに登録します。|

## <a name="microsoftsql"></a>Microsoft.Sql

| 操作 | [説明] |
|---|---|
|/checkNameAvailability/action|指定されたサブスクリプションの世界的なプロビジョニングに対して特定のサーバー名が使用可能かどうかを確認します。|
|/locations/auditingSettingsAzureAsyncOperation/read|拡張サーバー BLOB 監査ポリシー設定操作の結果を取得します。|
|/locations/auditingSettingsOperationResults/read|サーバー BLOB 監査ポリシーの設定操作の結果を取得します。|
|/locations/capabilities/read|指定された場所でのこのサブスクリプションの機能を取得します。|
|/locations/databaseAzureAsyncOperation/read|データベース操作の状態を取得します。|
|/locations/databaseOperationResults/read|データベース操作の状態を取得します。|
|/locations/deletedServerAsyncOperation/read|削除されるサーバーでの進行中の操作を取得します。|
|/locations/deletedServerOperationResults/read|削除されるサーバーでの進行中の操作を取得します。|
|/locations/deletedServers/read|削除されるサーバーの一覧を返すか、指定された削除されるサーバーのプロパティを取得します。|
|/locations/deletedServers/recover/action|削除されたサーバーを復旧します。|
|/locations/deleteVirtualNetworkOrSubnets/action|仮想ネットワークまたはサブネットに関連付けられた仮想ネットワーク規則を削除します。|
|/locations/elasticPoolAzureAsyncOperation/read|エラスティック プールの非同期操作に対する Azure 非同期操作を取得します。|
|/locations/elasticPoolOperationResults/read|エラスティック プールの操作の結果を取得します。|
|/locations/extendedAuditingSettingsAzureAsyncOperation/read|拡張サーバー BLOB 監査ポリシー設定操作の結果を取得します。|
|/locations/extendedAuditingSettingsOperationResults/read|拡張サーバー BLOB 監査ポリシー設定操作の結果を取得します。|
|/locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/action|管理されたデータベースの復元操作を完了します。|
|/locations/managedTransparentDataEncryptionAzureAsyncOperation/read|管理されたデータベースの Transparent Data Encryption での進行中の操作を取得します。|
|/locations/managedTransparentDataEncryptionOperationResults/read|管理されたデータベースの Transparent Data Encryption での進行中の操作を取得します。|
|/locations/read|指定されたサブスクリプションで使用可能な場所を取得します。|
|/locations/syncAgentOperationResults/read|同期エージェント リソース操作の結果を取得します。|
|/locations/syncDatabaseIds/read|特定のリージョンおよびサブスクリプションの同期データベース ID を取得します。|
|/locations/syncGroupOperationResults/read|同期グループ リソース操作の結果を取得します。|
|/locations/syncMemberOperationResults/read|同期メンバー リソース操作の結果を取得します。|
|/locations/usages/read|ある場所でのこのサブスクリプションの使用状況メトリックのコレクションを取得します。|
|/locations/virtualNetworkRulesAzureAsyncOperation/read|指定された仮想ネットワーク規則の Azure 非同期操作の詳細を返します。 |
|/locations/virtualNetworkRulesOperationResults/read|指定された仮想ネットワーク規則の操作の詳細を返します。 |
|/managedInstances/administrators/delete|マネージ インスタンスの既存の管理者を削除します。|
|/managedInstances/administrators/read|マネージ インスタンスの管理者の一覧を取得します。|
|/managedInstances/administrators/write|指定されたパラメーターでマネージ インスタンスの管理者を作成または更新します。|
|/managedInstances/databases/delete|既存の管理されたデータベースを削除します。|
|/managedInstances/databases/read|既存の管理されたデータベースを取得します。|
|/managedInstances/databases/securityAlertPolicies/read|指定された管理されたデータベースで構成されているデータベース脅威検出ポリシーの詳細を取得します。|
|/managedInstances/databases/securityAlertPolicies/write|指定された管理されたデータベースのデータベース脅威検出ポリシーを変更します。|
|/managedInstances/databases/securityEvents/read|管理されたデータベースのセキュリティ イベントを取得します。|
|/managedInstances/databases/transparentDataEncryption/read|指定された管理されたデータベースでのデータベース Transparent Data Encryption の詳細を取得します。|
|/managedInstances/databases/transparentDataEncryption/write|指定された管理されたデータベースのデータベース Transparent Data Encryption を変更します。|
|/managedInstances/databases/write|新しいデータベースを作成するか、既存のデータベースを更新します。|
|/managedInstances/delete|既存のマネージ インスタンスを削除します。|
|/managedInstances/metricDefinitions/read|マネージ インスタンスのメトリック定義を取得します。|
|/managedInstances/metrics/read|マネージ インスタンスのメトリックを取得します。|
|/managedInstances/read|マネージ インスタンスの一覧を返すか、指定されたマネージ インスタンスのプロパティを取得します。|
|/managedInstances/securityAlertPolicies/read|指定された管理サーバーで構成されている管理サーバー脅威検出ポリシーの詳細を取得します。|
|/managedInstances/securityAlertPolicies/write|指定された管理サーバーの管理サーバー脅威検出ポリシーを変更します。|
|/managedInstances/write|指定されたパラメーターでマネージ インスタンスを作成するか、指定されたマネージ インスタンスのプロパティまたはタグを更新します。|
|/operations/read|使用可能な REST 操作を取得します。|
|/register/action|Microsoft SQL Database リソース プロバイダーにサブスクリプションを登録し、Microsoft SQL Database を作成できるようにします。|
|/servers/administratorOperationResults/read|サーバー管理者で進行中の操作を取得します。|
|/servers/administrators/delete|サーバー管理者を削除します。|
|/servers/administrators/read|サーバー管理者の詳細を取得します。|
|/servers/administrators/write|サーバー管理者を作成または更新します。|
|/servers/advisors/read|サーバーで使用できる Advisor の一覧を返します。|
|/servers/advisors/recommendedActions/read|サーバーの指定された Advisor の推奨アクションの一覧を返します。|
|/servers/advisors/recommendedActions/write|サーバーに推奨アクションを適用します。|
|/servers/advisors/write|サーバー レベルでの Advisor の自動実行の状態を更新します。|
|/servers/auditingPolicies/read|指定されたサーバーで構成されている既定のサーバー テーブル監査ポリシーの詳細を取得します。|
|/servers/auditingPolicies/write|指定されたサーバーの既定のサーバー テーブル監査を変更します。|
|/servers/auditingSettings/operationResults/read|サーバー BLOB 監査ポリシーの設定操作の結果を取得します。|
|/servers/auditingSettings/read|指定されたサーバーで構成されているサーバー BLOB 監査ポリシーの詳細を取得します。|
|/servers/auditingSettings/write|指定されたサーバーのサーバー BLOB 監査を変更します。|
|/servers/automaticTuning/read|サーバーの自動調整の設定を返します。|
|/servers/automaticTuning/write|サーバーの自動調整の設定を更新し、更新された設定を返します。|
|/servers/backupLongTermRetentionVaults/delete|既存のバックアップ アーカイブ コンテナーを削除します。|
|/servers/backupLongTermRetentionVaults/read|この操作を使用して、バックアップ長期リテンション期間コンテナーを取得します。 このサーバーに登録されているコンテナーに関する情報を返します。|
|/servers/backupLongTermRetentionVaults/write|バックアップ長期リテンション期間コンテナーをサーバーに登録します。|
|/servers/communicationLinks/delete|既存のサーバー通信リンクを削除します。|
|/servers/communicationLinks/read|指定されたサーバーの通信リンクの一覧を返します。|
|/servers/communicationLinks/write|サーバー通信リンクを作成または更新します。|
|/servers/connectionPolicies/read|指定されたサーバーのサーバー接続ポリシーの一覧を返します。|
|/servers/connectionPolicies/write|サーバー接続ポリシーを作成または更新します。|
|/servers/databases/advisors/read|データベースで使用できる Advisor の一覧を返します。|
|/servers/databases/advisors/recommendedActions/read|データベースの指定された Advisor の推奨アクションの一覧を返します。|
|/servers/databases/advisors/recommendedActions/write|データベースに推奨アクションを適用します。|
|/servers/databases/advisors/write|データベース レベルでの Advisor の自動実行の状態を更新します。|
|/servers/databases/auditingPolicies/read|指定されたデータベースで構成されているテーブル監査ポリシーの詳細を取得します。|
|/servers/databases/auditingPolicies/write|指定されたデータベースのテーブル監査ポリシーを変更します。|
|/servers/databases/auditingSettings/read|指定されたデータベースで構成されている BLOB 監査ポリシーの詳細を取得します。|
|/servers/databases/auditingSettings/write|指定されたデータベースの BLOB 監査ポリシーを変更します。|
|/servers/databases/auditRecords/read|データベースの BLOB 監査レコードを取得します。|
|/servers/databases/automaticTuning/read|データベースの自動調整の設定を返します。|
|/servers/databases/automaticTuning/write|データベースの自動調整の設定を更新し、更新された設定を返します。|
|/servers/databases/azureAsyncOperation/read|データベース操作の状態を取得します。|
|/servers/databases/backupLongTermRetentionPolicies/read|指定されたデータベースのバックアップ アーカイブ ポリシーの一覧を返します。|
|/servers/databases/backupLongTermRetentionPolicies/write|データベース バックアップ アーカイブ ポリシーを作成または更新します。|
|/servers/databases/connectionPolicies/read|指定されたデータベースで構成されている接続ポリシーの詳細を取得します。|
|/servers/databases/connectionPolicies/write|指定されたデータベースの接続ポリシーを変更します。|
|/servers/databases/dataMaskingPolicies/read|データベースのデータ マスク ポリシーの一覧を返します。|
|/servers/databases/dataMaskingPolicies/rules/delete|指定されたデータベースのデータ マスク ポリシー ルールを削除します。|
|/servers/databases/dataMaskingPolicies/rules/read|指定されたデータベースで構成されているデータ マスク ポリシー ルールの詳細を取得します。|
|/servers/databases/dataMaskingPolicies/rules/write|指定されたデータベースのデータ マスク ポリシー ルールを変更します。|
|/servers/databases/dataMaskingPolicies/write|指定されたデータベースのデータ マスク ポリシーを変更します。|
|/servers/databases/dataWarehouseQueries/dataWarehouseQuerySteps/read|選択したステップ ID のデータ ウェアハウス クエリの分散クエリ ステップ情報を返します。|
|/servers/databases/dataWarehouseQueries/read|選択したクエリ ID のデータ ウェアハウス分散クエリ情報を返します。|
|/servers/databases/dataWarehouseUserActivities/read|実行中のクエリと中断されたクエリを含む SQL Data Warehouse インスタンスのユーザー アクティビティを取得します|
|/servers/databases/delete|既存のデータベースを削除します。|
|/servers/databases/export/action|Azure SQL Database をエクスポートします。|
|/servers/databases/extendedAuditingSettings/read|指定されたデータベースで構成されている拡張 BLOB 監査ポリシーの詳細を取得します。|
|/servers/databases/extendedAuditingSettings/write|指定されたデータベースの拡張 BLOB 監査ポリシーを変更します。|
|/servers/databases/extensions/read|データベースの拡張機能のコレクションを取得します。|
|/servers/databases/extensions/write|指定されたデータベースの拡張機能を変更します。|
|/servers/databases/geoBackupPolicies/read|指定されたデータベースの geo バックアップ ポリシーを取得します。|
|/servers/databases/geoBackupPolicies/write|データベースの geo バックアップ ポリシーを作成または更新します。|
|/servers/databases/importExportOperationResults/read|進行中のインポート/エクスポート操作を取得します。|
|/servers/databases/metricDefinitions/read|データベースの利用可能なメトリックの種類を返します。|
|/servers/databases/metrics/read|データベースのメトリックを返します。|
|/servers/databases/move/action|Azure SQL Database の名前を変更します。|
|/servers/databases/operationResults/read|データベース操作の状態を取得します。|
|/servers/databases/operations/cancel/action|Azure SQL Database のまだ終了していない保留中非同期操作を取り消します。|
|/servers/databases/operations/read|データベースで実行された操作の一覧を返します。|
|/servers/databases/pause/action|Azure SQL Datawarehouse データベースを一時停止します。|
|/servers/databases/providers/Microsoft.Insights/diagnosticSettings/read|リソースの診断設定を取得します。|
|/servers/databases/providers/Microsoft.Insights/diagnosticSettings/write|リソースの診断設定を作成または更新します。|
|/servers/databases/providers/Microsoft.Insights/logDefinitions/read|データベースの利用可能なログを取得します。|
|/servers/databases/providers/Microsoft.Insights/metricDefinitions/read|データベースの利用可能なメトリックの種類を返します。|
|/servers/databases/queryStore/queryTexts/read|指定されたパラメーターに対応するクエリ テキストのコレクションを返します。|
|/servers/databases/queryStore/read|データベースのクエリ ストアの設定の現在値を返します。|
|/servers/databases/queryStore/write|データベースのクエリ ストア設定を更新します。|
|/servers/databases/read|データベースの一覧を返すか、指定されたデータベースのプロパティを取得します。|
|/servers/databases/replicationLinks/delete|レプリケーション関係を強制的に終了します。データが失われる可能性があります。|
|/servers/databases/replicationLinks/failover/action|プライマリからすべての変更を同期した後にフェールオーバーします。このデータベースがレプリケーション関係のプライマリになり、リモート プライマリがセカンダリになります。|
|/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action|即座にフェールオーバーします。このデータベースがレプリケーション関係のプライマリになり、リモート プライマリがセカンダリになります。データが失われる可能性があります。|
|/servers/databases/replicationLinks/read|特定のデータベース用に確立されたレプリケーション リンクの詳細を返します。|
|/servers/databases/replicationLinks/unlink/action|レプリケーション関係を強制的に終了するか、パートナーとの同期後に終了します。|
|/servers/databases/replicationLinks/updateReplicationMode/action|リンクのレプリケーション モードを同期モードまたは非同期モードに更新します。|
|/servers/databases/restorePoints/action|新しい復元ポイントを作成します。|
|/servers/databases/restorePoints/read|データベースの復元ポイントを返します。|
|/servers/databases/resume/action|Azure SQL Datawarehouse データベースを再開します。|
|/servers/databases/schemas/read|データベースのスキーマの一覧を取得します。|
|/servers/databases/schemas/tables/columns/read|テーブルの列の一覧を取得します。|
|/servers/databases/schemas/tables/columns/sensitivityLabels/delete|指定された列の機密ラベルを削除します。|
|/servers/databases/schemas/tables/columns/sensitivityLabels/read|指定された列の機密ラベルを取得します。|
|/servers/databases/schemas/tables/columns/sensitivityLabels/write|指定された列の機密ラベルを作成または更新します。|
|/servers/databases/schemas/tables/read|データベースのテーブルの一覧を取得します。|
|/servers/databases/schemas/tables/recommendedIndexes/read|データベースのインデックス推奨事項の一覧を取得します。|
|/servers/databases/schemas/tables/recommendedIndexes/write|インデックス推奨事項を適用します。|
|/servers/databases/securityAlertPolicies/read|指定されたデータベースで構成されている脅威検出ポリシーの詳細を取得します。|
|/servers/databases/securityAlertPolicies/write|指定されたデータベースの脅威検出ポリシーを変更します。|
|/servers/databases/securityMetrics/read|データベースのセキュリティ メトリックのコレクションを取得します。|
|/servers/databases/sensitivityLabels/read|指定されたデータベースの機密ラベルを一覧表示します。|
|/servers/databases/serviceTierAdvisors/read|パフォーマンスを向上させたり、コストを削減したりするために、クエリ実行の統計に基づいて、データベースのスケールアップまたはスケールダウンに関する提案を返します。|
|/servers/databases/syncGroups/cancelSync/action|同期グループの同期を取り消します。|
|/servers/databases/syncGroups/delete|既存の同期グループを削除します。|
|/servers/databases/syncGroups/hubSchemas/read|同期ハブ データベース スキーマの一覧を返します。|
|/servers/databases/syncGroups/logs/read|同期グループのログの一覧を返します。|
|/servers/databases/syncGroups/read|同期グループの一覧を返すか、指定された同期グループのプロパティを取得します。|
|/servers/databases/syncGroups/refreshHubSchema/action|同期ハブのデータベース スキーマを更新します。|
|/servers/databases/syncGroups/refreshHubSchemaOperationResults/read|同期ハブのスキーマ更新操作の結果を取得します。|
|/servers/databases/syncGroups/syncMembers/delete|既存の同期メンバーを削除します。|
|/servers/databases/syncGroups/syncMembers/read|同期メンバーの一覧を返すか、指定された同期メンバーのプロパティを取得します。|
|/servers/databases/syncGroups/syncMembers/refreshSchema/action|同期メンバーのスキーマを更新します。|
|/servers/databases/syncGroups/syncMembers/refreshSchemaOperationResults/read|同期メンバーのスキーマ更新操作の結果を取得します。|
|/servers/databases/syncGroups/syncMembers/schemas/read|同期メンバー データベース スキーマの一覧を返します。|
|/servers/databases/syncGroups/syncMembers/write|指定されたパラメーターで同期メンバーを作成するか、指定され同期メンバーのプロパティを更新します。|
|/servers/databases/syncGroups/triggerSync/action|同期グループの同期をトリガーします。|
|/servers/databases/syncGroups/write|指定されたパラメーターで同期グループを作成するか、指定され同期グループのプロパティを更新します。|
|/servers/databases/topQueries/queryText/action|選択したクエリ ID の Transact-SQL テキストを返します。|
|/servers/databases/topQueries/read|選択した期間の選択したクエリの集計されたランタイム統計を返します。|
|/servers/databases/topQueries/statistics/read|選択した期間の選択したクエリの集計されたランタイム統計を返します。|
|/servers/databases/transparentDataEncryption/operationResults/read|Transparent Data Encryption での進行中の操作を取得します。|
|/servers/databases/transparentDataEncryption/read|指定されたデータベースの Transparent Data Encryption セキュリティ機能の状態と詳細を取得します。|
|/servers/databases/transparentDataEncryption/write|Transparent Data Encryption の状態を変更します。|
|/servers/databases/upgradeDataWarehouse/action|Azure SQL Datawarehouse データベースをアップグレードします。|
|/servers/databases/usages/read|Azure SQL Database の使用状況の情報を取得します。|
|/servers/databases/vulnerabilityAssessments/delete|指定されたデータベースの脆弱性評価を削除します。|
|/servers/databases/vulnerabilityAssessments/read|指定されたデータベースで構成されている脆弱性評価の詳細を取得します。|
|/servers/databases/vulnerabilityAssessments/rules/baselines/delete|指定されたデータベースの脆弱性評価規則ベースラインを削除します。|
|/servers/databases/vulnerabilityAssessments/rules/baselines/read|指定されたデータベースの脆弱性評価規則ベースラインを取得します。|
|/servers/databases/vulnerabilityAssessments/rules/baselines/write|指定されたデータベースの脆弱性評価規則ベースラインを変更します。|
|/servers/databases/vulnerabilityAssessments/scans/action|データベースの脆弱性評価スキャンを実行します。|
|/servers/databases/vulnerabilityAssessments/scans/export/action|既存のスキャン結果を人間が読める形式に変換します。 既に存在する場合は何も行われません。|
|/servers/databases/vulnerabilityAssessments/scans/read|データベース脆弱性評価スキャン レコードの一覧を返すか、指定されたスキャン ID のスキャン レコードを取得します。|
|/servers/databases/vulnerabilityAssessments/write|指定されたデータベースの脆弱性評価を変更します。|
|/servers/databases/vulnerabilityAssessmentScans/action|データベースの脆弱性評価スキャンを実行します。|
|/servers/databases/vulnerabilityAssessmentScans/operationResults/read|データベース脆弱性評価スキャンの実行操作の結果を取得します。|
|/servers/databases/vulnerabilityAssessmentSettings/read|指定されたデータベースで構成されている脆弱性評価の詳細を取得します。|
|/servers/databases/vulnerabilityAssessmentSettings/write|指定されたデータベースの脆弱性評価を変更します。|
|/servers/databases/write|指定されたパラメーターでデータベースを作成するか、指定されたデータベースのプロパティまたはタグを更新します。|
|/servers/delete|既存のサーバーを削除します。|
|/servers/disasterRecoveryConfiguration/delete|指定されたサーバーの既存のディザスター リカバリー構成を削除します。|
|/servers/disasterRecoveryConfiguration/failover/action|DisasterRecoveryConfiguration の構成をフェールオーバーします。|
|/servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/action|DisasterRecoveryConfiguration のフェールオーバーを強制します。|
|/servers/disasterRecoveryConfiguration/read|このサーバーを含むディザスター リカバリー構成のコレクションを取得します。|
|/servers/disasterRecoveryConfiguration/write|サーバーのディザスター リカバリー構成を変更します。|
|/servers/elasticPoolEstimates/read|このサーバー用に既に作成されているエラスティック プール推定値の一覧を返します。|
|/servers/elasticPoolEstimates/write|提供されているデータベースの一覧用に、新しいエラスティック プール推定値を作成します。|
|/servers/elasticPools/advisors/read|エラスティック プールで使用できる Advisor の一覧を返します。|
|/servers/elasticPools/advisors/recommendedActions/read|エラスティック プールの指定された Advisor の推奨アクションの一覧を返します。|
|/servers/elasticPools/advisors/recommendedActions/write|エラスティック プールに推奨アクションを適用します。|
|/servers/elasticPools/advisors/write|エラスティック プール レベルでの Advisor の自動実行の状態を更新します。|
|/servers/elasticPools/databases/read|エラスティック プールのデータベースの一覧を取得します。|
|/servers/elasticPools/delete|既存のエラスティック プールを削除します。|
|/servers/elasticPools/elasticPoolActivity/read|指定されたエラスティック データベース プールのアクティビティと詳細を取得します。|
|/servers/elasticPools/elasticPoolDatabaseActivity/read|エラスティック データベース プールに含まれる指定されたデータベースのアクティビティと詳細を取得します。|
|/servers/elasticPools/metricDefinitions/read|エラスティック データベース プールの利用可能なメトリックの種類を返します。|
|/servers/elasticPools/metrics/read|エラスティック データベース プールのメトリックを返します。|
|/servers/elasticPools/operations/cancel/action|Azure SQL エラスティック プールのまだ終了していない保留中非同期操作を取り消します。|
|/servers/elasticPools/operations/read|エラスティック プールで実行された操作の一覧を返します。|
|/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/read|リソースの診断設定を取得します。|
|/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/write|リソースの診断設定を作成または更新します。|
|/servers/elasticPools/providers/Microsoft.Insights/metricDefinitions/read|エラスティック データベース プールの利用可能なメトリックの種類を返します。|
|/servers/elasticPools/read|指定されたサーバーのエラスティック プールの詳細を取得します。|
|/servers/elasticPools/skus/read|このエラスティック プールで使用可能な SKU のコレクションを取得します。|
|/servers/elasticPools/write|新しいエラスティック プールを作成するか、既存のエラスティック プールのプロパティを変更します。|
|/servers/encryptionProtector/read|サーバー暗号化の保護機能の一覧を返すか、指定されたサーバー暗号化の保護機能のプロパティを取得します。|
|/servers/encryptionProtector/write|指定されたサーバー暗号化の保護機能のプロパティを更新します。|
|/servers/extendedAuditingSettings/read|指定されたサーバーで構成されている拡張サーバー BLOB 監査ポリシーの詳細を取得します。|
|/servers/extendedAuditingSettings/write|指定されたサーバーの拡張サーバー BLOB 監査を変更します。|
|/servers/failoverGroups/delete|既存のフェールオーバー グループを削除します。|
|/servers/failoverGroups/failover/action|既存のフェールオーバー グループで計画されたフェールオーバーを実行します。|
|/servers/failoverGroups/forceFailoverAllowDataLoss/action|既存のフェールオーバー グループで強制フェールオーバーを実行します。|
|/servers/failoverGroups/read|フェールオーバー グループの一覧を返すか、指定されたフェールオーバー グループのプロパティを取得します。|
|/servers/failoverGroups/write|指定されたパラメーターでフェールオーバー グループを作成するか、指定されたフェールオーバー グループのプロパティまたはタグを更新します。|
|/servers/firewallRules/delete|既存のサーバー ファイアウォール規則を削除します。|
|/servers/firewallRules/read|サーバー ファイアウォール規則の一覧を返すか、指定されたサーバー ファイアウォール規則のプロパティを取得します。|
|/servers/firewallRules/write|指定されたパラメーターでサーバー ファイアウォール規則を作成するか、指定された規則のプロパティを更新するか、新しいサーバー ファイアウォール規則ですべての既存規則を上書きします。|
|/servers/import/action|サーバーに新しいデータベースを作成するか、DacPac パッケージからスキーマとデータをデプロイします。|
|/servers/importExportOperationResults/read|進行中のインポート/エクスポート操作を取得します。|
|/servers/keys/delete|既存のサーバー キーを削除します。|
|/servers/keys/read|サーバー キーの一覧を返すか、指定されたサーバー キーのプロパティを取得します。|
|/servers/keys/write|指定されたパラメーターでキーを作成するか、指定されたサーバー キーのプロパティまたはタグを更新します。|
|/servers/operationResults/read|進行中のサーバー操作を取得します。|
|/servers/providers/Microsoft.Insights/metricDefinitions/read|サーバーで使用可能なメトリックの種類を返します。|
|/servers/read|サーバーの一覧を返すか、指定されたサーバーのプロパティを取得します。|
|/servers/recommendedElasticPools/databases/read|指定されたサーバーの推奨エラスティック データベース プールのメトリックを取得します。|
|/servers/recommendedElasticPools/read|リソース使用率の履歴に基づいて、コストを削減したり、パフォーマンスを向上させたりするために、エラスティック データベース プールの推奨事項を取得します。|
|/servers/recoverableDatabases/read|この操作は、最新の既知の良好なバックアップ ポイントにデータベースを復元するために、ライブ データベースのディザスター リカバリーに使用します。 最新の良好なバックアップに関する情報が返されますが、データベースを実際に復元するわけではありません。|
|/servers/restorableDroppedDatabases/read|指定されたサーバーで破棄されたデータベースのうち、まだリテンション期間ポリシーの範囲内にあるデータベースの一覧を取得します。|
|/servers/securityAlertPolicies/operationResults/read|サーバーの脅威検出ポリシーの書き込み操作の結果を取得します。|
|/servers/securityAlertPolicies/read|指定されたサーバーで構成されているサーバーの脅威検出ポリシーの詳細を取得します。|
|/servers/securityAlertPolicies/write|指定されたサーバーの脅威検出ポリシーを変更します。|
|/servers/serviceObjectives/read|指定されたサーバーで利用可能なサービス レベルの目標 (パフォーマンス レベルとも呼ばれます) の一覧を取得します。|
|/servers/syncAgents/delete|既存の同期エージェントを削除します。|
|/servers/syncAgents/generateKey/action|同期エージェントの登録キーを生成します。|
|/servers/syncAgents/linkedDatabases/read|同期エージェントのリンクされたデータベースの一覧を返します。|
|/servers/syncAgents/read|同期エージェントの一覧を返すか、指定された同期エージェントのプロパティを取得します。|
|/servers/syncAgents/write|指定されたパラメーターで同期エージェントを作成するか、指定され同期エージェントのプロパティを更新します。|
|/servers/usages/read|サーバーの DTU クォータと、サーバー内のすべてのデータベースによる現在の DTU 消費量を返します。|
|/servers/virtualNetworkRules/delete|既存の仮想ネットワーク ルールを削除します。|
|/servers/virtualNetworkRules/read|仮想ネットワーク ルールの一覧を返すか、指定された仮想ネットワーク ルールのプロパティを取得します。|
|/servers/virtualNetworkRules/write|指定されたパラメーターで仮想ネットワーク ルールを作成するか、指定された仮想ネットワーク ルールのプロパティまたはタグを更新します。|
|/servers/write|指定されたパラメーターでサーバーを作成するか、指定されたサーバーのプロパティまたはタグを更新します。|
|/unregister/action|Microsoft SQL Database リソース プロバイダーのサブスクリプションを登録解除し、Microsoft SQL Database を作成できるようにします。|
|/virtualClusters/read|仮想クラスターの一覧を返すか、指定された仮想クラスターのプロパティを取得します。|
|/virtualClusters/write|仮想クラスターのタグを更新します。|

## <a name="microsoftstorage"></a>Microsoft.Storage

| 操作 | [説明] |
|---|---|
|/checknameavailability/read|アカウント名が有効であり、使用されていないことを確認します。|
|/locations/deleteVirtualNetworkOrSubnets/action|仮想ネットワークまたはサブネットを削除していることを Microsoft.Storage に通知します。|
|/operations/read|非同期操作の状態をポーリングします。|
|/register/action|ストレージ リソース プロバイダーにサブスクリプションを登録し、ストレージ アカウントを作成できるようにします。|
|/skus/read|Microsoft.Storage でサポートされている SKU を一覧表示します。|
|/storageAccounts/blobServices/containers/clearLegalHold/action|BLOB コンテナーの訴訟ホールドをクリアします。|
|/storageAccounts/blobServices/containers/delete|コンテナーを削除した結果を返します。|
|/storageAccounts/blobServices/containers/immutabilityPolicies/delete|BLOB コンテナーの不変ポリシーを削除します。|
|/storageAccounts/blobServices/containers/immutabilityPolicies/extend/action|BLOB コンテナーの不変ポリシーを拡張します。|
|/storageAccounts/blobServices/containers/immutabilityPolicies/lock/action|BLOB コンテナーの不変ポリシーをロックします。|
|/storageAccounts/blobServices/containers/immutabilityPolicies/read|BLOB コンテナーの不変ポリシーを取得します。|
|/storageAccounts/blobServices/containers/immutabilityPolicies/write|BLOB コンテナーの不変ポリシーを設定します。|
|/storageAccounts/blobServices/containers/read|コンテナーまたはコンテナーの一覧を返します。|
|/storageAccounts/blobServices/containers/setLegalHold/action|BLOB コンテナーの訴訟ホールドを設定します。|
|/storageAccounts/blobServices/containers/write|BLOB コンテナーの設定またはリースの結果を返します。|
|/storageAccounts/blobServices/providers/Microsoft.Insights/diagnosticSettings/read|リソースの診断設定を取得します。|
|/storageAccounts/blobServices/providers/Microsoft.Insights/diagnosticSettings/write|リソースの診断設定を作成または更新します。|
|/storageAccounts/blobServices/providers/Microsoft.Insights/metricDefinitions/read|Microsoft ストレージ メトリックの定義の一覧を取得します。|
|/storageAccounts/blobServices/read|Blob service のプロパティまたは統計情報を返します。|
|/storageAccounts/blobServices/write|Blob service のプロパティの設定の結果を返します。|
|/storageAccounts/delete|既存のストレージ アカウントを削除します。|
|/storageAccounts/fileServices/providers/Microsoft.Insights/diagnosticSettings/read|リソースの診断設定を取得します。|
|/storageAccounts/fileServices/providers/Microsoft.Insights/diagnosticSettings/write|リソースの診断設定を作成または更新します。|
|/storageAccounts/fileServices/providers/Microsoft.Insights/metricDefinitions/read|Microsoft ストレージ メトリックの定義の一覧を取得します。|
|/storageAccounts/listAccountSas/action|指定されたストレージ アカウントのアカウント SAS トークンを返します。|
|/storageAccounts/listkeys/action|指定されたストレージ アカウントのアクセス キーを返します。|
|/storageAccounts/listServiceSas/action|指定されたストレージ アカウントのサービス SAS トークンを返します。|
|/storageAccounts/providers/Microsoft.Insights/diagnosticSettings/read|リソースの診断設定を取得します。|
|/storageAccounts/providers/Microsoft.Insights/diagnosticSettings/write|リソースの診断設定を作成または更新します。|
|/storageAccounts/providers/Microsoft.Insights/metricDefinitions/read|Microsoft ストレージ メトリックの定義の一覧を取得します。|
|/storageAccounts/queueServices/providers/Microsoft.Insights/diagnosticSettings/write|リソースの診断設定を作成または更新します。|
|/storageAccounts/queueServices/providers/Microsoft.Insights/metricDefinitions/read|Microsoft ストレージ メトリックの定義の一覧を取得します。|
|/storageAccounts/queueServices/queues/delete|キューを削除した結果を返します。|
|/storageAccounts/queueServices/queues/read|キューまたはキューの一覧を返します。|
|/storageAccounts/queueServices/queues/write|キューの書き込みの結果を返します。|
|/storageAccounts/queueServices/read|Queue サービスのプロパティまたは統計情報を返します。|
|/storageAccounts/queueServices/write|Queue サービスのプロパティを設定した結果を返します。|
|/storageAccounts/read|ストレージ アカウントの一覧を返すか、指定されたストレージ アカウントのプロパティを取得します。|
|/storageAccounts/regeneratekey/action|指定されたストレージ アカウントのアクセス キーを再生成します。|
|/storageAccounts/services/diagnosticSettings/write|ストレージ アカウントの診断設定を作成または更新します。|
|/storageAccounts/storageAccounts/queueServices/providers/ Microsoft.Insights/diagnosticSettings/read|リソースの診断設定を取得します。|
|/storageAccounts/tableServices/providers/Microsoft.Insights/diagnosticSettings/read|リソースの診断設定を取得します。|
|/storageAccounts/tableServices/providers/Microsoft.Insights/diagnosticSettings/write|リソースの診断設定を作成または更新します。|
|/storageAccounts/tableServices/providers/Microsoft.Insights/metricDefinitions/read|Microsoft ストレージ メトリックの定義の一覧を取得します。|
|/storageAccounts/write|指定されたパラメーターを使用してストレージ アカウントを作成するか、プロパティまたはタグを更新します。または、指定されたストレージ アカウントのカスタム ドメインを追加します。|
|/usages/read|指定されたサブスクリプションのリソースの制限と現在の使用回数を返します。|

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

| 操作 | [説明] |
|---|---|
|/storageSyncServices/delete|ストレージ同期サービスを削除します。|
|/storageSyncServices/providers/Microsoft.Insights/metricDefinitions/read|ストレージ同期サービスで使用可能なメトリックを取得します。|
|/storageSyncServices/read|ストレージ同期サービスを読み取ります。|
|/storageSyncServices/registeredServers/delete|登録済みサーバーを削除します。|
|/storageSyncServices/registeredServers/read|登録済みサーバーを読み取ります。|
|/storageSyncServices/registeredServers/write|登録済みサーバーを作成または更新します。|
|/storageSyncServices/syncGroups/cloudEndpoints/delete|クラウド エンドポイントを削除します。|
|/storageSyncServices/syncGroups/cloudEndpoints/operationresults/read|非同期バックアップ呼び出しの場所 API です。|
|/storageSyncServices/syncGroups/cloudEndpoints/postbackup/action|バックアップの後にこのアクションを呼び出します。|
|/storageSyncServices/syncGroups/cloudEndpoints/postrestore/action|復元の後にこのアクションを呼び出します。|
|/storageSyncServices/syncGroups/cloudEndpoints/prebackup/action|バックアップの前にこのアクションを呼び出します。|
|/storageSyncServices/syncGroups/cloudEndpoints/prerestore/action|復元の前にこのアクションを呼び出します。|
|/storageSyncServices/syncGroups/cloudEndpoints/read|クラウド エンドポイントを読み取ります。|
|/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/action|ハートビートを復元します。|
|/storageSyncServices/syncGroups/cloudEndpoints/write|クラウド エンドポイントを作成または更新します。|
|/storageSyncServices/syncGroups/delete|同期グループを削除します。|
|/storageSyncServices/syncGroups/read|同期グループを読み取ります。|
|/storageSyncServices/syncGroups/serverEndpoints/delete|サーバー エンドポイントを削除します。|
|/storageSyncServices/syncGroups/serverEndpoints/read|サーバー エンドポイントを読み取ります。|
|/storageSyncServices/syncGroups/serverEndpoints/recallAction/action|このアクションを呼び出して、サーバーにファイルを戻します。|
|/storageSyncServices/syncGroups/serverEndpoints/write|サーバー エンドポイントを作成または更新します。|
|/storageSyncServices/syncGroups/write|同期グループを作成または更新します。|
|/storageSyncServices/write|ストレージ同期サービスを作成または更新します。|

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

| 操作 | [説明] |
|---|---|
|/managers/accessControlRecords/delete|アクセス制御レコードを削除します。|
|/managers/accessControlRecords/read|アクセス制御レコードを一覧表示または取得します。|
|/managers/accessControlRecords/write|アクセス制御レコードを作成または更新します。|
|/managers/alerts/read|アラートを一覧表示または取得します。|
|/managers/bandwidthSettings/delete|既存の帯域幅設定を削除します (8000 シリーズのみ)。|
|/managers/bandwidthSettings/read|帯域幅設定を一覧表示します (8000 シリーズのみ)。|
|/managers/bandwidthSettings/write|新しい帯域幅設定を作成するか、帯域幅設定を更新します (8000 シリーズのみ)。|
|/Managers/certificates/write|"リソース証明書を更新" 操作では、リソース/コンテナー資格情報証明書を更新します。|
|/managers/clearAlerts/action|デバイス マネージャーに関連付けられているすべてのアラートをクリアします。|
|/managers/cloudApplianceConfigurations/read|クラウド アプライアンスでサポートされている構成を一覧表示します。|
|/managers/configureDevice/action|デバイスを構成します。|
|/managers/delete|デバイス マネージャーを削除します。|
|/Managers/delete|"コンテナーの削除" 操作では、指定された "コンテナー" 型の Azure リソースを削除します。|
|/managers/devices/alertSettings/read|アラート設定を一覧表示または取得します。|
|/managers/devices/alertSettings/write|アラート設定を作成または更新します。|
|/managers/devices/backupPolicies/backup/action|手動バックアップを実行して、ポリシーで保護されているすべてのボリュームのオンデマンド バックアップを作成します。|
|/managers/devices/backupPolicies/delete|既存のバックアップ ポリシーを削除します (8000 シリーズのみ)。|
|/managers/devices/backupPolicies/read|バックアップ ポリシーを一覧表示します (8000 シリーズのみ)。|
|/managers/devices/backupPolicies/schedules/delete|既存のスケジュールを削除します。|
|/managers/devices/backupPolicies/schedules/read|スケジュールを一覧表示します。|
|/managers/devices/backupPolicies/schedules/write|新しいスケジュールを作成するか、スケジュールを更新します。|
|/managers/devices/backupPolicies/write|新しいバックアップ ポリシーを作成するか、バックアップ ポリシーを更新します (8000 シリーズのみ)。|
|/managers/devices/backups/delete|バックアップ セットを削除します。|
|/managers/devices/backups/elements/clone/action|バックアップ要素を使用して、共有またはボリュームを複製します。|
|/managers/devices/backups/read|バックアップ セットを一覧表示または取得します。|
|/managers/devices/backups/restore/action|バックアップ セットからすべてのボリュームを復元します。|
|/managers/devices/backupScheduleGroups/delete|バックアップ スケジュール グループを削除します。|
|/managers/devices/backupScheduleGroups/read|バックアップ スケジュール グループを一覧表示または取得します。|
|/managers/devices/backupScheduleGroups/write|バックアップ スケジュール グループを作成または更新します。|
|/managers/devices/chapSettings/delete|CHAP 設定を削除します。|
|/managers/devices/chapSettings/read|CHAP 設定を一覧表示または取得します。|
|/managers/devices/chapSettings/write|CHAP 設定を作成または更新します。|
|/managers/devices/deactivate/action|デバイスを非アクティブ化します。|
|/managers/devices/delete|デバイスを削除します。|
|/managers/devices/download/action|デバイスの更新プログラムをダウンロードします。|
|/managers/devices/failover/action|デバイスをフェールオーバーします。|
|/managers/devices/fileservers/backup/action|ファイル サーバーのバックアップを作成します。|
|/managers/devices/fileservers/delete|ファイル サーバーを削除します。|
|/managers/devices/fileservers/metrics/read|メトリックを一覧表示または取得します。|
|/managers/devices/fileservers/metricsDefinitions/read|メトリック定義を一覧表示または取得します。|
|/managers/devices/fileservers/read|ファイル サーバーを一覧表示または取得します。|
|/managers/devices/fileservers/shares/delete|共有を削除します。|
|/managers/devices/fileservers/shares/metrics/read|メトリックを一覧表示または取得します。|
|/managers/devices/fileservers/shares/metricsDefinitions/read|メトリック定義を一覧表示または取得します。|
|/managers/devices/fileservers/shares/read|共有を一覧表示または取得します。|
|/managers/devices/fileservers/shares/write|共有を作成または更新します。|
|/managers/devices/fileservers/write|ファイル サーバーを作成または更新します。|
|/managers/devices/hardwareComponentGroups/changeControllerPowerState/action|ハードウェア コンポーネント グループのコントローラーの電源の状態を変更します。|
|/managers/devices/hardwareComponentGroups/read|ハードウェア コンポーネント グループを一覧表示します。|
|/managers/devices/install/action|デバイスに更新プログラムをインストールします。|
|/managers/devices/installUpdates/action|デバイスに更新プログラムをインストールします。|
|/managers/devices/iscsiservers/backup/action|iSCSI サーバーのバックアップを作成します。|
|/managers/devices/iscsiservers/delete|iSCSI サーバーを削除します。|
|/managers/devices/iscsiservers/disks/delete|ディスクを削除します。|
|/managers/devices/iscsiservers/disks/metrics/read|メトリックを一覧表示または取得します。|
|/managers/devices/iscsiservers/disks/metricsDefinitions/read|メトリック定義を一覧表示または取得します。|
|/managers/devices/iscsiservers/disks/read|ディスクを一覧表示または取得します。|
|/managers/devices/iscsiservers/disks/write|ディスクを作成または更新します。|
|/managers/devices/iscsiservers/metrics/read|メトリックを一覧表示または取得します。|
|/managers/devices/iscsiservers/metricsDefinitions/read|メトリック定義を一覧表示または取得します。|
|/managers/devices/iscsiservers/read|iSCSI サーバーを一覧表示または取得します。|
|/managers/devices/iscsiservers/write|iSCSI サーバーを作成または更新します。|
|/managers/devices/jobs/cancel/action|実行中のジョブを取り消します。|
|/managers/devices/jobs/read|ジョブを一覧表示または取得します。|
|/managers/devices/listFailoverSets/action|既存のデバイスのフェールオーバー セットを一覧表示します。|
|/managers/devices/listFailoverTargets/action|デバイスのフェールオーバー ターゲットを一覧表示します。|
|/managers/devices/metrics/read|メトリックを一覧表示または取得します。|
|/managers/devices/metricsDefinitions/read|メトリック定義を一覧表示または取得します。|
|/managers/devices/migrationSourceConfigurations/confirmMigration/action|移行が正常に完了したことを確認し、移行をコミットします。|
|/managers/devices/migrationSourceConfigurations/fetchConfirmMigrationStatus/action|移行の確認の状態を取得します。|
|/managers/devices/migrationSourceConfigurations/fetchMigrationEstimate/action|移行推定ジョブの状態を取得します。|
|/managers/devices/migrationSourceConfigurations/fetchMigrationStatus/action|移行の状態を取得します。|
|/managers/devices/migrationSourceConfigurations/import/action|移行のためにソース構成をインポートします。|
|/managers/devices/migrationSourceConfigurations/startMigration/action|ソース構成を使用して移行を開始します。|
|/managers/devices/migrationSourceConfigurations/startMigrationEstimate/action|移行プロセスの継続時間を推定するジョブを開始します。|
|/managers/devices/networkSettings/read|ネットワーク設定を一覧表示または取得します。|
|/managers/devices/networkSettings/write|新しいネットワーク設定を作成するか、ネットワーク設定を更新します。|
|/managers/devices/publicEncryptionKey/action|デバイス マネージャーの公開暗号化キーを一覧表示します。|
|/managers/devices/publishSupportPackage/action|Microsoft サポートによるトラブルシューティングのために、デバイスのサポート パッケージを発行します。|
|/managers/devices/read|デバイスを一覧表示または取得します。|
|/managers/devices/scanForUpdates/action|デバイスで更新プログラムをスキャンします。|
|/managers/devices/securitySettings/read|セキュリティ設定を一覧表示します。|
|/managers/devices/securitySettings/syncRemoteManagementCertificate/action|デバイスのリモート管理証明書を同期します。|
|/managers/devices/securitySettings/update/action|セキュリティ設定を更新します。|
|/managers/devices/securitySettings/write|新しいセキュリティ設定を作成するか、セキュリティ設定を更新します。|
|/managers/devices/sendTestAlertEmail/action|構成済みの電子メール受信者にテスト アラート メールを送信します。|
|/managers/devices/timeSettings/read|時刻設定を一覧表示または取得します。|
|/managers/devices/timeSettings/write|新しい時刻設定を作成するか、時刻設定を更新します。|
|/managers/devices/updateSummary/read|更新の概要を一覧表示または取得します。|
|/managers/devices/volumeContainers/delete|既存のボリューム コンテナーを削除します (8000 シリーズのみ)。|
|/managers/devices/volumeContainers/listEncryptionKeys/action|ボリューム コンテナーの暗号化キーを一覧表示します。|
|/managers/devices/volumeContainers/metrics/read|メトリックを一覧表示します。|
|/managers/devices/volumeContainers/metricsDefinitions/read|メトリック定義を一覧表示します。|
|/managers/devices/volumeContainers/read|ボリューム コンテナーを一覧表示します (8000 シリーズのみ)。|
|/managers/devices/volumeContainers/rolloverEncryptionKey/action|ボリューム コンテナーの暗号化キーをロールオーバーします。|
|/managers/devices/volumeContainers/volumes/delete|既存のボリュームを削除します。|
|/managers/devices/volumeContainers/volumes/metrics/read|メトリックを一覧表示します。|
|/managers/devices/volumeContainers/volumes/metricsDefinitions/read|メトリック定義を一覧表示します。|
|/managers/devices/volumeContainers/volumes/read|ボリュームを一覧表示します。|
|/managers/devices/volumeContainers/volumes/write|新しいボリュームを作成するか、ボリュームを更新します。|
|/managers/devices/volumeContainers/write|新しいボリューム コンテナーを作成するか、ボリューム コンテナーを更新します (8000 シリーズのみ)。|
|/managers/devices/write|デバイスを作成または更新します。|
|/managers/encryptionSettings/read|暗号化設定を一覧表示または取得します。|
|/Managers/extendedInformation/delete|"拡張情報の取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトの拡張情報を取得します。|
|/Managers/extendedInformation/read|"拡張情報の取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトの拡張情報を取得します。|
|/Managers/extendedInformation/write|"拡張情報の取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトの拡張情報を取得します。|
|/managers/getActivationKey/action|デバイス マネージャーのアクティブ化キーを取得します。|
|/managers/getEncryptionKey/action|デバイス マネージャーの暗号化キーを取得します。|
|/managers/listActivationKey/action|StorSimple デバイス マネージャーのアクティブ化キーを取得します。|
|/managers/listPrivateEncryptionKey/action|StorSimple デバイス マネージャーの秘密暗号化キーを取得します。|
|/managers/listPublicEncryptionKey/action|StorSimple デバイス マネージャーの公開暗号化キーを一覧表示します。|
|/managers/metrics/read|メトリックを一覧表示または取得します。|
|/managers/metricsDefinitions/read|メトリック定義を一覧表示または取得します。|
|/managers/provisionCloudAppliance/action|新しいクラウド アプライアンスを作成します。|
|/managers/read|デバイス マネージャーを一覧表示または取得します。|
|/Managers/read|"コンテナーの取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトを取得します。|
|/managers/regenarateRegistationCertificate/action|デバイス マネージャーの登録証明書を再生成します。|
|/managers/regenerateActivationKey/action|デバイス マネージャーのアクティブ化キーを再生成します。|
|/managers/storageAccountCredentials/delete|ストレージ アカウントの資格情報を削除します。|
|/managers/storageAccountCredentials/listAccessKey/action|ストレージ アカウントの資格情報のアクセス キーを一覧表示します。|
|/managers/storageAccountCredentials/read|ストレージ アカウントの資格情報を一覧表示または取得します。|
|/managers/storageAccountCredentials/write|ストレージ アカウントの資格情報を作成または更新します。|
|/managers/storageDomains/delete|ストレージ ドメインを削除します。|
|/managers/storageDomains/read|ストレージ ドメインを一覧表示または取得します。|
|/managers/storageDomains/write|ストレージ ドメインを作成または更新します。|
|/managers/write|デバイス マネージャーを作成または更新します。|
|/Managers/write|"コンテナーの作成" 操作では、"コンテナー" 型の Azure リソースを作成します。|

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

| 操作 | [説明] |
|---|---|
|/locations/quotas/Read|Stream Analytics サブスクリプションのクォータを読み取ります。|
|/operations/Read|Stream Analytics の操作を読み取ります。|
|/Register/action|Stream Analytics リソース プロバイダーにサブスクリプションを登録します。|
|/streamingjobs/Delete|Stream Analytics ジョブを削除します。|
|/streamingjobs/functions/Delete|Stream Analytics ジョブ関数を削除します。|
|/streamingjobs/functions/operationresults/Read|Stream Analytics ジョブ関数の操作の結果を読み取ります。|
|/streamingjobs/functions/Read|Stream Analytics ジョブ関数を読み取ります。|
|/streamingjobs/functions/RetrieveDefaultDefinition/action|Stream Analytics ジョブ関数の既定の定義を取得します。|
|/streamingjobs/functions/Test/action|Stream Analytics ジョブ関数をテストします。|
|/streamingjobs/functions/Write|Stream Analytics ジョブ関数を書き込みます。|
|/streamingjobs/inputs/Delete|Stream Analytics ジョブ入力を削除します。|
|/streamingjobs/inputs/operationresults/Read|Stream Analytics ジョブ入力の操作の結果を読み取ります。|
|/streamingjobs/inputs/Read|Stream Analytics ジョブ入力を読み取ります。|
|/streamingjobs/inputs/Sample/action|Stream Analytics ジョブ入力をサンプリングします。|
|/streamingjobs/inputs/Test/action|Stream Analytics ジョブ入力をテストします。|
|/streamingjobs/inputs/Write|Stream Analytics ジョブ入力を書き込みます。|
|/streamingjobs/metricdefinitions/Read|メトリック定義を読み取ります。|
|/streamingjobs/operationresults/Read|Stream Analytics ジョブの操作の結果を読み取ります。|
|/streamingjobs/outputs/Delete|Stream Analytics ジョブ出力を削除します。|
|/streamingjobs/outputs/operationresults/Read|Stream Analytics ジョブ出力の操作の結果を読み取ります。|
|/streamingjobs/outputs/Read|Stream Analytics ジョブ出力を読み取ります。|
|/streamingjobs/outputs/Test/action|Stream Analytics ジョブ出力をテストします。|
|/streamingjobs/outputs/Write|Stream Analytics ジョブ出力を書き込みます。|
|/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read|診断設定を読み取ります。|
|/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write|診断設定を書き込みます。|
|/streamingjobs/providers/Microsoft.Insights/logDefinitions/read|streamingjobs の利用可能なログを取得します。|
|/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read|streamingjobs の利用可能なメトリックを取得します。|
|/streamingjobs/Read|Stream Analytics ジョブを読み取ります。|
|/streamingjobs/Start/action|Stream Analytics ジョブを開始します。|
|/streamingjobs/Stop/action|Stream Analytics ジョブを停止します。|
|/streamingjobs/transformations/Delete|Stream Analytics ジョブ変換を削除します。|
|/streamingjobs/transformations/Read|Stream Analytics ジョブ変換を読み取ります。|
|/streamingjobs/transformations/Write|Stream Analytics ジョブ変換を書き込みます。|
|/streamingjobs/Write|Stream Analytics ジョブを書き込みます。|

## <a name="microsoftsubscription"></a>Microsoft.Subscription

| 操作 | [説明] |
|---|---|
|/SubscriptionDefinitions/read|管理グループ内の Azure サブスクリプションの定義を取得します。|
|/SubscriptionDefinitions/write|Azure サブスクリプションの定義を作成します。|

## <a name="microsoftsupport"></a>Microsoft.Support

| 操作 | [説明] |
|---|---|
|/register/action|サポート リソース プロバイダーに登録します。|
|/supportTickets/read|サポート チケットの詳細 (ステータス、重大度、連絡先の詳細、やり取りなど) を取得するか、サブスクリプション全体のサポート チケットの一覧を取得します。|
|/supportTickets/write|サポート チケットを作成または更新します。 技術、課金、クォータ、またはサブスクリプション管理に関する問題のサポート チケットを作成できます。 既存のサポート チケットの重大度、連絡先の詳細、やり取りを更新できます。|

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

| 操作 | [説明] |
|---|---|
|/environments/accesspolicies/delete|アクセス ポリシーを削除します。|
|/environments/accesspolicies/read|アクセス ポリシーのプロパティを取得します。|
|/environments/accesspolicies/write|環境に対する新しいアクセス ポリシーを作成するか、既存のアクセス ポリシーを更新します。|
|/environments/delete|環境を削除します。|
|/environments/eventsources/delete|イベント ソースを削除します。|
|/environments/eventsources/eventsources/providers/Microsoft.Insights/ diagnosticSettings/write|リソースの診断設定を作成または更新します。|
|/environments/eventsources/providers/Microsoft.Insights/diagnosticSettings/read|リソースの診断設定を取得します。|
|/environments/eventsources/providers/Microsoft.Insights/metricDefinitions/read|イベント ソースで使用可能なメトリックを取得します。|
|/environments/eventsources/read|イベント ソースのプロパティを取得します。|
|/environments/eventsources/write|環境に対する新しいイベント ソースを作成するか、既存のイベント ソースを更新します。|
|/environments/providers/Microsoft.Insights/diagnosticSettings/read|リソースの診断設定を取得します。|
|/environments/providers/Microsoft.Insights/diagnosticSettings/write|リソースの診断設定を作成または更新します。|
|/environments/providers/Microsoft.Insights/metricDefinitions/read|環境で使用可能なメトリックを取得します。|
|/environments/read|環境のプロパティを取得します。|
|/environments/referencedatasets/delete|参照データ セットを削除します。|
|/environments/referencedatasets/read|参照データ セットのプロパティを取得します。|
|/environments/referencedatasets/write|環境に対する新しい参照データ セットを作成するか、既存の参照データ セットを更新します。|
|/environments/status/read|環境の状態、およびそれに関連するイングレスなどの操作の状態を取得します。|
|/environments/write|新しい環境を作成するか、既存の環境を更新します。|
|/register/action|Time Series Insights リソース プロバイダーにサブスクリプションを登録し、Time Series Insights 環境を作成できるようにします。|

## <a name="microsoftweb"></a>microsoft.web

| 操作 | [説明] |
|---|---|
|/apimanagementaccounts/apiacls/read|API Management アカウントの API ACL を取得します。|
|/apimanagementaccounts/apis/apiacls/delete|API Management アカウント API の API ACL を削除します。|
|/apimanagementaccounts/apis/apiacls/read|API Management アカウント API の API ACL を取得します。|
|/apimanagementaccounts/apis/apiacls/write|API Management アカウント API の API ACL を更新します。|
|/apimanagementaccounts/apis/connectionacls/read|API Management アカウントの API の Connectionacls を取得します。|
|/apimanagementaccounts/apis/connections/confirmconsentcode/action|同意コード API Management アカウントの API 接続を確認します。|
|/apimanagementaccounts/apis/connections/connectionacls/delete|API Management アカウントの API 接続の Connectionacls を削除します。|
|/apimanagementaccounts/apis/connections/connectionacls/read|API Management アカウントの API 接続の Connectionacls を取得します。|
|/apimanagementaccounts/apis/connections/connectionacls/write|API Management アカウントの API 接続の Connectionacls を更新します。|
|/apimanagementaccounts/apis/connections/delete|API Management アカウントの API 接続を削除します。|
|/apimanagementaccounts/apis/connections/getconsentlinks/action|API Management アカウントの API 接続の同意リンクを取得します。|
|/apimanagementaccounts/apis/connections/listconnectionkeys/action|接続キー API Management アカウントの API 接続を一覧表示します。|
|/apimanagementaccounts/apis/connections/listsecrets/action|シークレット API Management アカウントの API 接続を一覧表示します。|
|/apimanagementaccounts/apis/connections/read|API Management アカウントの API 接続を取得します。|
|/apimanagementaccounts/apis/connections/write|API Management アカウントの API 接続を更新します。|
|/apimanagementaccounts/apis/delete|API Management アカウントの API を削除します。|
|/apimanagementaccounts/apis/localizeddefinitions/delete|API Management アカウントの API のローカライズされた定義を削除します。|
|/apimanagementaccounts/apis/localizeddefinitions/read|API Management アカウントの API のローカライズされた定義を取得します。|
|/apimanagementaccounts/apis/localizeddefinitions/write|API Management アカウントの API のローカライズされた定義を更新します。|
|/apimanagementaccounts/apis/read|API Management アカウントの API を取得します。|
|/apimanagementaccounts/apis/write|API Management アカウントの API を更新します。|
|/apimanagementaccounts/connectionacls/read|API Management アカウントの Connectionacls を取得します。|
|/availablestacks/read|使用可能なスタックを取得します。|
|/billingmeters/read|請求測定の一覧を取得します。|
|/certificates/Delete|既存の証明書を削除します。|
|/certificates/Read|証明書の一覧を取得します。|
|/certificates/Write|新しい証明書を追加するか、既存の証明書を更新します。|
|/checknameavailability/read|リソース名を使用できるかどうかを確認します。|
|/classicmobileservices/read|従来の Mobile Services を取得します。|
|/connectionGateways/Delete|接続ゲートウェイを削除します。|
|/connectionGateways/Join/Action|接続ゲートウェイを接続します。|
|/connectiongateways/liststatus/action|状態接続ゲートウェイを一覧表示します。|
|/connectionGateways/ListStatus/Action|接続ゲートウェイの状態を一覧表示します。|
|/connectionGateways/Move/Action|接続ゲートウェイを移動します。|
|/connectionGateways/Read|接続ゲートウェイの一覧を取得します。|
|/connectionGateways/Write|接続ゲートウェイを作成または更新します。|
|/connections/confirmconsentcode/action|接続の同意コードを確認します。|
|/connections/Delete|接続を削除します。|
|/connections/Join/Action|接続に参加します。|
|/connections/listconsentlinks/action|接続の同意リンクを一覧表示します。|
|/connections/Move/Action|接続を移動します。|
|/connections/Read|接続の一覧を取得します。|
|/connections/Write|接続を作成または更新します。|
|/customApis/Delete|カスタム API を削除します。|
|/customApis/extractApiDefinitionFromWsdl/Action|WSDL から API の定義を抽出します。|
|/customApis/Join/Action|カスタム API を結合します。|
|/customApis/listWsdlInterfaces/Action|カスタム API の WSDL インターフェイスを一覧表示します。|
|/customApis/Move/Action|カスタム API を移動します。|
|/customApis/Read|カスタム API の一覧を取得します。|
|/customApis/Write|カスタム API を作成または更新します。|
|/deploymentlocations/read|デプロイの場所を取得します。|
|/geoRegions/Read|地理的リージョンの一覧を取得します。|
|/hostingenvironments/capacities/read|ホスティング環境の容量を取得します。|
|/hostingEnvironments/Delete|App Service Environment を削除します。|
|/hostingenvironments/diagnostics/read|ホスティング環境の診断を取得します。|
|/hostingenvironments/inboundnetworkdependenciesendpoints/read|すべての受信依存関係のネットワーク エンドポイントを取得します。|
|/hostingenvironments/metricdefinitions/read|ホスティング環境のメトリック定義を取得します。|
|/hostingenvironments/multirolepools/metricdefinitions/read|ホスティング環境のマルチロール プールのメトリック定義を取得します。|
|/hostingenvironments/multirolepools/metrics/read|ホスティング環境のマルチロール プールのメトリックを取得します。|
|/hostingEnvironments/multiRolePools/providers/Microsoft.Insights/ metricDefinitions/Read|App Service 環境のマルチロールで使用可能なメトリックを取得します。|
|/hostingEnvironments/multiRolePools/Read|App Service Environment のフロントエンド プールのプロパティを取得します。|
|/hostingenvironments/multirolepools/skus/read|ホスティング環境のマルチロール プールの SKU を取得します。|
|/hostingenvironments/multirolepools/usages/read|ホスティング環境のマルチロール プールの使用状況を取得します。|
|/hostingEnvironments/multiRolePools/Write|App Service Environment に新しいフロントエンド プールを作成するか、既存のフロントエンド プールを更新します。|
|/hostingenvironments/operations/read|ホスティング環境の操作を取得します。|
|/hostingenvironments/outboundnetworkdependenciesendpoints/read|すべての送信依存関係のネットワーク エンドポイントを取得します。|
|/hostingenvironments/providers/Microsoft.Insights/diagnosticSettings/read|リソースの診断設定を取得します。|
|/hostingenvironments/providers/Microsoft.Insights/diagnosticSettings/write|リソースの診断設定を作成または更新します。|
|/hostingEnvironments/Read|App Service Environment のプロパティを取得します。|
|/hostingEnvironments/reboot/Action|App Service Environment 内のすべてのマシンを再起動します。|
|/hostingenvironments/resume/action|ホスティング環境を再開します。|
|/hostingenvironments/serverfarms/read|ホスティング環境の App Service プランを取得します。|
|/hostingenvironments/sites/read|ホスティング環境の Web アプリを取得します。|
|/hostingenvironments/suspend/action|ホスティング環境を中断します。|
|/hostingenvironments/usages/read|ホスティング環境の使用状況を取得します。|
|/hostingenvironments/workerpools/metricdefinitions/read|ホスティング環境のワーカー プールのメトリック定義を取得します。|
|/hostingenvironments/workerpools/metrics/read|ホスティング環境のワーカー プールのメトリックを取得します。|
|/hostingEnvironments/workerPools/providers/Microsoft.Insights/metricDefinitions/Read|App Service 環境のワーカー プールで使用可能なメトリックを取得します。|
|/hostingEnvironments/workerPools/Read|App Service Environment のワーカー プールのプロパティを取得します。|
|/hostingenvironments/workerpools/skus/read|ホスティング環境のワーカー プールの SKU を取得します。|
|/hostingenvironments/workerpools/usages/read|ホスティング環境のワーカー プールの使用状況を取得します。|
|/hostingEnvironments/workerPools/Write|App Service Environment に新しいワーカー プールを作成するか、既存のワーカー プールを更新します。|
|/hostingEnvironments/Write|新しい App Service Environment を作成するか、既存の App Service Environment を更新します。|
|/ishostingenvironmentnameavailable/read|ホスティング環境名を使用できるかどうかを取得します。|
|/ishostnameavailable/read|ホスト名を使用できるかどうかを確認します。|
|/isusernameavailable/read|ユーザー名を使用できるかどうかを確認します。|
|/listSitesAssignedToHostName/Read|ホスト名に割り当てられたサイトの名前を取得します。|
|/locations/apioperations/read|API 操作の場所を取得します。|
|/locations/connectiongatewayinstallations/read|接続ゲートウェイのインストールの場所を取得します。|
|/locations/extractapidefinitionfromwsdl/action|場所の WSDL から API の定義を抽出します。|
|/locations/listwsdlinterfaces/action|場所の WSDL インターフェイスを一覧表示します。|
|/locations/managedapis/apioperations/read|場所のマネージ API 操作を取得します。|
|/locations/managedapis/Join/Action|マネージ API を結合します。|
|/locations/managedapis/read|マネージ API の場所を取得します。|
|/operations/read|操作を取得します。|
|/publishingusers/read|発行ユーザーを取得します。|
|/publishingusers/write|発行ユーザーを更新します。|
|/recommendations/Read|サブスクリプションの推奨事項の一覧を取得します。|
|/register/action|Microsoft.Web リソース プロバイダーをサブスクリプションに登録します。|
|/resourcehealthmetadata/read|Resource Health のメタデータを取得します。|
|/serverfarms/capabilities/read|App Service プランの機能を取得します。|
|/serverfarms/Delete|既存の App Service プランの削除|
|/serverfarms/firstpartyapps/settings/delete|App Service プランのファースト パーティ アプリの設定を削除します。|
|/serverfarms/firstpartyapps/settings/read|App Service プランのファースト パーティ アプリの設定を取得します。|
|/serverfarms/firstpartyapps/settings/write|App Service プランのファースト パーティ アプリの設定を更新します。|
|/serverfarms/hybridconnectionnamespaces/relays/delete|App Service プランのハイブリッド接続名前空間のリレーを削除します。|
|/serverfarms/hybridconnectionnamespaces/relays/read|App Service プランのハイブリッド接続名前空間のリレーを取得します。|
|/serverfarms/hybridconnectionnamespaces/relays/sites/read|App Service プランのハイブリッド接続の名前空間のリレーの Web アプリを取得します。|
|/serverfarms/hybridconnectionplanlimits/read|App Service プランのハイブリッド接続プランの制限を取得します。|
|/serverfarms/hybridconnectionrelays/read|App Service プランのハイブリッド接続リレーを取得します。|
|/serverfarms/metricdefinitions/read|App Service プランのメトリック定義を取得します。|
|/serverfarms/metrics/read|App Service プランのメトリックを取得します。|
|/serverfarms/operationresults/read|App Service プランの操作の結果を取得します。|
|/serverfarms/providers/Microsoft.Insights/diagnosticSettings/read|リソースの診断設定を取得します。|
|/serverfarms/providers/Microsoft.Insights/diagnosticSettings/write|リソースの診断設定を作成または更新します。|
|/serverfarms/providers/Microsoft.Insights/metricDefinitions/Read|App Service プランで使用可能なメトリックを取得します。|
|/serverfarms/Read|App Service プランのプロパティを取得します。|
|/serverfarms/restartSites/Action|App Service プランのすべての Web アプリを再起動します。|
|/serverfarms/sites/read|App Service プランの Web アプリを取得します。|
|/serverfarms/skus/read|App Service プランの SKU を取得します。|
|/serverfarms/usages/read|App Service プランの使用状況を取得します。|
|/serverfarms/virtualnetworkconnections/gateways/write|App Service プランの仮想ネットワーク接続のゲートウェイを更新します。|
|/serverfarms/virtualnetworkconnections/read|App Service プランの仮想ネットワーク接続を取得します。|
|/serverfarms/virtualnetworkconnections/routes/delete|App Service プランの仮想ネットワーク接続のルートを削除します。|
|/serverfarms/virtualnetworkconnections/routes/read|App Service プランの仮想ネットワーク接続のルートを取得します。|
|/serverfarms/virtualnetworkconnections/routes/write|App Service プランの仮想ネットワーク接続のルートを更新します。|
|/serverfarms/workers/reboot/action|App Service プランの worker を再起動します。|
|/serverfarms/Write|新しい App Service プランを作成するか、既存の App Service プランを更新します。|
|/sites/analyzecustomhostname/read|カスタム ホスト名を分析します。|
|/sites/applySlotConfig/Action|ターゲット スロットの Web アプリ スロット構成を現在の Web アプリに適用します。|
|/sites/backup/Action|Web アプリの新しいバックアップを作成します。|
|/sites/backup/read|Web アプリのバックアップを取得します。|
|/sites/backup/write|Web アプリのバックアップを更新します。|
|/sites/backups/delete|Web アプリのバックアップを削除します。|
|/sites/backups/list/action|Web アプリのバックアップを一覧表示します。|
|/sites/backups/Read|Web アプリのバックアップのプロパティを取得します。|
|/sites/backups/restore/action|Web アプリのバックアップを復元します。|
|/sites/config/delete|Web アプリの構成を削除します。|
|/sites/config/list/Action|Web アプリのセキュリティに関する設定 (発行資格情報、アプリ設定、接続文字列など) を一覧表示します。|
|/sites/config/Read|Web アプリの構成設定を取得します。|
|/sites/config/Write|Web アプリの構成設定を更新します。|
|/sites/continuouswebjobs/delete|Web アプリの継続的な Web ジョブを削除します。|
|/sites/continuouswebjobs/read|Web アプリの継続的な Web ジョブを取得します。|
|/sites/continuouswebjobs/start/action|Web アプリの継続的な Web ジョブを開始します。|
|/sites/continuouswebjobs/stop/action|Web アプリの継続的な Web ジョブを停止します。|
|/sites/Delete|既存の Web アプリの削除|
|/sites/deployments/delete|Web アプリのデプロイを削除します。|
|/sites/deployments/log/read|Web アプリのデプロイのログを取得します。|
|/sites/deployments/read|Web アプリのデプロイを取得します。|
|/sites/deployments/write|Web アプリのデプロイを更新します。|
|/sites/diagnostics/analyses/execute/Action|Web アプリ診断の分析を実行します。|
|/sites/diagnostics/analyses/read|Web アプリ診断の分析を取得します。|
|/sites/diagnostics/aspnetcore/read|ASP.NET Core アプリの Web アプリ診断を取得します。|
|/sites/diagnostics/autoheal/read|Web アプリ診断の Autoheal を取得します。|
|/sites/diagnostics/deployment/read|Web アプリ診断のデプロイを取得します。|
|/sites/diagnostics/deployments/read|Web アプリ診断のデプロイを取得します。|
|/sites/diagnostics/detectors/execute/Action|Web アプリ診断の検出機能を実行します。|
|/sites/diagnostics/detectors/read|Web アプリ診断の検出機能を取得します。|
|/sites/diagnostics/failedrequestsperuri/read|URI ごとに Web アプリ診断の失敗した要求を取得します。|
|/sites/diagnostics/frebanalysis/read|Web アプリの診断の FREB 分析を取得します。|
|/sites/diagnostics/loganalyzer/read|Web アプリ診断ログ アナライザーを取得します。|
|/sites/diagnostics/read|Web アプリ診断のカテゴリを取得します。|
|/sites/diagnostics/runtimeavailability/read|Web アプリの診断の実行時の可用性を取得します。|
|/sites/diagnostics/servicehealth/read|Web アプリの診断のサービス正常性を取得します。|
|/sites/diagnostics/sitecpuanalysis/read|Web アプリ診断のサイト CPU 分析を取得します。|
|/sites/diagnostics/sitecrashes/read|Web アプリ診断のサイト クラッシュを取得します。|
|/sites/diagnostics/sitelatency/read|Web アプリ診断のサイト待機時間を取得します。|
|/sites/diagnostics/sitememoryanalysis/read|Web アプリ診断のサイト メモリ分析を取得します。|
|/sites/diagnostics/siterestartsettingupdate/read|Web アプリ診断のサイト再起動設定更新を取得します。|
|/sites/diagnostics/siterestartuserinitiated/read|Web アプリ診断のサイト再起動ユーザー開始を取得します。|
|/sites/diagnostics/siteswap/read|Web アプリ診断のサイト スワップを取得します。|
|/sites/diagnostics/threadcount/read|Web アプリ診断のスレッド数を取得します。|
|/sites/diagnostics/workeravailability/read|Web アプリの診断の Workeravailability を取得します。|
|/sites/diagnostics/workerprocessrecycle/read|Web アプリの診断のワーカー プロセスのリサイクルを取得します。|
|/sites/domainownershipidentifiers/read|Web アプリのドメイン所有権識別子を取得します。|
|/sites/domainownershipidentifiers/write|Web アプリのドメイン所有権識別子を更新します。|
|/sites/functions/action|Web アプリの関数。|
|/sites/functions/delete|Web アプリの関数を削除します。|
|/sites/functions/listsecrets/action|Web アプリの関数のシークレットを一覧表示します。|
|/sites/functions/masterkey/read|Web アプリの関数のマスター キーを取得します。|
|/sites/functions/read|Web アプリの関数を取得します。|
|/sites/functions/token/read|Web アプリの関数のトークンを取得します。|
|/sites/functions/write|Web アプリの関数を更新します。|
|/sites/hostnamebindings/delete|Web アプリのホスト名バインドを削除します。|
|/sites/hostnamebindings/read|Web アプリのホスト名バインドを取得します。|
|/sites/hostnamebindings/write|Web アプリのホスト名バインドを更新します。|
|/sites/hybridconnection/delete|Web アプリのハイブリッド接続を削除します。|
|/sites/hybridconnection/read|Web アプリのハイブリッド接続を取得します。|
|/sites/hybridconnection/write|Web アプリのハイブリッド接続を更新します。|
|/sites/hybridconnectionnamespaces/relays/delete|Web アプリのハイブリッド接続名前空間リレーを削除します。|
|/sites/hybridconnectionnamespaces/relays/listkeys/action|Web アプリのハイブリッド接続名前空間リレーのキーを一覧表示します。|
|/sites/hybridconnectionnamespaces/relays/read|Web アプリのハイブリッド接続名前空間リレーを取得します。|
|/sites/hybridconnectionnamespaces/relays/write|Web アプリのハイブリッド接続名前空間リレーを更新します。|
|/sites/hybridconnectionrelays/read|Web アプリのハイブリッド接続リレーを取得します。|
|/sites/instances/deployments/delete|Web アプリのインスタンスデプロイを削除します。|
|/sites/instances/deployments/read|Web アプリのインスタンスのデプロイを取得します。|
|/sites/instances/extensions/log/read|Web アプリのインスタンス拡張機能ログを取得します。|
|/sites/instances/extensions/read|Web アプリのインスタンス拡張機能を取得します。|
|/sites/instances/processes/delete|Web アプリのインスタンスのプロセスを削除します。|
|/sites/instances/processes/read|Web アプリのインスタンスのプロセスを取得します。|
|/sites/instances/read|Web アプリのインスタンスを取得します。|
|/sites/listsyncfunctiontriggerstatus/action|Web アプリの同期関数のトリガーの状態を一覧表示します。|
|/sites/metricdefinitions/read|Web アプリのメトリック定義を取得します。|
|/sites/metrics/read|Web アプリのメトリックを取得します。|
|/sites/metricsdefinitions/read|Web アプリのメトリック定義を取得します。|
|/sites/migratemysql/action|MySql の Web アプリを移行します。|
|/sites/migratemysql/read|Web アプリ移行 MySql を取得します。|
|/sites/networktrace/action|Web アプリのネットワーク トレース。|
|/sites/newpassword/action|Web アプリの Newpassword。|
|/sites/operationresults/read|Web アプリの操作の結果を取得します。|
|/sites/operations/read|Web アプリの操作を取得します。|
|/sites/perfcounters/read|Web アプリのパフォーマンス カウンターを取得します。|
|/sites/premieraddons/delete|Web アプリのプレミア アドオンを削除します。|
|/sites/premieraddons/read|Web アプリのプレミア アドオンを取得します。|
|/sites/premieraddons/write|Web アプリのプレミア アドオンを更新します。|
|/sites/processes/read|Web アプリのプロセスを取得します。|
|/sites/providers/Microsoft.Insights/diagnosticSettings/read|リソースの診断設定を取得します。|
|/sites/providers/Microsoft.Insights/diagnosticSettings/write|リソースの診断設定を作成または更新します。|
|/sites/providers/Microsoft.Insights/metricDefinitions/Read|Web アプリで使用可能なメトリックを取得します。|
|/sites/publiccertificates/delete|Web アプリのパブリック証明書を削除します。|
|/sites/publiccertificates/read|Web アプリのパブリック証明書を取得します。|
|/sites/publiccertificates/write|Web アプリのパブリック証明書を更新します。|
|/sites/publish/Action|Web アプリを発行します。|
|/sites/publishxml/Action|Web アプリの発行プロファイル XML を取得します。|
|/sites/publishxml/read|Web アプリの発行 XML を取得します。|
|/sites/Read|Web アプリのプロパティを取得します。|
|/sites/recommendationhistory/read|Web アプリの推奨事項の履歴を取得します。|
|/sites/recommendations/disable/action|Web アプリの推奨事項を無効にします。|
|/sites/recommendations/Read|Web アプリの推奨事項の一覧を取得します。|
|/sites/recover/action|Web アプリを復旧します。|
|/sites/resetSlotConfig/Action|Web アプリの構成をリセットします。|
|/sites/resourcehealthmetadata/read|Web アプリの Resource Health のメタデータを取得します。|
|/sites/restart/Action|Web アプリを再起動します。|
|/sites/restore/read|Web アプリの復元を取得します。|
|/sites/restore/write|Web アプリを復元します。|
|/sites/siteextensions/delete|Web アプリのサイト拡張機能を削除します。|
|/sites/siteextensions/read|Web アプリのサイト拡張機能を取得します。|
|/sites/siteextensions/write|Web アプリのサイト拡張機能を更新します。|
|/sites/slots/analyzecustomhostname/read|Web アプリとスロットのカスタム ホスト名の分析を取得します。|
|/sites/slots/applySlotConfig/Action|ターゲット スロットの Web アプリ スロット構成を現在のスロットに適用します。|
|/sites/slots/backup/Action|Web アプリ スロットの新しいバックアップを作成します。|
|/sites/slots/backup/read|Web アプリのスロット バックアップを取得します。|
|/sites/slots/backup/write|Web アプリとスロットのバックアップを更新します。|
|/sites/slots/backups/delete|Web アプリのスロット バックアップを削除します。|
|/sites/slots/backups/list/action|Web アプリとスロットのバックアップを一覧表示します。|
|/sites/slots/backups/Read|Web アプリ スロットのバックアップのプロパティを取得します。|
|/sites/slots/backups/restore/action|Web アプリとスロットのバックアップを復元します。|
|/sites/slots/config/delete|Web アプリとスロットの構成を削除します。|
|/sites/slots/config/list/Action|Web アプリ スロットのセキュリティに関する設定 (発行資格情報、アプリ設定、接続文字列など) を一覧表示します。|
|/sites/slots/config/Read|Web アプリ スロットの構成設定を取得します。|
|/sites/slots/config/Write|Web アプリ スロットの構成設定を更新します。|
|/sites/slots/continuouswebjobs/delete|Web アプリとスロットの継続的な Web ジョブを削除します。|
|/sites/slots/continuouswebjobs/read|Web アプリとスロットの継続的な Web ジョブを取得します。|
|/sites/slots/continuouswebjobs/start/action|Web アプリとスロットの継続的な Web ジョブを開始します。|
|/sites/slots/continuouswebjobs/stop/action|Web アプリとスロットの継続的な Web ジョブを停止します。|
|/sites/slots/Delete|既存の Web アプリ スロットを削除します。|
|/sites/slots/deployments/delete|Web アプリとスロットのデプロイを削除します。|
|/sites/slots/deployments/log/read|Web アプリとスロットのデプロイのログを取得します。|
|/sites/slots/deployments/read|Web アプリとスロットのデプロイを取得します。|
|/sites/slots/deployments/write|Web アプリとスロットのデプロイを更新します。|
|/sites/slots/diagnostics/analyses/execute/Action|Web アプリ スロット診断の分析を実行します。|
|/sites/slots/diagnostics/analyses/read|Web アプリ スロット診断の分析を取得します。|
|/sites/slots/diagnostics/aspnetcore/read|ASP.NET Core アプリの Web アプリ スロット診断を取得します。|
|/sites/slots/diagnostics/autoheal/read|Web アプリ スロット診断の Autoheal を取得します。|
|/sites/slots/diagnostics/deployment/read|Web アプリ スロット診断のデプロイを取得します。|
|/sites/slots/diagnostics/deployments/read|Web アプリ スロット診断のデプロイを取得します。|
|/sites/slots/diagnostics/detectors/execute/Action|Web アプリ スロット診断の検出機能を実行します。|
|/sites/slots/diagnostics/detectors/read|Web アプリ スロット診断の検出機能を取得します。|
|/sites/slots/diagnostics/frebanalysis/read|Web アプリ スロット診断の FREB 分析を取得します。|
|/sites/slots/diagnostics/loganalyzer/read|Web アプリ スロット診断ログ アナライザーを取得します。|
|/sites/slots/diagnostics/read|Web アプリ スロット診断を取得します。|
|/sites/slots/diagnostics/runtimeavailability/read|Web アプリ スロット診断の実行時の可用性を取得します。|
|/sites/slots/diagnostics/servicehealth/read|Web アプリ スロット診断の Service Health を取得します。|
|/sites/slots/diagnostics/sitecpuanalysis/read|Web アプリ スロット診断のサイト CPU 分析を取得します。|
|/sites/slots/diagnostics/sitecrashes/read|Web アプリ スロット診断のサイト クラッシュを取得します。|
|/sites/slots/diagnostics/sitelatency/read|Web アプリ スロット診断のサイト待機時間を取得します。|
|/sites/slots/diagnostics/sitememoryanalysis/read|Web アプリ スロット診断のサイト メモリ分析を取得します。|
|/sites/slots/diagnostics/siterestartsettingupdate/read|Web アプリ スロット診断のサイト再起動設定更新を取得します。|
|/sites/slots/diagnostics/siterestartuserinitiated/read|Web アプリ スロット診断のサイト再起動ユーザー開始を取得します。|
|/sites/slots/diagnostics/siteswap/read|Web アプリ スロット診断のサイト スワップを取得します。|
|/sites/slots/diagnostics/threadcount/read|Web アプリ スロット診断のスレッド数を取得します。|
|/sites/slots/diagnostics/workeravailability/read|Web アプリ スロット診断の Workeravailability を取得します。|
|/sites/slots/diagnostics/workerprocessrecycle/read|Web アプリ スロット診断のワーカー プロセス リサイクルを取得します。|
|/sites/slots/domainownershipidentifiers/read|Web アプリ スロットのドメイン所有権識別子を取得します。|
|/sites/slots/hostnamebindings/delete|Web アプリとスロットのホスト名バインドを削除します。|
|/sites/slots/hostnamebindings/read|Web アプリとスロットのホスト名バインドを取得します。|
|/sites/slots/hostnamebindings/write|Web アプリとスロットのホスト名バインドを更新します。|
|/sites/slots/hybridconnection/delete|Web アプリとスロットのハイブリッド接続を削除します。|
|/sites/slots/hybridconnection/read|Web アプリとスロットのハイブリッド接続を取得します。|
|/sites/slots/hybridconnection/write|Web アプリとスロットのハイブリッド接続を更新します。|
|/sites/slots/hybridconnectionnamespaces/relays/delete|Web アプリ スロットのハイブリッド接続名前空間リレーを削除します。|
|/sites/slots/hybridconnectionnamespaces/relays/write|Web アプリ スロットのハイブリッド接続名前空間リレーを更新します。|
|/sites/slots/hybridconnectionrelays/read|Web アプリ スロットのハイブリッド接続リレーを取得します。|
|/sites/slots/instances/deployments/read|Web アプリとスロットのインスタンスのデプロイを取得します。|
|/sites/slots/instances/processes/delete|Web アプリ スロットのインスタンス プロセスを削除します。|
|/sites/slots/instances/processes/read|Web アプリとスロットのインスタンスのプロセスを取得します。|
|/sites/slots/instances/read|Web アプリとスロットのインスタンスを取得します。|
|/sites/slots/metricdefinitions/read|Web アプリとスロットのメトリック定義を取得します。|
|/sites/slots/metrics/read|Web アプリとスロットのメトリックを取得します。|
|/sites/slots/migratemysql/read|Web アプリ スロットの移行 MySql を取得します。|
|/sites/slots/networktrace/action|Web アプリ スロットのネットワーク トレースです。|
|/sites/slots/newpassword/action|Web アプリとスロットの Newpassword。|
|/sites/slots/operationresults/read|Web アプリとスロットの操作の結果を取得します。|
|/sites/slots/operations/read|Web アプリ スロットの操作を取得します。|
|/sites/slots/perfcounters/read|Web アプリ スロットのパフォーマンス カウンターを取得します。|
|/sites/slots/phplogging/read|Web アプリとスロットの Phplogging を取得します。|
|/sites/slots/premieraddons/delete|Web アプリとスロットのプレミア アドオンを削除します。|
|/sites/slots/premieraddons/read|Web アプリとスロットのプレミア アドオンを取得します。|
|/sites/slots/premieraddons/write|Web アプリとスロットのプレミア アドオンを更新します。|
|/sites/slots/providers/Microsoft.Insights/diagnosticSettings/read|リソースの診断設定を取得します。|
|/sites/slots/providers/Microsoft.Insights/diagnosticSettings/write|リソースの診断設定を作成または更新します。|
|/sites/slots/providers/Microsoft.Insights/metricDefinitions/Read|Web アプリ スロットで使用可能なメトリックを取得します。|
|/sites/slots/publiccertificates/read|Web アプリ スロットのパブリック証明書を取得します。|
|/sites/slots/publiccertificates/write|Web アプリ スロットのパブリック証明書を作成または更新します。|
|/sites/slots/publish/Action|Web アプリ スロットを発行します。|
|/sites/slots/publishxml/Action|Web アプリ スロットの発行プロファイル XML を取得します。|
|/sites/slots/Read|Web アプリのデプロイ スロットのプロパティを取得します。|
|/sites/slots/resetSlotConfig/Action|Web アプリ スロット構成をリセットします。|
|/sites/slots/resourcehealthmetadata/read|Web アプリ スロットの Resource Health のメタデータを取得します。|
|/sites/slots/restart/Action|Web アプリ スロットを再起動します。|
|/sites/slots/restore/read|Web アプリとスロットの復元を取得します。|
|/sites/slots/restore/write|Web アプリ スロットを復元します。|
|/sites/slots/siteextensions/delete|Web アプリ スロットのサイト拡張機能を削除します。|
|/sites/slots/siteextensions/read|Web アプリ スロットのサイト拡張機能を取得します。|
|/sites/slots/siteextensions/write|Web アプリ スロットのサイト拡張機能を更新します。|
|/sites/slots/slotsdiffs/Action|Web アプリとスロットの構成の違いを取得します。|
|/sites/slots/slotsswap/Action|Web アプリのデプロイ スロットを入れ替えます。|
|/sites/slots/snapshots/read|Web アプリ スロットのスナップショットを取得します。|
|/sites/slots/sourcecontrols/Delete|Web アプリ スロットのソース管理の構成設定を削除します。|
|/sites/slots/sourcecontrols/Read|Web アプリ スロットのソース管理の構成設定を取得します。|
|/sites/slots/sourcecontrols/Write|Web アプリ スロットのソース管理の構成設定を更新します。|
|/sites/slots/start/Action|Web アプリ スロットを起動します。|
|/sites/slots/stop/Action|Web アプリ スロットを停止します。|
|/sites/slots/sync/action|Web アプリとスロットを同期します。|
|/sites/slots/triggeredwebjobs/delete|Web アプリとスロットのトリガーされた Web ジョブを削除します。|
|/slots/triggeredwebjobs/delete|Web アプリとスロットのトリガーされた Web ジョブを取得します。|
|/sites/slots/triggeredwebjobs/run/action|Web アプリとスロットのトリガーされた Web ジョブを実行します。|
|/sites/slots/usages/read|Web アプリとスロットの使用状況を取得します。|
|/sites/slots/virtualnetworkconnections/delete|Web アプリとスロットの仮想ネットワーク接続を削除します。|
|/sites/slots/virtualnetworkconnections/gateways/write|Web アプリとスロットの仮想ネットワーク接続のゲートウェイを更新します。|
|/sites/slots/virtualnetworkconnections/read|Web アプリとスロットの仮想ネットワーク接続を取得します。|
|/sites/slots/virtualnetworkconnections/write|Web アプリとスロットの仮想ネットワーク接続を更新します。|
|/sites/slots/webjobs/read|Web アプリとスロットの Web ジョブを取得します。|
|/sites/slots/Write|新しい Web アプリ スロットを作成するか、既存の Web アプリ スロットを更新します。|
|/sites/slotsdiffs/Action|Web アプリとスロットの構成の違いを取得します。|
|/sites/slotsswap/Action|Web アプリのデプロイ スロットを入れ替えます。|
|/sites/snapshots/read|Web アプリのスナップショットを取得します。|
|/sites/sourcecontrols/Delete|Web アプリのソース管理の構成設定を削除します。|
|/sites/sourcecontrols/Read|Web アプリのソース管理の構成設定を取得します。|
|/sites/sourcecontrols/Write|Web アプリのソース管理の構成設定を更新します。|
|/sites/start/Action|Web アプリを起動します。|
|/sites/stop/Action|Web アプリを停止します。|
|/sites/sync/action|Web アプリを同期します。|
|/sites/syncfunctiontriggers/action|Web アプリの関数トリガーを同期します。|
|/sites/triggeredwebjobs/delete|Web アプリのトリガーされた Web ジョブを削除します。|
|/sites/triggeredwebjobs/history/read|Web アプリのトリガーされた Web ジョブの履歴を取得します。|
|/sites/triggeredwebjobs/read|Web アプリのトリガーされた Web ジョブを取得します。|
|/sites/triggeredwebjobs/run/action|Web アプリのトリガーされた Web ジョブを実行します。|
|/sites/usages/read|Web アプリの使用状況を取得します。|
|/sites/virtualnetworkconnections/delete|Web アプリの仮想ネットワーク接続を削除します。|
|/sites/virtualnetworkconnections/gateways/read|Web アプリの仮想ネットワーク接続のゲートウェイを取得します。|
|/sites/virtualnetworkconnections/gateways/write|Web アプリの仮想ネットワーク接続のゲートウェイを更新します。|
|/sites/virtualnetworkconnections/read|Web アプリの仮想ネットワーク接続を取得します。|
|/sites/virtualnetworkconnections/write|Web アプリの仮想ネットワーク接続を更新します。|
|/sites/webjobs/read|Web アプリの Web ジョブを取得します。|
|/sites/Write|新しい Web アプリを作成するか、既存の Web アプリを更新します。|
|/skus/read|SKU を取得します。|
|/sourcecontrols/read|ソース管理を取得します。|
|/sourcecontrols/write|ソース管理を更新します。|
|/unregister/action|サブスクリプションへの Microsoft.Web リソース プロバイダーの登録を解除します。|
|/validate/action|検証します。|
|/verifyhostingenvironmentvnet/action|ホスティング環境の Vnet を検証します。|

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

| 操作 | [説明] |
|---|---|
|/components/read|操作のリソースを読み取ります。|
|/healthInstances/read|操作のリソースを読み取ります。|
|/Operations/read|操作のリソースを読み取ります。|
|/workloads/delete|ワークロードのリソースを削除します。|
|/workloads/read|ワークロードのリソースを読み取ります。|
|/workloads/write|ワークロードのリソースを書き込みます。|

## <a name="next-steps"></a>次の手順

- [カスタム ロールを作成する](custom-roles.md)方法を確認します。
- [組み込みの RBAC ロール](built-in-roles.md)を確認します。
- アクセス権の割り当てを[ユーザーごと](role-assignments-users.md)または[リソースごと](role-assignments-portal.md)に管理する方法を確認します。 
- [リソースのアクションを監査するアクティビティ ログを表示する](~/articles/azure-resource-manager/resource-group-audit.md)方法を確認します
