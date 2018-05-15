---
title: 说说eosio.token合约
date: 2018-05-15 10:55:39
tags: [Blockchain]
categories: 攻城狮那些事儿
---

# 一、eosio.token合约的请用
- 使用账户 *hily* 部署系统的 *eosio.token* 合约
> \$ cleos set contract hily build/contracts/eosio.token -p hily

<!--more-->

- 使用账户 *hily* 创建 *HH* 币，发行总量100000个
> \$ cleos push action hily create '["hily","100000.0000 HH",1,1,1]' -p hily

- 向账户 *hily* 空投100个 *HH*
> \$ cleos push action hily issue '["hily","100.0000 HH","memo"]' -p hily

- 查询账户 *hily* 的余额
> \$ cleos get table hily hily accounts

- 从账户 *hily* 转账10 *HH* 给 *jay*
> \$ cleos  push action hily transfer '["hily","jay","10.0000 HH","memo"]' -p hily
- 查看账户 *jay* 的余额
> \$ cleos get table hily jay accounts

- 使用 *hily* 部署 *token* 合约后，可以像上面一样， *hily* 自己创建一个 *HH* 币，同时账户 *jay* 也可以创建他的 *JAY* 币
> \$ cleos push action hily create '["jay","10000.0000 JAY",1,1,1]' -p hily

- 空投 *jay* 的 *JAY* 币到 *jack* 时，要注意命令中各个账户的使用
> \$ cleos push action hily issue '["jack","20.0000 JAY","memo"]' -p jay

- 这时查看 *jack* 账户时，可以看到拥有的 *JAY* 币余额
> \$ cleos get table hily jack accounts

- 如果 *jack* 除了拥有 *JAY* 币外，还有拥有其他币，则上面的命令的结果将会显示 *jack* 不同的币的余额。

# 二、问题
- 比如上面我们将会发行10000个 *JAY* 币，并且空投了20个到 *jack* 账户，那么我们如何获得 *JAY* 币的剩余发行量（10000 - 20 = 9980）？

# 三、eosio.token源码分析
构造函数：使用 *hily* 账户部署 *token* 合约时（ `cleos set contract hily build/contracts/eosio.token -p hily` ）， *hily* 将传给 *self* ，而该 *self* 传到父类 *contract* 中时，将会存到到父类的 *_self* 属性中。
```
token( account_name self ):contract(self){}
```

- - -

create函数：创建加密币，对应到这个命令 `cleos push action hily create '["hily","100000.0000 HH",1,1,1]' -p hily` 。
```
void token::create( account_name issuer,
                    asset        maximum_supply,
                    uint8_t      issuer_can_freeze,
                    uint8_t      issuer_can_recall,
                    uint8_t      issuer_can_whitelist )
{
    // 这里的 _self 就是部署合约时的 hily ，所以上面的 create 命令需要加上 "-p hily"
    require_auth( _self );

    auto sym = maximum_supply.symbol;
    eosio_assert( sym.is_valid(), "invalid symbol name" ); // 这里检查代币符合是否合法，比如上面的"HH"
    eosio_assert( maximum_supply.is_valid(), "invalid supply");
    eosio_assert( maximum_supply.amount > 0, "max-supply must be positive");  // 这里检查代币的数量必须大于0，比如上面是100000.0000

    stats statstable( _self, sym.name() );
    auto existing = statstable.find( sym.name() );  // 根据代币的符号，比如上面命令中的"HH"，在合约中进行查找
    eosio_assert( existing == statstable.end(), "token with symbol already exists" );  // 保证在这个合约中之前没有创建过这样的代币("HH")

    // 把参数存放到statstable中
    statstable.emplace( _self, [&]( auto& s ) {
       s.supply.symbol = maximum_supply.symbol;
       s.max_supply    = maximum_supply;
       s.issuer        = issuer;
       s.can_freeze    = issuer_can_freeze;
       s.can_recall    = issuer_can_recall;
       s.can_whitelist = issuer_can_whitelist;
    });
}
```


- - -

issue：空投或者发币`cleos push action hily issue ‘[“jack“,”20.0000 JAY”,”memo”]’ -p jay`
```
void token::issue( account_name to, asset quantity, string memo )
{
    print( "issue" );
    auto sym = quantity.symbol;
    eosio_assert( sym.is_valid(), "invalid symbol name" );  // 首先确保正确的代币

    auto sym_name = sym.name();
    stats statstable( _self, sym_name );
    auto existing = statstable.find( sym_name );
    eosio_assert( existing != statstable.end(), "token with symbol does not exist, create token before issue" );  // 确保该代币，比如"JAY"，是存在的
    const auto& st = *existing;

    require_auth( st.issuer );  // 通过代币"JAY"找到对应的发行者是"jay"，所以上面的issue需要加上"-p jay"
    eosio_assert( quantity.is_valid(), "invalid quantity" );
    eosio_assert( quantity.amount > 0, "must issue positive quantity" );

    eosio_assert( quantity.symbol == st.supply.symbol, "symbol precision mismatch" );
    eosio_assert( quantity.amount <= st.max_supply.amount - st.supply.amount, "quantity exceeds available supply");  // 确保空投的数量不能超过剩余的可发行量

    statstable.modify( st, 0, [&]( auto& s ) {
       s.supply += quantity;
    });

    // 这里有点意思，你会发现在这里jay账户会增加20 JAY，虽然上面的命令是空投20 JAY到jack账户
    add_balance( st.issuer, quantity, st, st.issuer );

    // 到这里的时候，因为to是jack，st.issue是jay，所以不相等
    if( to != st.issuer ) {
       // 这里call transfer的时候，就会先call sub_balance然后call sub_balance
       // 实作就是把jay账户减去20（因为上面加了20到jay，而实际需要加到jack的），然后把jack账户增加20 JAY
       SEND_INLINE_ACTION( *this, transfer, {st.issuer,N(active)}, {st.issuer, to, quantity, memo} );
    }
}
```

- - -

transfer：转账`cleos push action hily transfer ‘[“jay”,”jack”,”10.0000 HH”,”memo”]’ -p jay`
```
void token::transfer( account_name from,
                      account_name to,
                      asset        quantity,
                      string       /*memo*/ )
{
    print( "transfer from ", eosio::name{from}, " to ", eosio::name{to}, " ", quantity, "\n" );
    eosio_assert( from != to, "cannot transfer to self" );  // 杜绝自己给自己转账，每个人都这么干将会造成EOS拥堵
    require_auth( from );  // 在这里就可以知道上面的命令为什么加上"-p jay"了
    eosio_assert( is_account( to ), "to account does not exist");  // 检查账户合法性
    auto sym = quantity.symbol.name();
    stats statstable( _self, sym );
    const auto& st = statstable.get( sym );

    require_recipient( from );
    require_recipient( to );

    // 检查转账的数量和代币正常性
    eosio_assert( quantity.is_valid(), "invalid quantity" );
    eosio_assert( quantity.amount > 0, "must transfer positive quantity" );
    eosio_assert( quantity.symbol == st.supply.symbol, "symbol precision mismatch" );

    // 从from账户中减去指定的币额并加到to账户
    sub_balance( from, quantity, st );
    add_balance( to, quantity, st, from );
}
```
