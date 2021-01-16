# วิธีการออกเหรียญTreeCoin และ ขาย ICO บน Ethereum Smart Contract
1.	ติดตั้ง NodeJS โดยไปดูวิธีติดตั้งได้เลยที่ https://nodejs.org/en/
2.  สำหรับ Linux ทำตามนี้จะไม่ต้อง sudo เพื่อติดตั้งโปรแกรมผ่าน npm
3.  ติดตั้งตัว Ethereum จำลองและตัว client สำหรับทดสอบด้วยคำสั่ง
    ```npm install -g ganache-cli```

    ติดตั้ง Truffle เฟรมเวิร์คสำหรับพัฒนาภาษา Solidity (ภาษาหลักที่ใช้เขียน smart contract) ดังนี้
    ```npm install -g truffle ```
 4. สร้างโฟลเดอร์ที่จะเก็บ smart contract และย้ายเข้าไปใช้งาน โดยชื่อเหรียญที่ผมจะออกในตัวอย่างนี้จะใช้ชื่อว่า TreeCoin ตัวย่อ Tree ล่ะกันจะได้ดูน่ารักๆ
   ``` mkdir Treecoin && cd Treecoin```
 5  สั่งให้ truffle สร้างไฟล์เริ่มต้นของโปรเจค
     ```truffle init```
เนื่องจากการเขียน smart contract นั้นยากและถ้าเราเริ่มเขียนเองทั้งหมดอาจจะเกิดข้อผิดพลาดได้ง่ายๆ เราก็เลยต้องหาโค้ดที่เขียนและทดสอบออกมาเป็นอย่างดีแล้วมาเป็นตัวตั้ง ในที่นี้ผมจะใช้ OpenZeppelin ที่ค่อนข้างดีกว่าเขียนเองหมดอย่างแน่นอน โดยติดตั้งดังนี้
npm init -y
npm install --save @openzeppelin/contracts@v2.5 web3
6) เขียน smart contract ของเหรียญก่อนเลย โดยสร้างไฟล์ชื่อ Treecoin.sol ที่โฟลเดอร์ contracts แล้วใส่โค้ดดังนี้
เป็นอันว่าได้เหรียญแล้วโดยการสืบทอดคุณสมบัติของ Contract MintableToken ที่ OpenZeppelin เขียนเอาไว้นั้นเอง เอาจริงๆ 
แล้ว contract ก็เหมือน Class ในภาษาเชิงวัตถุอื่นๆ นั่นแหละ Contract MintableToken คือ Token ที่สามารถทำเหรียญขึ้นมาเพิ่มเติมมาเพิ่มเรื่อยๆ นั้นเอง จึงไม่ได้จำกัดจำนวนเหรียญเอาไว้
7) เขียน Contract เพื่อขายเหรียญนั่นเอง โดยการสร้างไฟล์ชื่อ Treecoin.sol ที่โฟลเดอร์ contracts แล้วใส่โค้ดดังนี้
 ```pragma solidity >=0.4.21 <0.7.0;

import "@openzeppelin/contracts/token/ERC20/ERC20Mintable.sol";
import "@openzeppelin/contracts/ownership/Ownable.sol";


contract TreeCoin is ERC20Mintable {

    string public constant name = "Tree COIN"; // solium-disable-line uppercase
    string public constant symbol = "Tree"; // solium-disable-line uppercase
    uint8 public constant decimals = 18; // solium-disable-line uppercase

} ```







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
