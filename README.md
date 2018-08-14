# TronInfura
这个工程使用SpringBoot开发，提供查询接口，把Java-tron写到Mongdb中的智能合约事件数据展示出来，用户可以轮询智能合约的事件。
JavaTron工程参见：
```
https://github.com/tronprotocol/java-tron
test_ev2分支
核心逻辑请参考：org.tron.core.db.Manager#sendEventLog
```


## 有以下四种查询方式
1、根据合约地址进行查询
<br>
curl http://52.44.75.99:18889/event/contract/TMbeJmpG1nrvMJYQeqSXyvWxg6qcqf7EwL

2、根据合约地址以及事件名称进行查询
<br>
curl http://52.44.75.99:18889/event/contract/TMbeJmpG1nrvMJYQeqSXyvWxg6qcqf7EwL/Notify

3、根据合约地址、事件名称以及所在的区块高度进行查询
<br>
curl http://52.44.75.99:18889/event/contract/TMbeJmpG1nrvMJYQeqSXyvWxg6qcqf7EwL/Notify/300

4、、根据交易ID进行查询
<br>
curl http://52.44.75.99:18889/event/transaction/1b892ca7c885cf5038d2c100850a80ec88295769369c8b0cfa00331234facd3c

原始合约代码
```
pragma solidity ^0.4.2;

contract Fibonacci {

    event Notify(uint input, uint result);

    function fibonacci(uint number) constant returns(uint result) {
        if (number == 0) return 0;
        else if (number == 1) return 1;
        else return Fibonacci.fibonacci(number - 1) + Fibonacci.fibonacci(number - 2);
    }

    function fibonacciNotify(uint number) returns(uint result) {
        result = fibonacci(number);
        Notify(number, result);
    }
}
```
调用fibonacciNotify后，会触发Notify事件，
```
triggercontract 《智能合约地址 TMbeJmpG1nrvMJYQeqSXyvWxg6qcqf7EwL》 fibonacciNotify(uint256) 7 false 1000000 0000000000000000000000000000000000000000000000000000000000000000
```

返回数据示例
```
[
  {
    "block_number": 300,
    "block_timestamp": 1534237563000,
    "contract_address": "TMbeJmpG1nrvMJYQeqSXyvWxg6qcqf7EwL",
    "event_name": "Notify",
    "result": [
      "7",
      "13"
    ],
    "transaction_id": "1b892ca7c885cf5038d2c100850a80ec88295769369c8b0cfa00331234facd3c"
  }
]
```
数据格式说明：
```
block_number:事件所在的区块高度
block_timestamp:事件所在的区块时间戳
contract_address:合约地址
event_name:事件名
result:事件参数
transaction_id:交易ID
```


## 编译
```
mvn package
```

## 运行
```
cd target
nohup java -jar infura-0.0.1-SNAPSHOT.jar &
```
