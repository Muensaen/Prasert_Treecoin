# วิธีการออกเหรียญTreeCoin และ ขาย ICO บน Ethereum Smart Contract
1.	ติดตั้ง NodeJS โดยไปดูวิธีติดตั้งได้เลยที่ https://nodejs.org/en/
2.  สำหรับ Linux ทำตามนี้จะไม่ต้อง sudo เพื่อติดตั้งโปรแกรมผ่าน npm
3.  ติดตั้งตัว Ethereum จำลองและตัว client สำหรับทดสอบด้วยคำสั่ง
    ```npm install -g ganache-cli```

    ติดตั้ง Truffle เฟรมเวิร์คสำหรับพัฒนาภาษา Solidity (ภาษาหลักที่ใช้เขียน smart contract) ดังนี้
    ```npm install -g truffle ```
 4. สร้างโฟลเดอร์ที่จะเก็บ smart contract และย้ายเข้าไปใช้งาน โดยชื่อเหรียญที่ผมจะออกในตัวอย่างนี้จะใช้ชื่อว่า TreeCoin ตัวย่อ Tree ล่ะกันจะได้ดูน่ารักๆ
   ``` mkdir Treecoin && cd Treecoin```





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
