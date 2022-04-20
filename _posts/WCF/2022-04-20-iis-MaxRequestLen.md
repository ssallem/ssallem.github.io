---
title:  "[IIS MaxRequestLength] IIS 최대 업로드 파일 크기 조정" 
excerpt: "IIS MaxRequestLength 조정해보자 (기본 4M)"
 
categories:
  - WCF
tags:
  - [WCF, Csharp, IIS, Max]

toc: true
toc_sticky: true
 
date: 2022-04-20
last_modified_at: 2022-04-20
---

# 📒 **iis 최대 업로드 파일 크기 조정 (기본 4M)**

## **1.  IIS6 이하**

<br>

기본적으로 [ASP.Net에서](http://ASP.Net에서) 서버에 업로드 할 파일의 최대 크기는 약 **4MB** 입니다. web.config 에서 **maxRequestLength** 속성 을 수정 **하여이** 값을 늘릴 수 있습니다.

**기억하십시오 : `maxRequestLenght는 KB`입니다**

**예** : 업로드를 15MB로 제한하려면 maxRequestLength를“15360” (`15 x 1024`)으로 설정하십시오.

```html
<system.web>
   <!-- maxRequestLength for asp.net, in KB -->
   <httpRuntime maxRequestLength="15360" ></httpRuntime>
</system.web>
```

<br>

## **2. IIS7 이상**

<br>

files.IIS7을 업로드 여기에 사용되는 약간 다른 방법이 도입 **모듈 필터링 요청을** 하는 방식 파이프 라인의 작품이다 .Which이 [ASP.Net.Means](http://ASP.Net.Means) 전에 실행을 **Length 요청 헤더**  먼저 [ASP.NET](http://ASP.NET) 값 (체크 **maxRequestLength의**  확인됩니다. maxAllowedContentLength 속성의 기본값은 **28.61MB** 입니다. 동일한 web.config 에서 두 속성을 수정 **하여이** 값을 늘릴 수 있습니다 .

**기억하십시오 : `maxAllowedContentLength는 바이트`입니다**

**예** : 업로드를 15MB로 제한하려면 maxRequestLength를 “15360”으로 설정하고 maxAllowedContentLength를 “15728640” `15 x 1024 x 1024`으로 설정하십시오.

```html
<system.web>
   <!-- maxRequestLength for asp.net, in KB -->
   <httpRuntime maxRequestLength="15360" ></httpRuntime>
</system.web>

<system.webServer>
   <security>
      <requestFiltering>
         <!-- maxAllowedContentLength, for IIS, in bytes -->
         <requestLimits maxAllowedContentLength="15728640" ></requestLimits>
      </requestFiltering>
   </security>
</system.webServer>
```
