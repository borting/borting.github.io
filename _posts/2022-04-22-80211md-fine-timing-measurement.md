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
1. RSSI-based positioning: 不精準, 訊號強度容易受到天線場型和環境等影響
2. 802.11mc Fine Timing Measurement (FTM): 借用 802.11v Timing Measurement 實做 Time of Flight
3. 802.11az Next Generation Positioning (NGP): 強化 FTM 的隱蔽性與準確度

# Introduction to Fine Timing Measurement

A STA can use FTM to accurately measure the round trip time (RTT) between it and another STA to determine its distance from the STA.
By performing FTM procedure with multiple STAs, a STA can know its location in the environment.

An FTM session is an instance of a FTM procedure between an initiating STA (iSTA) and a responding STA (rSTA).
A session is composed of:
* negotiation
* measurement exchange
* termination

A STA might have multiple concurrent FTM sessions, of which corresponding responding STA may outside of the current BSS/ESS.


多工:
* An initiating STA might have multiple ongoing FTM sessions on the same or different channels with different responding STAs, while being associated with an AP for the exchange of data or signaling.
(同時定位+傳輸資料)
* A responding STA (e.g. AP) might be required to establish overlapping FTM sessions with a large number of initiating STAs 

為了滿足多工:
* During the negotiation phase the initiating STA initially requests a preferred periodic time window allocation
(See 802.11mc Figure 11-34—Concurrent FTM sessions.)
* During each burst instance
	* the initiating STA indicates its availability by transmitting a Fine Timing Measurement Request frame, and
	* the responding STA transmits one or more Fine Timing Measurement frames as negotiated.


During the measurement procedure:
* iSTA sends Fine Timing Measurement Request frames w/ various elements
* rSTA sends Fine Timing Measurement frames w/ various elements
Both frames are public action frames.


## Negotiation Phase

### Step 1
An initiating STA shall transmit a Fine Timing Measurement Request frame.
This frame called the initial Fine Timing Measurement Request frame, of which
* Trigger field set to 1
* a set of scheduling parameters in a Fine Timing Measurement Parameters element (Section 9.4.2.168 Fine Timing Measurement Parameters element)

### Step 2
The responding STA should transmit a Fine Timing Measurement frame within 10 ms in response to the initial Fine Timing Measurement Request frame.
The first Fine Timing Measurement frame in the FTM session is called the initial Fine Timing Measurement frame, of which
* Fine Timing Measurement Parameters element
	* Format and Bandwidth field should be same as that of the initiating STA if supported or narrower bandwidth.
	* ASAP field
		* A responding STA that is an AP shall support and select non-ASAP operation, and so does the initiating STA shall support non-ASAP operation
		* A responding STA that is not an AP shall support and select ASAP operation, and so does the initiating STA shall support ASAP operation

## Measurement Exchange


# Fine Timing Measurement Range Report

# Related Sections in IEEE 802.11mc

## Description
* 4.3.18.19 Fine timing measurement
* 11.11.2 Measurement on operating and nonoperating channels
* 11.11.9.11 Fine Timing Measurement Range report
* 11.24.6 Fine timing measurement (FTM) procedure

## Frame format
* 9.3.3.14 Action frame format
* 9.6.8.32 Fine Timing Measurement Request frame format
	* Public Action frame
	* Category: 4
	* Public Action field: 32
	* Trigger: 1 for start, 0 for stop
	* LCI Measurement Request element (Opt.)
	* Location Civic Measurement Request element (Opt.)
	* Fine Timing Measurement Parameter element (Opt.)
* 9.6.8.33 Fine Timing Measurement frame format
	* Category: 4
	* Public Action field: 33
	* Dialog Token
	* Follow Up Dialog Token
	* TOD/TOA
	* TOD/TOA Error
	* LCI Measurement Report element (Opt.)
	* Location Civic Measurement Report element (Opt.)
	* Fine Timing Measurement Parameter element (Opt.)
	* FTM Synchronization Information (Opt.)

## Element Format
* 9.4.2.45 RM Enabled Capabilities element
	* RM Enabled Capabilities:
		* bit 34: FTM Range Report Capability Enabled
* 9.4.2.27 Extended Capabilities element
	* Extended Capabilities field
		* bit 70: Fine Timing Measurement Responder: 1, if supports FTM as a responder
		* bit 71: Fine Timing Measurement Initiator: 1, if supports FTM as a initiator
* 9.4.2.21 Measurement Request element
	* Element ID: 38
	* 9.4.2.21.10 LCI request (Location configuration information request)
		* Measurement Type: 8
	* 9.4.2.21.14 Location Civic request
		* Measurement Type: 11
	* 9.4.2.21.19 Fine Timing Measurement Range request
		* Measurement Type: 16
* 9.4.2.22 Measurement Report element
	* Element ID: 39
	* 9.4.2.22.10 LCI report (Location configuration information report)
		* Measurement Type: 8
	* 9.4.2.22.13 Location Civic report
		* Measurement Type: 11
	* 9.4.2.22.18 Fine Timing Measurement Range report
		* Measurement Type: 16
* 9.4.2.168 Fine Timing Measurement Parameters element
	* Element ID: 206
	* This element is included in the initial Fine Timing Measurement Request frame and the initial Fine Timing Measurement frame.
	* Number of Bursts Exponent field:
		* Will execute 2 ^ (Number of Bursts Exponent) burst instance
	* Burst Duration field
		*the duration of a burst instance
	* Min Delta FTM field
		* minimum time between consecutive Fine Timing Measurement frames, in units of 100 μs
	* Partial TSF Timer field
	* Partial TSF Timer No Preference field
	* ASAP Capable field: the responding STA is capable of sending a Fine Timing Measurement frame as soon as possible
	* ASAP field: the initiating STA’s request to start the first burst instance of the FTM session as soon as possible
	* FTMs per Burst field
		* how many successfully transmitted Fine Timing Measurement frames per burst instance
	* Format And Bandwidth field
		* See Table 9-258
	* Burst Period field
		* the interval between two consecutive burst instances, in units of 100 ms
* 9.4.2.173 FTM Synchronization Information element
	* Element ID: 255
	* Element ID Extension: 9

## SME-MLME SAP
* 6.3.58 Fine timing measurement (FTM)
* 6.3.70 Fine timing measurement request

## Misc
* P.1 Location via Time Difference of arrival
* P.3 Differential Distance Computation using Fine Timing Measurement frames

## Related
* 11.24.4 Location track procedures
* 6.3.55 Location configuration request
* 6.3.56 Location track notification

# Implementation Concern

# Glossary

* SAP (service access point)
* SME (station management entity)
* MLME (MAC sublayer management entity)
* PLME (PHY sublayer management entity)
* WNM (Wireless Network Management)
* ToA (Time of Arrival)
* ToD (Time of Departure)
* FTM (Fine Timing Measurement)
* NGP (Next Generation Positioning)
* LCI (Location Configuration Information)
* LO (Local Oscillator)
* TU (Time Unit): 1024 microseconds (i.e. 10^(-6) second)

Non-official
* iSTA (initiating STA)
* rSTA (responding STA)

# Reference
