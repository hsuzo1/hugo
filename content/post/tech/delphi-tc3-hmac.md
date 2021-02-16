---
title: "Delphi调用腾讯云TC3-HMAC-SHA256鉴权接口v3发送短信"
date: 2021-02-16T22:09:20+08:00
draft: false
categories: ["tech"]
Tags : ["delphi", "hmac", "sms", "tencent"]
keywords : "张旭州 sms hmac delphi"
---

腾讯云的TC3 v3鉴权比较复杂，当然这是相对于Delphi下而言，其实在Python下直接使用SDK要省事儿的多。本文的源代码就不贴了，需要的可以去原文下载。  
参考了[高文杰](https://blog.csdn.net/xunchima123/article/details/93863574)先生的代码，该代码使用的接口是旧版，目前仍可正常使用，我结合自己的学习增添了最新TC3-HMAC-SHA256 V3版本的鉴权接口。本文最后提供了源码及编译成品。

{{< image src="/img/tech/20210216223459.jpg" alt="tencent sms" position="center" style="border-radius: 8px;" >}}

### 一、TC3鉴权单元
为了方便复用，写了一个TC3鉴权单元文件：**TC3_Authorization.pas**：
```pascal
{
2021-02-15
广西南宁
张旭州
腾讯云 TC3-HMAC-SHA256 生成鉴权数据
}
 
unit TC3_Authorization;
 
interface
 
uses
  System.SysUtils, System.hash, System.NetEncoding, System.DateUtils;
 
{ -------------------------------------------------------------------------------
  过程名:    genTC3Auth
  作者:      张旭州
  日期:      2021.02.15
  参数:      SecretKey, SecretId, sDomain, bodyJSON, Service: string
  参数说明： AccessKeyID，AccessKeySecret，域名, 待发送的数据主体JSON， 服务ocr, cvm
  返回值:    string
  
  参数参考如下：
    SecretKey := '您的腾讯云SecretKey';
    SecretId := '您的腾讯云SecretId';
    Service := 'sms'  //发送短信
    sDomain := 'sms.tencentcloudapi.com'   //短信发送的域名
  ------------------------------------------------------------------------------- }
function genTC3Auth(SecretKey, SecretId, sDomain, bodyJSON, Service: string): string;
 
implementation
 
 
function DateTimeToUnix(const AValue: TDateTime): Int64;
// 日期转Unix时间戳
begin
  Result := System.DateUtils.DateTimeToUnix(AValue) - 8 * 60 * 60;
end;
 
//腾讯云TC3 V3签名鉴权
function genTC3Auth(SecretKey, SecretId, sDomain, bodyJSON, Service: string): string;
var
  httpRequestMethod: string; // = "POST";
  canonicalUri: string; //= "/";
  canonicalQueryString: string; //= "";
  canonicalHeaders: string; // = "content-type:application/json; charset=utf-8\n" + "host:" + host + "\n";
  signedHeaders: string; //= "content-type;host";
  SecretDate, SecretService, SecretSigning, Signature: TBytes;
  StringToSign, payload, CanonicalRequest, HashedRequestPayload, HashedCanonicalRequest: string;
  sDate,timestamp : string;
  Authorization, CredentialScope : string;
begin
  sDate := FormatDateTime('YYYY-MM-DD', now());
  timestamp := DateTimeToUnix(now).ToString;
  httpRequestMethod := 'POST';
  canonicalUri := '/';
  canonicalQueryString := '';
  canonicalHeaders := 'content-type:application/json' + #10
      + 'host:' + sDomain + #10;
  signedHeaders := 'content-type;host';
 
  payload := bodyJSON;
  //待发送的数据的哈希值:
  HashedRequestPayload := THashSHA2.GetHashString(payload);
 
  //拼接规范请求串
  CanonicalRequest := httpRequestMethod + #10
      + canonicalUri + #10
      + canonicalQueryString + #10
      + canonicalHeaders + #10
      + signedHeaders + #10
      + HashedRequestPayload;
 
  //计算派生签名密钥
  SecretDate := THashSHA2.GetHMACAsBytes(sDate, TEncoding.utf8.GetBytes('TC3' + SecretKey));
  SecretService := THashSHA2.GetHMACAsBytes(Service, SecretDate);
  SecretSigning := THashSHA2.GetHMACAsBytes('tc3_request', SecretService);
 
  //规范请求串的哈希值
  HashedCanonicalRequest := THashSHA2.GetHashString(CanonicalRequest);
  //组装待签名字符串
  StringToSign := 'TC3-HMAC-SHA256' + #10
      + timestamp + #10
      + sDate + '/' + Service + '/tc3_request' + #10
      + HashedCanonicalRequest;
  //计算签名
  Signature := THashSHA2.GetHMACAsBytes(Bytesof(StringToSign), SecretSigning);
//  Application.MessageBox(PChar(THash.DigestAsString(Signature)),
//      '提示', MB_OK + MB_ICONINFORMATION + MB_TOPMOST);
 
  CredentialScope := sDate + '/' + Service + '/tc3_request';
  //拼接 Authorization
  Authorization :=
    'TC3-HMAC-SHA256' + ' ' +
    'Credential=' + SecretId + '/' + CredentialScope + ', ' +
    'SignedHeaders=' + SignedHeaders + ', ' +
    'Signature=' + StringReplace(PChar(THash.DigestAsString(Signature)), Chr(13) + Chr(10), '',
    [rfReplaceAll]);
 
  Result := Authorization;
end;
 
end.
```  

### 二、、短信发送模块
在implementation下 
```pascal
uses
Unit2, TC3_Authorization;
```
```pascal
procedure TForm1.btn_tc3_sendClick(Sender: TObject);
var
  strMobile, SecretKey, SecretId,sdkappid,sign, params, tpl_id, strjson:string;
  tc3 : string;
  url : string;
  http : TIdHTTP;
  jsonToSend : TStringStream;
  Aresult:string;
  //申明变量
  Root:TJSONObject;    //uses System.JSON;
  jsonArray: TJSONArray; // JSON数组变量
  list : TStringList;
  i : Integer;
  phoneSet : string;
 
begin
  if (trim(edt_key.Text) = '') or (Trim(edt_id.Text)='') then
  begin
    ShowMessage('使用TC3鉴权方式发送需要提供腾讯云账户的Key和Id');
    exit;
  end;
  SecretKey := trim(edt_key.Text);
  SecretId := Trim(edt_id.Text);
  list := TStringList.Create;
  list.CommaText := edt_strMobile.Text;
  for i := 0 to list.Count -1  do
  begin
    phoneSet := phoneSet + '"86' + list[i] + '",'
  end;

  strMobile := phoneSet.Remove(Length(phoneSet)-1);
  list.Free;

  sdkappid := edt_sdkappid.Text;
  sign := edt_sign.Text;
  params := edt_params.Text;
  tpl_id := edt_tpl_id.Text;
 
  strjson := '{'
  + '"PhoneNumberSet":['
  + strMobile
  + '],'
  + '"TemplateParamSet":' + params
  + ','
  + '"TemplateID":"'
  + tpl_id
  + '",'
  + '"SmsSdkAppid":"'
  + sdkappid
  + '",'
  + '"Sign":"'
  + sign
  +'"}';
 
  tc3 := TC3_Authorization.genTC3Auth(SecretKey, SecretId, 'sms.tencentcloudapi.com',
     strjson, 'sms');
  url := 'https://sms.tencentcloudapi.com/';
  http := TIdHttp.Create(nil);
  http.HandleRedirects := True;
  http.ReadTimeout := 3000;
  http.Request.ContentType := 'application/json';//设置内容类型为json
 
  jsonToSend := TStringStream.Create(strjson, TEncoding.UTF8);
  jsonToSend.Position := 0;//将流位置置为0
  http.Request.CustomHeaders.Clear;
  http.Request.CustomHeaders.AddValue('Authorization', ' ' + tc3);
  http.Request.CustomHeaders.AddValue('Content-Type', ' application/json');
  http.Request.CustomHeaders.AddValue('Host', ' sms.tencentcloudapi.com');
  http.Request.CustomHeaders.AddValue('X-TC-Action', ' SendSms');
  http.Request.CustomHeaders.AddValue('X-TC-Timestamp', ' ' + gwj_DateTimeToUnix(now).ToString);
  http.Request.CustomHeaders.AddValue('X-TC-Version', ' 2019-07-11');
 
  http.Request.AcceptCharSet := 'UTF-8';
  http.Request.AcceptEncoding := 'UTF-8';
  http.Request.AcceptLanguage := 'UTF-8';
  http.Request.CharSet := 'UTF-8';
//  http.HTTPOptions := IdHTTP.HTTPOptions + [hoKeepOrigProtocol];
 
  Aresult := http.Post(url, jsonToSend);//用MEMO控件接收POST后的数据返回
  Aresult := UnicodeToChinese(Aresult);
  Memo1.Text := Aresult;
  jsonToSend.free;
  http.free;
 
  //发送成功的示例
  //{"Response":{"SendStatusSet":[{"SerialNo":"2019:2892974270720676287","PhoneNumber":"+8613377131696","Fee":1,"SessionContext":"","Code":"Ok","Message":"send success","IsoCode":"CN"}],"RequestId":"b2e1fdcb-e877-4bbe-89cc-e7cae7cb567d"}}
 
  Root:= TJSONObject.ParseJSONValue(Trim(Aresult)) as TJSONObject;   //uses System.JSON;
  Root := Root.GetValue('Response') as TJSONObject;
  jsonArray := Root.GetValue('SendStatusSet') as TJSONArray ;
 
  Root := jsonArray.Get(0) as TJSONObject; //第一个号码结果
 
  edt_result.Text := Root.GetValue('Code').Value;
  edt_errmsg.Text := Root.GetValue('Message').Value;
  edt_ext.Text := Root.GetValue('SessionContext').Value;
end;
```
**点击按钮下载本文源代码：**
{{< link "/files/DelphiWithTencentSMS.zip" >}}

