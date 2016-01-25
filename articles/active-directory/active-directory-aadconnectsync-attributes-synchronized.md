<properties
	pageTitle="Azure AD Connect Sync: Azure Active Directory に同期される属性 | Microsoft Azure"
	description="Azure Active Directory に同期される属性の一覧を示します。"
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/08/2016"
	ms.author="markusvi;andkjell"/>


# Azure AD Connect Sync: Azure Active Directory に同期される属性

このトピックでは、Azure AD Connect Sync によって同期される属性の一覧を示します。<br> 属性は、関連する Azure AD アプリによってグループ化されます。


## Office 365 ProPlus

| 属性名| ユーザー| コメント |
| --- | :-: | --- |
| accountEnabled| ○| アカウントが有効な場合に定義します。|
| cn| ○| |
| displayName| ○| |
| objectSID| ○| 機械的なプロパティ。Azure AD と AD 間で同期を維持するために使用される AD ユーザー識別子です。|
| pwdLastSet| ○| 機械的なプロパティ。既に発行されているトークンを無効にする時期を確認するために使用されます。パスワード同期とフェデレーションの両方で使用されます。|
| sourceAnchor| ○| 機械的なプロパティ。ADDS と Azure AD 間の関係を維持する変更不可の識別子です。|
| usageLocation| ○| 機械的なプロパティ。ユーザーの国。ライセンスの割り当てに使用されます。|
| userPrincipalName| ○| UPN は、ユーザーのログイン ID です。多くの場合、[mail] 値と同じです。|


## Exchange Online

| 属性名| ユーザー| 連絡先| グループ| コメント |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| ○| | | アカウントが有効な場合に定義します。|
| assistant| ○| ○| | |
| authOrig| ○| ○| ○| |
| c| ○| ○| | |
| cn| ○| | ○| |
| co| ○| ○| | |
| company| ○| ○| | |
| countryCode| ○| ○| | |
| department| ○| ○| | |
| description| ○| ○| ○| |
| displayName| ○| ○| ○| |
| dLMemRejectPerms| ○| ○| ○| |
| dLMemSubmitPerms| ○| ○| ○| |
| extensionAttribute1| ○| ○| ○| |
| extensionAttribute10| ○| ○| ○| |
| extensionAttribute11| ○| ○| ○| |
| extensionAttribute12| ○| ○| ○| |
| extensionAttribute13| ○| ○| ○| |
| extensionAttribute14| ○| ○| ○| |
| extensionAttribute15| ○| ○| ○| |
| extensionAttribute2| ○| ○| ○| |
| extensionAttribute3| ○| ○| ○| |
| extensionAttribute4| ○| ○| ○| |
| extensionAttribute5| ○| ○| ○| |
| extensionAttribute6| ○| ○| ○| |
| extensionAttribute7| ○| ○| ○| |
| extensionAttribute8| ○| ○| ○| |
| extensionAttribute9| ○| ○| ○| |
| facsimiletelephonenumber| ○| ○| | |
| givenName| ○| ○| | |
| homePhone| ○| ○| | |
| info| ○| ○| ○| この属性は現在、グループには使用されていません。|
| Initials| ○| ○| | |
| l| ○| ○| | |
| legacyExchangeDN| ○| ○| ○| |
| mailNickname| ○| ○| ○| |
| mangedBy| | | ○| |
| manager| ○| ○| | |
| member| | | ○| |
| mobile| ○| ○| | |
| msDS-HABSeniorityIndex| ○| ○| ○| |
| msDS-PhoneticDisplayName| ○| ○| ○| |
| msExchArchiveGUID| ○| | | |
| msExchArchiveName| ○| | | |
| msExchAssistantName| ○| ○| | |
| msExchAuditAdmin| ○| | | |
| msExchAuditDelegate| ○| | | |
| msExchAuditDelegateAdmin| ○| | | |
| msExchAuditOwner| ○| | | |
| msExchBlockedSendersHash| ○| ○| | |
| msExchBypassAudit| ○| | | |
| msExchCoManagedByLink| | | ○| |
| msExchDelegateListLink| ○| | | |
| msExchELCExpirySuspensionEnd| ○| | | |
| msExchELCExpirySuspensionStart| ○| | | |
| msExchELCMailboxFlags| ○| | | |
| msExchEnableModeration| ○| | ○| |
| msExchExtensionCustomAttribute1| ○| ○| ○| この属性は現在、Exchange Online では使用されていません。 |
| msExchExtensionCustomAttribute2| ○| ○| ○| この属性は現在、Exchange Online では使用されていません。 |
| msExchExtensionCustomAttribute3| ○| ○| ○| この属性は現在、Exchange Online では使用されていません。 |
| msExchExtensionCustomAttribute4| ○| ○| ○| この属性は現在、Exchange Online では使用されていません。 |
| msExchExtensionCustomAttribute5| ○| ○| ○| この属性は現在、Exchange Online では使用されていません。 |
| msExchHideFromAddressLists| ○| ○| ○| |
| msExchImmutableID| ○| | | |
| msExchLitigationHoldDate| ○| ○| ○| |
| msExchLitigationHoldOwner| ○| ○| ○| |
| msExchMailboxAuditEnable| ○| | | |
| msExchMailboxAuditLogAgeLimit| ○| | | |
| msExchMailboxGuid| ○| | | |
| msExchModeratedByLink| ○| ○| ○| |
| msExchModerationFlags| ○| ○| ○| |
| msExchRecipientDisplayType| ○| ○| ○| |
| msExchRecipientTypeDetails| ○| ○| ○| |
| msExchRemoteRecipientType| ○| | | |
| msExchRequireAuthToSendTo| ○| ○| ○| |
| msExchResourceCapacity| ○| | | |
| msExchResourceDisplay| ○| | | |
| msExchResourceMetaData| ○| | | |
| msExchResourceSearchProperties| ○| | | |
| msExchRetentionComment| ○| ○| ○| |
| msExchRetentionURL| ○| ○| ○| |
| msExchSafeRecipientsHash| ○| ○| | |
| msExchSafeSendersHash| ○| ○| | |
| msExchSenderHintTranslations| ○| ○| ○| |
| msExchTeamMailboxExpiration| ○| | | |
| msExchTeamMailboxOwners| ○| | | |
| msExchTeamMailboxSharePointUrl| ○| | | |
| msExchUserHoldPolicies| ○| | | |
| msOrg-IsOrganizational| | | ○| |
| objectSID| ○| | ○| 機械的なプロパティ。Azure AD と AD 間で同期を維持するために使用される AD ユーザー識別子です。|
| oOFReplyToOriginator| | | ○| |
| otherFacsimileTelephone| ○| ○| | |
| otherHomePhone| ○| ○| | |
| otherTelephone| ○| ○| | |
| pager| ○| ○| | |
| physicalDeliveryOfficeName| ○| ○| | |
| postalCode| ○| ○| | |
| proxyAddresses| ○| ○| ○| |
| publicDelegates| ○| ○| ○| |
| pwdLastSet| ○| | | 機械的なプロパティ。既に発行されているトークンを無効にする時期を確認するために使用されます。パスワード同期とフェデレーションの両方で使用されます。|
| reportToOriginator| | | ○| |
| reportToOwner| | | ○| |
| securityEnabled| | | ○| groupType から派生|
| sn| ○| ○| | |
| sourceAnchor| ○| ○| ○| 機械的なプロパティ。ADDS と Azure AD 間の関係を維持する変更不可の識別子です。|
| st| ○| ○| | |
| streetAddress| ○| ○| | |
| targetAddress| ○| ○| | |
| telephoneAssistant| ○| ○| | |
| telephoneNumber| ○| ○| | |
| thumbnailphoto| ○| ○| | |
| title| ○| ○| | |
| unauthOrig| ○| ○| ○| |
| usageLocation| ○| | | 機械的なプロパティ。ユーザーの国。ライセンスの割り当てに使用されます。|
| userCertificate| ○| ○| | |
| userPrincipalName| ○| | | UPN は、ユーザーのログイン ID です。多くの場合、[mail] 値と同じです。|
| userSMIMECertificates| ○| ○| | |
| wWWHomePage| ○| ○| | |



## SharePoint Online

| 属性名| ユーザー| 連絡先| グループ| コメント |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| ○| | | アカウントが有効な場合に定義します。|
| authOrig| ○| ○| ○| |
| c| ○| ○| | |
| cn| ○| | ○| |
| co| ○| ○| | |
| company| ○| ○| | |
| countryCode| ○| ○| | |
| department| ○| ○| | |
| description| ○| ○| ○| |
| displayName| ○| ○| ○| |
| dLMemRejectPerms| ○| ○| ○| |
| dLMemSubmitPerms| ○| ○| ○| |
| extensionAttribute1| ○| ○| ○| |
| extensionAttribute10| ○| ○| ○| |
| extensionAttribute11| ○| ○| ○| |
| extensionAttribute12| ○| ○| ○| |
| extensionAttribute13| ○| ○| ○| |
| extensionAttribute14| ○| ○| ○| |
| extensionAttribute15| ○| ○| ○| |
| extensionAttribute2| ○| ○| ○| |
| extensionAttribute3| ○| ○| ○| |
| extensionAttribute4| ○| ○| ○| |
| extensionAttribute5| ○| ○| ○| |
| extensionAttribute6| ○| ○| ○| |
| extensionAttribute7| ○| ○| ○| |
| extensionAttribute8| ○| ○| ○| |
| extensionAttribute9| ○| ○| ○| |
| facsimiletelephonenumber| ○| ○| | |
| givenName| ○| ○| | |
| hideDLMembership| | | ○| |
| homephone| ○| ○| | |
| info| ○| ○| ○| |
| initials| ○| ○| | |
| ipPhone| ○| ○| | |
| l| ○| ○| | |
| mail| ○| ○| ○| |
| mailnickname| ○| ○| ○| |
| managedBy| | | ○| |
| manager| ○| ○| | |
| member| | | ○| |
| middleName| ○| ○| | |
| mobile| ○| ○| | |
| msExchTeamMailboxExpiration| ○| | | |
| msExchTeamMailboxOwners| ○| | | |
| msExchTeamMailboxSharePointLinkedBy| ○| | | |
| msExchTeamMailboxSharePointUrl| ○| | | |
| objectSID| ○| | ○| 機械的なプロパティ。Azure AD と AD 間で同期を維持するために使用される AD ユーザー識別子です。|
| oOFReplyToOriginator| | | ○| |
| otherFacsimileTelephone| ○| ○| | |
| otherHomePhone| ○| ○| | |
| otherIpPhone| ○| ○| | |
| otherMobile| ○| ○| | |
| otherPager| ○| ○| | |
| otherTelephone| ○| ○| | |
| pager| ○| ○| | |
| physicalDeliveryOfficeName| ○| ○| | |
| postalCode| ○| ○| | |
| postOfficeBox| ○| ○| | |
| preferredLanguage| ○| | | |
| proxyAddresses| ○| ○| ○| |
| pwdLastSet| ○| | | 機械的なプロパティ。既に発行されているトークンを無効にする時期を確認するために使用されます。パスワード同期とフェデレーションの両方で使用されます。|
| reportToOriginator| | | ○| |
| reportToOwner| | | ○| |
| securityEnabled| | | ○| groupType から派生|
| sn| ○| ○| | |
| sourceAnchor| ○| ○| ○| 機械的なプロパティ。ADDS と Azure AD 間の関係を維持する変更不可の識別子です。|
| st| ○| ○| | |
| streetAddress| ○| ○| | |
| targetAddress| ○| ○| | |
| telephoneAssistant| ○| ○| | |
| telephoneNumber| ○| ○| | |
| thumbnailphoto| ○| ○| | |
| title| ○| ○| | |
| unauthOrig| ○| ○| ○| |
| url| ○| ○| | |
| usageLocation| ○| | | 機械的なプロパティ。ユーザーの国。ライセンスの割り当てに使用されます。|
| userPrincipalName| ○| | | UPN は、ユーザーのログイン ID です。多くの場合、[mail] 値と同じです。|
| wWWHomePage| ○| ○| | |

## Lync Online

| 属性名| ユーザー| 連絡先| グループ| コメント |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| ○| | | アカウントが有効な場合に定義します。|
| c| ○| ○| | |
| cn| ○| | ○| |
| co| ○| ○| | |
| company| ○| ○| | |
| department| ○| ○| | |
| description| ○| ○| ○| |
| displayName| ○| ○| ○| |
| facsimiletelephonenumber| ○| ○| ○| |
| givenName| ○| ○| | |
| homephone| ○| ○| | |
| ipPhone| ○| ○| | |
| l| ○| ○| | |
| mail| ○| ○| ○| |
| mailNickname| ○| ○| ○| |
| managedBy| | | ○| |
| manager| ○| ○| | |
| member| | | ○| |
| mobile| ○| ○| | |
| msExchHideFromAddressLists| ○| ○| ○| |
| msRTCSIP-ApplicationOptions| ○| | | |
| msRTCSIP-DeploymentLocator| ○| ○| | |
| msRTCSIP-Line| ○| ○| | |
| msRTCSIP-OptionFlags| ○| ○| | |
| msRTCSIP-OwnerUrn| ○| | | |
| msRTCSIP-PrimaryUserAddress| ○| ○| | |
| msRTCSIP-UserEnabled| ○| ○| | |
| objectSID| ○| | ○| 機械的なプロパティ。Azure AD と AD 間で同期を維持するために使用される AD ユーザー識別子です。|
| otherTelephone| ○| ○| | |
| physicalDeliveryOfficeName| ○| ○| | |
| postalCode| ○| ○| | |
| preferredLanguage| ○| | | |
| proxyAddresses| ○| ○| ○| |
| pwdLastSet| ○| | | 機械的なプロパティ。既に発行されているトークンを無効にする時期を確認するために使用されます。パスワード同期とフェデレーションの両方で使用されます。|
| securityEnabled| | | ○| groupType から派生|
| sn| ○| ○| | |
| sourceAnchor| ○| ○| ○| 機械的なプロパティ。ADDS と Azure AD 間の関係を維持する変更不可の識別子です。|
| st| ○| ○| | |
| streetAddress| ○| ○| | |
| telephoneNumber| ○| ○| | |
| thumbnailphoto| ○| ○| | |
| title| ○| ○| | |
| usageLocation| ○| | | 機械的なプロパティ。ユーザーの国。ライセンスの割り当てに使用されます。|
| userPrincipalName| ○| | | UPN は、ユーザーのログイン ID です。多くの場合、[mail] 値と同じです。|
| wWWHomePage| ○| ○| | |


## Azure RMS

| 属性名| ユーザー| 連絡先| グループ| コメント |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| ○| | | アカウントが有効な場合に定義します。|
| cn| ○| | ○| 共通名または別名です。多くの場合、[mail] 値のプレフィックスです。|
| displayName| ○| ○| ○| 多くの場合フレンドリ名 (姓名) として示される名前を表す文字列。|
| mail| ○| ○| ○| 完全な電子メール アドレス。|
| member| | | ○| |
| objectSID| ○| | ○| 機械的なプロパティ。Azure AD と AD 間で同期を維持するために使用される AD ユーザー識別子です。|
| proxyAddresses| ○| ○| ○| 機械的なプロパティ。Azure AD によって使用されます。ユーザー向けのすべてのセカンダリの電子メール アドレスが含まれています。|
| pwdLastSet| ○| | | 機械的なプロパティ。既に発行されているトークンを無効にする時期を確認するために使用されます。|
| securityEnabled| | | ○| groupType から派生。|
| sourceAnchor| ○| ○| ○| 機械的なプロパティ。ADDS と Azure AD 間の関係を維持する変更不可の識別子です。|
| usageLocation| ○| | | 機械的なプロパティ。ユーザーの国。ライセンスの割り当てに使用されます。|
| userPrincipalName| ○| | | この UPN は、ユーザーのログイン ID です。多くの場合、[mail] 値と同じです。|


## Intune

| 属性名| ユーザー| 連絡先| グループ| コメント |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| ○| | | アカウントが有効な場合に定義します。|
| c| ○| ○| | |
| cn| ○| | ○| |
| description| ○| ○| ○| |
| displayName| ○| ○| ○| |
| mail| ○| ○| ○| |
| mailnickname| ○| ○| ○| |
| member| | | ○| |
| objectSID| ○| | ○| 機械的なプロパティ。Azure AD と AD 間で同期を維持するために使用される AD ユーザー識別子です。|
| proxyAddresses| ○| ○| ○| |
| pwdLastSet| ○| | | 機械的なプロパティ。既に発行されているトークンを無効にする時期を確認するために使用されます。パスワード同期とフェデレーションの両方で使用されます。|
| securityEnabled| | | ○| groupType から派生|
| sourceAnchor| ○| ○| ○| 機械的なプロパティ。ADDS と Azure AD 間の関係を維持する変更不可の識別子です。|
| usageLocation| ○| | | 機械的なプロパティ。ユーザーの国。ライセンスの割り当てに使用されます。|
| userPrincipalName| ○| | | UPN は、ユーザーのログイン ID です。多くの場合、[mail] 値と同じです。|



## Dynamics CRM

| 属性名| ユーザー| 連絡先| グループ| コメント |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| ○| | | アカウントが有効な場合に定義します。|
| c| ○| ○| | |
| cn| ○| | ○| |
| co| ○| ○| | |
| company| ○| ○| | |
| countryCode| ○| ○| | |
| description| ○| ○| ○| |
| displayName| ○| ○| ○| |
| facsimiletelephonenumber| ○| ○| | |
| givenName| ○| ○| | |
| l| ○| ○| | |
| managedBy| | | ○| |
| manager| ○| ○| | |
| member| | | ○| |
| mobile| ○| ○| | |
| objectSID| ○| | ○| 機械的なプロパティ。Azure AD と AD 間で同期を維持するために使用される AD ユーザー識別子です。|
| physicalDeliveryOfficeName| ○| ○| | |
| postalCode| ○| ○| | |
| preferredLanguage| ○| | | |
| pwdLastSet| ○| | | 機械的なプロパティ。既に発行されているトークンを無効にする時期を確認するために使用されます。パスワード同期とフェデレーションの両方で使用されます。|
| securityEnabled| | | ○| groupType から派生|
| sn| ○| ○| | |
| sourceAnchor| ○| ○| ○| 機械的なプロパティ。ADDS と Azure AD 間の関係を維持する変更不可の識別子です。|
| st| ○| ○| | |
| streetAddress| ○| ○| | |
| telephoneNumber| ○| ○| | |
| title| ○| ○| | |
| usageLocation| ○| | | 機械的なプロパティ。ユーザーの国。ライセンスの割り当てに使用されます。|
| userPrincipalName| ○| | | UPN は、ユーザーのログイン ID です。多くの場合、[mail] 値と同じです。|

## サード パーティ製アプリケーション
これは、Office 365、Dynamics、Intune のサポートに Azure AD ディレクトリを使用しない場合に使用できる一連の属性です。この中には、少数のコア属性が含まれます。

| 属性名| ユーザー| 連絡先| グループ| コメント |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| ○| | | アカウントが有効な場合に定義します。|
| cn| ○| | ○| |
| displayName| ○| ○| ○| |
| givenName| ○| ○| | |
| mail| ○| | ○| |
| managedBy| | | ○| |
| mailNickName| ○| ○| ○| |
| member| | | ○| |
| objectSID| ○| | | 機械的なプロパティ。Azure AD と AD 間で同期を維持するために使用される AD ユーザー識別子です。|
| proxyAddresses| ○| ○| ○| |
| pwdLastSet| ○| | | 機械的なプロパティ。既に発行されているトークンを無効にする時期を確認するために使用されます。パスワード同期とフェデレーションの両方で使用されます。|
| sn| ○| ○| | |
| sourceAnchor| ○| ○| ○| 機械的なプロパティ。ADDS と Azure AD 間の関係を維持する変更不可の識別子です。|
| usageLocation| ○| | | 機械的なプロパティ。ユーザーの国。ライセンスの割り当てに使用されます。|
| userPrincipalName| ○| | | UPN は、ユーザーのログイン ID です。多くの場合、[mail] 値と同じです。|

## Windows 10
Windows 10 のドメイン参加コンピューター (デバイス) は、一部の属性を Azure AD に同期します。シナリオの詳細については、「[Windows 10 エクスペリエンスのためにドメイン参加済みデバイスを Azure AD に接続する](active-directory-azureadjoin-devices-group-policy.md)」を参照してください。これらの属性は常に同期し、Windows 10 は選択を解除できるアプリとして表示されません。Windows 10 のドメイン参加コンピューターは、属性 userCertificate が設定されていることで識別されます。

| 属性名| デバイス| コメント |
| --- | :-: | --- |
| accountEnabled| ○| |
| deviceTrustType| ○| ドメイン参加コンピューターのハードコーディングされた値です。 |
| displayName | ○| |
| ms-DS-CreatorSID | ○| registeredOwnerReference とも呼ばれます。|
| objectGUID | ○| deviceID とも呼ばれます。|
| objectSID | ○| onPremisesSecurityIdentifier とも呼ばれます。|
| operatingSystem | ○| deviceOSType とも呼ばれます。|
| operatingSystemVersion | ○| deviceOSVersion とも呼ばれます。|
| userCertificate | ○| |

選択したアプリに加えてユーザーには以下の属性があります。

| 属性名| ユーザー| コメント |
| --- | :-: | --- |
| domainFQDN| ○| dnsDomainName とも呼ばれます。例: contoso.com|
| domainNetBios| ○| netBiosName とも呼ばれます。例: CONTOSO|

## Exchange ハイブリッドの書き戻し
次の属性は、Exchange ハイブリッドを有効にした場合に Azure AD からオンプレミスの Active Directory に書き戻されます。Exchange のバージョンに応じて、同期される属性が少なくなる場合があります。

| 属性名| ユーザー| 連絡先| グループ| コメント |
| --- | :-: | :-: | :-: | --- |
| msDS ExternalDirectoryObjectID| ○| | | Azure AD の cloudAnchor から派生します。これは Exchange 2016 で新たに追加されました。|
| msExchArchiveStatus| ○| | | オンライン アーカイブ: 顧客によるメールのアーカイブを有効にします。|
| msExchBlockedSendersHash| ○| | | フィルター処理: オンプレミスのフィルター処理、オンラインの安全性、ブロックされた送信者データをクライアントから書き戻します。|
| msExchSafeRecipientsHash| ○| | | フィルター処理: オンプレミスのフィルター処理、オンラインの安全性、ブロックされた送信者データをクライアントから書き戻します。|
| msExchSafeSendersHash| ○| | | フィルター処理: オンプレミスのフィルター処理、オンラインの安全性、ブロックされた送信者データをクライアントから書き戻します。|
| msExchUCVoiceMailSettings| ○| | | ユニファイド メッセージング (UM) の有効化 - オンラインのボイス メール: Microsoft Lync Server の統合で使用され、オンプレミスの Lync Server に対して、ユーザーがオンライン サービスでボイス メールを使用していることを示します。|
| msExchUserHoldPolicies| ○| | | 訴訟ホールド: クラウド サービスが訴訟ホールド状態のユーザーを特定できるようにします。|
| proxyAddresses| ○| ○| ○| Exchange Online の x500 アドレスのみが挿入されます。|

## 属性に関する注意事項
- 代替 ID を使用する場合、オンプレミスの userPrincipalName 属性は Azure AD の onPremisesUserPrincipalName 属性と同期されます。mail などの代替 ID 属性は、Azure AD の属性 userPrincipalName と同期されます。


## 次のステップ
[Azure AD Connect Sync](active-directory-aadconnectsync-whatis.md) の構成に関するページをご覧ください。

「[オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)」をご覧ください。

<!---HONumber=AcomDC_0114_2016-->