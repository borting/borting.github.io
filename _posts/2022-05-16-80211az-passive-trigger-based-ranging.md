---
layout: post
title: "802.11az NGP: Passive Trigger-based Ranging"
author: "Borting"
categories: journal
tags: [IEEE80211]
image: rocks.jpg
---

Passive triggered based (TB) ranging measurement exchange is based on TB ranging.
A Passive STA (PSTA) listens ranging exchanges between an RSTA and a set of ISTAs.
PSTA uses the ranging results, differential distances between RSTA and ISTAs, together with knowledge of the RSTA and ISTA locations to estimates its own location.

A PSTA acquires ranging availability window information for passive TB ranging from an AP's beacon.

Security mechanisms, protected management frames and secure LTF, do not apply to passive TB Ranging.

Difference with TB ranging
* When phase shift feedback is negotiated for passive TB ranging, both the RSTA and the ISTA measures and reports PSTOAs, in addition to measuring and reporting TOAs.
* Number of spatial streams (NSTS) for passive TB ranging is limited to 4
* RSTA uses the Ranging Trigger frame of subtype passive TB ranging for its sounding trigger frames, which is transmitted to one ISTA at a time.
* ISTAs use HE Ranging NDPs for its I2R NDPs
* RSTA sends the Primary and Secondary RSTA Broadcast Passive TB Ranging Measurement Report frames at the end of the measurement exchange
* ISTAs use Passive TB Ranging Measurement Report frame instead of reporting in I2R LMR frame.
Transmitting Passive TB Ranging Measurement Report frame is mandatory in passive TB Ranging.

The passive TB ranging is scheduled by the RSTA in an availability window used for passive location
* RSTA includes an RSTA Availability Window element (with Broadcast Format subfield == 1) in its Beacon frame to signal availability windows for Passive TB.
This enables PSTAs to listen to the passive TB ranging exchanges that are occurring during the availability windows.

The passive TB ranging protocol also allows the same ISTAs to measure time of arrivals of each other’s ranging NDPs

An RSTA can temporarily take on the role of being an ISTA, such that it can participate in another RSTA's passive TB ranging operation and perform passive TB ranging exchanges as an ISTA.


# Measurement Procedure

Passive TB ranging negotiation follows the rules and procedures for the TB ranging measurement negotiation.

When an RSTA has set the 'Passive TB Ranging Responder Measurement Support' field to 1 in the Extended Capabilities element, an ISTA may set the Passive TB Ranging field in the TB specific subelement in an IFTMR frame.

## Negotiation

ISTA transmits an IFTMR frame to an RSTA, which has set the 'Passive TB Ranging Responder Measurement Support' field to 1 in the Extended Capabilities element":
* Ranging parameters
	* I2R LMR Feedback is reserved since the transmission of the ISTA Passive TB Ranging Measurement Report frame is mandatory
	* Format and Bandwidth
	* Max R2I Repetition
		* must > 0, if Secure LTF Required field = 1
	* Max I2R Repetition
		* must > 0, if Secure LTF Required field = 1
	* Max R2I STS ≤ 80 MHz
	* Max R2I STS > 80 MHz
	* Max I2R STS ≤ 80 MHz
	* Max I2R STS > 80 MHz
	* Max R2I LTF Total
	* Max I2R LTF Total
	* I2R AOA Requested
	* R2I AOA Requested
	* I2R LMR feedback
	* R2I TOA Type
	* I2R TOA Type
		* 1: if I2R LMR Feedback is set to 1
	* Secure LTF (shall not be included)
	* TB Specific subelement
		* Passive TB Ranging field to 1
		* ISTA Availability Window element
			* Availability Bitmap
			* Count: periodicity in units of 10 TUs, shall be a multiple of the Beacon Interval of the RSTA in units of 10 TUs (10 * 1024 microseconds)
	* Secure LTF subelement (optional)
* LCI report must be included

RSTA responds with an IFTM:
* Ranging parameters
	* I2R LMR Feedback
		* if I2R LMR Feedback in IFTMR is 0 and RSTA's I2R LMR Feedback Policy is 1
			* set to 0
		* if I2R LMR Feedback in IFTMR is 0 and RSTA's I2R LMR Feedback Policy is 0 
			* set to 0
			* set to 1 ==> ISTA may either proceed with measurement exchange or terminate the FTM session
		* 1: if I2R LMR Feedback in IFTMR is 1
			* set to 1 or 0
	* I2R AOA Requested
	* R2I AOA Requested
	* I2R LMR feedback
	* R2I TOA Type
	* I2R TOA Type
		* 1: if ISTA's I2R LMR Feedback is set to 1
		* 0
	* TB Specific subelement
		* Passive TB Ranging field to 1
		* RSTA Availability Window element
			* Availability Window Information, if Session Indication = 1
				* contain only one
				* Availability Window Broadcast Format subfield: 0
				* represents the availability window assigned by the RSTA to the ISTA
			* Availability Window Information (optionally), if Session Indication = 2 or 3
				* contain one or more Availability Window Information
				* Availability Window Broadcast Format subfield: 0
				* represents an availability window that the RSTA can assign to that ISTA if requested by the ISTA in future
			* passive TB ranging availability window bit = 1
		* AID/RSID
		* Max Session Exp
			* the time before which a new measurement exchange should be initiated and completed
			* Max Session Expiry = 2 ^ (Max Session Exp + 8), unit: ms
			* Larger than Periodicity field in RSTA Availability Window element


RSTA shall reject a request for TB ranging from an ISTA if the RSTA cannot assign the ISTA to an availability window that overlaps with a 10 TU interval in which the ISTA is available

### Availability Window

Be used in TB ranging only for allocting time slots for measurement exchange.
* ISTA: IFTMR --> Ranging Parameter element's Ranging subelement --> TB Specific subelemet's Availibility Window field --> ISTA Availibility Wondow element
* RSTA: IFTM  --> Ranging Parameter element's Ranging subelement --> TB Specific subelemet's Availibility Window field --> RSTA Availibility Wondow element

ISTA Availability Window element
* Count subfield:
	* indicates the size in bits of the Availability Bitmap subfield
	* shall be a multiple of the Beacon Interval of the RSTA in units of 10 TUs 
	  By default the beacon interval is 100 TUs, hence the value in Count field will be a multiple of 10.


RSTA Availability Window element
* If Status Indication == 1, only one Availability Window Information
* If Status Indication == 2 or 3, may have more than one Availablity Windown Information, each represents an availability window that the RSTA can assign to that ISTA if requested by the ISTA in future.

## Measurement Exchange

Within availability window, RSTA and ISTAs shall not transmit or trigger transmission of any Data frames, only perform ranging-related activities
* Polling
* Measurement Sounding
* Measurement Reporting
* signaling of modification of availability window parameters
* TB ranging session termination

Each availability window consists of one or more triplets of sequential phases
* Polling phase
* Measurement Sounding phase
* Measurement Reporting phase

RSTA shall use an AID or Ranging Session ID (RSID) to identify an associated or unassociated ISTA respectively.

Difference with TB ranging
* Sounding phase
	* RSTA shall transmit the Passive Sounding Ranging Trigger frame instead of the the Sounding Ranging Trigger frame.
	* ISTA shall respond with an HE Ranging NDP instead of an HE TB Ranging NDP.
* Reporting phase
	* RSTA shall broadcast two frames, the Primary and Secondary RSTA Broadcast Passive TB Ranging Measurement Report frames containing measurement data and related information.

RSTA should poll all ISTAs assigned to that availability window
* typically done in a single polling/sounding/reporting triplet.
* multiple polls if the available bandwidth is insufficient to allow for the polling of all ISTAs assigned to the availability window
	* multiple polling/sounding/reporting triplets within a single TxOP
	* multiple polling/sounding/reporting triplets in separate TxOPs

### Polling Phase

RSTA sends a Poll Ranging Trigger frame and allocates each RU in the TF Ranging poll to only one ISTA.
Only ISTA addressed by a User Info field in a TF Ranging Poll frame can response to the TF Ranging Poll.
If ISTA decide to participate in measurements in this availability window, the ISTA responds with a CTS-to-self in an S-MPDU within an HE TB PPDU in its designated RU allocation.

RSTA shall set RA field to the broadcast address and the More TF subfield in the Common Info field to
* 0: if there are no additional polling/sounding/reporting triplets in the same availability window
* 1: indicate the extra polling/sounding/reporting triplets in the following TFs in the same availability window
	* TF Ranging Poll frame
	* TFs in Measurement Sounding phase 
	* TFs in Measurement Reporting phase

ISTAs that not have been addressed by a TF ranging poll w/ More TF = 0 shall enter doze state.

To aid in synchronizing the TSF time at the ISTAs, RSTA maintains a trigger poll counter
* The counter is increased by one before transmitting a TF Ranging Poll
* The counter modulo 8 is set to the Token subfield of the trigger Dependent Common Info subfield in TF ranging poll
* The same value shall set the Token subfield in the STA Info field with the AID11 subfield equal to 2044 in the following Ranging NDPA in Sounding Phase
The Partial TSF subfield should be equals to the RSTA’s TSF[21:6] at the time of transmission of the preceeding TF Ranging Poll.
(Note: The 'Token subfield' in the STA Info field with the AID11 subfield equal to 2044 is **different** from the 'Sounding Dialog Token' of Ranging NDPA)

If delayed I2R LMR is negotiated and TOA measurement for the previous availability window is not ready, ISTA shall not respond to the TF Ranging Poll until the I2R LMR is ready.

### Sounding Phase

RSTA sends a Passive Sounding Ranging TF (includes a single User Info field) to one ISTA once at a time.
Upon receiving Passive Sounding Ranging TF, ISTA respones an I2R NDP (in HE Ranging NDP).
After RSTA has sent Passive Sounding Ranging TF to each ISTA, RSTA sends a Ranging NDPA frame solliciting an R2I NDP.

Bandwidth selection
* less than or equal to the Max Bandwidth RSTA indicated to the ISTA
* RSTA shall set the TXVECTOR parameter CH\_BANDWIDTH be the same value as the BW subfield of the Common Info field in the Passive Sounding Ranging Trigger frame.
* ISTA responses an HE Ranging NDP (I2R NDP) shall set the TXVECTOR parameter CH\_BANDWIDTH to be the same value as the BW subfield of the Common Info field in the Passive Sounding Ranging Trigger frame.

ISTA may measure and report either the TOAs, or both the TOAs and the PSTOAs, when it receives the HE Ranging NDPs transmitted by the other ISTAs participating in the passive TB ranging exchange.
By reporting the timestamps for when it received the other ISTAs NDP transmissions, the quality of the location estimate for a PSTA listening in to the passive TB ranging exchanges can be improved.

If phase shift feedback is negotiated:
* RSTA shall measure TOA and PSTOA on the I2R NPD it receives from the ISTA
* ISTA measure
	* (shall) TOA and PSTOA on the R2R NPD it receives from the RSTA
	* (may) TOA and PSTOA on the I2R NPD it receives from other ISTA

In Ranging NDP Announcement
* STA Info filed w/ AID is less than 2008
	* it identifies a STA that is intended to receive this frame and assigns the parameters
	* LTF Offset filed 
		* For secure LTF: indicates the number of HE-LTF to skip when processing the following NDP
		* 0, otherwise
	* R2I N\_STS
		* if the TXVECTOR parameter CH_BANDWIDTH of this frame is less than or equal to 80 MHz, R2I N_STS subfield value shall not exceed the RSTA assigned R2I STS ≤ 80 MHz for the corresponding ISTA
		* if the TXVECTOR parameter CH_BANDWIDTH of this frame is larger than 80 MHz, R2I N_STS subfield value shall not exceed the RSTA assigned R2I STS > 80 MHz for the corresponding ISTA
	* I2R N\_STS
	* R2I Rep: the number of HE-LTF repetitions of the corresponding HE Ranging NDP minus 1
		* set to a value not to exceed the RSTA Assigned R2I Rep, for the corresponding ISTA
		* The combination of the values of the R2I N_STS and the R2I Rep shall not lead to a total number of LTF that exceeds the RSTA Assigned R2I LTF Total for each corresponding ISTA
	* I2R Rep: the number of HE-LTF repetitions of the corresponding HE Ranging NDP minus 1
	* Disambiguation: 1
* STA Info filed w/ AID is 2044 (TB only)
	* carry the Partial TSF of RSTA, TSF[21:6]
	* The Partial TSF subfiled is set to the value of the TF ranging poll of this Available Window
	* The Token subfiled is set to the value of the TF ranging poll of this Available Window
	* For timer sync ???

Usage of Partial TSF Timer subfield in Ranging NDPA
* For ISTA, especially a unassociated one (?), to synchronize its timer w/ RSTA and determine the start of a subsequent TB ranging availability window
* ISTA need to keep track of the difference between its local TSF[63:22] and the RSTA’s TSF[63:22] when updating the TSF[21:6]
	* if ISTA's TSF[21:6] at the reception of a TF Ranging Poll is larger than the received Partial TSF and the absolute difference is more than 2^15, increase the RSTA’s tracked TSF[53:32] by 1
	* if ISTA's TSF[21:6] at the reception of a TF Ranging Poll is less than the received Partial TSF and the absolute difference is more than 2^15, decrease the RSTA’s tracked TSF[53:32] by 1
	* For the definition of absolute time difference, please refer this [article talking about how NTP works](https://sookocheff.com/post/time/how-does-ntp-work/).

If RSTA's PHY indicate IntegrityCheckError, when receiving I2R NDP (HE TB Ranging NDP)
* RSTA shall set the Invalid Measurement field in the R2I LMR frame carrying the TOA to 1

If ISTA's PHY indicate IntegrityCheckError, when receiving R2I NDP (HE Ranging NDP)
* ISTA shall set the Invalid Measurement field in the I2R LMR frame carrying the TOA to 1 (if I2R LMR feedback is negotiated)

### Reporting Phase

Step 1
RSTA sends R2I LMR reports to ISTAs.
If phase shift feedback is negotiated, RSTA shall report its measured PSTOA in the R2I LMR frame.

Step 2
RSTA sends a Report Ranging Trigger frame to one or more ISTAs that sent an HE Ranging NDP in the preceding passive TB ranging measurement sounding phase.

Step 3
ISTA addressed by the Report Ranging Trigger frame shall transmit an ISTA Passive TB Ranging Measurement Report as a public Action No Ack frame.
* ISTA Passive TB Ranging Measurement Report element
	* Sounding Dialog Token Number field: the value of the Sounding Dialog Token Number subfield in the Ranging NDP Announcement frame
	* CFO of the ISTA with respect to the RSTA
	* More subfield
		* 1: if it has more timestamps ready to report but does not have space in its allocated resources by the RSTA for ISTA Passive TB Ranging Measurement Report frame.
	* Timestamp Measurement Report subfield
		* Type 00: TOD (must included)
			* AID12/RSID12 of ISTA
			* Sounding Dialog Token Number identifying the measurement sounding phase
			* TOD of I2R NDP
			* Timestamp field has 48-bit long, unit 1 ps
		* Type 01: TOA
			* AID12/RSID12
				* 0: R2I NDP from RSTA
				* !0: TOA timestamps for the I2R NDPs received from other ISTAs participating in the passive TB ranging
			* Sounding Dialog Token Number
				* Sounding phase of this ISTA
				* Sounding phase of the I2R NDPs received from other ISTAs
			* TOA of R2I NDP
			* Timestamp field has 32-bit long, unit 16 ps
		* Type 10: PSTOA
			* PS-TOA timestamp of the R2I NDP that the ISTA received from the RSTA
			* PS-TOAs for the I2R NDPs received from other ISTAs participating in the passive TB ranging
			* Timestamp field has 32-bit long, unit 16 ps

Step 4
RSTA sends the Primary RSTA Broadcast Passive TB Ranging Measurement Report frames
* Passive TB Ranging LCI Table Counter
	* a reference to the version of the latest Passive TB Ranging LCI Table element transmitted by the RSTA
	* Initial value: 0
	* Incremented by 1, if a Passive TB Ranging LCI Table element is included, which has different content as compared to the last transmitted Passive TB Ranging LCI Table element
	* Otherwise, same as previous one
* Passive TB Ranging LCI Table Countdown Info
	* New LCI Table
		* 0: if the current LCI table and LCI table to be transmitted at the end of the countdown are the same
	* Passive TB Ranging LCI Table Countdown
		* an index pointing to the next passive TB ranging availability window where the Passive LCI Table element will be contained
		* 0 if the Passive TB Ranging LCI Table element is contained
* RSTA Passive TB Ranging Measurement Report
	* Sounding Dialog Token Number
		* the Sounding Dialog Token Number subfield in the Ranging NDP Announcement frame corresponding to the sounding phase in which the reported RSTA timestamps were measured
	* N Timestamp Measurement Reports
	* Timestamp Measurement Report subfield
		* Type 00: TOD (must included)
			* AID12/RSID12 of ISTA
			* Sounding Dialog Token Number identifying the measurement sounding phase
			* TOD of R2I NDP
			* Timestamp field has 48-bit long
		* Type 01: TOA
			* AID12/RSID12 od an ISTA
			* Sounding Dialog Token Number
			* TOA of I2R NDP
			* Timestamp field has 32-bit long
		* Type 10: PSTOA
			* PS-TOA timestamp of the I2R NDP that the RSTA received from a ISTA
* Passive TB Ranging LCI Table element
	* Contain RSTA/ISTA location info
	* RSTA LCI Report
		* if the RSTA has dot11PassiveTBRangingAODImplemented = 1, contain the Antenna Placement and Calibration subelement
	* ISTA LCI Reports Entries
		* if the ISTA has dot11PassiveTBRangingAoDImplemented = 1, contain the Antenna Placement and Calibration subelement

Step 5
RSTA sends the Secondary RSTA Broadcast Passive TB Ranging Measurement Report frames
* ISTA Passive TB Ranging Measurement Reports
	* Timestamp Measurement Report subfield
		* Type 00: TOD (must included)
			* AID12/RSID12 of ISTA
			* Sounding Dialog Token Number identifying the measurement sounding phase
			* TOD of I2R NDP
			* Timestamp field has 48-bit long
		* Type 01: TOA
			* AID12/RSID12 of an ISTA
			* Sounding Dialog Token Number
			* TOA of R2I NDP
			* Timestamp field has 32-bit long
		* Type 10: PSTOA
			* PS-TOA timestamp of the R2I NDP that the ISTA received from the RSTA

If phase shift feedback is negotiated
* PS-TOAs and TODs reported by the RSTA shall be immediate feedback/broadcast in the Primary RSTA Broadcast Passive TB Ranging Measurement Report frame
* PS-TOAs, TODs, and CFOs shall be immediate feedback/rebroadcasted in the Secondary RSTA Broadcast Passive TB Ranging Measurement Report
* TOAs may be immediate or delayed feedback


Bug???
How can we get the TOA/PSTOA of a ISTA reported by another ISTA ?


CFO
* When using CFO in the conversion from the ISTA’s time basis to the RSTA’s, the RSTA uses the CFO reported in the CFO Parameter field of the I2R LMR. (In R2I LMR, CFO Parameter is reserved.)
* The CFO between the ISTA and the RSTA exceeds the allowed tolerance from the values, this can be an indication of a security attack.
* RSTA may account for clock rate differences between ISTA and RSTA based on the CFO parameter included in the received I2R LMR

## Differential Time of Flight (DToF) Calculation

* ΔPI : Clock diff b/w PSTA and ISTA
* ΔPR : Clock diff b/w PSTA and RSTA
* ΔRI : Clock diff b/w RSTA and ISTA = ((t2 - t1) - (t4 - t3)) / 2 = ΔPI - ΔRI
* ToFPR = t6 - (t3 + ΔPR) = t6 - (t3 + ΔPI - ΔRI)
* ToFPI = t5 - (t1 + ΔPI)
* DToFPRI  = ToFPR - ToFPI = t6 - t5 - t3 + t1 + ΔRI = t6 - t5 - t3 + t1 +((t2 - t1) - (t4 - t3)) / 2 = t6 - t5 + (-t3 - t4 + t2 + t1) / 2


## LMR frame


* frame type
	* Action No Ack frame of category Public, so no ack nor retransmission
	* If secure FTM is executed, Protected Fine Timing Action frames shall be used
* Immediate feedback type for I2R and R2I LMR
	* determined by ranging parameter during negotiation phase
	* immediate: from the current availability window
	* delayed: from the last availability window (前一個 ?) in which the ISTA responded to the TF Ranging Poll frame and the RSTA allocated resources to that ISTA during the measurement sounding phase
* Dialog Token
	* Same as the 'Sounding Dialog Token' in the corresponding Ranging NDP Announcement
	* The Token may not refer to current polling/sounding/reporting triplet, if immediate R2I/I2R feedback is set to 0 (delayed)
* Invalid Measurement field in ToA Error
	* If 1
		* RSTA shall discard TOA field in I2R LMR
		* ISTA shall discard TOD field in R2I LMR
* TOA
	* The measurement value from the sounding phase of which Sounding Dialog Token equals to the Dialog Token of this LMR frame
* TOD
	* The measurement value from the sounding phase of which Sounding Dialog Token equals to the Dialog Token of this LMR frame

For delayed reporting
* set the Invalid Measurement subfield in the TOA Error field of R2I/I2R LMR to 1, if the first instance of the R2I LMR and the optional I2R LMR do not have valid TOA/TOD timestamps to include.


## FTM Modification

ISTA can initiate an FTM modification
* out side of the availability window
* transmit a Fine Timing Measurement Request frame (can be treated as another IFTMR for the new session), with
	* modified ranging parameters
	* Trigger field set to 1
* this indicates both ISTA and RSTA terminate the current session and start a new session

(TBR) 11.21.6.5.1 Availability Window parameter modification

## FTM Termination

A TB ranging FTM session may be terminated, if
* (by both) ISTA fails to respond to a TF Ranging Poll frame and receive one TF Ranging (Secured) Sounding frame containing its AID/RSID at least once within the Max Session Expiry interval
	* Max Session Expiry interval starts from either the end of the successful FTM session negotiation or the beginning of the last successful TB ranging measurement exchange
* (by RSTA) during the session when the RSTA is permitted to transmit an R2I LMR frame, RSTA transmits an A-MPDU containing an LMR frame and a Fine Timing Measurement frame
	* LMR frame
		* Dialog Token field set to 0 
		* type Action No ACK
	* FTM frame
		* Follow Up Dialog Token field is set as 0
		* not include any Ranging Parameters field
* (by ISTA) ISTA sends a Fine Timing Measurement Request frame 
	* Trigger field set to 0
	* not include Ranging Parameters element
	* not include Measurement Request element
* (by ISTA) ISTA sends an IFTMR requests a new session with modified ranging parameters


## Ranging Trigger Frame

TF Ranging's Trigger Type is 8.

TF Ranging has the following variants
* Poll
* Sounding, Secure Sounding, Passive Sounding
* Report

The Token field of Trigger Dependent Comon Info field in a Poll Ranging Trigger is used to match the partial TSF time in a following Ranging NDP Announcement frame.

Sounding Dialog Token Number subfield is only used in Passive Sounding Rangging Trigger for identifing a Measurement Sounding phase.
The same value is included in the Sounding Dialog Token field of the Ranging NDP Announcement frame transmitted within the same Availability Window.


# Beacon

To announce scheduling and parameters of the availability window for passive TB ranging, RSTA includes an RSTA Availability Window element in its Beacon frame
* RSTA Availability Window
	* Availability Window Broadcast Format subfield in the Header subfield = 1
	* Passive TB Ranging Parameters subfield
		* indicates the requested or allocated PPDU format and nominal bandwidth used to transmit the I2R/R2I NDP exchange
		* the bandwidth used for the exchanged frames is equal to or smaller than the declared bandwidth


# Passive TB Ranging Measurement Exchange

Passive TB ranging is a variant of the TB ranging mode.
passive TB ranging mode consists of ranging exchanges between an RSTA and a set of ISTAs

estimate its differential distances to the pairs of RSTAs and/or ISTAs.

Passive TB ranging mode follows the rules for TB ranging mode, except
* not use protected management frames and secure LTF.
* secure version of TB ranging does not apply to passive TB ranging
* RSTA uses the Ranging Trigger frame of subtype passive TB ranging for its sounding trigger frames
* ISTAs use HE Ranging NDPs for its I2R NDPs
* ISTAs do not use the LMR frame for reporting of I2R LMR but instead uses the ISTA Passive TB Ranging Measurement Report frame
* RSTA sends the Primary and Secondary RSTA Broadcast Passive TB Ranging Measurement Report frames at the end of the measurement exchange
* number of spatial streams (NSTS) for passive TB ranging is limited to 4
* If phase shift feedback is negotiated for passive TB ranging, both the RSTA and the ISTA measures and reports PSTOAs


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


MFPC and MFPR determines whether PTKSA between RSTA and ISTA should be established

How to establish a PTKSA
* If the ISTA and the RSTA are associated
	* PTKSA
	* 4-way handshake
	* FILS authentication protocol
	* FT Protocol
* If the ISTA and the RSTA are not associated
	* Preassociation Security Negotiation

What types of measurement exchange can apply proteced measurement
	* TB ranging
	* non-TB ranging
	* EDCA based ranging for DMG/EDMG STA

When to establish a PTKSA before initating a FTM procedure
	* URNM-MFPR = 1
	* URNM-MFPR = 0 and URNM-MFPR-X20 = 1 (unless 20 MHz is speified in Format and Bandwidth subfield of the Ranging Parameters field)



# Scheduling

Centric
* ISTA centric scheduling --> Non-TB ranging
* RSTA centric scheduling
	* EDCA-based ranging
	* TB ranging
	* passive TB ranging is scheduled by the RSTA in an availability window used for passive location

To announce scheduling and parameters of the availability window for passive TB ranging, RSTA includes an RSTA Availability Window element in its Beacon frame

# Related Sections in IEEE 802.11md

## Description
* 4.3.19.19 Fine timing measurement
* 11.10.2 Measurement on operating and nonoperating channels
* 11.10.9.6 LCI report (Location configuration information report)
* 11.10.9.9 Location Civic report
* 11.10.9.11 Fine Timing Measurement Range report
* 11.21.6 Fine timing measurement (FTM) procedure

## Frame format
* 9.3.3.13 Action frame format
* 9.6.7.32 Fine Timing Measurement Request frame format
	* Public Action frame
	* Category: 4
	* Public Action field: 32
	* Trigger: 1 for start, 0 for stop
	* LCI Measurement Request element (Opt.)
	* Location Civic Measurement Request element (Opt.)
	* Fine Timing Measurement Parameter element (Opt.)
* 9.6.7.33 Fine Timing Measurement frame format
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
* 9.4.2.44 RM Enabled Capabilities element
	* RM Enabled Capabilities:
		* bit 34: FTM Range Report Capability Enabled
* 9.4.2.26 Extended Capabilities element
	* Extended Capabilities field
		* bit 70: Fine Timing Measurement Responder: 1, if supports FTM as a responder
		* bit 71: Fine Timing Measurement Initiator: 1, if supports FTM as a initiator
* 9.4.2.20 Measurement Request element
	* Element ID: 38
	* 9.4.2.20.10 LCI request (Location configuration information request)
		* Measurement Type: 8
	* 9.4.2.20.14 Location Civic request
		* Measurement Type: 11
	* 9.4.2.20.19 Fine Timing Measurement Range request
		* Measurement Type: 16
* 9.4.2.21 Measurement Report element
	* Element ID: 39
	* 9.4.2.21.10 LCI report (Location configuration information report)
		* Measurement Type: 8
	* 9.4.2.21.13 Location Civic report
		* Measurement Type: 11
	* 9.4.2.21.18 Fine Timing Measurement Range report
		* Measurement Type: 16
* 9.4.2.167 Fine Timing Measurement Parameters element
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
* 9.4.2.172 FTM Synchronization Information element
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
* 6.3.53 Location configuration request
* 6.3.54 Location track notification






# Related Sections in IEEE 802.11az

## Description
* 4.3.19.19 Fine timing measurement
* 11.21.6.4.2 EDCA based ranging measurement exchange
* 11.21.6.4.3 TB ranging measurement exchange
* 11.21.6.4.4 Non-TB ranging measurement exchange
* 11.21.6.4.8 Passive TB ranging measurement exchange
* 12.12 Preassociation security negotiation

## Frame format
* 9.3.1.19 VHT/HE/Ranging NDP Announcement frame format
* 9.3.1.22 Trigger frame format
* 9.6.6.6 Neighbor Report Request frame format
* 9.6.7.49 Location Measurement Report (LMR) frame format
* 9.6.7.50 ISTA Passive TB Ranging Measurement Report frame format
* 9.6.7.51 Primary RSTA Broadcast Passive TB Ranging Measurement Report frame format
* 9.6.7.52 Secondary RSTA Broadcast Passive TB Ranging Measurement Report frame format
* 9.6.34 Protected Fine Timing Frame details

## Element Format
* 9.4.2.26 Extended Capabilities element
	* Extended Capabilities field
		* bit 70: Fine Timing Measurement Responder: 1, if supports FTM as a responder
		* bit 71: Fine Timing Measurement Initiator: 1, if supports FTM as a initiator
		* bit 90: Non-TB Ranging Responder
		* bit 91: TB Ranging Responder
		* bit 92: Passive TB Ranginng Responder Measurement Support
		* bit 93: Passive TB Ranging Initiator Measurement Support
		* bit 94: AOA Measurements Available
		* bit 95: Phase Shift TOA Feedback Support
			* Can be set to 1, if one of the bits in bit 90 ~ 93 is set
			* indicate the RSTA’s capability to support phase shift TOA feedback
		* bit 96: DMG/location supporting APs in the area
		* bit 97: I2R LMR Feedback Policy
			* if bit 90 is set or bit 91 is set
				* 1: RSTA does not require ISTAs to support the capability to generate and transmit I2R LMRs
				* 0: indicates that ISTAs shall negotiate the transmission of I2R LMR
* 9.4.2.167 Fine Timing Measurement Parameters element
	* Element ID: 206
* 9.4.2.241 RSN Extension element (RSNXE)
	* Secure LTF Support
	* Secure RTT Supported
	* URNM-MFPR-X20
	* URNM-MFPR
* 9.4.2.296 ISTA Availability Window
	* Element ID: 255
	* Element ID Extension: 98
* 9.4.2.297 RSTA Availability Window
	* Element ID: 255
	* Element ID Extension: 99
* 9.4.2.298 Ranging Parameters element
	* Element ID: 255
	* Element ID Extension: 101
	* Ranging Parameters
		* sdef
	* Ranging Subelements
		* one or more subelement
		* Non-TB Specific subelement
		* TB-specific subelement
		* Secure LTF subelement
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

## SME-MLME SAP
* 6.3.5 Authenticate
* 6.3.56 Fine timing measurement (FTM)


## Frame format

* 9.6.34 Protected Fine Timing Action Frame
















# Implementation Concern

* Can A RSTA support EDCA-based, TB, and non-TB at the same time?

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
	* As defined in IETF RFC 6225: includes latitude, longitude, and altitude, with uncertainty indicators for each.
* LO (Local Oscillator)
* TU (Time Unit)
	* 1024 microseconds (us), roughly 1 milisecond (ms)
	* 1 microseconds (us) = 10^(-6)
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
* AOA (angle of arrival)
* AOD (angle of departure)
* STS (space-time string)
* TSF (Timing synchronization function)
* DMG (Directional Multi-Gigabit)
* EDMG (Enhanced Directional Multi-Gigabit)
* LTF (Long Training Field)
* URNM-MFPR (Unassociated Range Negotiation and Measurement Management Frame Protection Required)
* URNM-MFPR-X20 (Unassociated Range Negotiation and Measurement Management Frame Protection Required Exempt 20MHz)
* RSID (ranging session Identifier)
* AWV (antenna weight vector)
* TF (trigger frame)
* LOS (line of sight)

* HE-LTF (high efficiency – long training field)
	* HE-LTP Repetitions: multiple transmissions of HE-LTF symbols in an HE Ranging NDP or HE TB Ranging NDP

# Reference
