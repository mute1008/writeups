---
title: "TrillionEther"
points: N/A
tags: ["blockchain", "stuck"]
---

ソースコードが渡されているので、まずはremix.ethereum.orgでこのスクリプトを動かす。

```sol
// SPDX-License-Identifier: UNLICENSED
pragma solidity 0.8.28;

contract TrillionEther {
    struct Wallet {
        bytes32 name;
        uint256 balance;
        address owner;
    }

    Wallet[] public wallets;

    constructor() payable {
        require(msg.value == 1_000_000_000_000 ether);
    }

    function isSolved() external view returns (bool) {
        return address(this).balance == 0;
    }

    function createWallet(bytes32 name) external payable {
        wallets.push(_newWallet(name, msg.value, msg.sender));
    }

    function transfer(uint256 fromWalletId, uint256 toWalletId, uint256 amount) external {
        require(wallets[fromWalletId].owner == msg.sender, "not owner");
        wallets[fromWalletId].balance -= amount;
        wallets[toWalletId].balance += amount;
    }

    function withdraw(uint256 walletId, uint256 amount) external {
        require(wallets[walletId].owner == msg.sender, "not owner");
        wallets[walletId].balance -= amount;
        payable(wallets[walletId].owner).transfer(amount);
    }

    function _newWallet(bytes32 name, uint256 balance, address owner) internal returns (Wallet storage wallet) {
        wallet = wallet;
        wallet.name = name;
        wallet.balance = balance;
        wallet.owner = owner;
    }
}
```

Remixを使う場合、各アカウントには10しか渡されないので、コントラクトの初期状態を10に設定。 コンパイラタブを開いて、コンパイラバージョン0.8.28を指定してコンパイルした後、Deployタブを開いて、VALUEに10 Etherを指定してデプロイ。

```cassandraql
constructor() payable {
        require(msg.value == 10 ether);
}
```

コントラクトが使えるようになったので、 nameに`0x1111111111111111111111111111111111111111111111111111111111111111`を指定して`createWallet`してみると、Storageが次のような状態になる。

```json
{
	"0x290decd9548b62a8d60345a988386fc84ba6bc95484008f6362f93160ef3e563": { // keccak256(0)
		"key": "0x0000000000000000000000000000000000000000000000000000000000000000", // slot 0
		"value": "0x1111111111111111111111111111111111111111111111111111111111111112" // value
	},
	"0xb10e2d527612073b26eecdfd717e6a320cf44b4afac2b0732d9fcbe2b7fa0cf6": { // kccak256(1)
		"key": "0x0000000000000000000000000000000000000000000000000000000000000001", // slot 1
		"value": "0x0000000000000000000000000000000000000000000000000000000000000000" // value
	},
	"0x405787fa12a823e0f2b7631cc41b3ba8828b3321ca811111fa75cd3aa3bb5ace": { // keccak256(2)
		"key": "0x0000000000000000000000000000000000000000000000000000000000000002", // slot 2
		"value": "0x0000000000000000000000005b38da6a701c568545dcfcb03fcb875f56beddc4" // value
	},
	"0x3714c00e73e618506e47cb5ba290bf07c1d5eb74b0fe528b77db3fc4befe9e0f": {
		"key": "0x5c41200c87be95dc093678dcbb6ba2fb7ed9efc87b733c296962c64942271896",
		"value": "0x1111111111111111111111111111111111111111111111111111111111111112"
	},
	"0xad58f05441d7cd7f026be5e48f515033965f8c34a818d086ae8c42853cc53455": {
		"key": "0x5c41200c87be95dc093678dcbb6ba2fb7ed9efc87b733c296962c64942271897",
		"value": "0x0000000000000000000000000000000000000000000000000000000000000000"
	},
	"0x7dbc4821e83fc5af6ab7a8a3555176b355ce4560371beaa0a313995f03dc3795": {
		"key": "0x5c41200c87be95dc093678dcbb6ba2fb7ed9efc87b733c296962c64942271898",
		"value": "0x0000000000000000000000005b38da6a701c568545dcfcb03fcb875f56beddc4"
	}
}
```

設定したnameは1ズレており、明らかに同じデータが2つ書き込まれている状態。`0x3714c.....9e0f`以降のアドレスは毎回同様のストレージに書き込まれており、未定義のwalletは何らか固定のslotに保存されていそう。

ドキュメントを読んでもなぜこういう動作になるのか分からなかったので、upsolve諦め...

- https://chocorusk.hatenablog.com/entry/2024/11/28/050300#blockchain-Trillion-Ether
- https://github.com/SECCON/SECCON_Beginners_CTF_2024/tree/main/misc/vote4b/files
- https://solidity-jp.readthedocs.io/ja/latest/miscellaneous.html#mappings-and-dynamic-arrays
