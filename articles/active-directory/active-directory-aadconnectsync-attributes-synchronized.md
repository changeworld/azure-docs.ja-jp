<properties
	pageTitle="Azure AD Connect Sync: Azure Active Directory に同期される属性"
	description="Azure Active Directory に同期される属性の一覧を示します。"
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/27/2015"
	ms.author="markusvi"/>


# Azure AD Connect Sync: Azure Active Directory に同期される属性

このトピックでは、Azure AD Connect Sync によって同期される属性の一覧を示します。<br> 属性は、関連する Azure AD アプリによってグループ化されます。
 




## Office 365 ProPlus

| 属性名| ユーザー| コメント |
| --- | :-: | --- |
| accountEnabled| ○| userAccountControl から派生|
| cn| ○| |
| displayName| ○| |
| objectSID| ○| |
| pwdLastSet| ○| |
| sourceAnchor| ○| ユーザー向けに使用される属性は、インストール ガイドで構成されます。|
| usageLocation| ○| AD DS の msExchUsageLocation|
| userPrincipalName| ○| |


## Exchange Online

| 属性名| ユーザー| 連絡先| グループ| コメント |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| ○| | | userAccountControl から派生|
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
| info| ○| ○| ○| |
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
| msExchExtensionCustomAttribute1| ○| ○| ○| |
| msExchExtensionCustomAttribute2| ○| ○| ○| |
| msExchExtensionCustomAttribute3| ○| ○| ○| |
| msExchExtensionCustomAttribute4| ○| ○| ○| |
| msExchExtensionCustomAttribute5| ○| ○| ○| |
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
| objectSID| ○| | ○| |
| oOFReplyToOriginator| | | ○| |
| otherFacsimileTelephone| ○| ○| | |
| otherHomePhone| ○| ○| | |
| otherTelephone| ○| ○| | |
| pager| ○| ○| | |
| physicalDeliveryOfficeName| ○| ○| | |
| postalCode| ○| ○| | |
| proxyAddresses| ○| ○| ○| |
| publicDelegates| ○| ○| ○| |
| pwdLastSet| ○| | | |
| reportToOriginator| | | ○| |
| reportToOwner| | | ○| |
| securityEnabled| | | ○| groupType から派生|
| sn| ○| ○| | |
| sourceAnchor| ○| ○| ○| ユーザー向けに使用される属性は、インストール ガイドで構成されます。|
| st| ○| ○| | |
| streetAddress| ○| ○| | |
| targetAddress| ○| ○| | |
| telephoneAssistant| ○| ○| | |
| telephoneNumber| ○| ○| | |
| thumbnailphoto| ○| ○| | |
| title| ○| ○| | |
| unauthOrig| ○| ○| ○| |
| usageLocation| ○| | | AD DS の msExchUsageLocation|
| userCertificate| ○| ○| | |
| userPrincipalName| ○| | | |
| userSMIMECertificates| ○| ○| | |
| wWWHomePage| ○| ○| | |


## SharePoint Online

| 属性名| ユーザー| 連絡先| グループ| コメント |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| ○| | | userAccountControl から派生|
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
| objectSID| ○| | ○| |
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
| pwdLastSet| ○| | | |
| reportToOriginator| | | ○| |
| reportToOwner| | | ○| |
| securityEnabled| | | ○| groupType から派生|
| sn| ○| ○| | |
| sourceAnchor| ○| ○| ○| ユーザー向けに使用される属性は、インストール ガイドで構成されます。|
| st| ○| ○| | |
| streetAddress| ○| ○| | |
| targetAddress| ○| ○| | |
| telephoneAssistant| ○| ○| | |
| telephoneNumber| ○| ○| | |
| thumbnailphoto| ○| ○| | |
| title| ○| ○| | |
| unauthOrig| ○| ○| ○| |
| url| ○| ○| | |
| usageLocation| ○| | | AD DS の msExchUsageLocation|
| userPrincipalName| ○| | | |
| wWWHomePage| ○| ○| | |

## Lync Online

| 属性名| ユーザー| 連絡先| グループ| コメント |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| ○| | | userAccountControl から派生|
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
| objectSID| ○| | ○| |
| otherTelephone| ○| ○| | |
| physicalDeliveryOfficeName| ○| ○| | |
| postalCode| ○| ○| | |
| preferredLanguage| ○| | | |
| proxyAddresses| ○| ○| ○| |
| pwdLastSet| ○| | | |
| securityEnabled| | | ○| groupType から派生|
| sn| ○| ○| | |
| sourceAnchor| ○| ○| ○| ユーザー向けに使用される属性は、インストール ガイドで構成されます。|
| st| ○| ○| | |
| streetAddress| ○| ○| | |
| telephoneNumber| ○| ○| | |
| thumbnailphoto| ○| ○| | |
| title| ○| ○| | |
| usageLocation| ○| | | AD DS の msExchUsageLocation|
| userPrincipalName| ○| | | |
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
| accountEnabled| ○| | | userAccountControl から派生|
| c| ○| ○| | |
| cn| ○| | ○| |
| description| ○| ○| ○| |
| displayName| ○| ○| ○| |
| mail| ○| ○| ○| |
| mailnickname| ○| ○| ○| |
| member| | | ○| |
| objectSID| ○| | ○| |
| proxyAddresses| ○| ○| ○| |
| pwdLastSet| ○| | | |
| securityEnabled| | | ○| groupType から派生|
| sourceAnchor| ○| ○| ○| ユーザー向けに使用される属性は、インストール ガイドで構成されます。|
| usageLocation| ○| | | AD DS の msExchUsageLocation|
| userPrincipalName| ○| | | |


## Dynamics CRM

| 属性名| ユーザー| 連絡先| グループ| コメント |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| ○| | | userAccountControl から派生|
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
| objectSID| ○| | ○| |
| physicalDeliveryOfficeName| ○| ○| | |
| postalCode| ○| ○| | |
| preferredLanguage| ○| | | |
| pwdLastSet| ○| | | |
| securityEnabled| | | ○| groupType から派生|
| sn| ○| ○| | |
| sourceAnchor| ○| ○| ○| ユーザー向けに使用される属性は、インストール ガイドで構成されます。|
| st| ○| ○| | |
| streetAddress| ○| ○| | |
| telephoneNumber| ○| ○| | |
| title| ○| ○| | |
| usageLocation| ○| | | AD DS の msExchUsageLocation|
| userPrincipalName| ○| | | |


## その他のリソース

* [Azure AD Connect Sync: 同期オプションのカスタマイズ](active-directory-aadconnectsync-whatis.md)
* [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)
 
<!--Image references-->

<!---HONumber=August15_HO6-->