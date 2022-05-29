# Hub, Switch and Router

## Collision domain
Collision domain is an Ethernet term used to describe a network scenario in which one device sends a packet out on a network segment and every other device on that same segment is forced to pay attention to it no matter what.

This isn’t efficient because if a different device tries to transmit at the same time, a collision will occur, requiring both devices to retransmit, one at a time—not good! And this happens a lot in a hub environment, where each host segment connects to a hub that represents only one collision domain and a single broadcast domain. By contrast, each and every port on a switch represents its own collision domain, allowing network traffic to flow much more smoothly.[CCNA_study_guide_volume_2_Exam_200_301_by_Todd_Lammle]

## Broadcast domain
Broadcast domain is the domain in which a broadcast is forwarded.

## HUB
- Single broadcast domain.
- Single collision domain.
- DO not segment networks.Just connect network segments.
- Operate at application layer

## SWITCH
- Every port on a switch is in a different collision domain.(Collision domain separator)
- Single broadcast domain.
- Operate at layer 2.

## ROUTER
- Multiple broadcast domains.
- Multiple collision domains.
- Operate at layer 3.

## Repeater
- Operate on physical layer.
- Single broadcast domain and collision domain.

## BRIDGE
- Multiple collision domains.
- Single broadcast domain.

## Transparent Bridging
Switches read each frame as it passes through the network. The layer 2 device then puts the source hardware address in a fi lter table and keeps track of which port the frame was received on. This information (logged in the bridge’s or switch’s fi lter table) is what helps the machine determine the location of the specifi c sending device.

After a filter table is built on the layer 2 device, it will forward frames only to the segment where the destination hardware address is located. If the destination device is on the
same segment as the source host, the layer 2 device will block the frame from going to any
other segments. If the destination is on a different segment, the frame can be transmitted
only to that segment. This is called transparent bridging.