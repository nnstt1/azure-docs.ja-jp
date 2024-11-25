---
title: デバイス ID とデスクトップ仮想化 - Azure Active Directory
description: VDI と Azure AD デバイス ID を一緒に使用する方法について説明します
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: b81bdc056bd56e96669bde7e6d0473ef3feb3402
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131049845"
---
# <a name="device-identity-and-desktop-virtualization"></a>デバイス ID とデスクトップ仮想化

管理者は、通常、Windows オペレーティング システムをホストする仮想デスクトップ インフラストラクチャ (VDI) プラットフォームを組織に展開します。 管理者が VDI を展開する目的:

- 管理の合理化。
- リソースの統合と一元化によるコストの削減。
- エンドユーザーのモビリティを確保し、任意のデバイスでいつでもどこからでも仮想デスクトップへの自由なアクセスを提供する。

仮想デスクトップには次の 2 つの主な種類があります。

- 永続的
- 非永続的

永続的バージョンは、ユーザーまたはユーザーのプールごとに一意のデスクトップ イメージを使用します。 これらの一意のデスクトップは、将来使用するためにカスタマイズして保存できます。 

非永続的バージョンは、ユーザーが必要に応じてアクセスできる一連のデスクトップを使用します。 これらの非永続的デスクトップは、元の状態に戻されます。最新の Windows<sup>1</sup> の場合、これは仮想マシンがシャットダウン/再起動/OS リセット プロセスを経たときに発生します。ダウンレベルの Windows<sup>2</sup> の場合、これはユーザーがサインアウトしたときに発生します。

リモート作業が引き続き新しい基準となっているため、非永続的な VDI デプロイが増加しています。 お客様が非永続的な VDI をデプロイするときには、古くなったデバイスを確実に管理することが重要です。これは、デバイスのライフサイクル管理を適切な戦略のもとで行うことなく頻繁にデバイスを登録すると結果、作成されるものです。

> [!IMPORTANT]
> 古くなったデバイスの管理に失敗すると、テナント クォータの使用量に関して負荷が増加し、テナント クォータが不足した場合にはサービスが中断する潜在的リスクを負う状況に至る可能性があります。 この状況を回避するためには、非永続的な VDI 環境をデプロイするときに、以下に記載されているガイダンスに従う必要があります。

この記事では、デバイス ID と VDI のサポートに関する、管理者向けの Microsoft のガイダンスについて説明します。 デバイスID の詳細については、「[デバイス ID とは](overview.md)」の記事を参照してください。

## <a name="supported-scenarios"></a>サポートされるシナリオ

VDI 環境の Azure AD でデバイス ID を構成する前に、サポートされているシナリオについて理解を深めてください。 次の表に、サポートされているプロビジョニング シナリオを示します。 このコンテキストでのプロビジョニングとは、管理者がエンドユーザーの操作を必要とせずに、デバイス ID を大規模に構成できることを意味します。

| デバイス ID の種類 | ID インフラストラクチャ | Windows デバイス | VDI プラットフォームのバージョン | サポートされています |
| --- | --- | --- | --- | --- |
| ハイブリッド Azure AD 参加済み | フェデレーション<sup>3</sup> | 最新の Windows とダウンレベルの Windows | 永続的 | はい |
|   |   | 最新の Windows | 非永続的 | ○<sup>5</sup> |
|   |   | ダウンレベルの Windows | 非永続的 | はい<sup>6</sup> |
|   | マネージド<sup>4</sup> | 最新の Windows とダウンレベルの Windows | 永続的 | はい |
|   |   | 最新の Windows | 非永続的 | いいえ |
|   |   | ダウンレベルの Windows | 非永続的 | はい<sup>6</sup> |
| Azure AD 参加済み | フェデレーション | 最新の Windows | 永続的 | 制限付き<sup>7</sup> |
|   |   |   | 非永続的 | いいえ |
|   | マネージド | 最新の Windows | 永続的 | 制限付き<sup>7</sup> |
|   |   |   | 非永続的 | いいえ |
| Azure AD 登録済み | フェデレーション/マネージド | 最新の Windows/ダウンレベルの Windows | 永続的/非永続的 | 適用外 |

<sup>1</sup> **最新の Windows**  デバイスは、Windows 10、Windows Server 2016 バージョン 1803 以降、および Windows Server 2019 を表します。

<sup>2</sup> **ダウンレベルの Windows** デバイスは、Windows 7、Windows 8.1、Windows Server 2008 R2、Windows Server 2012、および Windows Server 2012 R2 を表します。 Windows 7 のサポート情報については、「[Windows 7 のサポート終了が近づいています](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support)」を参照してください。 Windows Server 2008 R2 のサポート情報については、「[Windows Server 2008 のサポート終了に備える](https://www.microsoft.com/cloud-platform/windows-server-2008)」を参照してください。

<sup>3</sup> **フェデレーション** ID インフラストラクチャ環境は、AD FS やその他のサードパーティ IDP などの ID プロバイダーを備えた環境を表します。

<sup>4</sup> **マネージド** ID インフラストラクチャ環境は、Azure AD が ID プロバイダーとしてデプロイされていて、[パスワード ハッシュ同期 (PHS)](../hybrid/whatis-phs.md) または [パススルー認証 (PTA)](../hybrid/how-to-connect-pta.md) のいずれかが、[シームレス シングル サインオン](../hybrid/how-to-connect-sso.md)と共に使用される環境を表します。

<sup>5</sup> **最新の Windows で非永続的機能をサポートする** には、下のガイダンス セクションに記載されているように、追加の考慮事項が必要になります。 このシナリオでは、Windows 10 1803、Windows Server 2019、または Windows Server (半期チャネル) バージョン 1803 以降が必要です。

<sup>6</sup> **ダウンレベルの Windows で非永続的機能をサポートする** には、下のガイダンス セクションに記載されているように、追加の考慮事項が必要になります。

<sup>7</sup> **Azure AD 参加のサポート** は、Azure Virtual Desktop および Windows 365 でのみ利用できます。

## <a name="microsofts-guidance"></a>Microsoft のガイダンス

管理者は、ID インフラストラクチャに基づいて次の記事を参照して、ハイブリッド Azure AD 参加を構成する方法を理解してください。

- [フェデレーション環境用のハイブリッド Azure Active Directory 参加を構成する](hybrid-azuread-join-federated-domains.md)
- [マネージド環境用のハイブリッド Azure Active Directory 参加を構成する](hybrid-azuread-join-managed-domains.md)

### <a name="non-persistent-vdi"></a>非永続的 VDI

非永続的 VDI をデプロイする場合は、IT 管理者が以下のガイダンスを実行に移すことをお勧めします。 そのようにしないと、非永続的な VDI プラットフォームから登録された古い Hybrid Azure AD 参加済みデバイスが、ディレクトリに内に多数存在することになります。これにより、テナント クォータの負荷が増加し、テナント クォータの不足が原因でサービスが中断されるリスクが生じます。

- システム準備ツール (sysprep.exe) を使用していて、インストールに Windows 10 1809 より前のイメージを使用している場合は、そのイメージが、既にハイブリッド Azure AD 参加済みとして Azure AD に登録されているデバイスからのものではないことを確認します。
- 仮想マシン (VM) のスナップショットを利用して追加の VM を作成する場合は、そのスナップショットが、既にハイブリッド Azure AD 参加として Azure AD に登録されている VM からのものではないことを確認します。
- デスクトップが非永続的な VDI ベースであることを示す、コンピューターの表示名のプレフィックス(例: NPVDI-) を作成して使用します。
- ダウンレベルの Windows の場合:
   - ログオフ スクリプトの一部として **autoworkplacejoin /leave** コマンドを実装します。 このコマンドは、ユーザーのコンテキストでトリガーする必要があります。また、ユーザーが完全にログオフする前の、まだネットワーク接続がある間に実行する必要があります。
- フェデレーション環境 (AD FS など) の最新の Windows の場合:
   - ユーザーがサインインする前に、VM ブート シーケンス/順序の一部として、**dsregcmd/join** を実行します。
   - VM のシャットダウン/再起動プロセスの一部として dsregcmd /leave を実行 **しない** でください。
- [古いデバイスの管理](manage-stale-devices.md)のプロセスを定義して実装します。
   - 非永続的な Hybrid Azure AD 参加済みデバイスを識別する方法 (コンピューター表示名のプレフィックスなど) を用意したら、ディレクトリが多くの古いデバイスで占められないように、これらのデバイスのクリーンアップをより積極的に行う必要があります。
   - 最新の Windows とダウンロードレベルの Windows への非永続的 VDI のデプロイでは、**ApproximateLastLogonTimestamp** が 15 日より古いデバイスを削除する必要があります。

> [!NOTE]
> 非永続的 VDI を使用する場合、職場または学校アカウントの追加を回避するには、次のレジストリ キーが設定されていることを確認します。  
> `HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin: "BlockAADWorkplaceJoin"=dword:00000001`    
>
> Windows 10 バージョン 1803 以降を実行していることを確認します。  
>
> パス `%localappdata%` にあるデータのローミングはサポートされていません。 `%localappdata%` にあるコンテンツを移動する場合は、次のフォルダーとレジストリキーの内容がどのような状況でもデバイスを離れることが **ない** ようにしてください。 例: プロファイル移行ツールは、次のフォルダーとキーをスキップする必要があります。
>
> * `%localappdata%\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy`
> * `%localappdata%\Packages\Microsoft.Windows.CloudExperienceHost_cw5n1h2txyewy`
> * `%localappdata%\Packages\<any app package>\AC\TokenBroker`
> * `%localappdata%\Microsoft\TokenBroker`
> * `HKEY_CURRENT_USER\SOFTWARE\Microsoft\IdentityCRL`
> * `HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows\CurrentVersion\AAD`
> * `HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows NT\CurrentVersion\WorkplaceJoin`
>
> 職場アカウントのデバイス証明書のローミングはサポートされていません。 "MS 組織アクセス" によって発行された証明書は、現在のユーザーとローカル マシンの個人用 (MY) 証明書ストアに格納されます。


### <a name="persistent-vdi"></a>永続的 VDI

永続的 VDI をデプロイする場合は、IT 管理者が以下のガイダンスを実行に移すことをお勧めします。 そうしないと、デプロイと認証の問題が発生します。 

- システム準備ツール (sysprep.exe) を使用していて、インストールに Windows 10 1809 より前のイメージを使用している場合は、そのイメージが、既にハイブリッド Azure AD 参加済みとして Azure AD に登録されているデバイスからのものではないことを確認します。
- 仮想マシン (VM) のスナップショットを利用して追加の VM を作成する場合は、そのスナップショットが、既にハイブリッド Azure AD 参加として Azure AD に登録されている VM からのものではないことを確認します。

また、[古くなったデバイスを管理](manage-stale-devices.md)するためのプロセスを実装することをお勧めします。 これにより、VM を定期的にリセットすると、ディレクトリで古くなった多くのデバイスを使用しないで済むようになります。
 
## <a name="next-steps"></a>次のステップ

[フェデレーション環境用のハイブリッド Azure Active Directory 参加を構成する](hybrid-azuread-join-federated-domains.md)
