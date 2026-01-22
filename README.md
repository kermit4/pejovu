PEJOVU -  PEer-to-peer Json OVer Udp

A simple, interoperable, expansible, p2p protocol, inspired by https://farcaster.xyz/vitalik.eth/0xd6b8e141 and https://medium.com/@webseanhickey/the-evolution-of-a-software-engineer-db854689243

# protocol 
JSON array of messages, sent over UDP

Please return any cookies you receive in your responses. { .... cookie: ... };
## message types seen in the wild
- { message_type: "These are peers."; 
    [ "1.2.3.4:1234", ... ]
}
- { message_type: "Please send content."; 
    content_id: "...";
    content_offset: 0;
    content_length: 1024; 
    }
- { message_type: "Here is content."; 
    content_id: "...";
    content_offset: 0;
    content_b64: "<base64>"
}

## message type ideas:
- timestamp requests to learn most responsive service from your location
- public keys in "Receive peers."
- message_type hash of me  hash type sha2526 sha256: ".."; // checksum the json array string     so far before appending this object
- { message_type: "try these others for the content"; content_id: "..."; peer_list: [ ip,port ... ] }
- chksums of the JSON in the JSON
- channels, like a stream but multiple senders
- encryption
- economics to insentivize resource sharing
- chat
- chat message whitelisting to avoid spam

# known implementations
https://github.com/kermit4/pejovu-rust

# known likely to be running nodes
148.71.89.128:24254
159.69.54.127:24254
