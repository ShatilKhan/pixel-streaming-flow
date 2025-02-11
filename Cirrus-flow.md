# pixel-streaming-flow
```mermaid
sequenceDiagram
    participant S as Streamer
    participant SS as Signaling Server
    participant SFU as SFU
    participant P as Player

    %% Initial Connection Phase
    S->>SS: WebSocket Connection
    SS->>S: config
    SS->>S: identify
    S->>SS: endpointId
    SS->>S: endpointIdConfirm

    %% SFU Connection (Optional)
    SFU->>SS: WebSocket Connection
    SS->>SFU: Auto-subscribe to Streamer

    %% Player Connection & Stream Setup
    P->>SS: WebSocket Connection
    SS->>P: config
    SS->>P: playerCount
    P->>SS: listStreamers
    SS->>P: streamerList
    P->>SS: subscribe
    SS->>S: playerConnected

    %% WebRTC Negotiation
    S->>SS: offer
    SS->>P: offer
    P->>SS: answer
    SS->>S: answer
    
    %% ICE Candidate Exchange
    P->>SS: iceCandidate
    SS->>S: iceCandidate
    S->>SS: iceCandidate
    SS->>P: iceCandidate

    %% Streaming & Data Channels
    Note over S,P: Active Streaming Session
    S-->>P: Media Stream
    P-->>S: Data Channel (Optional)

    %% Disconnect Scenarios
    opt Player Disconnects
        P->>SS: Close Connection
        SS->>S: playerDisconnected
        SS-->>SFU: playerDisconnected
    end

    opt Streamer Disconnects
        S->>SS: Close Connection
        SS->>P: streamerDisconnected
        SS-->>SFU: streamerDisconnected
    end

    %% Health Checks
    loop Keep Alive
        S->>SS: ping
        SS->>S: pong
    end
```
