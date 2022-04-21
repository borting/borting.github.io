---
layout: post
title: "802.11 Indoor Positioning"
author: "Borting"
categories: journal
tags: [IEEE80211]
image: rocks.jpg
---

這篇紀錄一下 IEEE 802.11 (Wi-Fi) 的室內定位技術 (Indoor Positioning).

# Revolution

發展順序:
1. RSSI-based positioning
2. 802.11mc Fine Timing Measurement (FTM): 借用 802.11v Timing Measurement 引入的 Time of Flight 技術實現
3. 802.11az Next Generation Positioning (NGP): 強化 FTM 的隱蔽性與準確度

# Introduction

Allow a recipient STA to measure the offset of its clock relative to a clock in the sending STA, thus the recipient STA can detect and compensate for any drift between the clocks.

Timing Measurement is part of the wireless network management (WNM) Service.
Hence, all its frames are action frames.

One higher-layer protocol for synchronizing a local clock time between STAs using this feature is specified in IEEE Std 802.1AS.

# Timing Measurement Procedure

流程參考 802.11mc Figure 11-33 Timing measurement procedure

## Start/Stop Measurement

STA send Timing measurement request (WMM action frame) with Trigger filed set to 1/0 to indicate start or stop timing measurement.
See 802.11mc Section 9.6.14.28 (frame format) and 6.3.69 (SME-MLME SAP)

## Measurement

需要 sending STA 發送兩筆 Timing Measurement frame 加 reciving STA 發送一筆 ACK 才能完成一次計算.
第二筆 Timing Measurement frame 會夾帶第一次 Timing Measurement frame 發送的時間 ToD\_action 和收到 receiving STA ACK 的抵達時間 ToA\_ack.

Receiving STA 在收到第二筆 Timing Measurement frame 後, 會將夾帶的 ToD\_action 與 ToA\_ack, 與之前紀錄的第一筆 Timing Measurement frame 的抵達持間 ToA\_action 與 ACK 發送時間 ToD\_ack 做下列計算
```
Clock offset at receiving STA relative to sending STA = [(ToA_action – ToD_action) – (ToA_ack – ToD_ack)]/2
```

前後二筆 Timing Measurement frame 是靠 frame 的 Dialog Token 和 Follow Up Dialog Token 關聯.
第二筆 Follow Up Dialog Token 的值為第一筆的 Dialog Token 值.

可進一步參考 IEEE 802.1AS 了解上層如何透過 Timing Measurement 做 time synchronization.

## Operation for Retransmission

If the Ack frame for a transmitted Timing Measurement frame is not received, the
sending STA may retransmit the frame.
The sending STA shall capture a new set of timestamps for the
retransmitted frame and its Ack frame.

On receiving a Timing Measurement frame with a Dialog Token for which timestamps have previously been
captured, the receiving STA shall discard previously captured timestamps and capture a new set of
timestamps.

# Frame format

若 STA 支援 Timing Measurement, 會將 Extended Capabilities element 的 bit 23 設成 1.
Extended Capabilities element 會帶在下列 frames 中:
* Beacon
* Association request/response
* Reassociation request/response
* Probe request/response
* Timing Advertisement frame (IEEE 802.11p)

# Related Sections in IEEE 802.11mc

## Description
* 4.3.18.18 Timing measurement
* 11.24.5 Timing measurement procedure

## Frame format
* 9.3.3.14 Action frame format
* 9.4.2.27 Extended Capabilities element
	* Extended Capabilities field, bit 23
* 9.6.14.28 Timing Measurement Request frame format
	* Category: 10
	* WNM Action: 25
	* Trigger:
		* 1: sending STA requests a timing measurement procedure at the receiving STA
		* 0: stop sending Timing Measurement frames
* 9.6.15.3 Timing Measurement frame format
	* Unprotected WNM Action
	* Figure 9-730—Timing Measurement Action field format
	* Category: 11
	* Action: 1
	* Dialog Token
		* 0: indicate that the Timing Measurement frame will not be followed by a subsequent follow-up Timing Measurement frame
		* !0
	* Follow Up Dialog Token
		* 0: No previous Timing Measurement frame
		* !0
	* TOD, TOA, Max TOD Error, and Max TOA Error
		* expressed in units of 10 ns
		* Max TOD/TOA Error field contains an upper bound for the error in the value specified in the TOD/TOA field
		* fields are reserved if Follow Up Dialog Token field is 0

## SME-MLME SAP
* 6.3.57 Timing measurement
* 6.3.69 Timing measurement request

## Misc
* P.1 Location via Time Difference of arrival
* P.2 Time Difference of departure accuracy test


# Implementation Concern

* 若收到的 Timing Measurement frame 的 Follow Up Dialog Token 與前一筆的 Dialog Token 不同時, 要 (1) drop 前一次計算? or (2) 保留多筆 dialog token 紀錄?

# Glossary

* SAP (service access point)
* SME (station management entity)
* MLME (MAC sublayer management entity)
* PLME (PHY sublayer management entity)

# Reference
