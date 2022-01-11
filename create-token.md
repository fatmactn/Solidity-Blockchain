# Mintable,  Burnable, Pausable, Blacklist ve Presale Özelliklerine Sahip Token Oluşturma

## Interface
Kontrat oluşturulurken solidity verssiyonu ve kullanılan lisans belirtilir.
```js
// SPDX-License-Identifier: MIT

pragma solidity >=0.5.0 <0.9.0;
```

Kontratta kullanılacak metotlar tanımlanır.
```js
interface ERC20Interface {
    function totalSupply() external view returns (uint);
    function balanceOf(address tokenOwner) external view returns (uint balance);
    function transfer(address to, uint tokens) external returns (bool success);
    
    function allowance(address tokenOwner, address spender) external view returns (uint remaining);
    function approve(address spender, uint tokens) external returns (bool success);
    function transferFrom(address from, address to, uint tokens) external returns (bool success);
    
    event Transfer(address indexed from, address indexed to, uint tokens);
    event Approval(address indexed tokenOwner, address indexed spender, uint tokens);
}
```
## Base Contract
Token oluşturmak için base kontrat oluşturulur. Tokenın adı, sembolü, decimal(0'dan sonraki basamak sayısı), supply değeri gibi temel değişkenleri tanımlanır.
```js
contract Cryptos is ERC20Interface {
    string public name = "AcademyChain";
    string public symbol = "AC";
    uint public decimals = 0;
    uint public override totalSupply;
}
```

Base kontrata constructor metot eklenir. Bu metotla tokenın oluşturucusu, total supply değeri belirlenir, oluşturucunun balance'ı total supply olarak atanır. <br>
Total Supply: Oluşturulabilecek maksimum token sayısı <br>
msg.sender(owner): Aktif olan kullanıcı cüzdan adresi <br>
balances: Kullanıcıların cüzdanındaki token miktarları
```js
    address public owner;
    mapping(address => uint) public balances;
    
    constructor() {
        totalSupply = 1000000;
        owner = msg.sender;
        balances[owner] = totalSupply;
    }
```

Sadece oluşturucunun erişebileceği metotlar için modifier yazılır.
```js
    modifier onlyOwner(){
        require(msg.sender == owner);
        _;
    }
```

Verilen adresin cüzdanındaki token miktarını veren get metodu yazılır. 
```js
    function balanceOf(address tokenOwner) public view override returns (uint balance) {
        return balances[tokenOwner];
    }
```

Tokenın satış işlemlerinin durdurulabilmesi ve devam ettirilebilmesi için gerekli değişken tanımlanıp set ve get metotları yazılır.
```js
     bool public pausedState = false;
     
     function paused() public onlyOwner {
        pausedState = true;
    }

     function removePaused() public onlyOwner {
        pausedState = false;
    }
    
     function getPaused() public onlyOwner returns view (bool){
        return pausedState;
    }   
```

Tokenda işlem yapmasını engellemek istediğimiz hesapları blackliste'e eklemek-çıkarmak için gerekli dizi, get ve set metotları yazılır.
```js
    mapping(address => bool) public isBlackListed;
    
    function addToBlackList(address addresses) public onlyOwner {
        isBlackListed[addresses] = true;
    }

    function removeFromBlackList(address account) external onlyOwner {
        isBlackListed[account] = false;
    }
    
    function isBlackList(address account) public view returns(bool) onlyOwner {
        return isBlackListed[account];
    }
```

Kontrat sahibinin başka bir hesaba token gönderimi için interface'de bulunan transfer metodu override edilir.
```js
    function transfer(address to, uint tokens) public virtual override returns(bool success) {
        require(pausedState, "All transactions paused");
        require(!isBlackListed[msg.sender] && !isBlackListed[to], "This address is blacklisted");
        require(balances[msg.sender] >= tokens);

        balances[to] += tokens;
        balances[msg.sender] -= tokens;
        emit Transfer(msg.sender, to, tokens);

        return true;

    }
```

Başka bir hesaba kendi cüzdanından token kullanma yetkisi vermek için allowance metodu override edilir.
```js
    mapping(address => mapping(address => uint)) allowed;

    function allowance(address tokenOwner, address spender) view public override returns(uint) {
        return allowed[tokenOwner][spender];
    }
```

Yetki verilen cüzdana token miktarı vermek için approve metodu override edilir.
```js
    function approve(address spender, uint tokens) public override returns(bool success) {
        require(balances[msg.sender] >= tokens);
        require(tokens > 0);

        allowed[msg.sender][spender] = tokens;

        emit Approval(msg.sender, spender, tokens);
        return true;
    }
```

Kontrat sahibi dışında satış işlemlerinin gerçekleşmesini sağlayan transferFrom metodu override edilir
```js
    function transferFrom(address from, address to, uint tokens) public virtual override returns (bool success) {
        require(pausedState, "All transactions paused");
        require(!isBlackListed[from] && !isBlackListed[to], "This address is blacklisted");
        require(allowed[from][to] >= tokens);
        require(balances[from] >= tokens);
        
        balances[from] -= tokens;
        balances[to] += tokens;
        allowed[from][to] -= tokens;

        return true;
    }
```

## Main Contract

Tokenı satışa açmak için ICO(initial coin offering) kontratı yazılır.
```js
contract CryptosICO is Cryptos{
    address public admin;
    address payable public deposit;
    uint tokenPrice = 0.001 ether; 
    uint public hardCap = 300 ether;
    uint public raisedAmount; 
    uint public saleStart = block.timestamp;
    uint public saleEnd = block.timestamp + 604800; 
    
    uint public tokenTradeStart = saleEnd + 604800;
    uint public maxInvestment = 5 ether;
    uint public minInvestment = 0.1 ether;
    
    enum State { beforeStart, running, afterEnd, halted} 
    State public icoState;
    
    constructor(address payable _deposit){
        deposit = _deposit; 
        admin = msg.sender; 
        icoState = State.beforeStart;
    }
}
```

```js       
    function halt() public onlyOwner{
        icoState = State.halted;
    }
    
    
    function resume() public onlyOwner{
        icoState = State.running;
    }
    
    
    function changeDepositAddress(address payable newDeposit) public onlyOwner{
        deposit = newDeposit;
    }
    
    
    function getCurrentState() public view returns(State) {
        if(icoState == State.halted){
            return State.halted;
        }else if(block.timestamp < saleStart) {
            return State.beforeStart;
        }else if(block.timestamp >= saleStart && block.timestamp <= saleEnd) {
            return State.running;
        }else {
            return State.afterEnd;
        }
    }


    event Invest(address investor, uint value, uint tokens);
    
    function invest() payable public returns(bool){ 
        icoState = getCurrentState();
        require(icoState == State.running);
        require(msg.value >= minInvestment && msg.value <= maxInvestment);
        
        raisedAmount += msg.value;
        require(raisedAmount <= hardCap);
        
        uint tokens = msg.value / tokenPrice;

        balances[msg.sender] += tokens;
        balances[owner] -= tokens; 
        deposit.transfer(msg.value); 
        
        emit Invest(msg.sender, msg.value, tokens);
        
        return true;
    }
   
   
   receive () payable external{
        invest();
    }

    function transfer(address to, uint tokens) public override returns(bool success) {
        require(block.timestamp > tokenTradeStart);
        Cryptos.transfer(to, tokens);
        return true;
    }

    function transferFrom(address from, address to, uint tokens) public override returns (bool success) {
        require(block.timestamp > tokenTradeStart);
        Cryptos.transferFrom(from, to, tokens);
        return true;
    }

    function burn() public returns(bool) {
        icoState = getCurrentState();
        require(icoState == State.afterEnd);
        balances[owner] = 0;
        return true;
    }  
  
```
Bu <a href="https://wizard.openzeppelin.com/">link</a> ile hızlıca oluşturabilirsiniz
