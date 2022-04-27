---
layout: post
title: "802.11az Next Generation Positioning"
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

A STA can use FTM to accurately measure its range/direction relative to another STA using Time Of Flight (TOF) time difference of arrival and phase measurement.

An FTM session is an instance of a FTM procedure between an initiating STA (ISTA) and a responding STA (RSTA).
A session is composed of:
* negotiation
* measurement exchange
* termination
And how the session works is negotiated and determined by
* Fine Timing Measurement Parameters element
* Ranging Parameters element


The FTM procedure provides 4 mechanisms for measurement exchange:
* EDCA based ranging measurement exchange
	* location estimates are based on ToD and ToA of the exchanged FTM frames and their corresponding Ack
* Trigger based (TB) ranging measurement exchange
	* location estimates are based on the execution of the trigger based measurement exchange
	* allows for the execution of the measurement exchange between a responding STA (RSTA) and multiple initiating STAs (ISTAs) at the same time
* Non-Trigger based (non-TB) ranging measurement exchange
	* location estimates are based on the execution of the non-TB measurement exchange
* Passive triggered based (TB) ranging measurement exchange
	* determine its location based on periodic measurement reports from other STAs that execute the passive TB ranging measurement exchange amongst themselves

Optionally enable security parameters enabling mechanisms to ensure that the measurement exchange is executed with the intended peer:
* EDCA based exchange of Fine Timing Measurement frames, only over EDMG (802.11ay)
* Trigger based (TB) measurement
* Non-Trigger based (non-TB) measurement


# Measurement Procedure

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


Types of frames involved in the ranging procedure:
* Initial Fine Timing Measurement Request frame
	* Trigger field: 1
	* Fine Timing Measurement Parameters element which describes the initiating STA’s availability
* Fine Timing Measurement Request frame
* Initial Fine Timing Measurement frame
	* Respond in 10ms (10^-3 sec)
	* Status Indication field
	* Fine Timing Measurement Parameters element
* Fine Timing Measurement frame
* Ack

# EDCA-based Ranging Measurement


## Negotiation Phase

### Step 1
An initiating STA shall transmit a Fine Timing Measurement Request frame.
This frame called the initial Fine Timing Measurement Request frame, of which
* Trigger field set to 1
* a set of scheduling parameters in a Fine Timing Measurement Parameters element (Section 9.4.2.168 Fine Timing Measurement Parameters element)
* Format and Bandwidth field
	* ISTA shall indicate an EDCA based HE format only if
		* STAs are operating in the 6 GHz band
		* at least one of the STAs does not support TB or does not support non-TB ranging
	* Otherwise, a WIFI6 ISTA shall not indicate an EDCA based HE format

### Step 2
The responding STA should transmit a Fine Timing Measurement frame within 10 ms in response to the initial Fine Timing Measurement Request frame.
The first Fine Timing Measurement frame in the FTM session is called the initial Fine Timing Measurement frame, of which
* Fine Timing Measurement Parameters element
	* Format and Bandwidth field should be same as that of the initiating STA if supported or narrower bandwidth.
	* ASAP capable field
	* ASAP field
		* A responding STA that is an AP
			* shall support and select non-ASAP operation, if the initiating STA requests non-ASAP operation
			* can select ASAP/non-ASAP, if the initiating STA requests ASAP operation. And the initiating STA must select non-ASAP, if the responding STA responds to do so.
			* If a responding STA is ASAP capable, the responding STA should select ASAP as that requested by the initiating STA.
		* A responding STA that is a non-AP
			* shall support and select ASAP operation, if the initiating STA request ASAP operation
			* can select ASAP/non-ASAP, if the initiating STA requests non-ASAP operation. And the initiating STA must select ASAP, if the responder STA responds to do so.
	* FTMs per Burst
		* be the same as the one requested by the initiating STA if the requested value of the Burst Duration field is 15 (no preference) 
	* Burst Period: responding STA’s selection shall be greater than or equal the responding STA’s selection of Burst Duration
		

## Measurement Exchange Phase

A burst instance is a period in which Fine Timing Measurement frames are sent.
The timing is defined by:
* Partial TSF Time
* Burst Duration
* Burst Period

The first burst instance shall start at the value indicated by the value of the Partial TSF Timer field in the initial Fine Timing Measurement frame, regardless of the ASAP field’s value.
If ASAP is set to 1 by the responding STA, the Partial TSF Timer field value shall be set to a value less than 10 ms from the reception

### Step1

initiating STA shall transmit a Fine Timing Measurement Request frame
* Trigger 1
* w/o Measurement Request element
* w/o Fine Timing Measurement Parameters element

### Step2

responding STA sends an ack, then starts to sends FTM framesa

The first Fine Timing Measurement frame and its retransmissions in the burst instance should include an FTM Synchronization Information element.
	* The TSF Sync Info field might be used by the initiating STA to synchronize its TSF with the responding STA 

Subsequent FTM frames within the burst instance shall not include a Fine Timing Measurement Parameters element and shall not include an FTM Synchronization Information field

If a Fine Timing Measurement frame is sent outside a burst instance, it might not be acknowledged.

Fine Timing Measurement frames shall not be transmitted in DSSS (802.11-1997), HR/DSSS (802.11b), [HT Duplicate (MCS 32)](https://www.cwnp.com/ht-duplicate-mcs-32-and-non-ht-duplicate/) format, or HT-greenfield format

A responding STA that transmits a Fine Timing Measurement frame with the ASAP field set to 0
* Set Partial TSF Timer field to an offset value D TSF from the partial value of the responding STA’s TSF timer at the time of the transmission of the Ack to the last Fine Timing Measurement Request frame


If (1) not received Ack for initial Fine Timing Measurement frame, (2) nor received Fine Timing Measurement Request frame,
the responding STA shall not terminate the FTM session before the time indicated by the Partial TSF timer plus the Burst Duration

A responding STA set the Dialog Token field to 0 in the last Fine Timing Measurement frame and its FTM retransmissions

### Step3

The initiating STA may perform FTM on the last Fine Timing Measurement frame in a burst instance. (最後一個可以不計算 FTM)
#### Calculation
Round Trip Time (RTT):
```
RTT = [(t4' – t1') – (t3 – t2)]
```

SME at the initiating STA may estimate the offset of the local clock relative to that at the responding STA
```
clock offset = [(t2 - t1') - (t4' - t3)]/2
```

#### FTM retransmission

If the Ack frame for a transmitted Fine Timing Measurement frame is not received, the responding STA shall not retry the frame.

The responding STA shall send a Fine Timing Measurement frame with the same Action frame body as the Fine Timing Measurement frame for which the Ack was not received, except:
* updating the Dialog Token if it was nonzero.
* updating Sequence Number in the MAC header


#### FTM Modification

如果 iSTA sent a Fine Timing Measurement Request frame with
* Trigger field set to 1
* including a new Fine Timing Measurement Parameters element
This means current FTM session is terminated and shall use new parameters

## Termination Phase

* Case 1: ended after the last burst instance
* Case 2: responding STA sends a Fine Timing Measurement frame with the Dialog Token field set to 0
* Case 3: initiating STA sends a Fine Timing Measurement Request frame with the Trigger field set to 0 (not include Measurement Request element nor Fine Timing Measurement Parameters element)
* Case 4: initiating STA sends a Fine Timing Measurement Request frame with the Trigger field set to 1 and includes a new Fine Timing Measurement Parameters element


# LCI and Location Civic retrieval

???

# Fine Timing Measurement Range Report


???

# Preassociation Security Negotiation

PASN authentication allows association by establishing a PTKSA using authentication frames.
This enables the exchange of protected frames without association
For example, two unassociated peers can establish a secure FTM session and perform the corresponding secure measurement exchange between each other.


PASN authentication is used 
* in an RSN for an infrastructure BSS when it is based on a PMKSA established by another RSN authentication protocol
* Otherwise, it does not guarantee mutual authentication, and can be used as a non-RSN protocol in an infrastructure BSS


A secure fine timing measurement session is established when an ISTA and an RSTA establish a PTKSA and use it to exchange proteced action frames in the session.
The proected action frames includes:
* IFTMR
* Protected Fine Timing Measurement Request Action frame
* Protected Fine Timing Measurement Action frame
* IFTM frame in the Protected Fine Timing Frame Action format

IFTMR is not protected ???

A secure fine timing measurement session can only established with the following measurement exchange:
* a TB ranging measurement exchange
* a non-TB ranging measurement exchange
* an EDCA based ranging measurement exchange with a Format And Bandwidth field indicating DMG or EDMG format 

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
	* Value
		* If Status Indication field is 3, indicates not send new request for Value seconds.
	* Number of Bursts Exponent field:
		* Will execute 2 ^ (Number of Bursts Exponent) burst instance
		* The responding STA’s selection should be 0 if the initiating STA requested it to be 0
	* Burst Duration field
		* the duration of a burst instance
		* Initial FTM request
			* 15: no preference
		* responding STA’s selection
			* less than or equal to the one requested by the initiating STA, if the requested FTMs per Burst field value is set to 0 (no preference), and subjeced to the following min and max
			* (min) if the Number of Bursts Exponent field is set to 0 and the ASAP field is set to 1: (See Figure 11-37)
				BD1 = ((N_FTMPB * (K + 1)) – 1) * T_MDFTM + T_FTM + aSIFSTime + T_Ack
			* (max) otherwise
				BD >= BD1 + T_FTMR + aSIFSTime + T_Ack + T_ACCESS_FTM
	* Min Delta FTM field
		* minimum time between consecutive Fine Timing Measurement frames, in units of 100 μs
		* responding STA’s selection should greater than or equal to that of the initiating STA
	* Partial TSF Timer field
	* Partial TSF Timer No Preference field
	* ASAP Capable field:
		* the responding STA is capable of sending a Fine Timing Measurement frame as soon as possible
		* reserved in the initial Fine Timing Measurement Request frame
	* ASAP field: the initiating STA’s request to start the first burst instance of the FTM session as soon as possible
	* FTMs per Burst field
		* how many successfully transmitted Fine Timing Measurement frames per burst instance
		* 0 indicates no preference by the initiating STA
	* Format And Bandwidth field
		* See Table 9-258
	* Burst Period field
		* The interval from the beginning of one burst instance to the beginning of the following burst instance, in units of 100 ms
		* 0 indicates no preference by the initiating STA
		* reserved when the Number of Bursts Exponent field is set to 0
* 9.4.2.173 FTM Synchronization Information element
	* Element ID: 255
	* Element ID Extension: 9
	* TSF Sync Info field: the 4 least significant bytes of the value of TSF, initiating STA might uses this to sync TSF w/ responding STA to determine the start of next burst instanceExponent

## SME-MLME SAP
* 6.3.56 Fine timing measurement (FTM)

## Misc
* P.1 Location via Time Difference of arrival
* P.3 Differential Distance Computation using Fine Timing Measurement frames

## Related
* 11.24.4 Location track procedures
* 6.3.55 Location configuration request
* 6.3.56 Location track notification






# Related Sections in IEEE 802.11az

## Description
* 11.21.6.4.2 EDCA based ranging measurement exchange
* 11.21.6.4.3 TB ranging measurement exchange
* 11.21.6.4.4 Non-TB ranging measurement exchange
* 11.21.6.4.8 Passive TB ranging measurement exchange

## Element Format 
* 9.4.2.167 Fine Timing Measurement Parameters element
	* Element ID: 206
* 9.4.2.296 ISTA Availability Window
	* Element ID: 255
	* Element ID Extension: 98
* 9.4.2.297 ISTA Availability Window
	* Element ID: 255
	* Element ID Extension: 99
* 9.4.2.298 Ranging Parameters element
	* Element ID: 255
	* Element ID Extension: 101
* 9.4.2.299 Secure LTF Parameters
	* Element ID: 255
	* Element ID Extension: 94
* 9.4.2.300 Direction Measurement Results
	* Element ID: 255
	* Element ID Extension: 102
* 9.4.2.301 Multiple Best AWV ID
	* Element ID: 255
	* Element ID Extension: 104
* 9.4.2.302 Multiple AOD Feedback
	* Element ID: 255
	* Element ID Extension: 103
* 9.4.2.303 PASN Parameters
	* Element ID: 255
	* Element ID Extension: 100
* 9.4.2.304 ISTA Passive TB Ranging Measurement Report
	* Element ID: 255
	* Element ID Extension: 95
* 9.4.2.305 RSTA Passive TB Ranging Measurement Report
	* Element ID: 255
	* Element ID Extension: 96
* 9.4.2.306 Passive TB Ranging LCI Table
	* Element ID: 255
	* Element ID Extension: 97
* 9.4.2.307 LOS Likelihood
	* Element ID: 255
	* Element ID Extension: 105


## Frame format

* 9.6.34 Protected Fine Timing Action Frame
















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
* PASN (Preassociation Security Negotiation)
* ISTA (initiating STA)
* RSTA (responding STA)
* PSTA (passive STA)
* I2R (ISTA to RSTA)
* R2I (RSTA to ISTA)
* FPBT (first path beamforming training)
* IFTM (initial fine timing measurement)
* FTMR (fine timing measurement request)
* IFTMR (initial fine timing measurement request)
* LMR (location measurement report)
* PSTOA (phase shift time of arrival)
* RSID (ranging session Identifier)

# Reference
