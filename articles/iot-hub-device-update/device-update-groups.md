---
title: Azure IoT Hub 用デバイス更新のデバイス グループについて | Microsoft Docs
description: デバイス グループの使用方法を理解します。
author: aysancag
ms.author: aysancag
ms.date: 2/09/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: aa3ee7e5b92044c35ac1856309f7265ad06923a1
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2021
ms.locfileid: "110083532"
---
# <a name="device-groups"></a>デバイス グループ

デバイス グループは、デバイスのコレクションです。 デバイス グループによって、多数のデバイスへ配置をスケーリングする方法が提供されます。 各デバイスは、一度に 1 つのデバイス グループにだけ属します。
デバイスを整理するために、複数のデバイス グループを作成することも選択できます。 たとえば、Contoso は、テスト ラボ内のデバイスに "Flighting" デバイス グループを使用し、そのフィールド チームがオペレーション センターで使用するデバイスに "Evaluation" デバイス グループを使用する場合があります。 さらに、Contoso は、地域のタイムゾーンと整合するスケジュールでデバイスを更新できるように、地理的地域に基づいて運用デバイスをグループ化することを選択する場合があります。 


## <a name="using-device-or-module-twin-tag-for-device-group-creation"></a>デバイス グループの作成にデバイスまたはモジュール ツイン タグを使用する

タグを使用すると、ユーザーがデバイスをグループ化できます。 デバイスをグループ化できるようにするには、それらに ADUGroup キーとデバイスまたはモジュール ツインの値が必要です。

### <a name="device-or-module-twin-tag-format"></a>デバイスまたはモジュール ツイン タグの形式

```markdown
"tags": {
  "ADUGroup": "<CustomTagValue>"
}
```


## <a name="uncategorized-device-group"></a>未分類のデバイス グループ

未分類は、次のようなデバイスをグループ化するために使用される予約語です。
- ADUGroup デバイスまたはモジュール ツイン タグがありません。
- ADUGroup デバイスまたはモジュール ツイン タグはありますが、このグループ名でグループが作成されていません。

たとえば、次のデバイス ツイン タグを持つデバイスを考えてみます。

```markdown
"deviceId": "Device1",
"tags": {
  "ADUGroup": "Group1"
}
```

```markdown
"deviceId": "Device2",
"tags": {
  "ADUGroup": "Group1"
}
```

```markdown
"deviceId": "Device3",
"tags": {
  "ADUGroup": "Group2"
}
```

```markdown
"deviceId": "Device4",
```

次に、デバイスと、それらに対して作成できるグループを示します。

|Device |グループ  |
|-----------|--------------|
|Device1    |Group1|
|Device2    |Group1|
|Device3    |Group2|
|Device4    |未分類|



## <a name="next-steps"></a>次のステップ

[デバイス グループの作成](./create-update-group.md)
