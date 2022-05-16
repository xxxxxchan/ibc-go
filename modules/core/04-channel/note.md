## IBC数据传输确认

### 1.首先IBCBlockCommit 和 IBCPacketTx 传递到 Hub上,证明IBCPacket 在 Zone1 上存在

- `FromChainID`: "Zone1"
- `FromBlockHeight`: 100 (say)
- `Packet`: an `IBCPacket`:
  - `Header`: an `IBCPacketHeader`:
    - `SrcChainID`: "Zone1"
    - `DstChainID`: "Zone2"
    - `Number`: 200 (say)
    - `Status`: `AckPending`
    - `Type`: "coin"
    - `MaxHeight`: 350 (say "Hub" is currently at height 300)
  - `Payload`: &lt;The bytes of a "coin" payload&gt;

### 2.IBCBlockCommit 和 IBCPacketTx 传递到 Zone2 证明 IBCPacket 在 Hub 上存在
- `FromBlockHeight`: 100 -> 300

### 3.接下来，Zone2 必须在其 app-hash 中包含一个缩写数据包，该数据包显示AckSent. 一个IBCBlockCommit和IBCPacketTx被发回“Hub”上，证明IBCPacket“Zone2”上存在一个缩写。说它IBCPacketTx具有以下值：
- `FromChainID`: "Zone2"
- `FromBlockHeight`: 400 (say)
- `Packet`: an `IBCPacket`:
  - `Header`: an `IBCPacketHeader`:
    - `SrcChainID`: "Zone1"
    - `DstChainID`: "Zone2"
    - `Number`: 200
    - `Status`: `AckSent`
    - `Type`: "coin"
    - `MaxHeight`: 350
  - `PayloadHash`: &lt;The hash bytes of the same "coin" payload&gt;

### 4.最后，“Hub”必须将数据包的状态从 更新AckPending为 AckReceived。这种新的最终状态的证据应该回到“Zone2”。说它IBCPacketTx具有以下值：
- `FromChainID`: "Hub"
- `FromBlockHeight`: 301
- `Packet`: an `IBCPacket`:
  - `Header`: an `IBCPacketHeader`:
    - `SrcChainID`: "Zone1"
    - `DstChainID`: "Zone2"
    - `Number`: 200
    - `Status`: `AckReceived`
    - `Type`: "coin"
    - `MaxHeight`: 350
  - `PayloadHash`: &lt;The hash bytes of the same "coin" payload&gt;
### 如果“Hub”在AckSent块 350 之前没有从“Zone2”接收到状态，它将自动将状态设置为Timeout。这个超时证据可以在“Zone1”上发布，并且可以返回任何令牌。