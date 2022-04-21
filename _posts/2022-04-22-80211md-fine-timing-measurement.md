---
layout: post
title: "802.11 Fine Timing Measurement for Positioning"
author: "Borting"
categories: journal
tags: [IEEE80211]
image: rocks.jpg
---

這篇介紹 802.11md 如何利用 time synchronization 與電磁波傳輸速度定值的特性, 達成室內定位技術 (Indoor Positioning).

# Revolution

發展順序:
1. RSSI-based positioning
2. 802.11mc Fine Timing Measurement (FTM): 借用 802.11v Timing Measurement 引入的 Time of Flight 技術實現
3. 802.11az Next Generation Positioning (NGP): 強化 FTM 的隱蔽性與準確度

# Introduction

# Related Sections in IEEE 802.11mc

## Description
* 4.3.18.19 Fine timing measurement
* 11.11.9.11 Fine Timing Measurement Range report
* 11.24.4 Location track procedures
* 11.24.6 Fine timing measurement (FTM) procedure

## Frame format
* 9.3.3.14 Action frame format
* 9.4.2.27 Extended Capabilities element, Extended Capabilities field
	* bit 70: Fine Timing Measurement Responder
	* bit 71: Fine Timing Measurement Initiator
* 9.4.2.21.19 Fine Timing Measurement Range request
* 9.4.2.22.18 Fine Timing Measurement Range report
* 9.4.2.168 Fine Timing Measurement Parameters element
* 9.6.8.32 Fine Timing Measurement Request frame format
* 9.6.8.33 Fine Timing Measurement frame format

## SME-MLME SAP
* 6.3.55 Location configuration request
* 6.3.56 Location track notification
* 6.3.58 Fine timing measurement (FTM)
* 6.3.70 Fine timing measurement request

## Misc
* P.1 Location via Time Difference of arrival
* P.3 Differential Distance Computation using Fine Timing Measurement frames


# Implementation Concern

# Glossary

* SAP (service access point)
* SME (station management entity)
* MLME (MAC sublayer management entity)
* PLME (PHY sublayer management entity)
* ToA (Time of Arrival)
* ToD (Time of Departure)
* FTM (Fine Timing Measurement)
* NGP (Next Generation Positioning)

# Reference
