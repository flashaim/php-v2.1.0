# PHP Library for LINE Pay API 使用說明<br> (使用 php_curl 實作)

## 目錄
* [Library 使用方式](#library-使用方式)
  * [角色](#角色)
  * [使用流程](#使用流程)
* [Library 使用-程式碼範例 (以退款為例)](#library-使用-程式碼範例-以退款為例)

## Library 使用方式
以下流程是以[**測試用網頁**] (https://trial-tw-pay.line.me/merchant/)介面為例：
> 在網頁中可以分別對 LINE Pay 的每一支 API 進行呼叫並查看呼叫所使用的內容以及伺服器回傳的內容

#### 角色
在呼叫使用 LINE Pay API 的過程中，以下的動作皆可透過單一 Library 所宣告產生的物件來進行：
* 一般付款 (Reserve & Confirm)
* 付款記錄查詢
* 退款 (Refund)
* 授權紀錄查詢
* 授權作廢
* 自動付款
* 在畫面上檢視對 LINE Pay 伺服器送出的請求 (註2)
* 在畫面上檢視由 LINE Pay 伺服器回傳的回應內容 (註2)

> [註1]：商家的購物網站除了要有前端頁面提供給使用者進行付款之外，也需要有後端伺服器來發送 API 請求給 LINE Pay 伺服器（不可從網站使用者的瀏覽器發送 LINE Pay API 請求） 
<br><br>
> [註2]：Library 中使用的 showRequest 以及 showResponse 兩個函式僅用來完成上述動作中，最後兩項顯示在螢幕上方便檢視的功能。商家實際使用 LINE Pay API 時並不需要使用，可逕行忽略。

#### 使用流程
1. 在用來呼叫不同 API 的各檔案中，使用在商家後台取得的**通路ID**和**通路密鑰**以及選擇的**呼叫環境**宣告 LINEPay 物件。(註)
2. 完成物件宣告後，提供 API 所需要的參數來呼叫相同名稱的函式，便能完成 API 的呼叫並傳回伺服器的回應內容。

> [註]：「呼叫環境」參數在這邊是為了能直接在測試網頁中選取要使用的環境，來設定對應的 apiEndpoint 。實際使用 LINE Pay API 時請修改 LINEPay class 將此參數移除，直接在物件宣告中設定要使用的 apiEndpoint。

## Library 使用-程式碼範例 (以退款為例)
* PHP 版本: v5.4 以上
* LINE Pay API 版本: v2
* 使用 LINE Pay API 的[事前準備](/README.md)
* [測試工具原始碼（PHP、HTML 網頁介面）] (/sample)
* [PHP Library for LINE Pay API] (/sample/LINEPay.php)

``` php
<?php
    
include "LINEPay.php"; // 引用LinePay PHP Library

$channelId     = "...";  // 通路ID
$channelSecret = "...";  // 通路密鑰
$environ       = "...";  // 呼叫環境

$LINEPay = new LINEPay($environ, $channelId, $channelSecret); // 建立 LINEPay 物件

//// Call Refund API
$txnId = $_REQUEST["refTxnId"]; // 要退款的交易編碼
$refAmt = $_REQUEST["refundAmount"]; // 要退款的金額
$rfArray = array(); 
// 如果有指定金額，依照退款 API 指定的參數名稱包裝成呼叫函式的參數 (若無指定則全額退費)
if (!empty($refAmt)) { 
	$rfArray = array(
		"refundAmount" => $refAmt 
		);
}
// 在畫面上檢視對 LINE Pay 伺服器送出的請求
$LINEPay->showRequest("POST", $rfArray);
// 透過建立的 LINEPay 物件，以退款 API 要求的交易編碼、退款金額參數呼叫同名函式
$response = $LINEPay->refund($txnId, $rfArray);
// 在畫面上檢視由 LINE Pay 伺服器回傳的回應內容
$LINEPay->showResponse($response);

?>
```
