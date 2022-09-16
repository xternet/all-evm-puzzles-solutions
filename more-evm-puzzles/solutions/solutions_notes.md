## 1. Satisfy "value**callData = 71" to jump to 0x47 (71):
</br>```value=71```
</br>```calldata=0x01```

## 2. Deploy contract that returns data with size 0x0a (10 bytes):
</br>```600A80600080("0x0a size with no offsets")39("copy code")6000F3("return")```
</br>```0x600A80600080396000F3```

## 3. Deploy contract that has "0xAA" in the 5th storage slot:
</br>```64("push")60AA600555("0xAA in 5th storage slot")600052("into memory")600A("size: 10 bytes")601B("offset to ignore leading 0s")F3("return")```
</br>```calldata=0x6460AA600555600052600A601BF3```

## 4. Make call to send 2 wei to addr 0:
</br>```calldata=0x600080808060("call args")02("2 wei")81("arg: addr")5A("arg: gas")F1("call")```

## 5. Provide calldata that "size>0x20 && size=0x40-0x03=0x3d":
</br>```calldata=0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000```

## 6. Do overflow "0xff..f0" + 0x11 (17) = 1 (desired):
</br>```value=17```

## 7. Break from the loop by iterating "value" times && consuming 0xa6 (166) gas:
</br>```1: ffffffffadf5-ffffffffadd0 = 0x25 (37) //1st loop```
</br>```2: ffffffffadf5-ffffffffada5 = 0x50 (80) //2nd loop; one_loop_gas = 80-37 = 43```
</br>```desired = 166/43 + 1 = 4```
</br>```value=4```

## 8. BUG?
</br>```calldata=0x00```

## 9. Brute-force to get desired "value"<=255 (0xff) e.g. in Remix:
```solidity
pragma solidity 0.8.7;
contract MoreEVMPuzzles {
    function find() public view returns(uint) {
        for(uint i=0;i<1000;i++){
            bytes32 valueToShift = keccak256(abi.encode(i));
            bytes32 shiftedValue = valueToShift >> 0xf8;
            if (uint(shiftedValue)==uint(0xa8)){
                return i; //47
            }
        }
        return 0;
    }
}
```
</br>```value=47```

## 10. In single calldata provide 2x 32 bytes. 1st to discard "f0...f0" AND 2nd to match "ab...ab":
</br>```calldata=0x0000000000000000000000000000000000000000000000000000000000000000ABABABABABABABABABABABABABABABABABABABABABABABABABABABABABABABAB```