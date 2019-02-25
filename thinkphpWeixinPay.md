---
title: thinkPHP整合微信公众号支付
date:  2016-11-13 17:55:21
tags: php
---

给thinkphp添加扩展实现微信支付，基于官方微信支付的Demo开发

些此篇文章的原因主要是最近在做有关微信支付的开发，由于初次开发走了很多弯路，所以整理了一下写下这篇文章，参考文章thinkphp整合系列之微信公众号支付（来自白俊遥博客[http://baijunyao.com）由于他的源码有点问题，所以我做了一些修改，整个项目我也fork过来了。](http://baijunyao.com%EF%BC%89%E7%94%B1%E4%BA%8E%E4%BB%96%E7%9A%84%E6%BA%90%E7%A0%81%E6%9C%89%E7%82%B9%E9%97%AE%E9%A2%98%EF%BC%8C%E6%89%80%E4%BB%A5%E6%88%91%E5%81%9A%E4%BA%86%E4%B8%80%E4%BA%9B%E4%BF%AE%E6%94%B9%EF%BC%8C%E6%95%B4%E4%B8%AA%E9%A1%B9%E7%9B%AE%E6%88%91%E4%B9%9Ffork%E8%BF%87%E6%9D%A5%E4%BA%86%E3%80%82/)

项目地址：<http://git.oschina.net/crazy_boy/thinkphp-bjyadmin> ，里面整合了很多扩展都可以参考。

由于大致用法原文里面都有，一下写一些原文中没有讲清楚或没有讲到的东西。

### 配置项

`/Application/Common/Conf/config.php`源码如下：

```php
'WEIXINPAY_CONFIG' => array(
    'APPID'              => '', // 微信支付APPID  
    'MCHID'              => '', // 微信支付MCHID 商户收款账号
    'KEY'                => '', // 微信支付KEY      
    'APPSECRET'          => '', // 公众帐号secert (公众号支付专用)
    'NOTIFY_URL'         => 'http://域名/Home/Weixinpay/notify', // 接收支付状态的连接
  ),
```





其中NITIFY_URL是支付成功后微信返回支付结果的一个连接，在我的测试中，只有在支付成功后微信才会向此链接放回结果，其中要特别注意的一点是此链接不能带有任何的参数，如这种连接是不行的:`http://域名/index.php/Home/Weixinpay/notify`。

微信支付key需要在微信商户平台中设置。（账户中心->API安全）

### 异步接收通知

这个讲到的就是上面提到的`notify_url`，源码如下，由于是微信异步请求的链接，所以切记不要用session来操作，因为他是访问不了的，微信会放回订单号`out_trade_no`。

```php
public function notify(){
      // 导入微信支付sdk
      Vendor('Weixinpay.Weixinpay');
      $wxpay=new \Weixinpay();
      $result=$wxpay->notify();
      if ($result) {
          // 验证成功 修改数据库的订单状态等 $result['out_trade_no']为订单id
      }
}
```





### 订单号$out_trade_no

这个订单号是由你自己来生成的，官方推荐的方式是时间+字符串的方式，time()+uid，即当前时间+用户id这是我的做法。生成好之后直接生成订单并存入数据库，下面是我的源码:

```php
public function js(){
    // 此处根据实际业务情况生成订单 然后拿着订单去支付
    // 用时间戳虚拟一个订单号  （请根据实际业务更改）
    session('total_fee',I('get.total_fee'));
    $order['id']=$out_trade_no;
    $order['uid']=session('id');
    $order['amount']=I('get.total_fee');
    M('order')->add($order);
    // 组合url
    $url=U('Wx/pay',array('out_trade_no'=>$out_trade_no));
    // 前往支付
    $this->redirect($url);
}
```



之后修改`/ThinkPHP/Library/Vendor/Weixinpay/Weixinpay.php` 的170行，用获得的订单号查询订单信息，并传入，源码如下：

```php
$order=M('order')->find($out_trade_no);
$order=array(
    'body'=>“充值”.$order['amount'].'元',// 商品描述（需要根据自己的业务修改
    'total_fee'=>$order['amount']*100,// 订单金额  以(分)为单位（需要根据自己的业务修改
    'out_trade_no'=>$out_trade_no,// 订单号（需要根据自己的业务修改）
    'product_id'=>'1',// 商品id（需要根据自己的业务修改）
    'trade_type'=>'JSAPI',// JSAPI公众号支付
    'openid'=>$openid// 获取到的openid
);
```



### html配合使用

html的配合：`/tpl/Home/Weixinpay/pay.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>充值</title>
</head>
<body style="text-align: center;">
  <div class="header">
  <div class="all_w ">
    <div class="ttwenz">
      <h4>确认交易</h4>
      <h5>微信安全支付</h5>
    </div>
  </div>
</div>
<div class="wenx_xx">
  <div class="mz">充值</div>
  <div class="wxzf_price">￥{:session("total_fee")}</div>
</div>
<div class="skf_xinf">
  <div class="all_w"> <span class="bt">收款方</span> <span class="fr">商家名字</span> </div>
</div>
<a onclick="pay()" class="ljzf_but all_w">确认支付</a>
<script>

function onBridgeReady(){
    var data={$data};
    WeixinJSBridge.invoke(
        'getBrandWCPayRequest', data,
        function(res){
            if(res.err_msg == "get_brand_wcpay_request:ok" ) {
              alert('支付成功!');
            }else{
              alert('支付失败!');
            }
            history.go(-1);
        }
    );
}
function pay(){
  if (typeof WeixinJSBridge == "undefined"){
      if( document.addEventListener ){
          document.addEventListener('WeixinJSBridgeReady', onBridgeReady, false);
      }else if (document.attachEvent){
          document.attachEvent('WeixinJSBridgeReady', onBridgeReady);
          document.attachEvent('onWeixinJSBridgeReady', onBridgeReady);
      }
  }else{
       onBridgeReady();
  }
}



</script>
</body>
</html>
```