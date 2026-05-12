# LCDP for libp2p Developers

If you know libp2p, you already understand 90 percent of the problem LCDP is trying to solve. You have built transports, negotiated multistream-select, wired Noise or TLS, multiplexed yamux streams, and run Kademlia and gossipsub on top. You also know the cost: a spec stack that is powerful, correct, and takes weeks to learn.

LCDP is not a better libp2p. It is a deliberate step down the stack to a place where a human can debug a network with `tcpdump -A`.

## The whole difference in one paragraph

libp2p gives you a composable toolkit for production p2p: peer IDs, secure channels, stream multiplexing, NAT hole punching with relays, DHT routing, pubsub. You choose pieces, you get guarantees.

LCDP gives you one wire format: UTF-8 JSON arrays of single-key objects sent over UDP.

`[{"ChatMessage":{"message":"hi"}}]`

No handshake, no version, no stream abstraction, no required encryption. Compatibility comes from "ignore what you do not understand" rather than negotiation.

Think of libp2p as the Linux kernel networking stack. Think of LCDP as writing directly to a raw socket and deciding what reliability means for your app.

## 1. Is this meant to be an alternative to libp2p?

No, not directly. It is an alternative to the assumption that you always need a full stack.

Use LCDP when you want more control, minimal latency, zero dependencies, works from bash with netcat, survives for decades without a version bump, lets you keep state for 10 peers or 10,000 peers without kernel socket exhaustion.

They solve different scarcities. libp2p optimizes for machine efficiency and protocol correctness. LCDP optimizes for human understanding and implementation speed in 2026, where bandwidth is cheap and developer time is not.

You can think of LCDP as the thing you would build *before* you need libp2p, or the fallback language two libp2p implementations could use when they have nothing else in common.

## 2. Could this be a libp2p transport?

libp2p expects transports to have some amount of ordering and retransmission, so you'd have to abstract that in.

## 3. Should someone switch from libp2p to this?

Almost never as a wholesale switch. Switch if your pain points match what LCDP removes.

Stay on libp2p if:
- you need interop with IPFS, Filecoin, Polkadot, or any existing libp2p network
- you rely on gossipsub for scalable pubsub
- you need authenticated encryption by default, not as an optional wrapper
- you have a team that can maintain upgrades across Noise, yamux, identify, etc.

Consider LCDP if:
- you are prototyping and want a working p2p demo in an afternoon, not a month
- you need to talk to thousands of peers with almost no per-peer state
- you want AI to generate clients reliably (JSON is far easier for LLMs than protobufs)
- you want messages that survive tcpdump, grep, and ten years of bitrot
- you are building something where partial understanding is fine, like gossiping sensor data or game state

## 4. On a new project, how do I choose?

Ask what you are optimizing for.

| Choose LCDP when | Choose libp2p when |
| --- | --- |
| You value readability over bandwidth | You value bandwidth and CPU efficiency |
| You want 20 lines of Python, not 20 crates | You need production-grade backpressure and flow control |
| You want the option for 0.5 RTT loss tolerant messaging | You only will ever need reliable ordered streams and latency is not a concern |
| You want perpetual compatibility without versions | You are okay with protocol negotiation and upgrades |
| You want any language, even bash, to join | You need a mature ecosystem in Go, Rust, JS, Nim |

A practical heuristic: if your first milestone is "two laptops behind home routers exchange a JSON ping without a server," start with LCDP. If your first milestone is "secure, multiplexed streams with peer routing at internet scale," start with libp2p or further evolve LCDP.


## What libp2p developers usually miss at first

- **No streams.** If you want streams, build them as messages: `{"StreamOpen":{"id":"a"}}`, `{"StreamData":{"id":"a","seq":1}}`. You choose reliability per stream.
- **Identity is optional.** `MyPublicKey` and `SignedMessage` exist, but you can send unauthenticated datagrams. That is intentional for bootstrapping and for low-risk gossip.
- **NAT without servers.** LCDP does not use STUN. It relies on both peers sending from the same port they listen on. The `PleaseAlwaysReturnThisMessage` cookie prevents amplification attacks. It works on most home NATs, fails on the hardest symmetric NATs, which is the same failure mode libp2p has without relays.

## Bottom line

Do not think "LCDP versus libp2p." Think "messages versus connections."

If you are comfortable with multistream-select, you will feel at home adding a new JSON key. If you are tired of upgrading six protocol versions to add one field, you will feel relief.

Start by implementing two messages: `PleaseSendPeers` and `PleaseReturnThisMessage`. Talk to the bootstrap node at 148.71.89.128:24254 with netcat. You will have a better intuition in five minutes than any spec can give.
