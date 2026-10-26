# Proposed communication protocol (Task 1)

**Scope:** educational simulation, not a production automotive V2V protocol.

## Transport choice
UDP for periodic, freshness-sensitive `STATUS_UPDATE` datagrams. Application-layer acknowledgment and bounded retries for join/leave coordination. Vehicle deployments may use ITS-G5/GeoNetworking/BTP or C-V2X; the Labroots image does not establish its transport protocol.

## Proposed message schema
```json
{
  "protocol_version": 1,
  "message_type": "STATUS_UPDATE",
  "platoon_id": "P1",
  "sender_truck_id": "T1",
  "sequence_number": 105,
  "timestamp_ms": 10500,
  "speed_mps": 20.0,
  "acceleration_mps2": -1.5,
  "brake_active": true,
  "emergency_flag": false,
  "mode": "FOLLOWING"
}
```

## Events
`JOIN_REQUEST`, `JOIN_ACK`, `JOIN_REJECT`, `LEAVE_REQUEST`, `LEAVE_ACK`, `STATUS_UPDATE`, `EMERGENCY_BRAKE`, `HEARTBEAT_TIMEOUT`, `LINK_RESTORED`, `SENSOR_FAULT`.

## Proposed rules
1. Periodic status every 100 ms (simulation assumption).
2. Reject unexpected senders, malformed payloads, duplicate/old sequence numbers, and stale timestamps.
3. Warn at 200 ms without valid status; fallback at 300 ms (simulation assumptions, not proven safe thresholds).
4. For join/leave: acknowledgment, timeout, bounded retries; do not assume success without confirmation.
5. Emergency message sent immediately and repeated; onboard sensors remain authoritative for collision avoidance.
6. Resume platooning only after link, identity, freshness and sensor checks succeed.

## Verification
Inject 5%, 20% and 50% packet loss, random delay, out-of-order messages, complete outages and reconnection; verify mode transitions and minimum following gap.
