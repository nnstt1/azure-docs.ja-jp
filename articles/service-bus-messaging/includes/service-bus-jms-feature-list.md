---
title: インクルード ファイル
description: インクルード ファイル
services: service-bus-messaging
author: axisc
ms.service: service-bus-messaging
ms.topic: include
ms.date: 09/28/2021
ms.author: aschhab
ms.custom: include file
ms.openlocfilehash: 6d2207b01f6ea9eff3dd2d41e9738f756f3f9fff
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129220766"
---
次の表は、現在 Azure Service Bus でサポートされている Java Message Service (JMS) 機能の一覧を示しています。 また、サポートされていない機能も示しています。


| 機能 | API |Status |
|---|---|---|
| キュー   | <ul> <li> JMSContext.createQueue(String queueName) </li> </ul>| **サポートされています** |
| トピック   | <ul> <li> JMSContext.createTopic(String topicName) </li> </ul>| **サポートされています** |
| 一時キュー |<ul> <li> JMSContext.createTemporaryQueue() </li> </ul>| **サポートされています** |
| 一時トピック |<ul> <li> JMSContext.createTemporaryTopic() </li> </ul>| **サポートされています** |
| メッセージ プロデューサー/<br/> JMSProducer |<ul> <li> JMSContext.createProducer() </li> </ul>| **サポートされています** |
| キュー ブラウザー |<ul> <li> JMSContext.createBrowser(Queue queue) </li> <li> JMSContext.createBrowser(Queue queue, String messageSelector) </li> </ul> | **サポートされています** |
| メッセージ コンシューマー/ <br/> JMSConsumer | <ul> <li> JMSContext.createConsumer(Destination destination) </li> <li> JMSContext.createConsumer(Destination destination, String messageSelector) </li> <li> JMSContext.createConsumer(Destination destination, String messageSelector, boolean noLocal)</li> </ul>  <br/> noLocal は現在サポートされていません。 | **サポートされています** |
| 共有の永続的サブスクリプション | <ul> <li> JMSContext.createSharedDurableConsumer(Topic topic, String name) </li> <li> JMSContext.createSharedDurableConsumer(Topic topic, String name, String messageSelector) </li> </ul>| **サポートされています**|
| 非共有の永続的サブスクリプション | <ul> <li> JMSContext.createDurableConsumer(Topic topic, String name) </li> <li> createDurableConsumer(Topic topic, String name, String messageSelector, boolean noLocal) </li> </ul> <br/> noLocal は現在サポートされていません。False に設定する必要があります | **サポートされています** |
| 共有の非永続的サブスクリプション |<ul> <li> JMSContext.createSharedConsumer(Topic topic, String sharedSubscriptionName) </li> <li> JMSContext.createSharedConsumer(Topic topic, String sharedSubscriptionName, String messageSelector) </li> </ul> | **サポートされています** |
| 非共有の非永続的サブスクリプション |<ul> <li> JMSContext.createConsumer(Destination destination) </li> <li> JMSContext.createConsumer(Destination destination, String messageSelector) </li> <li> JMSContext.createConsumer(Destination destination, String messageSelector, boolean noLocal) </li> </ul> <br/> noLocal は現在サポートされていません。False に設定する必要があります | **サポートされています** |
| メッセージ セレクター | 作成されるコンシューマーによって異なります | **サポートされています** |
| 配信の遅延 (スケジュール済みメッセージ) | <ul> <li> JMSProducer.setDeliveryDelay( long deliveryDelay) </li> </ul>|**サポートされています**|
| メッセージの作成 |<ul> <li> JMSContext.createMessage() </li> <li> JMSContext.createBytesMessage() </li> <li> JMSContext.createMapMessage() </li> <li> JMSContext.createObjectMessage(Serializable object) </li> <li> JMSContext.createStreamMessage() </li> <li> JMSContext.createTextMessage() </li> <li> JMSContext.createTextMessage(String text) </li> </ul>| **サポートされています** |
| エンティティ間トランザクション |<ul> <li> Connection.createSession(true, Session.SESSION_TRANSACTED) </li> </ul> | **サポートされています** |
| 分散トランザクション || サポートなし |
