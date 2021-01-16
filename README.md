# วิธีการออกเหรียญTreeCoin และ ขาย ICO บน Ethereum Smart Contract
1.	ติดตั้ง NodeJS โดยไปดูวิธีติดตั้งได้เลยที่ https://nodejs.org/en/
2.  สำหรับ Linux ทำตามนี้จะไม่ต้อง sudo เพื่อติดตั้งโปรแกรมผ่าน npm
3.  ติดตั้งตัว Ethereum จำลองและตัว client สำหรับทดสอบด้วยคำสั่ง
    ```npm install -g ganache-cli```

    ติดตั้ง Truffle เฟรมเวิร์คสำหรับพัฒนาภาษา Solidity (ภาษาหลักที่ใช้เขียน smart contract) ดังนี้
    
    ```npm install -g truffle ```
    
 4. สร้างโฟลเดอร์ที่จะเก็บ smart contract และย้ายเข้าไปใช้งาน โดยชื่อเหรียญที่ผมจะออกในตัวอย่างนี้จะใช้ชื่อว่า TreeCoin ตัวย่อ Tree ล่ะกันจะได้ดูน่ารักๆ
   ```mkdir Treecoin && cd Treecoin```
   
 5  สั่งให้ truffle สร้างไฟล์เริ่มต้นของโปรเจค
 
```truffle init```
     
เนื่องจากการเขียน smart contract นั้นยากและถ้าเราเริ่มเขียนเองทั้งหมดอาจจะเกิดข้อผิดพลาดได้ง่ายๆ เราก็เลยต้องหาโค้ดที่เขียนและทดสอบออกมาเป็นอย่างดีแล้วมาเป็นตัวตั้ง ในที่นี้ผมจะใช้ OpenZeppelin ที่ค่อนข้างดีกว่าเขียนเองหมดอย่างแน่นอน โดยติดตั้งดังนี้

```npm init -y```

```npm install --save @openzeppelin/contracts@v2.5 web3 ```


6) เขียน smart contract ของเหรียญก่อนเลย โดยสร้างไฟล์ชื่อ Treecoin.sol ที่โฟลเดอร์ contracts แล้วใส่โค้ดดังนี้

``` pragma solidity >=0.4.21 <0.7.0;

import "@openzeppelin/contracts/token/ERC20/ERC20Mintable.sol";
import "@openzeppelin/contracts/ownership/Ownable.sol";


contract TreeCoin is ERC20Mintable {

    string public constant name = "Tree COIN"; // solium-disable-line uppercase
    string public constant symbol = "Tree"; // solium-disable-line uppercase
    uint8 public constant decimals = 18; // solium-disable-line uppercase

}
```


เป็นอันว่าได้เหรียญแล้วโดยการสืบทอดคุณสมบัติของ Contract MintableToken ที่ OpenZeppelin เขียนเอาไว้นั้นเอง เอาจริงๆ 
แล้ว contract ก็เหมือน Class ในภาษาเชิงวัตถุอื่นๆ นั่นแหละ Contract MintableToken คือ Token ที่สามารถทำเหรียญขึ้นมาเพิ่มเติมมาเพิ่มเรื่อยๆ นั้นเอง จึงไม่ได้จำกัดจำนวนเหรียญเอาไว้
7) เขียน Contract เพื่อขายเหรียญนั่นเอง โดยการสร้างไฟล์ชื่อ TreeCoinCrowdSale ที่โฟลเดอร์ contracts แล้วใส่โค้ดดังนี้

```pragma solidity >=0.4.21 <0.7.0;

import "@openzeppelin/contracts/crowdsale/validation/CappedCrowdsale.sol";
import "@openzeppelin/contracts/crowdsale/distribution/RefundableCrowdsale.sol";
import "@openzeppelin/contracts/crowdsale/emission/MintedCrowdsale.sol";

contract BNK48CoinCrowdSale is CappedCrowdsale, RefundableCrowdsale, MintedCrowdsale
{

    constructor(uint256 _openingTime, uint256 _closingTime, uint256 _rate, address payable _wallet, uint256 _cap, IERC20 _token, uint256 _goal) public
    Crowdsale(_rate, _wallet, _token)
    CappedCrowdsale(_cap)
    TimedCrowdsale(_openingTime, _closingTime)
    RefundableCrowdsale(_goal)
    {
        require(_goal <= _cap);
    }
}
```

Contract นี้ก็จะสืบทอดความสามารถของ Contract ต่างๆ ที่เกี่ยวของมาใส่ TreeCoinCrowdSale ไม่ว่าจะเป็นการจำกัดเวลาซื้อขายจาก TimedCrowdsale, การจำกัดจำนวนเงินสูงสุดที่จะระดมทุนด้วย CappedCrowdsale, การคืนเงินถ้าระดมทุนไม่ถึงเป้าจาก RefundableCrowdsale รวมกันจนเป็นสิ่งที่เราต้องการได้ถ้าต้องการเพิ่มหรือตัดออกฟีเจอร์ออกก็ทำได้เลยจากตรงนี้
8) การสร้าง script สำหรับติดตั้ง smart contract เข้าไปใน Ethereum Blockchain นั่นเอง โดยการสร้างไฟล์ 2_deploy_contracts.js ในโฟล์เดอร์ migrations ดังนี้

```const TreeCoinCrowdSale = artifacts.require("TreeCoinCrowdSale");
const TreeCoin = artifacts.require("TreeCoin");

const Web3 = require('web3')

function ether(n) {
  return new Web3.utils.BN(Web3.utils.toWei(n, 'ether'));
}

module.exports = function (deployer, network, accounts) {
  const startTime = new Web3.utils.BN(Math.floor(new Date().getTime() / 1000)); // Now
  const endTime = new Web3.utils.BN(Math.floor(new Date(2021, 9, 9, 9, 9, 9, 0).getTime() / 1000)); // Sale Stop at 9 Sep 2021 @09:09:09
  const rate = new Web3.utils.BN(20000); // At 20,000 Token/ETH
  const wallet = accounts[0];
  const goal = ether('5000');
  const cap = ether('10000');

  let token, crowdsale;
  deployer.then(function () {
    return TreeCoin.new({from: wallet});
  }).then(function (instance) {
    token = instance;
    return TreeCoinCrowdSale.new(startTime, endTime, rate, wallet, cap, token.address, goal);
  }).then(function (instance) {
    crowdsale = instance;
    token.addMinter(crowdsale.address);
    console.log('Token address: ', token.address);
    console.log('Crowdsale address: ', crowdsale.address);
    return true;
  });
};
```


ผมได้ตั้งไว้ว่าจะระดมทุนขั้นต่ำ 5,000 ETH และสูงสุด 10,000 ETH ด้วยราคาขาย 20,000 Token ต่อ 1 ETH เริ่มขายจากปัจจุบันไปจนถึง วันที่ 9 กันยายน 2021 เวลา 09:09:09 UTC ไฟล์นี้สำคัญมาก โดยมันจะไปสั่งสร้าง token ก่อนและก็สร้าง crowsale contract สุดท้ายให้สิทธิ์ address ของ crowdsale เป็นคนสร้างเหรียญได้ตรง token.addMinter(crowdsale.address);
9) ลอง compile โค้ดว่าคอมไฟล์ผ่านไหมด้วยคำสั่ง (ถ้าไม่ผ่านก็แก้ตามมันบอก)

```truffle compile```

10) แก้คอนฟิกไฟล์สำหรับการ migrate (deploy) contract ที่ไฟล์ truffle.js ดังนี้

```module.exports = {
    networks: {
        development: {
            host: "127.0.0.1",
            port: 7545,
            network_id: "*" // match any network
        },
        live: {
            host: "88.88.88.88", // Random IP for example purposes (do not use)
            port: 80,
            network_id: 1,        // Ethereum public network
            // optional config values:
            // gas
            // gasPrice
            // from - default address to use for any transaction Truffle makes during migrations
            // provider - web3 provider instance Truffle should use to talk to the Ethereum network.
            //          - function that returns a web3 provider instance (see below.)
            //          - if specified, host and port are ignored.
        },
    }
};
```

โดยคอนฟิก development จะชี้ไปที่เครื่องตัวเราเอง เป็นเน็ตเวิร์ค Ethereum ที่เราอยู่บนเครื่องเราคนเดียว
11) รันเน็ตเวิร์ค Ethereum ที่เราจะทดลอง deploy contract ลงไป เปิด terminal ใหม่และรันคำสั่งดังนี้

```ganache-cli -u 0```

เปิดค้างเอาไว้ จะได้ผลลัพธ์ดังนี้

![image](https://user-images.githubusercontent.com/48530299/104823993-ebcbeb00-5880-11eb-911a-b8ed0346286b.png)

มันจะใช้ user 0 ทำงาน และเราต้องจดหรือก็อปปี้ Mnemonic เอาไว้ ในของผมจะเป็น absent price plunge chair bone rely exotic hospital pudding rare ranch xxxx ของคุณเองจะไม่เหมือนของผม มันจะเปลี่ยนไปเรื่อยๆ ถ้ารันใหม่

![image](https://user-images.githubusercontent.com/48530299/104824118-ce4b5100-5881-11eb-91d1-bae18985c2de.png)

12) จากนั้นก็เริ่ม migrate contract เข้าไปใน ganache ด้วยคำสั่ง

```truffle migrate --reset```

จะได้ผลลัพธ์ดังนี้







Run
```
npm install -g ganache-cli
npm install -g truffle
mkdir Treecoin-ico && cd Treecoin-ico
truffle init
npm init -Y
npm install --save @openzeppelin/contracts@v2.5 web3
// open Ganache
truffle migrate --reset
```
![image](https://user-images.githubusercontent.com/48530299/104815565-e2725c80-5847-11eb-871a-639dda32f1ef.png)
![image](https://user-images.githubusercontent.com/48530299/104815625-45fc8a00-5848-11eb-8c84-262b8a193d2f.png)
