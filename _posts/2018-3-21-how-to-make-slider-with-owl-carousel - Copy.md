---
layout: post
author: آرمان بورخانی
title: ساخت اسلایدر با استفاده از پلاگین Owl Carousel 
---

ساخت اسلایدر برای افرادی که دانشی در مورد جاوا اسکریپ و جی کوئری ندارند می تونه سخت باشه ، توسعه دهنده هایی که دانش کافی دارند هم ممکنه به دلیل کمبود وقت و یا دلایل دیگه قادر نباشن یک اسکریپت کامل و انعطاف پذیر برای اسلایدر بنویسند.
در چنین شرایطی راه حل پیش رو استفاده از پلاگین های آماده است. در این نوشتار یکی از این پلاگین ها به نام Owl Carousel معرفی میشه.

اسلایدری که در تصویر زیر می بینید با استفاده از این پلاگین ساخته شده.
![اسلایدر آماده owl carousel](/assets/images/owl-carousel.gif){:.full-width}

معمولا اسلایدرها با استفاده از جاوا اسکریپت یا جی کوئری (jQuery) که یک کتابخانه جاوا اسکریپتی است ساخته می شوند؛ البته این امکان هم وجود داره که فقط با CSS و بدون استفاده از جاوا اسکریپت یا جی کوئری یک اسلایدر بسازید.


## ویژگی های پلاگین

– با استفاده از جی کوئری نوشته شده و رایگانه

– از قابلیت لمس (touch) پشتیبانی می کنه بنابراین کاربرانی که از دستگاه های لمسی استفاده می کنند به راحتی قادر خواهند بود با اسلایدر کار کنند.

– بیش از ۶۰ گزینه (option) وجود داره که با انتخاب هر کدوم می تونید تغییرات دلخواهتون را اعمال کنید.

– در مرورگر های قدیمی یا به تعبیر سازندگان این پلاگین “مرورگر های زامبی” بدون مشکل کار میکنه.

– واکنشگرا یا ریسپانسیو هست.

– از راست به چپ بودن (RTL) پشتیبانی می کنه.

مزایای دیگری هم برای این پلاگین ذکر شده اما فکر می کنم همینقدر برای آشنایی کافی باشه.

## نصب و راه اندازی

پلاگین را از صفحه اصلی سایت دانلود کنید؛ فایلی که دانلود کردید یه پوشه به نام dist داره که ساختار و محتواش به شکل زیر است.
~~~markup
owlcarousel/
├── assets/
│   ├── owl.carousel.css
│   ├── owl.carousel.min.css
│   ├── owl.theme.default.css
│   ├── owl.theme.default.min.css
│   ├── owl.theme.green.css
│   ├── owl.theme.green.min.css
│   └── owl.video.play.png
│
├── owl.carousel.js
├── owl.carousel.min.js
├── LICENSE-MIT
└── README.md
~~~
ابتدا باید فایل های owl.carousel.min.css و owl.theme.default.min.css را به سندتون اضافه کنید.

~~~markup
<link rel="stylesheet" href="owlcarousel/owl.carousel.min.css">
<link rel="stylesheet" href="owlcarousel/owl.theme.default.min.css">
~~~

چون این پلاگین با جی کوئری نوشته شده باید کتابخانه جی کوئری را به سندتون اضافه کنید؛ آخرین نسخه جی کوئری را می تونید از [وبسایتش](https://jquery.com/) دانلود کنید یا مسیر زیر را در فایلی که دانلود کردید دنبال کنید.

    docs/assets/vendors

همچنین فایل owl.carousel.min.js که اصلی ترین فایل به حساب میاد هم باید به سند اضافه بشه.

~~~markup
<script src="jquery.min.js"></script>
<script src="owlcarousel/owl.carousel.min.js"></script>
~~~
بهتره تگ های script بالا را در انتهای تگ body قرار بدید.

ساختار کلی تگ های html باید به شکل زیر باشه؛ بین تگ های div شما می تونید هر نوع تگ یا محتوایی قرار بدید.

~~~markup
<div class="owl-carousel owl-theme">
  <div> Your Content </div>
  <div> Your Content </div>
  <div> Your Content </div>
</div>
~~~

در آخر برای فراخوانی پلاگین کدهای زیر را بعد از تگ های script اضافه کنید.

~~~markup
<script>
 $(".owl-carousel").owlCarousel({
    autoplay:true,
    items:1,
    loop:true,
    }
 );
</script>
~~~

اگر خاصیت dir تگ html سند بصورت rtl مقدار دهی شده باید دستور rtl:true را بعد از متغییر loop اضافه کنید.

items تعداد اسلایدها یا آیتم ها را نشون میده.

متغییر autoplay تعیین می کنه که آیتم ها یا اسلایدها بصورت اتوماتیک جابه جا بشن یا نه.این متغییر از نوع Boolean هست؛ یعنی مقدارش میتونه true (صحیح) یا false (غلط) باشه.

دستور loop:true باعث ایجاد حلقه بی نهایت میشه؛ یعنی بعد از رسیدن به آخرین آیتم دوباره آیتم ها از اول نمایش داده می شوند.

با استفاده از کدهای بالا و اضافه کردن تصویر باید یک اسلایدر بشکل زیر داشته باشید.

![اسلایدر پیش فرض owl carousel](/assets/images/owl-carousel-default.gif){:.full-width}

## گزینه ها و تنظیمات پلاگین
همونطور که بالاتر توضیح داده شد یکسری متغییر برای این پلاگین تعریف شده که با مقداردهی مناسب می تونید به راحتی تنظیمات دلخواه را روی اسلایدر اعمال کنید.

در ادامه برخی از این متغییر را معرفی می شوند، لیست کامل متغییرها را هم می تونید [ایـنجـــــــــا](https://owlcarousel2.github.io/OwlCarousel2/docs/api-options.html) مشاهده کنید.

### dots

از نوع Boolean است و اگر مقدارش false باشه ، دایره هایی که زیر اسلایدر هستند مخفی میشن.

### margin

margin از راست آیتم هاست( در واقع فاصله بین آیتم ها ) و می تونید یک مقدار عددی بهش اختصاص بدید.

### autoplayHoverPause

از نوع Boolean هست و زمانی که مقدارش true باشه با قرار گرفتن ماوس روی هر آیتم ، پخش خودکار (atuoplay) غیرفعال میشه.

### autoplayTimeout

از نوع عددی است و مشخص می کند در پخش خودکار بعد از چند میلی ثانیه آیتم ها جایگزین شوند.
این متغییر بصورت پیش فرض مقدارش 5000 است، هر چه این مقدار کمتر باشه سرعت اسلایدر بیشتر میشه.

### responsive

با مقدار دهی به این متغییر می تونید تعیین کنید در عرض های مختلف صفحه نمایش چه تغییراتی اعمال بشه.

~~~javascript

responsive : {
 0 : {
    items:1,
 },
480 : {
    items:3,
 },
768 : {
    items:6,
  }
}
~~~

در مثال بالا تعداد آیتم های اسلایدر در نمایشگرهای با عرض 480px به بالا برابر با 3 خواهد بود و در عرض 768px به بالا 6 آیتم  نمایش داده می شود. به جای این دو عدد هر عددی که مایلید می تونید قرار بدید.