# درخواست اجازه از طریق OAuth

مشتری‌های OAuth نوبیتکس به هنگام ثبت‌نام یک شناسه‌ی مشتری (client ID) و یک پسورد کلاینت (client secret) از نوبیتکس دریافت خواهند کرد. سپس برای راهنمایی کردن کاربر به لاگین با نوبیتکس، دکمه‌ای با لینک مشابه زیر قرار خواهند داد:

```html
<a href="http://nobitex.ir/oauth/authorization
?response_type=code
&code_challenge=XRi41b-5yHtTojvCpXFpsLUnmGFz6xR15c3vpPANAvM
&code_challenge_method=S256
&client_id=vW1RcAl7Mb0d5gyHNQIAcH110lWoOW2BmWJIero8
&redirect_uri=http://some.redirect.uri.ir/subdomain
&scopes=user-info%20profile-info
&state=5ca75bd30">
لاگین با نوبیتکس</a>
```

پس از لاگین موفق کاربر در نوبیتکس، او با یک کد authorization به آدرس موجود در redirect_uri راهنمایی خواهد شد. مشتری نوبیتکس می‌تواند از این کد استفاده کرده و در API زیر آن را با توکن‌های دسترسی و بازیابی مبادله نماید.


## گرفتن توکن‌های دسترسی و بازیابی در ازای Authorization Code

این API هنگامی صدا زده می‌شود که کلاینت بخواهد یک توکن دسترسی و یک توکن بازیابی دریافت کند. این توکن‌ها یا در ازای یک authorization code (که از طریق API بالا دریافت می‌شود) به کلاینت داده می‌شوند یا در ازای یک توکن بازیابیِ معتبر (= استفاده نشده) که در فراخوانی قبلیِ همین API دریافت شده باشد.

>* هنگامی که این API با گرنت refresh_token فراخوانی شود، پاسخ صحیح API شامل توکن‌های جدید خواهد بود و توکن بازیابی قبلی از کار خواهد افتاد. در صورتی که این پاسخ به هر دلیلی (مثلا خطای شبکه) به کلاینت نرسد، کلاینت می‌تواند تا یک دقیقه بعد دوباره با همان توکن بازیابی که از کار افتاده است، درخواست جدیدی بدهد و دوباره توکن‌های دسترسی و بازیابی جدید را دریافت کند.
>* این API دارای هدر Authorization است (با basic authorization شامل client_id و client_secret قبل از انکد شدن که متعلق به کلاینت هستند و در هنگام ثبت‌نام مشخص می‌شوند)


### نمونه‌ی درخواست‌ها:

>نمونه درخواست تعویض Authorization Code با توکن‌ها:

```shell
curl --location 'https://api.nobitex.ir/oauth/token' \
--header 'Content-Type: application/json' \
--header 'Authorization: Basic YOUR_BASIC_AUTHORIZATION_HERE' \
--data '{
    "grantType": "authorization_code",
    "redirectUri": "https://redirect.uri.ir",
    "codeVerifier": "GENERATOR_OF_CODE_CHALLENGE",
    "code": "AUTHORIZATION_CODE_HERE"
}'
```

```plaintext
POST /oauth/token HTTP/1.1
Host: api.nobitex.ir
Content-Type: application/json
Authorization: Basic YOUR_BASIC_AUTHORIZATION_HERE
Content-Length: 200

{
    "grantType": "authorization_code",
    "redirectUri": "https://redirect.uri.ir",
    "codeVerifier": "GENERATOR_OF_CODE_CHALLENGE",
    "code": "AUTHORIZATION_CODE_HERE"
}
```


>نمونه درخواست تعویض توکن بازیابی با توکن‌های جدید:

```shell
curl --location 'https://api.nobitex.ir/oauth/token' \
--header 'Content-Type: application/json' \
--header 'Authorization: Basic YOUR_BASIC_AUTHORIZATION_HERE' \
--data '{
    "grantType": "refresh_token",
    "redirectUri": "https://redirect.uri.ir",
    "refreshToken": "LATEST_REFRESH_TOKEN_HERE"
}'
```

```plaintext
POST /oauth/token HTTP/1.1
Host: api.nobitex.ir
Content-Type: application/json
Authorization: Basic YOUR_BASIC_AUTHORIZATION_HERE
Content-Length: 131

{
    "grantType": "refresh_token",
    "redirectUri": "https://redirect.uri.ir",
    "refreshToken": "LATEST_REFRESH_TOKEN_HERE"
}
```

### پاسخ درست:

> در صورت فراخوانی درست، پاسخ شامل یک توکن دسترسی و یک توکن بازیابی به همراه اسکوپ این توکن‌ها و مدت زمان معتبر بودن توکن دسترسی خواهد بود:

```json
{
  "status": "ok",
  "accessToken": "waZfPWfiTDRvQ08VFCATwESWCnZqvM",
  "expiresIn": 3600,
  "tokenType": "Bearer",
  "scope": "read",
  "refreshToken": "KLnYFONGgtBZJC2q5NU3Po1Nx245KL"
}
```

### پاسخ‌های همراه با خطا:


> **InvalidGrant:** این خطا در حالت‌های زیر ارسال می‌شود:
> * (در گرنت نوع authorization_code) زمانی که کد ارسال شده (code) برابر با authorization code ی که قبلا (پس از لاگین موفق کاربر) برای کلاینت فرستادیم نباشد،
> * (در گرنت نوع authorization_code) زمانی که codeVerifier به درستی ارسال نشده باشد (یعنی هشِ آن برابر با مقدار قبلی codeChallenge نشود)،
> * (در گرنت نوع refresh_token) هنگامی که refreshToken فرستاده‌شده برابر با آخرین توکن بازیابی‌ای که قبلا برای همین کاربر و در همین اسکوپ برای کلاینت فرستاده‌ایم، نباشد،
> * (در گرنت نوع refresh_token) هنگامی که refreshToken فرستاده‌شده قبلا استفاده شده باشد،
> * (در هر دو نوع گرنت) هنگامی که هر کدام از اطلاعات بالا متعلق به گرنت کلاینت دیگری باشد.

```json
{
  "status": "failed",
  "code": "InvalidGrant",
  "message": "Invalid grant"
}
```

> **InvalidRequest:** هنگامی این خطا فرستاده می‌شود که redirectUri جز URI های مشخص‌شده از قبل برای کلاینت نباشد (کلاینت هنگام ثبت‌نام این URI ها را مشخص می‌کند)، یا در گرنت نوع refresh_token مقدار refreshToken ارسال نشده باشد.

```json
{
  "status": "failed",
  "code": "InvalidRequest",
  "message": "Mismatching redirect URI."
}
```

> **UnsupportedGrantType:** هنگامی که grantType در ورودی چیزی به غیر از “authorization_code“ یا “refresh_token“ باشد.

```json
{
  "status": "failed",
  "code": "UnsupportedGrantType",
  "message": "Unsupported grant type"
}
```

> **InvalidClient:** هنگامی دریافت می‌شود که حداقل یکی از مقادیر client_id یا client_secret که در هدر authorization ارسال شده‌اند درست نباشند.

```json
{
  "status": "failed",
  "code": "InvalidClient",
  "message": "Invalid client"
}
```

> **InvalidIp:** هر کلاینت تنها اجازه دارد از یک IP مشخص درخواست بدهد. اگر IP استخراج شده از درخواست برابر با IP ثبت‌شده برای کلاینت نباشد، خطای زیر برگردانده می‌شود.

```json
{
  "status": "failed",
  "code": "InvalidIp",
  "message": "Invalid ip"
}
```


برای دریافت توکن‌های دسترسی و بازیابی از این API استفاده نمایید:

* **درخواست:** `POST /oauth/token`
* **محدودیت‌ها:**
  * محدودیت نرخ فراخوانی: این API برای هر IP می‌تواند حداکثر 20 بار در هر ۱۰ دقیقه صدا زده شود.
  * این API فقط از IP از پیش تعریف‌شده‌ی کلاینت قابل فراخوانی است و در غیر این صورت خطای InvalidIp با کد ۴۰۳ ارسال می‌شود.


### پارامترهای ورودی

| پارامتر        |  نوع   |                                                      اجباری | توضیحات                                                                                                                                                                                                                          | نمونه    |
| ------------- |:------:|------------------------------------------------------------:|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----|
| grantType      | string |                                                         بله | اگر کلاینت در ازای authorization code بخواهد توکن دریافت کند، مقدار این فیلد برابر با “authorization_code“ خواهد بود و در صورتی که با استفاده از توکن بازیابی درخواست بدهد، مقدار این فیلد برابر با “refresh_token“ خواهد بود.   |  “authorization_code“ یا “refresh_token“   |
| codeVerifier      | string | بله، در صورتی که grantType برابر “authorization_code“ باشد. | مقدار این فیلد، سازنده‌ی codeChallenge است که در API قبلی دریافت شده بود. رابطه‌ی بین این دو مقدار در [اینجا](https://django-oauth-toolkit.readthedocs.io/en/latest/getting_started.html#authorization-code) توضیح داده شده است. |   'F7PGX1U2BDV6SUNLQGSE21ICNW5Z73G3F76LP93UPW7N13RT'  |
| redirectUri | string |                                                         بله | ادرس برگشت. باید با یکی از آدرس‌های ثبت‌شده در هنگام ثبت نام برابر باشد.                                                                                                                                                         |  “www.nobitex.ir“   |
|    code         | string |          بله، در صورتی که grantType برابر “authorization_code“ باشد.                                                   | authorization code دریافت شده پس از لاگین کاربر                                                                                                           |           “EkwjHBiAf4FY6TTs4XVuZW8JbpocaA“          |
|      refreshToken       | string |        بله، در صورتی که grantType برابر “refresh_token“ باشد.                                                     |                        توکن بازیابی دریافت‌شده در فراخوانی قبلی همین API                                                                                    |         “KLnYFONGgtBZJC2q5NU3Po1Nx245KL”            |


### پارامترهای پاسخ

| پارامتر       | نوع    | توضیحات                                  | نمونه |
|---------------|--------|------------------------------------------| --------- |
| status        | string | وضعیت پاسخ                               | ok یا failed |
| accessToken   | string | توکن دسترسی                              | waZfPWfiTDRvQ08VFCATwESWCnZqvM |
| expiresIn     | int    | مدت زمان معتبر بودن توکن دسترسی به ثانیه | 3600 |
| tokenType     | string | نوع توکن                                 | Bearer |
| scope         | string | اسکوپی که این توکن‌ها در آن معتبر هستند  | user-info |
|  refreshToken | string | توکن بازیابی                             | KLnYFONGgtBZJC2q5NU3Po1Nx245KL |



## گرفتن اطلاعات عمومی کاربر

کلاینت از طریق این API می‌تواند درخواست دریافت اطلاعات کاربر از قبیل نام و نام‌خانوادگی و سطحش را بدهد.

> * این API دارای هدر Authorization است و توکنِ دسترسیِ کلاینت به شکل Bearer ACCESS_TOKEN باید در این هدر فرستاده شود.

>نمونه درخواست:

```shell
curl --location 'https://api.nobitex.ir/oauth-services/user-info' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer CLIENTS_ACCESS_TOKEN_HERE'
```


```plaintext
GET /oauth-services/user-info HTTP/1.1
Host: api.nobitex.ir
Content-Type: application/json
Authorization: Bearer CLIENTS_ACCESS_TOKEN_HERE
```

### خروجی موفق:

> در صورت فراخوانی درست، پاسخ به این صورت خواهد بود:

```json
{
  "status": "ok",
  "data": {
    "firstName": "first_name",
    "lastName": "last_name",
    "birthdate": "2023-11-11",
    "level": "Level2"
  }
}
```

### خروجی‌های ناموفق:

> **توکن نامعتبر:** در صورتی که توکنی در هدر Authorization فرستاده نشود یا توکن اشتباه باشد، خطای زیر دریافت می‌شود:

```json
{"detail": "اطلاعات برای اعتبارسنجی ارسال نشده است."}
```

> **عدم داشتن مجوز:** در صورتی که توکنی که فرستاده می‌شود دارای اسکوپ user-info نباشد یا API از IP از پیش تعیین شده ارسال نشود، این خطا فرستاده می‌شود

```json
{"detail": "شما اجازه انجام این دستور را ندارید."}
```


برای گرفتن اطلاعات مخصوص به کاربر از این API استفاده کنید:

* **درخواست:** `GET /oauth-services/user-info`
- **محدودیتها:**
  * محدودیت نرخ فراخوانی: این API برای هر IP یا کاربر می‌تواند حداکثر ۱۰ بار در هر دقیقه صدا زده شود.
  * این API فقط از IP از پیش تعریف‌شده‌ی کلاینت قابل فراخوانی است و در غیر این صورت کد ۴۰۳ ارسال می‌شود.
  * این API فقط با توکنی که حداقل اسکوپ user-info را داشته باشد می‌تواند صدا زده شود، در این غیر این‌صورت کد ۴۰۳ ارسال می‌شود.


### پارامترهای پاسخ

پارامتر       | نوع    | توضیحات    |
------------- |--------|-------------------------------------------------------------| ---------|
status        | string | وضعیت پاسخ                                                  | ok|
data     | dict   | یک دیکشنری با کلیدهای firstName, lastName, birthdate, level | {"firstName": "first_name", "lastName": "last_name", "birthdate": "2023-11-11", "level": "Level2"}|



## گرفتن اطلاعات پروفایل کاربر

کلاینت از طریق این API می‌تواند درخواست دریافت اطلاعات پروفایل کاربر از جمله ID یکتا برای شناسایی او و شماره موبایل و ایمیلش را بدهد.

> * این API دارای هدر Authorization است و توکنِ دسترسیِ کلاینت به شکل Bearer ACCESS_TOKEN باید در این هدر فرستاده شود.

>نمونه درخواست:

```shell
curl --location 'https://api.nobitex.ir/oauth-services/profile-info' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer CLIENTS_ACCESS_TOKEN_HERE'
```


```plaintext
GET /oauth-services/profile-info HTTP/1.1
Host: api.nobitex.ir
Content-Type: application/json
Authorization: Bearer CLIENTS_ACCESS_TOKEN_HERE
```

### خروجی موفق:

> در صورت فراخوانی درست، پاسخ به این صورت خواهد بود:

```json
{
  "status": "ok",
  "data": {
    "userId": "2f1ee9b7415c7a9a43feaedcd89e264edc1863fd77e05683441b785c14d833dd",
    "email": "user2@example.com",
    "phoneNumber": "09123456789"
  }
}
```
در صورتی که هر کدام از ایمیل یا شماره‌ی موبایل کاربر اعتبارسنجی نشده باشند، به جای آن‌ها None برگردانده می‌شود.

### خروجی‌های ناموفق:

> **توکن نامعتبر:** در صورتی که توکنی در هدر Authorization فرستاده نشود یا توکن اشتباه باشد، خطای زیر دریافت می‌شود:

```json
{"detail": "اطلاعات برای اعتبارسنجی ارسال نشده است."}
```

> **عدم داشتن مجوز:** در صورتی که توکنی که فرستاده می‌شود دارای اسکوپ user-info نباشد یا API از IP از پیش تعیین شده ارسال نشود، این خطا فرستاده می‌شود

```json
{"detail": "شما اجازه انجام این دستور را ندارید."}
```


برای دریافت اطلاعات مربوط به پروفایل کاربر این API را فراخوانی کنید:

* **درخواست:** `GET /oauth-services/profile-info`
- **محدودیتها:**
  * محدودیت نرخ فراخوانی: این API برای هر IP یا کاربر می‌تواند حداکثر ۱۰ بار در هر دقیقه صدا زده شود.
  * این API فقط از IP از پیش تعریف‌شده‌ی کلاینت قابل فراخوانی است و در غیر این صورت کد ۴۰۳ ارسال می‌شود.
  * این API فقط با توکنی که حداقل اسکوپ user-info را داشته باشد می‌تواند صدا زده شود، در این غیر این‌صورت کد ۴۰۳ ارسال می‌شود.


### پارامترهای پاسخ

| پارامتر | نوع    | توضیحات                                                     | نمونه |
|---------|--------|-------------------------------------------------------------| --------- |
| status  | string | وضعیت پاسخ                                                  | ok |
|  data   | dict   | یک دیکشنری با کلیدهای userId, email, phoneNumber | {"userId": "2f1ee9b7415c7a9a43feaedcd89e264edc1863fd77e05683441b785c14d833dd", "email": "user2@example.com", "phoneNumber": "09123456789"} |

مقدار userId برابر با یک شناسه‌ی یکتا برای آن کاربر در سایت کلایت می‌باشد و برای این کاربر همواره همین مقدار خواهد بود (مثلا لاگین‌های جدید یا لاگین از دستگاه جدید و ... تغییری در این شناسه ایجاد نمی‌کنند)
