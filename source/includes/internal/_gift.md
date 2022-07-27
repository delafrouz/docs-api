# گیفت کارت

کارت هدیه‌ی نوبیتکس، امکان ارسال هدیه‌ به صورت فیزیکی و مجازی را به شخص دیگری فراهم می‌کند. فرایند دریافت هدیه نیز
می‌تواند به صورت داخلی و یا از نوع lightning باشد.
فرآیند کلی به این صورت هست که ابتدا کاربر درخواست خود را از طریق APIی ایجاد هدیه ثبت می‌کند و بعد از چک شدن محدودیت‌های برداشت کاربر شی هدیه ایجاد می‌شود. در این قسمت کاربر می‌تواند نوع هدیه که فیزیکی یا مجازی بودنش و همینطور نوع دریافت که به صورت برداشت داخلی و یا lightningای باشد را انتخاب کند.
مرحله‌ی بعد تایید برداشت ابتدایی هدیه است که در این گام از APIی تایید برداشت معمول استفاده می‌شود و فرآیند ایجاد هدیه تکمیل می‌شود.
گام بعد برای دریافت هدیه دریافت اطلاعات مربوط به هدیه از طریق api لندینگ هدیه است که اگر کاربر فرستنده شماره همراه گیرنده را وارد کرده باشد در این مرحله رمزیکبار مصرف برای کاربر ارسال می‌شود.
در نهایت گیرنده با وارد کردن رمزعبوری که گیرنده برای او مشخص کرده و رمز یکبار مصرف (در صورت ارسال) قادر به دریافت هدیه در APIی دریافت هدیه است.
اگر هدیه از نوع برداشت داخلی باشد برداشت به حساب گیرنده که حتما باید عضو نوبیتکس باشد زده می‌شود.
در غیر این صورت در پاسخ lnurl مربوط به هدیه در پاسخ فرستاده می‌شود.
لازم به ذکر است در صورتی که نوع دریافت lightning باشد گیرنده می‌تواند عضو نوبیتکس نباشد و در این حالت از APIی دریافت هدیه برای کاربران غیر عضو استفاده می‌شود که در صورت صحت اطلاعات ورودی پاسخ lnurl مربوط به هدیه است.
برای هر نوع از دریافت هدیه گیرنده می‌تواند کاربر نوبیتکس نباشد و قبل از دریافت هدیه عضو شود.
از آنجایی در حالت دریافت lightning دریافت کننده می‌تواند کاربر نوبیتکس نباشد برای بعضی از APIهای این مجموعه احراز هویت الزامی نیست.

##ایجاد هدیه

>نمونه درخواست:

```shell
curl -X POST 'https://api.nobitex.ir/gift/create-gift' \
-H 'Authorization: Token yourTOKENhereHEX0000000000' \
-H 'X-TOTP: 2568148' \
-H 'Content-Type: application/json' \
--data '{
    "amount": 0.02,
    "currency": "bnb",
    "gift_type": "digital",
    "package_type": "default",
    "mobile": "09127759318",
    "email": "test@gmail.com",
    "gift_sentence": "تقدیم به شما",
    "receiver_address": "iran-tehran",
    "receiver_postal_code": "3348-8841",
    "card_design": "default",
    "password": "testpass123",
    "redeem_type": "internal",
    "redeem_date": "2023-11-12",
    "receiver_full_name": "ali karimi",
    "otp_enabled": true,
    "is_sealed": false
}'
```


> در صورت فراخوانی درست، پاسخ به این صورت خواهد بود:

```json
{
    "status": "ok",
    "gift_withdraw_id": 158,
    "cost": "100"
}
```

برای ایجاد هدیه‌ی باید از درخواست با مشخصات زیر استفاده نمایید:
لازم به ذکر است برای فراخوانی این API کاربر باید لاگین باشد. همینطور در صورت انتخاب نوع هدیه‌ی فیزیکی آدرس و کد پستی گیرنده الزامی است.
مرحله‌ی بعد برای ایجاد هدیه و تکمیل فرآیند هدیه فراخوانی تایید برداشت هدیه است و پس از فراخوانی موفق تایید برداشت فرآیند ایجاد هدیه تکمیل خواهد شد. 

<aside class="notice">
مقادیر مورد قبول برای نوع هدیه:
1- digital 
2- physical
خواهند بود.
</aside>
<aside class="notice">
مقادیر مورد قبول برای نوع دریافت هدیه:
1- internal 
2- lightning
خواهند بود.
</aside>
<aside class="notice">
مقادیر مورد قبول برای طرح کارت:
1- grand 
2- mind
3- cute
2- light
خواهند بود.
</aside>
<aside class="notice">
مقادیر مورد قبول حال حاضر برای نوع پکیج:
1- default 
خواهند بود.
</aside>


* آدرس: `POST /gift/create-gift`

* پارامترهای ورودی:

پارامتر | نوع        | پیش‌فرض | توضیحات                     | نمونه
------- |------------|---------|-----------------------------| ---------
amount | monetary   | الزامی  | مقدار رمزارز                | "0.02"
currency | string     | الزامی  | رمزارز                      | "BTC"
mobile | string     | اختیاری | همراه گیرنده                | "9127774565"
email | string     | اختیاری | ایمیل گیرنده                | "test@gmail.com"
gift_type | string     | الزامی  | نوع هدیه                    | "pyshical"
gift_sentence | string     | الزامی  | جمله برای گیرنده            | "تقدیم به شما"
package_type | string     | اختیاری | نوع پکیج                    | "default"
receiver_address | string     | اختیاری | آدرس گیرنده                 | "iran-tehran"
receiver_postal_code | string     | اختیاری | کد پستی گیرنده              | "3354-7721"
receiver_full_name | string     | الزامی  | نام و نام خانوادگی گیرنده   | "masoud mohamadi"
card_design | string     | الزامی  | طرح کارت                    | "default"
redeem_date | iso-string | اختیاری | تاریخ دریافت                | "2022-09-17"
redeem_type | string     | الزامی  | نوع دریافت                  | "lightning"
password | string     | الزامی  | گذرواژه‌ی گیرنده            | "testpass123"
otp_enabled | boolean    | الزامی  | دومرحله‌ای بودن دریافت هدیه | true
is_sealed | boolean    | اختیاری | مهر و موم داشتن بسته‌بندی   | false


### حالت‌های خطا
کد خطا | توضیحات
---- | ----
ValidationError | وارد نشدن آدرس و کد پستی در صورت انتخاب نوع هدیه‌ی فیزیکی 
ValidationError |  در صورت وارد شدن تاریخ مربوط به گذشته 
InvalidAmount |مقدار وارد شده برای مقادیر ثابت ریالی جز مقادیر در نظر گرفته شده نباشد 
InvalidRequest |ایمیل وارد شده ایمیل درخواست دهنده باشد در حالت دیجیتال 
PleaseEnable2FA |سطح کاربر بالاتر از ۲ باشد و درخواست ریالی نباشد و دوعاملی کاربر فعال نشده باشد 
Invalid2FA |دوعاملی کاربر فعال باشد و رمزیکبار مصرف اشتباه وارد شده باشد 
WithdrawUnavailable |در صورتی که کاربر محدودیت برداشت داشته باشد و مجوز برداشتی برای او تعریف نشده باشد 
WithdrawAmountLimitation |در صورتی که کاربر سقف برداشت مجازش را برای رمزارز مشخصی رد کرده باشد 
WithdrawLimitReached |در صورتی که کاربر از سقف تعداد برداشتش عبور کرده باشد 
WithdrawCurrencyUnavailable |در صورتی که برداشت از رمزارز درخواست شده امکان پذیر نباشد 
WithdrawDisabled |در صورتی که برداشت برای رمزارزی موقتی بسته باشد 
InsufficientRialBalance |در صورتی که کاربر مقدار بالانس مورد نیاز برای هزینه‌ی صدور کارت فیزیکی را نداشته باشد 
InsufficientBalance |در صورتی که مقدار درخواستی کاربر از مقدار بالانسش بیشتر باشد 
AmountTooLow |اگر مقدار درخواستی کاربر کمتر از حداقل مقدار برداشت رمزارز مورد نظر باشد 
AmountTooHigh |اگر مقدار درخواستی کاربر بیشتر از حداکثر مقدار برداشت رمزارز مورد نظر باشد 
InvalidMobileNumber |اگر کاربر موبایل تایید شده نداشته باشد 
InsufficientBalance |در صورتی که تراکنش برای کسر هزینه‌ی صدور کارت فیزیکی موفقیت آمیز نباشد


* پارامترهای پاسخ:

پارامتر | نوع      | توضیحات             | نمونه
------- |----------|---------------------| ---------
status | string   | وضعیت پاسخ          | ok
gift_withdraw_id | integer  | شناسه‌ی برداشت      | 159
cost | monetary | هزینه‌ی ایجاد هدیه‌ | "100"

<aside class="notice">
محدودیت فراخوانی : 10 درخواست در دقیقه
</aside>

<aside class="notice">
آرگومان مورد نیاز برای فراخوانی APIی تایید برداشت در پاسخ این APIی موجود است و مقدار gift_withdraw_id است.
</aside>

<aside class="notice">
در صورتی که نوع هدیه دیجیتال باشد ارسال ایمیل گیرنده الزامی خواهد بود.
</aside>

<aside class="notice">
ارسال پارامتر is_sealed زمانی که هدیه فیزیکی است الزامی خواهد بود.
</aside>


## تایید برداشت
برای این منظور باید از APIی تایید درخواست برداشت در قسمت برداشت استفاده کرد.

```shell
curl -X POST 'https://api.nobitex.ir/users/wallets/withdraw-confirm' \
  -H 'Authorization: Token yourTOKENhereHEX0000000000' \
  -H 'Content-Type: application/json' \
  --data '{"withdraw": 432, "otp": 623005}'
```

* **درخواست:** `POST /users/wallets/withdraw-confirm`


<aside class="notice">
که مقدار withdraw را در این درخواست باید برابر مقدار gift_withdraw_id از پاسخ API ایجاد هدیه قرار داد.
</aside>

##لندینگ هدیه
کاربر بعد از اسکن کد هدیه‌ی خود به این صفحه انتقال داده می‌شود. که در انتهای مسیر این API کد هدیه‌ی کاربر قرار دارد.
لازم به ذکر است در صورتی که کاربر فرستنده، شماره موبایل کاربر گیرنده را وارد کرده باشد رمز یکبار مصرف در این مرحله برای کاربر فرستاده خواهد شد.

* آدرس: `GET /gift/<redeem-code>`

### حالت‌های خطا
کد خطا | توضیحات
---- | ----
InvalidRedeemCode | در صورتی که کد وارد شده نامعتبر باشد 

* پارامترهای پاسخ:

پارامتر | نوع      | توضیحات                                              | نمونه
------- |----------|------------------------------------------------------| ---------
redeem_code | string   | کد هدیه‌ي کاربر                                      | "FB57379C"
card_design | string   | طرح کارت                                             | "default"
currency | string   | رمزارز                                               | "bnb"
amount | monetary | مقدار رمزارز                                         | "0.02"
mobile_provided | boolean  | اینکه فرستنده شماره همراه گیرنده را ارسال کرده یا خیر | true
sentence | string   | جمله‌ی هدیه                                          | "تقدیم به شما"
redeem_type | string   | نوع دریافت هدیه                                      | "lightning"

<aside class="notice">
محدودیت فراخوانی : 5 درخواست در ۱ ساعت
</aside>

##دریافت هدیه‌ برای کاربران عضو

>نمونه درخواست:

```shell
curl -X POST 'https://api.nobitex.ir/gift/redeem' \
-H 'Authorization: Token yourTOKENhereHEX0000000000' \
-H 'Content-Type: application/json' \
--data '{
    "redeem_code": "30BBBBCA",
    "password": "testpass123",
    "otp": "813846"
}'
```

> در صورت فراخوانی درست، پاسخ به حالتی که دریافت به صورت lightning این صورت خواهد بود:

```json
{
    "status": "ok",
    "lnUrl": "lnurlZX6N..."
}
```

> در صورت فراخوانی درست، پاسخ به حالتی که دریافت داخلی باشد، به این صورت خواهد بود:

```json
{
    "status": "ok"
}
```

برای دریافت هدیه‌ی کاربری که عضو نوبیتکس است، فارق از نوع دریافت این API فراخوانی شود.
در این قسمت با توجه به نوع دریافت هدیه‌ی کاربر پاسخ متفاوت فرستاده خواهد شد.
اگر نوع دریافت به صورت داخلی انتخاب شده باشد بعد از بررسی صحت اطلاعات ورودی صرفا پاسخ موفقیت فرستاده خواهد شد.
در صورتی که نوع دریافت lightning باشد در پاسخ lnurl مربوط به هدیه فرستاده خواهد شد.
<aside class="notice">
آرگومان otp در این API اختیاری است زمانی نیاز به ارسال است که در پاسخ لندینگ مقدار mobile_provided صحیح (true) باشد.
</aside>
<aside class="notice">
در این مرحله برای امنیت بیشتر کاربران ارسال کپچا اجباری است.
</aside>

* آدرس: `POST /gift/redeem`

* پارامترهای ورودی:

پارامتر | نوع    | پیش‌فرض | توضیحات                          | نمونه
------- |--------|---------|----------------------------------| ---------
redeem_code | string | الزامی  | کد هدیه                          | "FB57379C"
password | string | الزامی  | گذرواژه‌ی هدیه                   | "testpass123"
otp | string | اختیاری | رمزیکبار مصرف ارسال شده به کاربر | "564785"
key | string | اختیاری | رمزیکبار مصرف ارسال شده به کاربر | "564785"
captcha | string | اختیاری | رمزیکبار مصرف ارسال شده به کاربر | "564785"
client | string | اختیاری | رمزیکبار مصرف ارسال شده به کاربر | "564785"


### حالت‌های خطا
کد خطا | توضیحات
---- | ----
InvalidCaptcha | در صورت اشتباه وارد شدن کپچا 
InvalidOTP |  اگر رمزیکبار مصرف ارسال شده به گوشی کاربر اشتباه وارد شده باشد 
InvalidPassword |اگر پین یا رمزعبور مشخص شده برای گیرند‌ه‌ی هدیه اشتباه وارد شده باشد 
CardIsCanceled |اگر وضعیت هدیه کنسل شده باشد 
AlreadyRedeemedOrCanceled |اگر هدیه داخلی باشد و وضعیت قابل ریدیم کردن نداشته باشد 
InvalidInitialWithdraw |در صورتی که برداشت اولیه مربوط به هدیه موفق نبوده باشد 
LnurlUnavailable |در صورتی که نوع کارت لایتنینگی باشد و مشکلی در دریافت lnurl باشد 
InternalWithdrawFailed |در صورتی که نوع کارت داخلی باشد و برداشت به ولت گیرنده موفق نباشد 


<aside class="notice">
در صورتی که کاربر ایجاده کننده‌ی هدیه شماره همراه کاربر گیرنده را وارد کرده باشد،‌ به این معناست که نحوه‌ی دریافت هدیه به‌صورت دوعاملی هست. 
</aside>

* پارامترهای پاسخ:

<aside class="notice">
در حالتی که نوع برداشت داخلی باشد:
</aside>

پارامتر | نوع      | توضیحات    | نمونه
------- |----------|------------| ---------
status | string   | وضعیت پاسخ | "ok"

<aside class="notice">
در حالتی که نوع برداشت lightning باشد:
</aside>

پارامتر | نوع      | توضیحات    | نمونه
------- |----------|------------| ---------
status | string   | وضعیت پاسخ | "ok"
lnUrl | string   | هدیه lnurl | "lnurlZX6N..."


<aside class="notice">
محدودیت فراخوانی : 5 درخواست در ۱ ساعت
</aside>



##دریافت هدیه‌ی lightning برای کاربران غیر عضو

>نمونه درخواست:

```shell
curl -X POST 'https://api.nobitex.ir/gift/redeem-lightning' \
-H 'Content-Type: application/json' \
--data '{
    "redeem_code": "8AFA1DF3",
    "password": "testpass123",
    "otp": "158354"
}'
```

> در صورت فراخوانی درست، پاسخ در حالت کلی، این صورت خواهد بود:

```json
{
  "status": "ok",
  "lnUrl": "lnurlPURQEQ..."
}
```

در صورتی که کاربری عضو نوبیتکس نباشد و نوع هدیه نیز lightning باشد باید از این API برای دریافت هدیه استفاده شود.
این API فقط برای دریافت هدایا از نوع دریافت lightning و زمانی که گیرنده عضو نوبیتکس نباشد استفاده می‌شود.

<aside class="notice">
در این مرحله برای امنیت بیشتر کاربران ارسال کپچا اجباری است.
</aside>


* آدرس: `POST /gift/redeem-lightning`

* پارامترهای ورودی:

پارامتر | نوع    | پیش‌فرض | توضیحات                          | نمونه
------- |--------|---------|----------------------------------| ---------
redeem_code | string | الزامی  | کد هدیه                          | "FB57379C"
password | string | الزامی  | گذرواژه‌ی هدیه                   | "testpass123"
otp | string | اختیاری | رمزیکبار مصرف ارسال شده به کاربر | "254862"

### حالت‌های خطا
کد خطا | توضیحات
---- | ----
InvalidCaptcha | در صورت اشتباه وارد شدن کپچا 
InvalidOTP |  اگر رمزیکبار مصرف ارسال شده به گوشی کاربر اشتباه وارد شده باشد 
InvalidPassword |اگر پین یا رمزعبور مشخص شده برای گیرند‌ه‌ی هدیه اشتباه وارد شده باشد 
CardIsCanceled |اگر وضعیت هدیه کنسل شده باشد 
InvalidRequest |اگر نوع دریافت هدیه لایتنینگ نباشد 
InvalidInitialWithdraw |در صورتی که برداشت اولیه مربوط به هدیه موفق نبوده باشد 
LnurlUnavailable |در صورتی که نوع کارت لایتنینگی باشد و مشکلی در دریافت lnurl باشد 


* پارامترهای پاسخ:

پارامتر | نوع      | توضیحات    | نمونه
------- |----------|------------| ---------
status | string   | وضعیت پاسخ | "ok"
lnUrl | string   | هدیه lnurl | "lnurlZX6N..."

<aside class="notice">
محدودیت فراخوانی : 5 درخواست در ۱ ساعت
</aside>
<aside class="notice">
در این API نیاز به توکن وجود ندازد.
</aside>

<aside class="notice">
در صورتی که موبایل گیرنده از طرف کاربر گیرنده ارسال شده باشد مقدار otp باید وارد شود.
</aside>


##لیست کارت‌های هدیه‌ی کاربر

> در صورت فراخوانی درست، پاسخ به این صورت خواهد بود:

```json
{
    "status": "ok",
    "sent_gift_cards": [
        {
            "id": 32,
            "full_name": "masoud mohamadi",
            "address": "iran-tehran",
            "mobile": "09127842542",
            "postal_code": "3579-4123",
            "package_type": "New",
            "redeem_code": "EF9472B7",
            "gift_sentence": "تقدیم به شما",
            "amount": "0.02",
            "sender": {
                "username": "test@gmail.com",
                "name": ""
            },
            "receiver": null,
            "currency": "bnb",
            "gift_type": "Digital",
            "gift_status": "Redeemed",
            "card_design": "Default",
            "created_at": "2022-02-13T11:42:27.338380+00:00",
            "redeem_date": "2023-11-11T20:30:00+00:00"
        }
    ],
    "received_gift_cards": [
        {
            "id": 21,
            "full_name": "mohsen amini",
            "address": "iran-tehran",
            "mobile": "09125412335",
            "postal_code": "8568-9982",
            "package_type": "New",
            "redeem_code": "D758A8A2",
            "gift_sentence": "جمله‌ی تست هدیه",
            "amount": "0.05",
            "sender": {
                "username": "test@gmail.com",
                "name": ""
            },
            "receiver": {
                "username": "new_user@gmail.com",
                "name": ""
            },
            "currency": "bnb",
            "gift_type": "Digital",
            "gift_status": "Redeemed",
            "card_design": "Default",
            "created_at": "2022-02-10T12:33:08.226370+00:00",
            "redeem_date": "2023-11-11T20:30:00+00:00"
        }
    ]
}
```


این API برای نمایش تمامی هدایای فرستاده شده و یا دریافت شده‌ی کاربر استفاده می‌شود.
در صورتی که کاربر گیرنده عضو نوبیتکس نباشه ایمیل و یا نام و نام خانوادگی مقدار receiver خواهند بود.

* آدرس: `GET /gift/user-gifts`

* پارامترهای پاسخ:

پارامتر | نوع                         | توضیحات                         | نمونه
------- |-----------------------------|---------------------------------| ---------
status | string                      | وضعیت پاسخ                      | "ok"
sent_gift_cards | list of user sent gifts     | لیست هدایای فرستاده شده‌ی کاربر | []
received_gift_cards | list of user received gifts | لیست هدایای گرفته شده‌ی کاربر   | []

<aside class="notice">
محدودیت فراخوانی : 60 درخواست در دقیقه
</aside>

##ایجاد درخواست تعداد بالای هدیه
در صورتی که کاربری درخواست برای تعداد بالای هدیه داشته باشد باید این API فراخوانی شود که درخواست کاربر ثبت شود.
برای این درخواست نیاز به ارسال پارامتر ورودی نیست و فقط کاربر درخواست کننده باید لاگین باشد.
در پاسخ شناسه‌ی درخواست ساخته شده نیز فرستاده می‌شود که باید در درخواست مربوط به تایید درخواست‌ هدیه‌ی دسته‌ای ارسال شود.

* آدرس: `POST /gift/create-gift-batch`

* پارامترهای پاسخ:

پارامتر | نوع     | توضیحات                 | نمونه
------- |---------|-------------------------| ---------
status | string  | وضعیت پاسخ              | "ok"
batch_id | integer | شناسه‌ی درخواست دسته‌ای | 58


<aside class="notice">
محدودیت فراخوانی : 10 درخواست در ساعت
</aside>

##تایید درخواست تعداد بالای هدیه
برای ارسال رمزیکبار مصرف فرستاده شده به کاربر برای تایید ایجاد تعداد بالای هدیه باید از این API استفاده شود.
مقادیر ورودی رمزیکبار مصرف و شناسه‌ی درخواست ایجاد شده هستند.

* آدرس: `POST /gift/confirm-gift-batch`


* پارامترهای ورودی:

پارامتر | نوع    | پیش‌فرض | توضیحات                          | نمونه
------- |--------|--------|----------------------------------| ---------
otp | string | اجباری | رمزیکبار مصرف ارسال شده به کاربر | "615519"
batch_id | string | اجباری | شناسه‌ی درخواست ایجاد شده        | 58


### حالت‌های خطا
کد خطا | توضیحات
---- | ----
InvalidOTP | در صورتی که رمزیکبار مصرف تایید برای ایجاد و تایید درخواست هدیه‌ی دسته‌ای اشتباه باشد 


* پارامترهای پاسخ:

پارامتر | نوع     | توضیحات                 | نمونه
------- |---------|-------------------------| ---------
status | string  | وضعیت پاسخ              | "ok"


<aside class="notice">
محدودیت فراخوانی : 10 درخواست در دقیقه
</aside>


##ارسال مجدد رمزیکبار مصرف

>نمونه درخواست:

```shell
curl -X POST 'https://api.nobitex.ir/gift/resend-gift-otp' \
-H 'Content-Type: application/json' \
--data '{
    "redeem_code": "07DB85D7210E4D29AEFF112FF53B2C74"
}'
```

> در صورت فراخوانی درست، پاسخ به این صورت خواهد بود:

```json
{
  "status": "ok"
}
```

در صورت نیاز به ارسال مجدد رمز یکبار مصرف برای دریافت هدیه این API فراخوانی می‌شود.

* آدرس: `POST /gift/resend-gift-otp`

* پارامترهای ورودی:

پارامتر | نوع    | پیش‌فرض | توضیحات                          | نمونه
------- |--------|---------|----------------------------------| ---------
redeem_code | string | الزامی  | کد هدیه                          | "07DB85D7210E4D29AEFF112FF53B2C74"


### حالت‌های خطا
کد خطا | توضیحات
---- | ----
MobileNotProvided | اگر موبایل گیرنده وجود نداشته باشد 
TooManyRequests |  اگر تعداد پیامک‌های ارسالی برای در ۱۲ ساعت گذشته بیشتر از تعداد مجاز ۷ عدد باشد 


* پارامترهای پاسخ:

پارامتر | نوع     | توضیحات      | نمونه
------- |---------|--------------| ---------
status | string  | وضعیت پاسخ   | "ok"

<aside class="notice">
محدودیت فراخوانی : 5 درخواست در ساعت
</aside>