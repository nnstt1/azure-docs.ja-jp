---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/15/2021
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 5798ad8721d8bf2924aa97876d0c8354681d3568
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718202"
---
Azure Portal で汎用 v2 ストレージ アカウントを作成するには、次の手順に従います。

1. Azure portal のメニューで **[すべてのサービス]** を選択します。 リソースの一覧で「**ストレージ アカウント**」と入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **[ストレージ アカウント]** を選択します。
1. 表示された **[ストレージ アカウント]** ウィンドウで **[+ 新規]** を選択します。
1. **[基本]** ブレードで、ストレージ アカウントを作成するサブスクリプションを選択します。
1. **[リソース グループ]** フィールドで、目的のリソース グループを選択するか、新しいリソース グループを作成します。  Azure リソース グループの詳細については、[Azure Resource Manager の概要](../articles/azure-resource-manager/management/overview.md)に関するページを参照してください。
1. 次に、ストレージ アカウントの名前を入力します。 選択する名前は Azure 全体で一意である必要があります。 また、名前の長さは 3 から 24 文字とし、数字と小文字のみを使用できます。
1. 対象のストレージ アカウントのリージョンを選択するか、既定のリージョンを使います。
1. パフォーマンス レベルを選択します。 既定のレベルは *Standard* です。
1. ストレージ アカウントのレプリケート方法を指定します。 既定の冗長オプションは *[Geo-redundant storage (GRS)]\(geo 冗長ストレージ (GRS)\)* です。 利用可能なレプリケーション オプションの詳細については、「[Azure Storage の冗長性](../articles/storage/common/storage-redundancy.md)」を参照してください。
1. その他のオプションは、 **[詳細]** 、 **[ネットワーク]** 、 **[データ保護]** 、 **[タグ]** の各ブレードで利用できます。 Azure Data Lake Storage を使用するには、 **[詳細]** ブレードを選択し、 **[階層構造名前空間]** を **[有効]** に設定します。 詳細については、[Azure Data Lake Storage Gen2 の概要](../articles/storage/blobs/data-lake-storage-introduction.md)に関するページを参照してください。
1. **[確認および作成]** を選択して、ストレージ アカウントの設定を確認し、アカウントを作成します。
1. **［作成］** を選択します

次の画像は、新しいストレージ アカウントの **[基本]** ブレードの設定を示しています。

:::image type="content" source="media/storage-create-account-portal-include/account-create-portal.png" alt-text="Azure portal でストレージ アカウントを作成する方法を示すスクリーンショット。" lightbox="media/storage-create-account-portal-include/account-create-portal.png":::
