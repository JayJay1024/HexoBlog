---
title: eosio的multi_index
date: 2018-06-05 22:26:46
tags: [Blockchain]
categories: 攻城狮那些事儿
---

## 概述
1. multi\_index是eosio上的数据库管理接口，通过eosio::multi\_index智能合约能够写入、读取和修改eosio数据库的数据
2. multi\_index在eosio中的位置：eos/contracts/eosiolib/multi\_index.hpp
<!--more-->
3. eosio::multi\_index来源于boost库的boost::multi_index
4. eosio::multi\_index在概念上和传统数据库的“表(table)”类似，数据“行(rows)”是独立的对象（通常是class对象或struct对象），数据“列(columns)”是对象的成员属性（class或struct的成员属性）
5. eosio::multi\_index提供和传统数据库的“键(key)”类似的成员属性，用于快速查找对象
6. eosio::multi\_index支持主键(primary key)，但必须是唯一的无符号64位整型(uint64\_t)
7. eosio::multi\_index按主键排序时，使用升序
8. eosio::multi\_index允许使用自定义函数作为索引，但它的返回值是受限制的，只能是支持的键类型
9. multi\_index表允许多索引排序，最多可以使用16个二级索引
10. 二级索引作为multi\_index表构造函数的一部分创建，不支持直接构建
11. multi\_index迭代器可以双向迭代，即const\_iterator或const\_reverse\_iterator

## 创建multi\_index表
1. 使用C++类(class)或结构体(struct)定义对象
2. 在class或struct中，定义一个const成员函数：primary\_key()，返回uint64\_t类型的主键值
3. 确定二级索引（最多支持16个），二级索引不局限于uint64\_t，它支持更多类型
```
二级索引支持的键类型：
    idx64：64位无符号整型键
    idx128：128位无符号整型键
    idx256：256位固定大小字典键
    idx_double：双精度浮点键
    idx_long_double：四倍精度浮点键
```
4. 为每个二级索引定义extractor，即一个函数，用于从Multi-Index表的函数中获取键，这个函数会被indexed_by（后面会讲）用到
5. 一个完整的multi\_index表定义如下：
```
//定义address表，i64表示索引使用默认的uint64_t类型

//@abi table address i64
struct address {
    uint64_t account;
    string name;
    uint64_t phone;
    uint64_t liked;

    //定义address表的主键
    uint64_t primary_key() const { return account; }
    //定义extractor，二级索引是phone
    uint64_t get_phone() const {return phone; }

    //序列化
    EOSLIB_SERIALIZE(address, (account)(name)(phone)(liked))
```

## 使用multi\_index表
### （一）.实例化
```
// 第一个参数是表名（即address），第二个参数是表对象的类型（address），其余为可变参数Indices（二级索引），数量不能超过16个
typedef eosio::multi_index< N(address), address, indexed_by<N(phone), const_mem_fun<address, uint64_t, &address::get_phone>>> address_index;

// 构造函数，有两个参数uint64_t类型参数：code, scope
// code: 拥有这张multi_index表的账户，该账户拥有对合约数据的读写权限
// scope: code层级内的范围标识符
address_index addresses(_self, _self);
```

### （二）.表的操作
###### 1. emplace
* 添加一个新对象（row）到表中
`const_iterator emplace( unit64_t payer, Lambda&& constructor )`
* 参数
payer：为新对象使用的存储付费的账户
constructor：lambda函数，可以让新创建的对象就地初始化
* 返回值
返回一个新创建的对象的主键迭代器
* 前置条件
payer是被当前Action授权的有效账户，允许为使用存储付费
* 操作结果
带有唯一主键的新对象在multi-index表中被创建；
这个对象会被序列化，然后写入表中；
如果表不存在，则创建表。
二级索引被更新，用以引用新添加的对象；
如果二级索引表不存在，则创建它们。
payer为创建新对象所使用的存储付费；
如果multi-index表和二级索引表需要被创建，则payer为表的创建付费。
* 异常
当前接收者（multi_index的code参数）不是表的拥有者时，抛出异常

###### 2. erase
* 使用主键从表中删除现有对象（两种形式）
`const_iterator erase( const_iterator itr )`
`void erase( const object_type& obj )`
* 参数
itr：指向待删除对象的迭代器
obj：待删除对象的引用
* 返回值
使用itr查找对象时，返回被删除对象之后的对象的指针
* 操作结果
对象从表中删除，相关的存储被回收；
表相关的二级索引被更新；
退还被删除对象的payer所支付的存储费用和相关费用。
* 异常
待删除对象不存在时、Action无权修改表数据时、给定迭代器无效时，抛出异常

###### 3. modify
* 修改表中已存在的对象（两种形式）
`void modify( const_iterator itr, uint64_t payer, Lambda&& updater )`
`void modify( const object_type &obj, uint64_t payer, Lambda&& updater )`
* 参数
itr：指向待更新对象的迭代器
obj：待更新对象的引用
payer：为更新数据付费的账户，为0表示更新数据的payer和创建时的payer相同
updater：用于更新目标对象的lambda函数
* 前置条件
itr指向的对象，或obj引用的对象是存在的
payer是被当前Action授权的有效账户，允许为使用存储付费
* 操作结果
更新后的对象被序列化，然后替换表中的现有对象；
二级索引被更新，被更新对象的主键不变。
payer为更新对象所使用的存储付费；
如果payer和该对象现有的payer相同，只需要为现有对象和更新对象不同的部分付费，如果差额为负，还会退还费用；
如果payer和该对象现有的payer不同，则会退还费用给现有的payer。
* 异常
无效的前提条件下调用，会抛出异常，并中止执行
当前接收者（multi_index的code参数）不是表的拥有者时，抛出异常

###### 4. get
- 使用主键从表中查询已存在的对象
`const object_type& get( uint64_t primary ) const`
* 参数
primary：要查询对象的主键值
* 返回值
包含指定主键的对象的常量引用
* 异常
没有任何对象与给定的主键匹配时，抛出异常

###### 5. find
* 使用主键从表中查询已存在的对象
`const_iterator find( uint64_t primary ) const`
* 参数
primary：要查询对象的主键值
* 返回值
返回一个查询到的对象的迭代器
如果没有查询到指定对象，返回一个end迭代器

### （三）.成员访问
1. 获取拥有主表的账户名
`uint64_t get_code() const`

2. 在code下的范围id（scope id），在该范围内可以找到期望的主表实例
`uint64_t get_scope() const`

### （四）. 不支持的C++特性
- eosio::multi\_index不支持拷贝构造函数（Copy constructor）
- eosio::multi\_index不支持赋值运算符（Assignment operator）

### （五）.迭代器
multi\_index迭代器遵循C++迭代器模式，所有迭代器都是双向迭代，即const\_iterator或const\_reverse\_iterator。
迭代器可以被间接引用，以提供对multi\_index表中对象的访问。
1. begin & cbegin
返回指向对象类型的、从最小主键值开始的迭代器
`const_iterator begin() const`
`const_iterator cbegin() const`

2. end & cend
返回指向虚拟行的迭代器，代表刚刚过去的最后一行，不能被间接引用；
可以向后推进，不能向前推进。
`const_iterator end() const`
`const_iterator end() const`

3. rbegin & crbegin
返回和begin/cbegin类似的，但反向的迭代器
`const_iterator rbegin() const`
`const_iterator crbegin() const`

4. rend & crend
返回和end/cend类似的，但反向的迭代器
`const_iterator rend() const`
`const_iterator crend() const`

5. lower\_bound
查找大于等于给定主键值的对象
`const_iterator lower_bound( uint64_t primary ) const`

6. upper\_bound
查找大于给定主键值的对象
`const_iterator upper_bound( uint64_t primary ) const`

7. get\_index
返回一个适当类型的二级索引
`secondary_index get_index<IndexName>()`
`secondary_index get_index<IndexName>() const`

8. iterator\_to
返回给定对象的迭代器
`const_iterator iterator_to( const object_type& obj ) const`

9. indexed\_by
indexed\_by结构体用于实例化multi\_index表的索引
indexed_by在multi_index.hpp中的定义如下：
```
// 参数IndexName是索引的名称，这个名称是base32编码后的64位整数，且需要符合EOS命名规范：
//     1. 最多13个字符，前12个字符只能是小写字母、0-5、“.”
//     2. 如果有第13个字符，则只能是小写字母a-p、“.”
// 参数Extractor是一个函数，用于从multi_index表的函数中获取键，返回一个二级索引类型或二级索引类型的引用
template<uint64_t IndexName, typename Extractor>
struct indexed_by {
   enum constants { index_name   = IndexName };
   typedef Extractor secondary_extractor_type;
};

// 推荐使用eosio::const_mem_fun模板，它是boost::multi_index::const_mem_fun的类型别名，例子：
// multi\_index表的名字是N(address), N是一个宏，可以把base32编码后的字符串转换为uint64_t
// multi\_index表的对象类型是address
// multi\_index通过名为N(phone)的二级索引进行检索
// const_mem_fun是一个用于address类型的键提取器（key extractor ）
// const_mem_fun提取的键类型是uint64_t，通过address::get_phone函数获取键
eosio::multi_index< N(address), address, indexed_by<N(phone), const_mem_fun<address, uint64_t, &address::get_phone>>>
```

10. 完整例子
```
//@abi action
void likebyphone(uint64_t phone) {
    address_index addresses(_self, _self);

    auto phone_index = addresses.get_index<N(phone)>();
    auto itr = phone_index.lower_bound(phone);
    for(; itr != phone_index.end() && itr->phone == phone; ++itr) {
        phone_index.modify(itr, 0, [&](auto& address){
            eosio::print("Liking: ", address.name.c_str(), "\n");
            address.liked++;
        });
    }
}
```

### (六). 工具函数
1. available_primary_key
返回一个可用（未使用）的主键值，用于主键严格自增的表，它不会被设置为自定义值
`uint64_t available_primary_key() const`


## 参考
- [https://bihu.com/article/328856](https://bihu.com/article/328856)
- [https://bihu.com/article/334710](https://bihu.com/article/334710)
