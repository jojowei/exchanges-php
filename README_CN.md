### 前言

这SDK集合了目前交易量最大的几个交易所的API，让开发人员只关注业务层。它目前只是简单的支持买卖以及查询，后期作者会集合更多的API。如果你有特殊的需求你可以单独通过该方法[getPlatform()](https://github.com/zhouaini528/exchanges-php/blob/master/README_CN.md#%E6%94%AF%E6%8C%81%E5%8E%9F%E5%A7%8B%E5%AF%B9%E8%B1%A1%E8%AF%B7%E6%B1%82)返回实例，调用底层API。

这SDK支持统一参数，也支持原生参数。建议使用者使用统一参数，有特殊需求可以使用原生参数。

所有提交参数与返回参数只要第一个字符为下划线的`_`全部为自定义参数。

很多接口还未完善，使用者可以根据我的设计方案继续扩展，欢迎与我一起改进它。

### 其他交易所API

[Bitmex](https://github.com/zhouaini528/bitmex-php)

[Okex](https://github.com/zhouaini528/okex-php)

[Huobi](https://github.com/zhouaini528/huobi-php)

[Binance](https://github.com/zhouaini528/binance-php)

以上所有交易所集合成的SDK

[Exchanges](https://github.com/zhouaini528/exchanges-php)

#### 安装方式
```
composer require linwj/exchanges:dev-master

如果安装中出问题composer.json 添加 "minimum-stability":"dev"
```

#### 更多用例
[Bitmex](https://github.com/zhouaini528/exchanges-php/tree/master/tests/bitmex.php)

[Binance](https://github.com/zhouaini528/exchanges-php/tree/master/tests/binance.php)

[Huobi](https://github.com/zhouaini528/exchanges-php/tree/master/tests/huobi.php)

[Okex](https://github.com/zhouaini528/exchanges-php/tree/master/tests/okex.php)


#### 交易所初始化
```php
$exchanges=new Exchanges('binance',$key,$secret);
$exchanges=new Exchanges('bitmex',$key,$secret);
$exchanges=new Exchanges('okex',$key,$secret,$passphrase,$host);
$exchanges=new Exchanges('huobi',$key,$secret,$account_id,$host);
```
[火币获取$account_id方式](https://github.com/zhouaini528/exchanges-php/blob/master/tests/huobi.php#L59)

#### 统一参数返回
```php
/**
 * Buy()   Sell()   Show() 三个方法都返回相同参数
 * @return [
 *      ***返回原始数据
 *      ...
 *      ...
 *      ***返回自定义数据，带'_'下划线的是统一返回参数格式。
 *      _status=>NEW 进行中   PART_FILLED 部分成交   FILLED 完全成交  CANCELING:撤销中   CANCELLED 已撤销   FAILURE 下单失败
 *      _filled_qty=>已交易完成数量
 *      _price_avg=>平均交易价格
 *      _order_id=>系统ID
 *      _client_id=>自定义ID
 * ]
 *
 * */
 
 /**
 * 系统错误
 * http request code 400 403 500 503
 * @return [
 *      _error=>[
 *          ***返回原始数据
 *          ...
 *          ...
 *          ***返回自定义数据，带'_'下划线的是统一返回参数格式。
 *          _method => POST
 *          _url => https://testnet.bitmex.com/api/v1/order
 *          _httpcode => 400
 *      ]
 * ]
 * */
```
买卖查询统一参数返回 [详情](https://github.com/zhouaini528/exchanges-php/blob/master/src/Api/Trader.php#L36)

系统错误统一参数返回 
[binance](https://github.com/zhouaini528/exchanges-php/blob/master/tests/binance.php#L33)
[okex](https://github.com/zhouaini528/exchanges-php/blob/master/tests/okex.php#L35)
[huobi](https://github.com/zhouaini528/exchanges-php/blob/master/tests/huobi.php#L35)
[bitmex](https://github.com/zhouaini528/exchanges-php/blob/master/tests/bitmex.php#L35)

#### 现货交易
##### 市价交易
```php
//binance
$exchanges->trader()->buy([
    '_symbol'=>'BTCUSDT',
    '_number'=>'0.01',
]);
//支持原生参数
$exchanges->trader()->buy([
    'symbol'=>'BTCUSDT',
    'type'=>'MARKET',
    'quantity'=>'0.01',
]);

//okex
$exchanges->trader()->buy([
    '_symbol'=>'BTC-USDT',
    '_price'=>'10',
]);
//支持原生参数
$exchanges->trader()->buy([
    'instrument_id'=>'btc-usdt',
    'type'=>'market',
    'notional'=>'10'
]);

//huobi
$exchanges->trader()->buy([
    '_symbol'=>'btcusdt',
    '_price'=>'10',
]);
//支持原生参数
$exchanges->trader()->buy([
    'account-id'=>$account_id,
    'symbol'=>'btcusdt',
    'type'=>'buy-market',
    'amount'=>10
]);

```
##### 限价交易
```php
//binance
$exchanges->trader()->buy([
    '_symbol'=>'BTCUSDT',
    '_number'=>'0.01',
    '_price'=>'2000',
]); 
//支持原生参数
$exchanges->trader()->buy([
    'symbol'=>'BTCUSDT',
    'type'=>'LIMIT',
    'quantity'=>'0.01',
    'price'=>'2000',
    'timeInForce'=>'GTC',
]);

//okex
$exchanges->trader()->buy([
    '_symbol'=>'BTC-USDT',
    '_number'=>'0.001',
    '_price'=>'2000',
]);
//支持原生参数
$exchanges->trader()->buy([
    'instrument_id'=>'btc-usdt',
    'price'=>'100',
    'size'=>'0.001',
]);

//huobi
$exchanges->trader()->buy([
    '_symbol'=>'btcusdt',
    '_number'=>'0.001',
    '_price'=>'2000',
]);
//支持原生参数
$exchanges->trader()->buy([
    'account-id'=>$account_id,
    'symbol'=>'btcusdt',
    'type'=>'buy-limit',
    'amount'=>'0.001',
    'price'=>'2001',
]);
```
#### 期货交易
##### 市价交易
```php
//bitmex
$exchanges->trader()->buy([
    '_symbol'=>'XBTUSD',
    '_number'=>'1',
]);
//支持原生参数
$exchanges->trader()->buy([
    'symbol'=>'XBTUSD',
    'orderQty'=>'1',
    'ordType'=>'Market',
]);

//okex
$exchanges->trader()->buy([
    '_symbol'=>'BTC-USD-190628',
    '_number'=>'1',
    '_entry'=>true,//open long
]);
//支持原生参数
$exchanges->trader()->buy([
    'instrument_id'=>'BTC-USD-190628',
    'size'=>1,
    'type'=>1,//1:open long 2:open short 3:close long 4:close short
    //'price'=>2000,
    'leverage'=>10,//10x or 20x leverage
    'match_price' => 1,
    'order_type'=>0,
]);
```
##### 限价交易
```php
//bitmex
$exchanges->trader()->buy([
    '_symbol'=>'XBTUSD',
    '_number'=>'1',
    '_price'=>100
]);
//支持原生参数
$exchanges->trader()->buy([
    'symbol'=>'XBTUSD',
    'price'=>'100',
    'orderQty'=>'1',
    'ordType'=>'Limit',
]);

//okex
$exchanges->trader()->buy([
    '_symbol'=>'BTC-USD-190628',
    '_number'=>'1',
    '_price'=>'2000',
    '_entry'=>true,//open long
]);
//支持原生参数
$exchanges->trader()->buy([
    'instrument_id'=>'BTC-USD-190628',
    'size'=>1,
    'type'=>1,//1:open long 2:open short 3:close long 4:close short
    'price'=>2000,
    'leverage'=>10,//10x or 20x leverage
    'match_price' => 0,
    'order_type'=>0,
]);
```

#### 支持原始对象请求
```php
//binance
$exchanges->getPlatform()->trade()->postOrder([
    'symbol'=>'BTCUSDT',
    'side'=>'BUY',
    'type'=>'LIMIT',
    'quantity'=>'0.01',
    'price'=>'2000',
    'timeInForce'=>'GTC',
]);


//bitmex
$exchanges->getPlatform()->order()->post([
    'symbol'=>'XBTUSD',
    'price'=>'100',
    'side'=>'Buy',
    'orderQty'=>'1',
    'ordType'=>'Limit',
]);


//okex
$exchanges->getPlatform('spot')->order()->post([
    'instrument_id'=>'btc-usdt',
    'side'=>'buy',
    'price'=>'100',
    'size'=>'0.001',
    //'type'=>'market',
    //'notional'=>'100'
]);
$exchanges->getPlatform('future')->order()->post([
    'instrument_id'=>'btc-usd-190628',
    'type'=>'1',
    'price'=>'100',
    'size'=>'1',
]);


//huobi
$exchanges->getPlatform('spot')->order()->postPlace([
    'account-id'=>$account_id,
    'symbol'=>'btcusdt',
    'type'=>'buy-limit',
    'amount'=>'0.001',
    'price'=>'100',
]);

$exchanges->getPlatform('future')->contract()->postOrder([
    'symbol'=>'BTC',//string    false   "BTC","ETH"...
    'contract_type'=>'quarter',//   string  false   Contract Type ("this_week": "next_week": "quarter":)
    'contract_code'=>'BTC190628',// string  false   BTC180914
    'price'=>'100',//   decimal true    Price
    'volume'=>'1',//    long    true    Numbers of orders (amount)
    'direction'=>'buy',//   string  true    Transaction direction
    'offset'=>'open',// string  true    "open", "close"
    //'client_order_id'=>'',//long  false   Clients fill and maintain themselves, and this time must be greater than last time
    //lever_rate    int true    Leverage rate [if“Open”is multiple orders in 10 rate, there will be not multiple orders in 20 rate
    //order_price_type   string true    "limit", "opponent"
]);

```


更多用例请查看 [more](https://github.com/zhouaini528/exchanges-php/tree/master/tests)

更多API请查看 [more](https://github.com/zhouaini528/exchanges-php/tree/master/src/Api)


