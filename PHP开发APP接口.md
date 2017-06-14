---
title: PHP开发APP接口
date: 2015-08-01
tags: php
---

## php面向对象接口

抽象类 interface 关键字定义，里面有一些方法（抽象方法）
implements 实现 
好处：制定一个标准
##APP接口 (通信接口)
1.接口地址 http://xxx.php 
2.接口文件  xxx.php 处理一些业务逻辑
3.接口数据  返回json xml
<!--more-->

扩展标记语言XML  Extensible Markup Language  

XML节点标签可以自定义 ，区别：html不可定义
json 轻量级数据交换格式，可读和便于快速编写的特性，可在不同平台之间进行数据交换


##app接口做的那些事
获取数据：从Database或缓存中获取数据，通过接口返回给客户端
提交数据：通过接口提交数据给服务器，然后服务器入库处理，或者其他处理

##php生成json数据
json_encode($value)
只接受utf-8编码数据，如果传递其他格式该函数返回null
```php
<?php

$arr = array(
	"id"=>"12",
	"name"=>"imd"
	);

echo json_encode($arr);
```
```php
输出{"id":"12","name":"imd"}
```
转换gbk格式
```php
$data = "输出json数据";
//iconv(in_charset, out_charset, str)
//按要求的字符编码来转换
$newData = iconv('UTF-8', 'GBK', $data);

echo json_encode($newData);
```
输出：null

### 通信数据标准格式
code  状态码 （200,400等）
message 提示信息（邮箱格式不正确，数据返回成功等）
data 返回数据
#### json方式封装接口数据
json.php
```php
<?php

class Response{
	/*
	 *按json方式输出通信数据
	 *@param integer $code 状态吗
	 *@param string $message 提示信息
	 *@param array $data 数据
	 *return string	 
	*/
	public static function json($code,$message='',$data=array()){
		if(!is_numeric($code)){
			return '';
		}

		$result = array(
				'code'=>$code,
				'message'=>$message,
				'data'=>$data
			);
		echo json_encode($result);
		exit;
	}
}
```
另外一个文件里调用 
```php
<?php

require_once('./json.php');
$arr = array(
	'id'=> 1,
	'name'=> 'imooc'
	);

Response::json(200,'数据返回成功',$arr);
```
输出 {"code":200,"message":"\u6570\u636e\u8fd4\u56de\u6210\u529f","data":{"id":1,"name":"imooc"}}

#### xml方式封装接口数据方法
php生成xml数据方式：1.组装字符串 2.使用系统类（DomDocument XMLWriter SimpleXML）


```php
//组装字符串
public static function xml(){
		header("Content-Type:text/xml");
		$xml = "<?xml version='1.0' encoding='UTF-8'?>\n"; // 换行\n
		$xml .= "<root>\n";
		$xml .= "<code>200</code>\n";
		$xml .= "<message>数据返回成功</message>\n";
		$xml .= "<data>\n";
		$xml .= "<id>1</id>\n";
		$xml .= "<name>imooc</name>\n";	

		$xml .= "</data>\n";

		$xml .= "<root>";

		echo $xml;
	}
```
调用 Response::xml();

输出:
```xml
<?xml version='1.0' encoding='UTF-8'?>
<root>
<code>200</code>
<message>数据返回成功</message>
<data>
<id>1</id>
<name>imooc</name>
</data>
<root>
```
#### XML方式封装接口数据方法
```php
	/**
	* 按xml方式输出通信数据
	* @param integer $code 状态码
	* @param string $message 提示信息
	* @param array $data 数据
	* return string
	*/
	public static function xmlEncode($code, $message, $data = array()) {
		if(!is_numeric($code)) {
			return '';
		}

		$result = array(
			'code' => $code,
			'message' => $message,
			'data' => $data,
		);

		header("Content-Type:text/xml");
		$xml = "<?xml version='1.0' encoding='UTF-8'?>\n";
		$xml .= "<root>\n";

		$xml .= self::xmlToEncode($result);

		$xml .= "</root>";
		echo $xml;
	}
	//解析数组拼装成xml返回
	public static function xmlToEncode($data) {

		$xml = $attr = "";
		foreach($data as $key => $value) {
			if(is_numeric($key)) {
				$attr = " id='{$key}'";
				$key = "item";
			}
			$xml .= "<{$key}{$attr}>";
			$xml .= is_array($value) ? self::xmlToEncode($value) : $value;
			$xml .= "</{$key}>\n";
		}
		return $xml;
	}
```

```php
//组装字符串
public static function xml(){
		header("Content-Type:text/xml");
		$xml = "<?xml version='1.0' encoding='UTF-8'?>\n"; // 换行\n
		$xml .= "<root>\n";
		$xml .= "<code>200</code>\n";
		$xml .= "<message>数据返回成功</message>\n";
		$xml .= "<data>\n";
		$xml .= "<id>1</id>\n";
		$xml .= "<name>imooc</name>\n";	

		$xml .= "</data>\n";

		$xml .= "<root>";

		echo $xml;
	}
```
调用 Response::xml();

输出:
```xml
<?xml version='1.0' encoding='UTF-8'?>
<root>
<code>200</code>
<message>数据返回成功</message>
<data>
<id>1</id>
<name>imooc</name>
</data>
<root>
```
#### XML方式封装接口数据方法
```php
	/**
	* 按xml方式输出通信数据
	* @param integer $code 状态码
	* @param string $message 提示信息
	* @param array $data 数据
	* return string
	*/
	public static function xmlEncode($code, $message, $data = array()) {
		if(!is_numeric($code)) {
			return '';
		}

		$result = array(
			'code' => $code,
			'message' => $message,
			'data' => $data,
		);

		header("Content-Type:text/xml");
		$xml = "<?xml version='1.0' encoding='UTF-8'?>\n";
		$xml .= "<root>\n";

		$xml .= self::xmlToEncode($result);

		$xml .= "</root>";
		echo $xml;
	}
	//解析数组拼装成xml返回
	public static function xmlToEncode($data) {

		$xml = $attr = "";
		foreach($data as $key => $value) {
			if(is_numeric($key)) {
				$attr = " id='{$key}'";
				$key = "item";
			}
			$xml .= "<{$key}{$attr}>";
			$xml .= is_array($value) ? self::xmlToEncode($value) : $value;
			$xml .= "</{$key}>\n";
		}
		return $xml;
	}
```
## 综合方式封装通信数据方法
response.php
```php
<?php

class Response {
	const JSON = "json";
	/**
	* 按综合方式输出通信数据
	* @param integer $code 状态码
	* @param string $message 提示信息
	* @param array $data 数据
	* @param string $type 数据类型
	* return string
	*/
	public static function show($code, $message = '', $data = array(), $type = self::JSON) {
		if(!is_numeric($code)) {
			return '';
		}

		$type = isset($_GET['format']) ? $_GET['format'] : self::JSON;

		$result = array(
			'code' => $code,
			'message' => $message,
			'data' => $data,
		);

		if($type == 'json') {
			self::json($code, $message, $data);
			exit;
		} elseif($type == 'array') {
			var_dump($result);
		} elseif($type == 'xml') {
			self::xmlEncode($code, $message, $data);
			exit;
		} else {
			// TODO
		}
	}
	/**
	* 按json方式输出通信数据
	* @param integer $code 状态码
	* @param string $message 提示信息
	* @param array $data 数据
	* return string
	*/
	public static function json($code, $message = '', $data = array()) {
		
		if(!is_numeric($code)) {
			return '';
		}

		$result = array(
			'code' => $code,
			'message' => $message,
			'data' => $data
		);

		echo json_encode($result);
		exit;
	}

	/**
	* 按xml方式输出通信数据
	* @param integer $code 状态码
	* @param string $message 提示信息
	* @param array $data 数据
	* return string
	*/
	public static function xmlEncode($code, $message, $data = array()) {
		if(!is_numeric($code)) {
			return '';
		}

		$result = array(
			'code' => $code,
			'message' => $message,
			'data' => $data,
		);

		header("Content-Type:text/xml");
		$xml = "<?xml version='1.0' encoding='UTF-8'?>\n";
		$xml .= "<root>\n";

		$xml .= self::xmlToEncode($result);

		$xml .= "</root>";
		echo $xml;
	}

	public static function xmlToEncode($data) {

		$xml = $attr = "";
		foreach($data as $key => $value) {
			if(is_numeric($key)) {
				$attr = " id='{$key}'";
				$key = "item";
			}
			$xml .= "<{$key}{$attr}>";
			$xml .= is_array($value) ? self::xmlToEncode($value) : $value;
			$xml .= "</{$key}>\n";
		}
		return $xml;
	}

}
```


test.php 
```
<?php
require_once('./response.php');
$data = array(
	'id' => 1,
	'name'=>'singwa',
	'type'=>array(4,5,6),
	'test'=>array(1,45,67=>array(123,'tsy'))
);

Response::show(200,'success',$data);
```
GET方式传递参数 format，下面即输出xml格式
//http://localhost/app/test.php?format=xml


















