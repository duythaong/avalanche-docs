---
sidebar_position: 1
---

# Tạo subnet trên mạng local

## Mục tiêu

Tìm hiểu được cách tạo tệp genesis file cho một subnet và sau đó là cấu hình nó.

Để mà có thể triển khai một subnet đơn giản nhất ta có thể dùng [Avalanche Cli](https://github.com/ava-labs/avalanche-cli). Sau đó, chúng ta sẽ tìm hiểu về tệp genesis và tùy chỉnh nó theo nhu cầu của chúng ta.


### Chuẩn bị

#### Avalanche Cli

Tạo một thư mục cho Avalanche CLI.

```bash
mkdir -p ~/avalanche
```

Đi tới thư mục đó và tải xuống Avalanche CLI version mới nhất.

```bash
cd ~/avalanche
curl -sSfL https://raw.githubusercontent.com/ava-labs/avalanche-cli/main/scripts/install.sh | sh -s
```

Đợi download khoảng 10 giây, tuỳ theo hệ điều hành mà bạn đang sử dụng sẽ có hiển thị khác nhau. Ở đây, mình dùng hệ điều hành macOS:
```bash
> ava-labs/avalanche-cli info checking GitHub for latest tag
> ava-labs/avalanche-cli info found version: 0.2.4 for darwin/amd64
> ava-labs/avalanche-cli info installed ./bin/avalanche
```

Di chuyển tới thư mục mà bạn vừa tải xuống CLI và export PATH. Nhờ vậy mà ta có thể chạy CLI từ mọi nơi.

```bash
cd ./bin/avalanche
export PATH=$PWD:$PATH
```

## Tạo và triển khai một Subnet

### Subnet là gì?

Subnet là tập hợp một nhóm validators ở Primary network của Avalanche. Một subnet có thể có và validate nhiều blockchain tham gia trong nó.  Mỗi blockchain sẽ chỉ được validate bởi một subnet. 

[Xem thêm giới thiệu chi tiết về subnet tại](https://docs.avax.network/subnets)

#### Genesis File

Genesis file là một file chứa cấu hình khởi tạo của subnet. Avalanche CLI sẽ tạo một genesis file dựa trên một số thông tin mà mình cung cấp. Mặt khác, mình cũng có thể tạo một genesis file tuỳ ý của riêng mình.

### Tạo một subnet đơn giản

Chạy lệnh này để tạo một Subnet, với subnetName là tên subnet mà bạn muốn tạo

```bash
avalanche subnet create <subnetName>
```

Ở đây, mình sẽ tạo một subnet có tên là localSubnet
```bash
avalanche subnet create localSubnet
```

Sẽ có tổng cộng 6 bước:

#### Chọn loại máy ảo(VM)

```bash
? Choose your VM:
  ▸ SubnetEVM
    SpacesVM
    Custom
```

Mình chọn SubnetEVM

#### Chain Id

Chọn một giá trị duy nhất cho blockchain sắp tạo, để chắc chắn nó chưa tồn tại, ta có thể lên [chainlist](https://chainlist.org/) để xem id này đã được sử dụng chưa.

Mình chọn ChainId là 130397.

#### Token Symbol
Token symbol là symbol của native token được sử dụng trong blockchain của mình. Ví dụ: trên C-Chain là `AVAX`, còn trên Ethereum là` ETH`. Ta có thể đặt symbol là bất cứ thứ gì.

Mình chọn token symbol là LDTQ

#### Subnet versin

```bash
? What version of Subnet-EVM would you like?:
  ▸ Use latest version
    Specify custom version
```

Mình sẽ chọn subnet version bản mới nhất, do đó chọn Use latest version

#### Gas

Cấu hình gas của blockchain. Ở đây chọn mặc định giống C-Chain

```bash
> Low disk use    / Low Throughput    1.5 mil gas/s (C-Chain's setting)
```

#### Airdropping Native Tokens

Mình có thể chỉ định address và số native tokens mình mong muốn khi tạo chain. Đây là supply của native tokens. Nhưng trong ví dụ này mình sẽ thực hiện đơn giản là nhận một triệu token vào 1 address chỉ định.

```bash
How would you like to distribute funds?
> Airdrop 1 million tokens to the default address (do not use in production)
```

#### Modify the Evm

```bash
? Advanced: Would you like to add a custom precompile to modify the EVM?:
  ▸ No
    Yes
    Go back to previous step
```

Tại đây, bạn có thể thiết lập một precompilers tùy chỉnh để thực hiện một số function. 

##### Native Minting

Cho phép admin mint thêm native token thông qua 1 smart contract đặc biệt.
Nếu giá trị là null, blockchain này không thể mint thêm native token. 
Nếu giá trị khác null, chỉ định thời gian bắt đầu mà những address trong adminAddresses có thể mint

###### Contract Deployment Whitelist

Hạn chế chỉ những address có khả năng deploy smart contract lên blockchain của mình.

###### Transaction Allow List

Hạn chế những address có thể thực hiện transaction trên blockchain của mình

Để cho đơn giản, chúng ta sẽ chọn `No` cho cả 2 phần trên. 

```bash
> Successfully created genesis
```
Chúng ta đã tạo thành công subnet

Bạn cũng có thể xem phần config các precompilers tại [`contractNativeMinterConfig`](#contractnativeminterconfig), [`contractDeployerAllowListConfig`](#contractdeployerallowlistconfig), [`txAllowListConfig`](#txallowlistconfig).

### Triển khai Subnet

Chúng ta sẽ triển khai Subnet mà chúng tôi vừa tạo tiếp tục bằng Avalanche CLI!

```bash
avalanche subnet deploy localSubnet

> ? Choose a network to deploy on:
  ▸ Local Network
    Fuji
    Mainnet
```

Chúng tôi sẽ triển khai Mạng con trên mạng local, vì vậy hãy chọn `Local Network`.

Quá trình này sẽ cài đặt Avalanchego (nếu chưa được cài đặt) và khởi động mạng, sẽ mất vài phút.

Sau khi quá trình này hoàn tất, bạn sẽ thấy chi tiết network mà Metamask có thể kết nối tới.

```bash
Metamask connection details (any node URL from above works):
RPC URL:          http://127.0.0.1:9654/ext/bc/psYQ7r5XmeGUc39dVjhEHxVhSsUjcGWDXFB3tjnKiiy3J3yM3/rpc
Funded address:   0x8db97C7cEcE249c2b98bDC0226Cc4C2A57BF52FC with 1000000 (10^18) - private key: 56289e99c94b6912bfc12adc093c9b51124f0dc54ac7a766b2bc5ccf558d8027
Network name:     localSubnet
Chain ID:         130397
Currency Symbol:  LDTQ

```

Ta có thể sử dụng cấu hình này để thêm mạng mới vào Metamask. Sau đó, import account mà tiền airdrop đã được gửi đến metamask của mình bằng private key.

Bây giờ, bạn có thể transfer hay deploy bất kỳ smart contract nào trên blockchain vừa tạo.

## Chi tiết thông tin về Genesis File

Như đã đề cập về [genesis file](#genesis-file) ở trên. Trong quá trình tương tác với CLI, nó đã giúp ta tạo ra file này. Tuy nhiên, mình cũng có thể tùy chỉnh dễ dàng và can thiệp chuyên sâu hơn.

Để xem file mà CLI đã tạo ra cho chúng ta

```bash
## localSubnet là tên Subnet mà mình đã tạo ra. Bạn có thể thay nó bằng tên Subnet của bạn
cat ~/.avalanche-cli/localSubnet_genesis.json
```
Hoặc ta cũng có thể dùng

```bash
avalanche subnet describe localSubnet --genesis
```

Genesis file có dạng Json. Nó như sau

```json
{
  "config": {
    "chainId": 130397,
    "homesteadBlock": 0,
    "eip150Block": 0,
    "eip150Hash": "0x2086799aeebeae135c246c65021c82b4e15a2c451340993aacfd2751886514f0",
    "eip155Block": 0,
    "eip158Block": 0,
    "byzantiumBlock": 0,
    "constantinopleBlock": 0,
    "petersburgBlock": 0,
    "istanbulBlock": 0,
    "muirGlacierBlock": 0,
    "SubnetEVMTimestamp": 0,
    "feeConfig": {
      "gasLimit": 8000000,
      "targetBlockRate": 2,
      "minBaseFee": 25000000000,
      "targetGas": 15000000,
      "baseFeeChangeDenominator": 36,
      "minBlockGasCost": 0,
      "maxBlockGasCost": 1000000,
      "blockGasCostStep": 200000
    },
    "contractDeployerAllowListConfig": {
      "blockTimestamp": null,
      "adminAddresses": null
    },
    "contractNativeMinterConfig": {
      "blockTimestamp": null,
      "adminAddresses": null
    },
    "txAllowListConfig": {
      "blockTimestamp": null,
      "adminAddresses": null
    }
  },
  "nonce": "0x0",
  "timestamp": "0x0",
  "extraData": "0x",
  "gasLimit": "0x7a1200",
  "difficulty": "0x0",
  "mixHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
  "coinbase": "0x0000000000000000000000000000000000000000",
  "alloc": {
    "8db97c7cece249c2b98bdc0226cc4c2a57bf52fc": {
      "balance": "0xd3c21bcecceda1000000"
    }
  },
  "airdropHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
  "airdropAmount": null,
  "number": "0x0",
  "gasUsed": "0x0",
  "parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
  "baseFeePerGas": null
}
```

Genesis filelà một file json chứa cấu hình của Mạng con và header của genesis block, là block đầu tiên trong blockchain.

### Cấu hình

#### `chainId`

ChainId. Ở đây mình chọn là 130397 [Tạo subnet](#chain-id).

#### Hardforks

`eip150Block`, `eip150Hash`, `eip155Block`, `eip158Block`, `byzantiumBlock`, `constantinopleBlock`, `petersburgBlock`, `istanbulBlock`, `muirGlacierBlock`, `SubnetEVMTimestamp`

Những bản hardfork update của blockchain. Mình chọn bắt đầu từ block 0 sẽ áp dụng những hardfork này 

Khi có bản phát hành giao thức mới, để kích hoạt giao thức đó trên Subnet, ta có thể thêm cấu hình cho giao thức mới vào genesis file và trỏ nó tới một block trong tương lai để block đó là block bắt đầu giao thức.

### Cấu hình phí gas

#### `gasLimit`

Tổng lượng gas có thể dùng trong 1 block. Thay đổi giá trị này sẽ làm ảnh hưởng đến khả năng tính toán trong 1 block. Config theo giá trị ở C-Chain giá trị này là `8,000,000`.

Ví dụ: transfer native token sẽ tốn 21,000 gas. Vậy 1 block ở Wraptag chain có thể xử lý tối đa 380 transactions transfer.
Block gas limit của DFK là 30,000,000. Còn của swimmer network là 20,000,000

#### `targetBlockRate`

Thời gian để đào 1 block mới. Config giống C-Chain là 2s/ block. Nếu tốc độ mine nhanh hơn mức này, baseFee sẽ tăng lên. Ngược lại, baseFee sẽ giảm xuống.

#### `minBaseFee`

Phí baseFee tối thiểu trong một transaction. 25000000000 tương ứng là 25GWei. Ở C-Chain giá trị này là 25Gwei.

#### `targetGas`

Lượng gas target sẽ dùng trong khoảng thời gian 10s

#### `baseFeeChangeDenominator`

Chênh lệch baseFee giữ các block

Nếu parent sử dụng gas lớn hơn mức target(ở đây là 15,000,000) thì baseFee sẽ tăng lên. Ngược lại, baseFee sẽ giảm xuống. 
- Sử dụng lớn hơn mức target: `baseFee = max(1, parentBaseFee * (parentGasUsed - parentGasTarget) / parentGasTarget / baseFeeChangeDenominator)`
- Sử dụng ít hơn mức target: `baseFee = max(0, parentBaseFee * (parentGasUsed - parentGasTarget) / parentGasTarget / baseFeeChangeDenominator)`

baseFeeChangeDenominator là 36 nghĩa là mức baseFee chênh lệch giữa block hiện tại và block trước đó là +- 1/36

Đặt giá trị này thành giá trị lớn hơn có nghĩa là baseFee sẽ thay đổi ít. Còn nếu ta đặt nó thành một giá trị nhỏ hơn sẽ làm cho baseFee thay đổi một cách nhanh chóng.

#### `minBlockGasCost`

Gas cost tối thiểu cho 1 block nên có. Config là `0` giống C-Chain

#### `maxBlockGasCost`

Gas cost tối thiểu đa 1 block nên có.  Config là `1,000,000` giống C-Chain

#### `blockGasCostStep`

Gas cost chênh lệch giữa các block khi tốc độ mine block nhanh/ châm hơn targetBlockRate 

```js
(blockGasCostStep) * (targetBlockRate - parent block time)
```

Ví dụ, tốc độ đào block mong muốn là 2 giây, nhưng nếu tốc độ đào block ở block trước là 10 giây. 
Thì block gas cost lúc này sẽ giảm 200000 * (2 - 10) = -1600000

Ở C-Chain, giá trị này là `200000`.

### Address nhận phí validate

Cho phép validator nhận phí khi mine 1 block mới

```json
{
  "config": {
    "allowFeeRecipients": true
  }
}
```

### Precompiles

Ta có thể kích hoạt một số smart contracts được pre-compile trước trong genesis file. Những smart contracts này cung cấp các chức năng hữu ích cho một số trường hợp. Nếu bạn không muốn sử dụng chúng, ta có thể tắt chúng bằng cách set giá trị là null hoặc chỉ cần xóa những thuộc tính này genesis file.


#### `contractDeployerAllowListConfig`

Cấu hình này cho phép những address nào được phép deploy smart contract.
Ta điều chỉnh danh sách này trong phần `contractDeployerAllowListConfig` thành một đối tượng JSON với các thuộc tính sau:

```json
"contractDeployerAllowListConfig": {
  "blockTimestamp": 0,
  "adminAddresses": ["0x64470E5F5DD38e497194BbcAF8Daa7CA578926F6", "0xf1684DaCa9FE469189A3202ae2dE25E80dcB90a1"]
}
```
Smart contract quản lý danh sách admin có thể deploy smart contract là `0x0200000000000000000000000000000000000000`. Ta vẫn có thể thay đổi danh sách này sau khi đã triển khai blockchain
[Có thể xem chi tiết tại document của Avalanche.](https://docs.avax.network/subnets/customize-a-subnet#restricting-smart-contract-deployers)

#### `contractNativeMinterConfig`

Cấu hình này cho phép những address nào được phép mint thêm native tokens thông qua một smart contract đặc biệt.

Ta điều chỉnh danh sách này trong phần `contractDeployerAllowListConfig` thành một đối tượng JSON với các thuộc tính sau:

```json
"contractNativeMinterConfig": {
  "blockTimestamp": 0,
  "adminAddresses": ["0x64470E5F5DD38e497194BbcAF8Daa7CA578926F6", "0xf1684DaCa9FE469189A3202ae2dE25E80dcB90a1"]
}
```

Smart contract quản lý danh sách admin có thể mint thêm native token là `0x0200000000000000000000000000000000000001`. Ta vẫn có thể thay đổi danh sách này sau khi đã triển khai blockchain
[Có thể xem chi tiết tại document của Avalanche.](https://docs.avax.network/subnets/customize-a-subnet#minting-native-coins)


#### `txAllowListConfig`

Cấu hình này cho phép những address nào được phép thực hiện transaction. Phù hợp cho việc build private blockchain.

Ta điều chỉnh danh sách này trong phần `txAllowListConfig` thành một đối tượng JSON với các thuộc tính sau:

```json
"txAllowListConfig": {
  "blockTimestamp": 0,
  "adminAddresses": ["0x64470E5F5DD38e497194BbcAF8Daa7CA578926F6", "0xf1684DaCa9FE469189A3202ae2dE25E80dcB90a1"]
}
```

Smart contract quản lý danh sách address có thể gửi transaction là `0x0200000000000000000000000000000000000002`. Ta vẫn có thể thay đổi danh sách này sau khi đã triển khai blockchain

[Có thể xem chi tiết tại document của Avalanche.](https://docs.avax.network/subnets/customize-a-subnet#restricting-who-can-submit-transactions)

### Genesis Block

`nonce`, `timestamp`, `extraData`, `gasLimit`, `difficulty`, `mixHash`, `coinbase`, `parentHash`, `gasUsed`, `number` là các trường được sử dụng để xác định genesis block header. Ta sẽ không cần phải thay đổi những điều này. (ngoại trừ trường `gasLimit`)

### Native Token Allocation

Để khởi tạo lượng cung ban đầu của native token. T sửa giá trị `alloc` trong file json với thuộc tính sau:

```json
{
  "address_without_0x": {
    "balance": "balance"
  }
}
```

Có hai điều cần lưu ý:

- `address_without_0x`: Là address acount sẽ nhận native tokens vàol lúc khởi tạo chain. Ta phải bỏ `0x` ở đầu address.
- `balance`: giá trị ở dạng wei mà account sẽ nhận được.

Ở trong genesis file của localSubnet, giá trị balance là `0xd3c21bcecceda1000000` tương ứng với `1000000000000000000000000` wei. Số này bằng với 1,000,000 ở dạng `ether`

## Tạo Genesis File và tuỳ biến

Bây giờ chúng ta có thể tạo tệp genesis của mình!

Ta cần tạo một blockchain mà phí gas sẽ cao hơn C-Chain một chút, đồng thời config burn gas fee để tránh lạm phát.

Để bắt đầu, hãy tạo một tệp mới có tên `genesis.json`:

```bash
touch genesis.json
```

Sau đó, xem cấu hình `genesis` file của localSubnet mà ta đã tạo ở trên, ta sẽ theo format này.

```bash
avalanche subnet describe localSubnet --genesis
```

Ta có kết quả như sau:

```json
{
  {
    "config": {
        "chainId": 130397,
        "feeConfig": {
            "gasLimit": 8000000,
            "targetBlockRate": 2,
            "minBaseFee": 25000000000,
            "targetGas": 15000000,
            "baseFeeChangeDenominator": 36,
            "minBlockGasCost": 0,
            "maxBlockGasCost": 1000000,
            "blockGasCostStep": 200000
        },
        "homesteadBlock": 0,
        "eip150Block": 0,
        "eip150Hash": "0x2086799aeebeae135c246c65021c82b4e15a2c451340993aacfd2751886514f0",
        "eip155Block": 0,
        "eip158Block": 0,
        "byzantiumBlock": 0,
        "constantinopleBlock": 0,
        "petersburgBlock": 0,
        "istanbulBlock": 0,
        "muirGlacierBlock": 0,
        "subnetEVMTimestamp": 0
    },
    "nonce": "0x0",
    "timestamp": "0x0",
    "extraData": "0x",
    "gasLimit": "0x7a1200",
    "difficulty": "0x0",
    "mixHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
    "coinbase": "0x0000000000000000000000000000000000000000",
    "alloc": {
        "8db97c7cece249c2b98bdc0226cc4c2a57bf52fc": {
            "balance": "0xd3c21bcecceda1000000"
        }
    },
    "airdropHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
    "airdropAmount": null,
    "number": "0x0",
    "gasUsed": "0x0",
    "parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
    "baseFeePerGas": null
}
}
```

Đây là một file gốc đã được generate sẵn từ Avalanche CLI. Ta sẽ copy cấu hình này để cho đúng định dạng, và sẽ thay đổi một số tin. 

### Chọn chainID

Chọn một số chưa tồn tại để làm [chain id](#chainid) cho blockchain của chúng ta. Kiểm tra tại [chainlist](https://chainlist.org/) để xem chainId này đã có chain nào dùng hay chưa. Hôm nay là ngày 3-10-2022 do đó mình chọn luôn `3102022` và nó chưa được ai dùng.

```json
{
  "config": {
    "chainId": 3102022
  }
}
```

### Cấu hình phí gas

Vì máy của mình cấu hình tương đối mạnh. Do đó, mình sẽ đặt `gasLimit` thành` 20.000.000` để có thể xử lý được nhiều transaction hơn trong 1 block, `targetBlockRate` thành 3 giây và` targetGas` thành `20000000`.

Phần `minBaseFee` thành 60 nAvax (gwei) là` 60000000000` wei để đảm bảo rằng phí giao dịch đủ cao để giúp network giảm phát.

Cấu hình giá trị `baseFeeChangeDenominator` thành` 50`, `minBlockGasCost` thành` 0 ', `maxBlockGasCost` thành` 5000000` và `blockGasCostStep` thành` 100000` vì mình không muốn chi phí gas khối thay đổi quá nhanh.

Cấu hình cuối cùng trông như sau:

```json
{
  "config": {
    "feeConfig": {
      "gasLimit": 20000000,
      "targetBlockRate": 2,
      "minBaseFee": 60000000000,
      "targetGas": 10000000,
      "baseFeeChangeDenominator": 50,
      "minBlockGasCost": 0,
      "maxBlockGasCost": 5000000,
      "blockGasCostStep": 10000
    }
  }
}
```

Đồng thời cập nhật `gasLimit` trong genesis block header.

```json
{
  "config": {
    ...
  },
  "gasLimit": "20000000"
}
```

### Cấu hình tổng cung ban đầu

Chỉ định address `0x149f7F3Eb5B5aAf004E6e2250A632BfD5600700C` sẽ nhận 1000 tokens, tương ứng là `1000000000000000000000` wei. Giá trị này sẽ được biểu diễn ở dạng hex. Ta dùng [tool](https://www.rapidtables.com/convert/number/decimal-to-hex.html) này để chuyển đổi.

```json
{
  "alloc": {
    "149f7F3Eb5B5aAf004E6e2250A632BfD5600700C": {
      "balance": "0x3635C9ADC5DEA00000"
    }
  }
}
```

Cuối cùng, ta có file config như sau:

```json
{
  "config": {
    "chainId": 3102022,
    "homesteadBlock": 0,
    "eip150Block": 0,
    "eip150Hash": "0x2086799aeebeae135c246c65021c82b4e15a2c451340993aacfd2751886514f0",
    "eip155Block": 0,
    "eip158Block": 0,
    "byzantiumBlock": 0,
    "constantinopleBlock": 0,
    "petersburgBlock": 0,
    "istanbulBlock": 0,
    "muirGlacierBlock": 0,
    "SubnetEVMTimestamp": 0,
    "feeConfig": {
      "gasLimit": 20000000,
      "targetBlockRate": 5,
      "minBaseFee": 60000000000,
      "targetGas": 20000000,
      "baseFeeChangeDenominator": 50,
      "minBlockGasCost": 0,
      "maxBlockGasCost": 5000000,
      "blockGasCostStep": 10000
    },
  },
  "alloc": {
    "149f7F3Eb5B5aAf004E6e2250A632BfD5600700C": {
      "balance": "0x3635C9ADC5DEA00000"
    }
  },
  "nonce": "0x0",
  "timestamp": "0x0",
  "extraData": "0x00",
  "gasLimit": "5000000",
  "difficulty": "0x0",
  "mixHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
  "coinbase": "0x0000000000000000000000000000000000000000",
  "number": "0x0",
  "gasUsed": "0x0",
  "parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000"
}
```

## Tạo và triển khai Subnet với Genesis file

Chúng tôi sẽ sử dụng `genesis.json` để tạo một subnet mới.

Trước tiên, xóa Subnet cũ mà chúng ta đã tạo lúc trước.

```bash
avalanche subnet delete localSubnet

> Deleted Subnet
```

Tiếp theo, chúng ta sẽ tạo một Subnet mới bằng cách sử dụng genesis file

```bash
## avalanche subnet create <SubnetName> --file <filepath>
avalanche subnet create localSubnet --file ./genesis.json
```

```bash
## Use SubnetEVM

> Using specified genesis
> ✔ SubnetEVM
> Successfully created genesis
```

Tiếp tục là deploy lên local

```bash
## avalanche subnet deploy <SubnetName>
avalanche subnet deploy localSubnet

## choose local network as deployment target

> Metamask connection details (any node URL from above works):
> RPC URL:          http://127.0.0.1:27200/ext/bc/HGwbjkQsvrqy2mncMWsA2YnrSWEybVmLcZJwz7nb5gNVvcPoi/rpc
> Funded address:   0x149f7F3Eb5B5aAf004E6e2250A632BfD5600700C with 1000
> Network name:     localSubnet
> Chain ID:         3102022
> Currency Symbol:  TH
```

Xong! Đã hoàn thành tạo được một blockchain chạy trên máy của ta.


