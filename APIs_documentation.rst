====================================
Pigeon Message Delivery Service APIs
====================================

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
#. (2) API_URI : 각 API의 URI입니다.
#. (3) 응답 포맷 : 응답 포맷 형식을 확장자 형태로 전달합니다. 예) ``/email.json``

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

2.2.1. 채널 활성화
^^^^
애플리케이션에서 메시지를 전송하기 위해서는 메시지를 보낼 채널에 대해 활성화를 해야합니다.
추후 타 API와 연동 시 OAuth2 등의 추가적인 인증이 필요할 수 있기 때문에, 각 채널에 대한 활성화를 별도로 진행해야 합니다.

2.3. API키 관리
----
애플리케이션에서 활성화한 채널의 API 키를 관리할 수 있습니다. API는 클라이언트의 용도에 따라 구분하여 사용할 수 있도록 채널에 다수의 API키를 추가하여 사용할 수 있습니다.

2.3.1. API 키 추가
^^^^
API 키를 추가하여 해당 채널에 대한 API 키를 받을 수 있습니다. API 키 유효기간의 기본값은 무제한입니다.

2.3.2. API 키 삭제
^^^^
추가한 API 키에서 삭제 버튼을 눌러 API 키를 삭제할 수 있으며, 삭제한 API 키로 요청한 API는 ``403 EXPIRED_API_KEY`` 응답을 받게 됩니다.

2.3.3. API 키 유효기간 설정
^^^^
API 키의 유효기간을 무제한, 날짜 지정, 상대 날짜 지정을 통해 설정할 수 있습니다.

----

3.1. E-mail 채널
====
메시지를 E-mail로 발송할 수 있는 채널입니다.

3.1.1. E-mail 발송 ``POST /email``
----
요청 본문

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
            "track_opens": null,
            "track_clicks": null,
            "auto_text": null,
            "auto_html": null,
            "inline_css": null,
            "url_strip_qs": null,
            "preserve_recipients": null,
            "view_content_link": null,
            "bcc_address": "message.bcc_address@example.com",
            "tracking_domain": null,
            "signing_domain": null,
            "return_path_domain": null,
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


머지(Merge)
^^^^
Pigeon은 Merge 기능을 제공합니다. Merge 기능을 통해 다수의 사용자에게 E-mail을 전송할 시 전역적 또는 사용자별로 E-mail 본문의 내용을 달리 보낼 수 있습니다.

3.1.3. E-mail 발송 상태 확인 ``GET /email/{task_id}``
----
E-mail 발송 요청이 정상적으로 처리되면 E-mail ``task_id``를 응답으로 받을 수 있습니다. ``task_id``를 통해 발송 상태를 조회할 수 있습니다.
