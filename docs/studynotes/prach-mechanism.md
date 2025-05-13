# Study Notes

# Overview

## Random Access Channel (RACH)
RACH is a mechanism in cellular communication system that allows a User Equipment (attacker or legitimate user) to communicate with the network, such as base station gNB for 5G and eNB for 4G.

RACH works between gNB and UE by performing these steps:
1.  Random Access Preamble Transmission: UE sends a preamble (opening message) to gNB on a spesific RACH resource.
2.  Random Access Response: The gNB responds with a Random Access Response
3.  UE Identification: UE sends a message to gNB carrying its unique identifier
4.  Contention Resolution: if multiple UEs chose the same preamble, gNB resolves by responding to the UE whose identifier match the response

RACH consists of these steps:
1. Msg1 (UE sends RACH preamble)
2. Msg2 (Base station sends Random Access Response)
3. Msg3 (UE sends connection request)
4. Msg4 (Base station sends contention resolution).
   
Physical Random Access Channel Attack or PRACH Attack involves  exploiting the random access procedure to disrupt normal network operation.
There are three types of PRACH attack:
1. PRACH Flooding Attack: An attacker floods the PRACH with a high volume of preambles.
2. Fake Random Access Attempts: The attacker sends false preambles frequently.
3. Preamble Collision Amplification: Exploiting the finite number of available preambles

## srsRAN 
srsRAN is a complete software stack that implements key components of LTE and 5G RAN on standard computing platforms and and software-defined radios. 

Typical srsRAN-based setup
[UE] ⇄ [SDR] ⇄ [gNB (srsRAN)] ⇄ [Core Network (EPC/5GC)] ⇄ [Internet]
- UE: connects wirelessly using radio signals (LTE/5G)
- SDR (Software Defined Radio): sends/receives the actual radio waves.
- gNB (Base Station): runs srsRAN and talks to both the UE and the Core Network.
- Core Network (EPC/5GC) gives the UE an IP address and routes its data.
- Internet is where the UE can go after registration is complete.


## Trace the RACH mechanism in srsRAN source code
The RACH (Random Access Channel) mechanism in the srsRAN [source code](https://github.com/srsran/srsRAN_Project):



1. **RACH Indication Handling (`mac_rach_handler`)**:
- File: lib/mac/mac_sched/mac_rach_handler.cpp 
  - Function: `mac_cell_rach_handler_impl::handle_rach_indication`
  - Role: Handles detected RACH indications by processing contention-based (CBRA) and contention-free (CFRA) preambles. Allocates TC-RNTIs and forwards indications to the scheduler.

- File: include/srsran/mac/mac_cell_rach_handler.h
  - Function: `mac_cell_rach_handler::handle_rach_indication`
  - Role: Interface function for handling incoming RACH indications from the PHY layer.
     
   - [Implementation File](https://github.com/srsran/srsRAN_Project/blob/main/lib/mac/mac_sched/mac_rach_handler.cpp)
   - [Header File](https://github.com/srsran/srsRAN_Project/blob/main/lib/mac/mac_sched/mac_rach_handler.h)


2. **RACH Configuration PHY-Layer Functions**:
- File: `include/srsran/ran/prach/rach_config_common.h`.
  - Function:
  - Role: Includes parameters like preamble count, response windows, timing advance, and root sequence index.
   - [Configuration File](https://github.com/srsran/srsRAN_Project/blob/main/include/srsran/ran/prach/rach_config_common.h)


3. **RACH Scheduling**:
- File: lib/scheduler/common_scheduling/ra_scheduler.h
  - Function: ra_scheduler::handle_rach_indication
  - Role: Manages RACH indications, including scheduling RA responses (RAR) and Msg3 grants.
- File: lib/scheduler/common_scheduling/prach_scheduler.h
  - Function: run_slot
  - Role: Handle the scheduling of PRACH occasions and RACH procedures. Function allocates RACH PDUs in a given slot.
    
   - [PRACH Scheduler](https://github.com/srsran/srsRAN_Project/blob/main/lib/scheduler/common_scheduling/prach_scheduler.h)
   - [RA Scheduler](https://github.com/srsran/srsRAN_Project/blob/main/lib/scheduler/common_scheduling/ra_scheduler.h)


4. **RACH Logging**:
- File: lib/fapi/loggers/message_loggers.cpp
  - Function: srsran::fapi::log_rach_indication
  - Role: Logs details of RACH indications, such as preamble power, timing

- File: lib/scheduler/logging/scheduler_event_logger.cpp
   - Function: `enqueue_impl`
   - Role: Logs details of RACH events, including preamble power, timing advance, and SNR.

- [Event Logger File](https://github.com/srsran/srsRAN_Project/blob/main/lib/scheduler/logging/scheduler_event_logger.cpp)


5. **PDU Definition**:
- File: `include/srsran/fapi/messages/rach_indication.h`.
  - Function: `rach_indication_pdu_preamble`
  - Role:   Represents a single detected RACH preamble, including:
     - `preamble_index`: The ID of the preamble.
     - `timing_advance_offset`: Timing advance in offset values and
nanoseconds.
     - `preamble_pwr`: Received power of the preamble.
     - `preamble_snr`: Signal-to-noise ratio (SNR) of the preamble.

  - Function: `rach_indication_pdu`
  - Role:   Represents a RACH PDU, which includes:
     - `slot_index`: Slot information.
     - `symbol_index`: Symbol index.
     - `avg_rssi`: Average RSSI value.
     - `preambles`: A list of detected preambles (instances of `rach_indication_pdu_preamble`).
    
  - Function: `rach_indication_message`
  - Role: Represents an entire RACH indication message, which includes:
     - `sfn`: System frame number.
     - `slot`: Slot number.
     - `pdus`: A list of RACH PDUs (instances of `rach_indication_pdu`).

   - Structures describe detected preambles, timing advance offsets, and SNR values.
   - [PDU Definitions](https://github.com/srsran/srsRAN_Project/blob/main/include/srsran/fapi/messages/rach_indication.h)


6. **Message Definitions**
- File: include/srsran/fapi/messages/rach_indication.h
  - Struct: rach_indication_message
  -  Role: Represents a RACH indication message, with details such as preamble index and power.

   - [Message Definitions](https://github.com/srsran/srsRAN_Project/blob/main/include/srsran/fapi/messages/rach_indication.h)

### srsRAN (crash course)
srsRAN (was srsLTE) is an open-source SDR (Software Defined Radio) stack for LTE/5G. SDR is like the signal path (in real world: airwaves)
super dummily put, srsRAN enables:
- a gNB to act like a real tower
- a UE to act like a phone
- a core (ex: 5G) to connect all to the Internet
### Msg1 to Msg 4
#### Msg 1
- The phone (UE) sends a RACH preamble on the PRACH. (((kinda like knocking)))
- It's anonymous — no identity yet.
- It chooses a random preamble and sends it on an allowed time/frequency.
#### Msg 2
- The gNB hears the preamble and sends a Random Access Response (RAR).
- This step helps fix timing and allows the UE to prepare for the next message.
#### Msg 3
- The UE sends an RRC Request or Scheduling Request using the slot it got in Msg2. Shares its identity (like IMSI or Temporary ID).
#### Msg 4
- The gNB responds with Msg4: Confirms the UE’s identity, welcomes, might assign a C-RNTI (unique ID for future messages)

## Create the message sequence chart (MSC) to map each function in the codeflow for RACH mechanism
![Editor _ Mermaid Chart-2025-04-19-094652](https://github.com/user-attachments/assets/a9f82858-2db4-46c1-8397-9ce14599bdb8)
Random Access Preamble Transmission (Msg1)
### Code Flow for the MSC
Code Reference:
rach_indication_pdu_preamble (File: include/srsran/fapi/messages/rach_indication.h)
Handles details like preamble_index, preamble_pwr, timing_advance_offset, and SNR.
MSC Representation:
UE sends a preamble to the gNB.
Random Access Response (Msg2)

Code Reference:
mac_cell_rach_handler_impl::handle_rach_indication (File: lib/mac/mac_sched/mac_rach_handler.cpp)
Responds to detected RACH preambles and forwards them to the scheduler.
MSC Representation:
gNB sends a response (RAR) to the UE.
UE Identification and Msg3

Code Reference:
ra_scheduler::handle_rach_indication (File: lib/scheduler/common_scheduling/ra_scheduler.h)
Manages scheduling RA responses and Msg3 grants.
MSC Representation:
UE sends its unique identifier (contention-based or contention-free).
Contention Resolution (Msg4)

Code Reference:
mac_cell_rach_handler::handle_rach_indication (File: include/srsran/mac/mac_cell_rach_handler.h)
Resolves contention by matching identifiers and responding appropriately.
MSC Representation:
gNB resolves contention and finalizes the connection.
