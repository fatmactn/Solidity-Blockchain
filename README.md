# Solidity-Blockchain

Herhangi bir conrtacta başlarken solidity versiyon aralığı belirtilir.
```js 
pragma solidity >= 0.7.0 < 0.9.0; 
``` 

<h4>Değişken/Fonksiyon Scope'ları</h4>
Private = Sadece contract için geçerlidir. <br>
Internal = Sadece çağırılan contract ya da contractı inherit alan contractlar tarafından çağırılır. <br>
External = Sadece extend edilen contractta çağırılabilir. <br>
Public = Her yerden erişim sağlanabilir. <br>

<h4>String Kullanımı</h4>
memory = RAM'e çok benzer, Solidity'de veri depolamak için geçici bir yerdir, string değişken tanımlamak ve çağırmak için memory kullanımı zorunludur.

```js
function sayHello() public view returns (string memory) {
    greetings; 
}

function changeGreetings(string memory change) public {
    greetings = change;
}
```

<h4>Enum Kullanımı</h4>

```js
enum frenchFriesSize {LARGE, MEDIUM, SMALL}
frenchFriesSize choice;
frenchFriesSize constant defaultChoice = frenchFriesSize.MEDIUM;
    
function setSmall() public {
    choice = frenchFriesSize.SMALL;
}
```
<h4>Mapping Kullanımı</h4>
Mapping, Structa veya diziye benzer - bir referans türüdür, verileri kaydetmenize ve belirttiğiniz bir anahtar eklemenize ve ardından bu bilgiyi daha sonra almanıza olanak tanır.
<br> Solidity'de bir mappingi yineleyemezsiniz - anahtarları bir dizide saklamanız gerekir ve boyut veremezsiniz.

```js
// key / value - key; string, uint ya da bool olabilir. - value herhangi bir şey olabilir.
mapping(address => uint) public myMap;
    
function getAddress(address _addr) public view returns (uint) {
   return myMap[_addr];
}
       
function set(address _addr, uint _i) public {
   myMap[_addr] = _i;
}
```

<h4>Ether Birimleri</h4>

Solidity'de yazılan tüm sayısal değerler(türü belirtilmediği sürece) wei olarak hesaplanır. ( 1000000000000000000 wei == 1 ether ) <br>
```js
assert(2 ether == 2000000000000000000 wei); //true
assert(1 minutes == 60 seconds); //true
assert(24 hours == 1440 minutes); //true
assert(1 days == 24 hours); //true
assert(1 weeks == 7 days); //true
       
assert(10 == 9 + 2); //false
```

<h4>Modifier</h4>

Fonksiyonu özelleştirmek ve clean code yapısını bozmamak adına(kod tekrarını önlemek) kullanılır.

```js
modifier onlyOwner {
  require(msg.sender == owner);
  _; //continue anlamına gelir
}

function changePrice(uint _price) public onlyOwner {
  price = _price;
}
```

<h4>View/Pure Fonksiyonları</h4>

View, return değerlerinin, durumunu değiştirmemelerini sağlar. (değerin sadece okunması) <br>
Pure, return hesaplamalarının durumun okunmamasını veya değiştirilmemesini sağlar. 


```js
function getValue() external view returns(uint) {
    // eth call 
    // value = 2;
    return value; 
}

function getNewValue() external pure returns(uint) {
    // eth call 
    // value = 2;
    return 3 + 3; 
}
```

<h4>Kriptoloji</h4>

Solidity klasik şifreleme yöntemlerini bize sunar. <br>
```js
//keccak256(bytes memory) returns (bytes32) 
//sha256(bytes memory) returns (bytes32)
//ripemd160(bytes memory) returns (bytes20) 

function randMod(uint range) external view returns(uint) {
    return uint(keccak256(abi.encodePacked(oracle.rand(), block.timestamp, block.difficulty, msg.sender))) % range;
}
```

<h4>Abstract ve Interface</h4>

Solidity abstract ve interface yapılarına izin vermektedir.

Abstract kullanımı
```js
abstract contract X {
    function y() public virtual;
}

contract Teacher is X {
    
    function y() public override{
        return "Abstract";
    }
}

```

Interface Kullanımı

```js
interface ICounter {
    function count() external view returns(uint);
    function increment() external;
}

contract MyContract {
    
    function incrementCounter(address _counter) external {
        ICounter(_counter).increment();
    }
    
    function getCount(address _counter) external view returns(uint) {
        return ICounter(_counter).count();
    }
    
}

```


<h4>Kütüphaneler</h4>

Kütüphaneler, contractlar gibidir ancak amaçları yeniden kullanılmaktır. Bir kütüphane diğer contractların çağırabileceği fonksiyonları içerir. <br>
Bir kütüphane inherit alınamaz ayrıca herhangi bir öğeyi inherit alamaz.

```js

library Search{
    function indexOf(uint[]  storage self, uint value) public view returns(uint) {
        for(uint i=0; i<self.length; i++) if(self[i] == value) return i;
    }
}

contract Test {
    uint[] data;
    constructor() public {
        data.push(1);
        data.push(2);
        data.push(3);
        data.push(4);
        data.push(5);
    }
    
    function isValuePresent(uint val) external view returns(uint) {
       uint value = val;
       uint index = Search.indexOf(data, value); 
       return index;
    }
}

```


