# Pigeon Message Delivery Service APIs


----

.. contents:: 목차

----

1. 개요
====
Pigeon 메일 발송 서비스 API입니다. 본 문서에서는 Pigeon API의 규격을 설명하고 있으며,  
기본적인 개념과 API 요청/응답 메시지에 대한 설명이 포함되어 있습니다.

1.1. Pigeon API 개요
----
Pigeon API는 RESTful 형태로 제공됩니다. 따라서 자원은 유일한 URI를 가지며 자원에 대한 작업은 HTTP 메서드와 매핑되어
HTTP 메서드의 GET, POST, PUT, DELETE는 각각 조회, 전송, 수정, 삭제에 해당됩니다.

1.2. API 요청
----
API 요청 메시지는 아래와 같이 구성됩니다.

``(1)API_SERVER_URL + (2)/API_URI + (3)응답 포맷``

1. (1) API_SERVER_URL : Pigeon API 서버 URL로써 ``https://pigeon.csb.io/api/v1`` 입니다.
2. (2) API_URI : 각 API의 URI입니다.
3. (3) 응답 포맷 : 응답 포맷 형식을 확장자 형태로 전달합니다. 예) ``/email.json``

API 요청 시 API 키가 필요하며, 콘솔에서 애플리케이션 등록 후 애플리케이션에서 메시지 채널을 활성화하여 API 키를 관리할 수 있습니다. 애플리케이션이나 메시지 채널은 다음 챕터에서 확인할 수 있습니다.

API 키는 요청 시 ``X-Pigeon-Api-Key`` 헤더에 API 키를 보내야 합니다. 예) ``X-Pigeon-Api-Key: API_KEY``

----

2. 애플리케이션, 채널, API 키 관리
====

2.1. 애플리케이션 등록
----
API를 사용하는 애플리케이션 식별 및 API 요청 할당량 관리를 위해 회원 가입 후 애플리케이션 정보를 등록해야합니다.

2.2. 채널
----
채널은 메시지를 보낼 대상이며, 현재는 E-mail에 대해서만 지원합니다.

추후 Slack, Google Hangout, 메신져 등과 연동할 수 있도록 확장할 예정입니다.

### 2.2.1. 채널 활성화
애플리케이션에서 메시지를 전송하기 위해서는 메시지를 보낼 채널에 대해 활성화를 해야합니다.
추후 타 API와 연동 시 OAuth2 등의 추가적인 인증이 필요할 수 있기 때문에, 각 채널에 대한 활성화를 별도로 진행해야 합니다.

2.3. API키 관리
----
애플리케이션에서 활성화한 채널의 API 키를 관리할 수 있습니다. API는 클라이언트의 용도에 따라 구분하여 사용할 수 있도록 채널에 다수의 API키를 추가하여 사용할 수 있습니다.

### 2.3.1. API 키 추가
API 키를 추가하여 해당 채널에 대한 API 키를 받을 수 있습니다. API 키 유효기간의 기본값은 무제한입니다.

### 2.3.2. API 키 삭제
추가한 API 키에서 삭제 버튼을 눌러 API 키를 삭제할 수 있으며, 삭제한 API 키로 요청한 API는 ``403 EXPIRED_API_KEY`` 응답을 받게 됩니다.

### 2.3.3. API 키 유효기간 설정
API 키의 유효기간을 무제한, 날짜 지정, 상대 날짜 지정을 통해 설정할 수 있습니다.  

----

3.1. E-mail 채널
====
메시지를 E-mail로 발송할 수 있는 채널입니다.

### 3.1.1. E-mail 발송 ``POST /email``
요청 파라미터

<table width="100%">
<tr><td colspan="2">파라미터 목록</td></tr>
<tr>
  <td>**message**\*<br>`Struct`</td>
  <td>전송할 E-mail 메시지 정보
    <table width="100%">
      <tr>
        <td>html<br><code>String</code></td>
        <td>발송할 E-mail 메시지의 HTML 본문</td>
      </tr>
      <tr>
        <td>text<br><code>String</code></td>
        <td>발송할 E-mail 메시지의 텍스트 본문</td>
      </tr>
      <tr>
        <td>subject<br><code>String</code></td>
        <td>제목</td>
      </tr>
      <tr>
        <td>**from_email**\*<br>`String`</td>
        <td>발신자 E-mail 주소</td>
      </tr>
      <tr>
        <td>from_name<br>`String`, `null`</td>
        <td>발신자 이름</td>
      </tr>
      <tr>
        <td>to<br><code>Array</code></td>
        <td>수신자 목록
          <table width="100%">
            <tr><td>email<br>`String`</td><td>수신자 E-mail 주소</td></tr>
            <tr><td>name<br>`String`</td><td>수신자 이름</td></tr>
            <tr><td>type<br>`String`</td><td>수신자 타입(to, cc, bcc)</td></tr>
          </table>
        </td>
      </tr>
      <tr><td>headers<br>``Struct``</td><td>E-mail 메시지에 추가할 부가적인 헤더
<pre>"headers": {
  "Header-Key1": "value1",
  "Header-Key2": "value2"
}</pre>
      </td></tr>
      <tr><td>important<br>`Boolean`</td><td>중요 메일 표시 여부</td></tr>
      <tr><td>preserve_recipients<br>`Boolean`</td><td>모든 수신자를 E-mail 헤더의 To에 표시할 지 여부</td></tr>
      <tr><td>bcc_address</td><td>개별 수신자의 E-mail을 수신 받을 숨긴 참조 이메일 주소</td></tr>
      <tr><td>merge<br>`Boolean`, true</td><td>머지(Merge)기능 사용 여부</td></tr>
      <tr><td>global\_merge\_vars<br>``Array``, `null`</td><td>전역 머지 태그
        <table width="100%">
          <tr><td>name\*<br>`String`</td><td>전역 머지 변수 이름, 대소문자를 구분하지 않으며 언더스코어(\_)로 시작할 수 없습니다.</td></tr>
          <tr><td>content\*<br>`String`</td><td>전역 머지 변수 내용</td></tr>
        </table>
<pre>"global\_merge\_vars": [
  {
    "name": "greeting",
    "content": "Hello world!"
  },
  {
    "name": "foo",
    "content": "bar"
  }
]</pre>
      </td></tr>
      <tr><td>merge\_vars<br>``Array``, `null`</td><td>사용자별 머지 변수, 전역 머지 변수와 이름이 같은 경우 오버라이드합니다.
        <table width="100%">
          <tr><td>**rcpt**\*<br>`String`</td><td>머지 변수를 적용할 수신자의 E-mail 주소</td></tr>
          <tr><td>vars<br>`Array`, `null`</td><td>머지 변수 목록
            <table width="100%">
              <tr><td>name\*<br>`String`</td><td>머지 변수 이름, 대소문자를 구분하지 않으며 언더스코어(_)로 시작할 수 없습니다.</td></tr>
              <tr><td>content\*<br>`String`</td><td>머지 변수 내용</td></tr>
            </table>
          </td></tr>
        </table>
<pre>"merge\_vars": {
  "rcpt": "pigeon@csb.io",
  "vars": [
    {
      "name": "foo",
      "content": "bar"
    },
    {
      "name": "created\_date",
      "content": "2015-04-26"
    }
  ]
}</pre>
      </td></tr>
      <tr><td>metadata<br>``Struct``, `null`</td><td>발송하는 E-mail과 관련된 메타 데이터이며 탐색할 수 있습니다
<pre>"metadata": {
  "custom_meta1": "value1",
  "custom_meta2": "value2"
}</pre>
      </td></tr>
      <tr><td>recipient_metadata<br>``Struct``, `null`</td><td>개별 사용자의 메타 데이터입니다.
<pre>"recipient_metadata": {
  "rcpt": "pigeon@csb.io",
  "values": {
    "custom_meta1": "foo",
    "custom_meta2": "bar"
  }
}</pre>
      </td></tr>
      <tr><td>attachments<br>``Struct``, `null`</td><td>첨부파일 목록입니다.
        <table width="100%">
          <tr><td>type\*<br>`String`</td><td>첨부 파일의 MIME 타입</td></tr>
          <tr><td>name\*<br>`String`</td><td>첨부 파일 이름</td></tr>
          <tr><td>content\*<br>`String`</td><td>첨부파일의 내용을 Base64로 인코딩한 문자열</td></tr>
        </table>
      </td></tr>
      <tr><td>images<br>``Struct``, `null`</td><td>메시지에 첨부할 이미지
        <table width="100%">
          <tr><td>type\*<br>`String`</td><td>이미지 파일의 MIME 타입, "image/"로 시작해야 합니다.</td></tr>
          <tr><td>name\*<br>`String`</td><td>이미지 ID, E-mail HTML 본문 내에서 &lt;img src="cid:{name}"&gt;와 같이 사용합니다.</td></tr>
          <tr><td>content\*<br>`String`</td><td>이미지 파일의 내용을 Base64로 인코딩한 문자열입니다.</td></tr>
        </table>
      </td></tr>
    </table>
  </td>
</tr>
</table>

요청 본문 예

    {
        "message": {
            "html": "<p>Example HTML content</p>",
            "text": "Example text content",
            "subject": "example subject",
            "from_email": "message.from_email@example.com",
            "from_name": "Example Name",
            "to": [
                {
                    "email": "recipient.email@example.com",
                    "name": "Recipient Name",
                    "type": "to"
                }
            ],
            "headers": {
                "Reply-To": "message.reply@example.com"
            },
            "important": false,
            "track_opens": `null`,
            "track_clicks": `null`,
            "auto_text": `null`,
            "auto_html": `null`,
            "inline_css": `null`,
            "url_strip_qs": `null`,
            "preserve_recipients": `null`,
            "view_content_link": `null`,
            "bcc_address": "message.bcc_address@example.com",
            "tracking_domain": `null`,
            "signing_domain": `null`,
            "return_path_domain": `null`,
            "merge": true,
            "merge_language": "mailchimp",
            "global_merge_vars": [
                {
                    "name": "merge1",
                    "content": "merge1 content"
                }
            ],
            "merge_vars": [
                {
                    "rcpt": "recipient.email@example.com",
                    "vars": [
                        {
                            "name": "merge2",
                            "content": "merge2 content"
                        }
                    ]
                }
            ],
            "tags": [
                "password-resets"
            ],
            "subaccount": "customer-123",
            "google_analytics_domains": [
                "example.com"
            ],
            "google_analytics_campaign": "message.from_email@example.com",
            "metadata": {
                "website": "www.example.com"
            },
            "recipient_metadata": [
                {
                    "rcpt": "recipient.email@example.com",
                    "values": {
                        "user_id": 123456
                    }
                }
            ],
            "attachments": [
                {
                    "type": "text/plain",
                    "name": "myfile.txt",
                    "content": "ZXhhbXBsZSBmaWxl"
                }
            ],
            "images": [
                {
                    "type": "image/png",
                    "name": "IMAGECID",
                    "content": "ZXhhbXBsZSBmaWxl"
                }
            ]
        },
        "async": false,
        "ip_pool": "Main Pool",
        "send_at": "example send_at"
    }

응답 본문
```
[
	{
    	"email": "pigeon@csb.io",
        "status": "sent",
        "reject_reason": `null`,
        "_id": "abc123abc123abc123abc123abc123"
    }
]
```
에러 응답 시
```
[
	"status": "error",
    "code": 12,
    "name": "Unknown_Subaccount",
    "message": "No subaccount exists with the id 'pigeon-sa-001'"
]
```
### 머지(Merge)

Pigeon은 Merge 기능을 제공합니다. Merge 기능을 통해 다수의 사용자에게 E-mail을 전송할 시 전역적 또는 사용자별로 E-mail 본문의 내용을 개인화하여 보낼 수 있습니다.
E-mail 전송 API 이용 시 머지 태그를 이용하여 머지 기능을 사용할 수 있으며 사용 방법은 아래와 같습니다.

#### 포맷
	
    *|MERGETAG_NAME|*
    
E-mail 본문 내에서 위와 같이 애스터리스크 문자(별표, *)와 파이프 문자(|)로 머지 태그 이름을 삽입하고, API 요청 시에 머지 태그와 대체할 문자열 값을 전달하면 변환된 본문 내용으로 발송하게 됩니다. 아래는 예시입니다.
```
*|NAME|*님, 안녕하세요
*|ORDERDATE|*에 CSB.IO에서 주문하신 내역입니다.

*|INVOICEDETAILS|*

감사합니다.
*|BRAND_NAME|* 드림
```
머지 태그는 `global_merge_vars`와 `merge_vars`로 나뉘어집니다. `global_merge_vars`는 모든 수신자에게 동일하게 적용되는 머지 태그이며, `merge_vars`는 개별 수신자에게 적용되는 머지 태그입니다. API 요청 시에 message 객체 내에 각각 `global_merge_vars` 속성과 `merge_vars` 속성으로 머지 태그를 전달합니다.  
머지 태그 키는 대소문자를 구분하지 않는 알파벳과 언더스코어(_) 문자를 사용할 수 있습니다.
```
"message": {
	
    ...
    
	"global_merge_vars": [
    	{
        	"name": "brand_name",
            "content": "CSB.IO"
        }
    ],
    "merge_vars": [
    	{
        	"rcpt": "junyoung.plum@gmail.com",
            "vars": [
            	{
                	"name": "name",
            		"content": "Junyoung Lee"
                },
                {
                	"name": "orderdate",
                    "content": "2015-04-30 13:42:11",
                },
                {
                	"name": "invoicedetails",
                    "content": "100,000 포인트 결제 (INICIS)"
                },
            ]
        }
    ]
    
    ...
    
}
```


### 3.1.3. E-mail 발송 상태 확인 ``GET /email/{task_id}``
E-mail 발송 요청이 정상적으로 처리되면 E-mail ``task_id``를 응답으로 받을 수 있습니다. ``task_id``를 통해 발송 상태를 조회할 수 있습니다.