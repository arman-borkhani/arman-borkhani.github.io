---
layout: post
author: آرمان بورخانی
title: آموزش استایل دهی به فرم جستجو با استفاده از css 
---

![search-box](/assets/images/Screenshot-2018-2-21-search-box2.png){:.full-width}
ساخت یک فرم جستجو یا باکس جستجو شاید خیلی ساده به نظر برسه اما نکاتی در خودش داره که میتونه کاربردی باشه. در انتها شما یاد می گیرید چطور باکس بالا را با کمک css بسازید. مرحله به مرحله توضیحاتی که لازم باشه را میدم با این حال سعی می کنم خیلی طولانی نشه؛ اگه به توضیحات اضافه نیاز ندارید می تونید کد های کامل شده را در آخر این نوشتار ببینید.

تگ های html مورد نیاز را وارد صفحه کنید.

~~~markup
<form class="search-form">
 <input type="text" class="search-input">
 <button type="submit" class="search-button">جستجو</button>
</form>
~~~

type تگ باتن را برابر با submit بزارید چون بعد از کلیک کردن روی این تگ اطلاعات وارد شده در ورودی متن باید برای فرم ارسال بشه، توضیح بیشتری در مورد تگ های html نمیدم چون اینجا بیشتر استایل دهی مد نظر هست.

به هر دو عنصر 10px از بالا و پایین و 15px از چپ و راست padding می دهیم.

~~~css
.search-input {
 padding: 10px;
 background: #eee;
 border: 1px solid #ddd;
}

.search-button {
 padding: 10px 15px;
 background: #0099ff;
 border: 1px solid #0099ff;
 color: white;
}
 ~~~

 دستورات بالا به ورودی متن یک رنگ پس زمینه (background) خاکستری و یک border یک پیکسلی تیره تر و به باتِن هم یک رنگ آبی و یک border یک پیکسل آبی میده .

در این مرحله فرم باید شبیه تصویر زیر باشه.
![search-box-1](/assets/images/search-box-1.png){:.aligncenter}

همونطور که می بینید ورودی و باتن به صورت پیش فرض کمی فاصله با هم دارند. برای اینکه این فاصله را از بین ببرید باید کدهای زیر را به کدهای کلاس search-button اضافه کنید.

~~~css
position: relative;
left: 5px;
~~~
خیلی ساده بخوام توضیح بدم باید بگم : زمانی که position یک عنصر یا تگ را relative انتخاب می کنید، می تونید عنصر را نسبت به محل فعلیش جابه جا کنید. این جابه جایی با استفاده از دستورات right ، left ، top ، bottom انجام میشه.

وقتی مقدار left را 5px قرار می دید عنصر باتن به اندازه 5px از محل اولیه اش به سمت راست میره.

دقت کنید که جابه جایی با این روش روی بقیه عناصر تاثیری نداره و اونها را جابه جا نمی کنه. اگر مایلید بیشتر در مورد ویژگی position بدونید [ایــــــــــن](http://css-tricks.ir/reference/position/) مطلب را بخونید.

سمت چپ ورودی نباید بوردر داشته باشه ، با کد زیر می تونید بوردر سمت چپ را حذف کنید.

~~~css
border-left: none;
~~~
برای اینکه به بوردر ها کمی انحنا بدیم از ویژگی border-radius استفاده می کنیم. اگه به این ویژگی چهار مقدار بدید اولین مقدار مربوط به گوشه بالا چپ خواهد بود و بقیه مقادیر به صورت ساعتگرد به گوشه های دیگه اختصاص پیدا می کنه.

برای ورودی متن فقط گوشه های بالا راست و پایین راست باید انحنا داشته باشه بنابراین مقادیر را بصورت زیر قرار بدید.

~~~css
border-radius: 0 5px 5px 0;
~~~

کد بالا را به کلاس search-input اضافه کنید و کد زیر را به کلاس search-button

~~~css
border-radius: 5px 0 0 5px;
~~~
font-weight را هم می تونید مساوی با bold قرار بدید تا ضخامت کلمه “جستجو” بیشتر بشه.

~~~css
border-radius: 5px 0 0 5px;
font-weight: bold;
~~~
تا اینجا فرم شما باید شبیه تصویر زیر شده باشه

![search-box-2](/assets/images/search-box-2.png){:.aligncenter}

## چطور میشه به جای کلمه جستجو یه ایکون اضافه کرد؟

ساده ترین راه برای این کار اینه که از [Font Awesome](https://fontawesome.com/) استفاده کنید. فونت آوسم آیکون های رایگان زیادی داره که می تونید از اونها به راحتی استفاده کنید و ویژگی هایی مثل اندازه و رنگ آیکون ها را در css تغییر بدید.

همونطور که در صفحه Get Started وبسایت فونت آوسم توضیح داده شده  برای اینکه بتونید از این کتابخانه در یک صفحه استفاده کنید باید کد زیر را در head صفحه قرار بدید.

~~~markup
<script defer src="https://use.fontawesome.com/releases/v5.0.6/js/all.js"></script> 
~~~

در صفحه icons می تونید همه ی ایکون ها را ببیند. برای ایکون search تگ زیر را به جای کلمه ی “جستجو” بین تگ های button کپی کنید. به همین سادگی ایکون جستجو به جای متن قرار می گیره.

~~~markup
<button type="submit" class="search-button">
  <i class="fas fa-search"></i>
</button> 
~~~
این نکته را هم اضافه کنم که اگر می خواهید ایکون های فونت آوسم بصورت آفلاین هم نمایش داده بشن باید این کتابخانه را دانلود کنید و در پروژه بهش لینک بدید.
و اینم کدهای css نهایی

~~~css
.search-input {
 padding: 10px 15px;
 background: #eee;
 border: 1px solid #ddd;
 border-radius: 0 5px 5px 0;
 font-style: italic;
 }

.search-button {
 padding: 10px 15px;
 background: #0099ff;
 border: 1px solid #0099ff;
 color: white;
 border-radius: 5px 0 0 5px;
 position: relative;
 left: 5px;
 font-weight: bold;
 cursor: pointer;
 }
 ~~~