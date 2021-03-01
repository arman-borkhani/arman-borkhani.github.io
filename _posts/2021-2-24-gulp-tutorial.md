---
layout: post
author: آرمان بورخانی
title: خودکارسازی کارها هنگام توسعه front-end با استفاده از Gulp
---
هنگام توسعه سمت کاربر (front-end) مبتنی بر وب ، کارهایی وجود دارند که همواره حین یا بعد از فرایند توسعه انجام می شوند، برای مثال باید تصاویری که در پروژه استفاده کردید را بهینه سازی کنید یا اگر هنگام توسعه از پیش‌پردازنده SASS استفاده می کنید ، فایل های SASS را به CSS تبدیل کنید و موارد دیگری که همواره در حال تکرار هستند ؛ نظرتان در مورد  خودکارسازی این کارها و افزایش سرعت توسعه چیست ؟

شما به ابزاری نیاز دارید تا با استفاده از آن کارهای تکراری و زمانبری که در تمام پروژه ها با آنها سروکار دارید را سریع و بصورت خودکار انجام دهید. بدین منظور گزینه‌های مختلفی در اختیار دارید که یکی از آنها [Gulp](https://gulpjs.com/) است، ابزاری متن‌باز بر پایه جاوا اسکریپت که در حال حاضر نسخه چهارم آن عرضه شده ؛ کار کردن با گالپ ساده‌تر از ابزارهای مشابه مانند Grunt و Webpack است بنابراین می‌تواند گزینه بهتری برای شروع باشد و یادگیری‌اش باعث می‌شود نحوه عملکرد این ابزارها را بیاموزید و لزوم استفاده از آنها را درک کنید. 

در ادامه یاد می‌گیرید چطور با استفاده از گالپ موارد زیر را انجام دهید و با  مقدمات و شالوده این پلاگین آشنا خواهید شد. 

*  راه اندازی وب سرور بصورت Local
*  Reload مرورگر بصورت خودکار بعد از انجام تغییرات در ویرایشگر
*  تبدیل فایل‌های SASS به CSS
*  الحاق و فشرده سازی فایل های CSS و JS
*  بهینه سازی تصاویر

## نصب گالپ

چون قرار است از npm یا node package manager استفاده کنیم ، باید از نصب بودن node بروی سیستم خود اطمینان حاصل کنید ، برای این کار کافیست دستور `node -v` را در خط فرمان (command line) اجرا کنید ؛ اگر node از قبل نصب باشد نسخه آن نمایش داده می‌شود ، در غیر اینصورت می توانید با مراجعه به آدرس [https://nodejs.org/en](https://nodejs.org/en) نسخه مناسب را نصب کنید.

در نسخه ۴ ، رابط خط فرمان یا CLI از کتابخانه اصلی Gulp مجزا شده و فقط باید gulp-cli را بصورت سراسری (global) نصب کنید، به همین دلیل اگر گالپ (کتابخانه اصلی) از قبل بصورت سراسری نصب شده ابتدا دستور 

~~~bash
npm rm --global gulp
~~~
و در غیر اینصورت دستور زیر را اجرا کنید.  
~~~bash
npm install --global gulp-cli
~~~

## ساخت پروژه Gulp
یک پوشه با نام project بسازید ، خط فرمان را داخل پوشه باز کنید و دستور `npm init` را اجرا کنید تا فایل package.json برای پروژه ساخته شود ، این فایل حاوی اطلاعاتی در مورد پروژه است ، از‌جمله پکیج های استفاده شده در پروژه که  اصطلاحآ به این پکیج ها dependencies گفته مي‌شود.

~~~bash
# ... from within our project folder
npm init
~~~

بعد از ساخته شدن فایل package.json با دستور زیر گالپ را داخل پروژه نصب کنید.

~~~bash
npm install gulp --save-dev
~~~
لازم نیست گالپ را بصورت سراسری نصب کنید به همین دلیل از global در دستور بالا استفاده نشده است.

بعد از نصب پکیج ، پوشه‌ای جدید بنام node_moduls در پوشه اصلی پروژه ایجاد می‌شود که می‌توانید پوشه gulp را درون آن ببینید و اگر فایل package.json را با یه ویرایشگر مانند sublime text باز کنید در قسمت devDependencies باید نام و نسخه پکیج Gulp نوشته شده باشد ؛ با نصب gulp برای شروع کار آماده ایم اما ابتدا باید ساختار کلی پروژه را آماده کنید.

## ساختار پوشه ها در پروژه

گالپ انعطاف زیادی داشته و می تواند با ساختارهای مخلتف کار کند ، برای این مقاله ساختار کلی بصورت زیر خواهد بود.

~~~html
|- app/
  |- css/
  |- fonts/
  |- images/ 
  |- index.html
  |- js/ 
  |- scss/
|- dist/
|- gulpfile.js
|- node_modules/
|- package.json
~~~

در این پروژه از پوشه app برای توسعه استفاده می‌کنیم و کدهایی که می‌نویسم در این پوشه قرار می‌گیرند . درانتها با اجرای task های مربوط به بهینه‌سازی، فایل‌های نهایی و بهینه شده (optimized) در پوشه dist ساخته خواهند شد.

نکته : dist مخفف کلمه distribution است.

فایل gulpfile.js را در ویرایشگر باز کنید تا اولین task را بنویسیم.

## نوشتن اولین task

برای استفاده از Gulp در قدم اول باید آن را با استفاده از تابع require در فایل gulpfile.js فراخوانی کنید.

~~~javaScript
var gulp = require('gulp');
~~~

تابع require به Node اعلام می کند در پوشه node_moduls به دنبال پکیج gulp بگردد و زمانی که پکیج فراخوانی شد به متغییری بنام gulp ارجاع می‌شود و با استفاده از این متغییر می توانید به متدهای Gulp دسترسی داشته باشید.

در این مقاله برای نوشتن task ها از توابع جاوا اسکریپ استفاده می کنیم .

~~~javaScript
function taskName() {
  // Stuff here
};

exports.taskname = taskName;
~~~
برای اجرای task بعد از export  تابع مانند کدهای بالا ، در  خط فرمان از دستور `gulp taskname`  استفاده کنید و اگر به export عبارت default را اضافه کنید task بعنوان پیش فرض شناخته شده و با دستور gulp اجرا خواهد شد.

برای امتحان ، یک task ساده می نویسیم که فقط مقداری را در خط فرمان نمایش دهد.

~~~javascript
function hello() {
  console.log('First gulp task');
};

exports.default = hello;
~~~
برای اجرای task کافی است دستور gulp را در خط فرمان اجرا کنید ، اگر تا این مرحله درست پیش رفته باشید باید متن First gulp task را در خط فرمان مشاهده کنید.

نکته : اگر از پاورشل (PowerShell ) در ویندوز استفاده می کنید و بعد از اجرای دستور gulp پیامی دریافت کردید مبنی بر اینکه به دلایل امنیتی اسکریپت اجرا نمی‌شود دستور زیر را اجرا کنید.

~~~bash
Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
~~~

روش دیگری هم برای نوشتن task ها وجود دارد که در نسخه‌های قبلی gulp استفاده می‌شده و ممکن است زیاد با آن مواجه شوید ؛ این روش در نسخه‌ جدید هم پشتیبانی می‌شود و syntax آن بشکل زیر است.

~~~javascript
gulp.task('task-name', function() {
  // Stuff here
});
~~~

در این مقاله از روش اول استفاده خواهد شد اما اگر بخواهید با استفاده از این روش دوباره task قبلی را بنویسید، بشکل زیر عمل کنید .

~~~javascript
gulp.task('hello', function() {
  console.log('first gulp task');
});
~~~

خب اگر موفق شدید خروجی بگیرید بهتون تبریک میگم ، اما task های واقعی کمی پیچیده تر هستند و از یکسری متد و پلاگین کمکی برای نوشتن آنها استفاده می‌شود، به مثال زیر توجه کنید.

~~~javascript
function task() {
  return gulp.src('source-files') // فایل مبداء را دریافت می کند
    .pipe(aGulpPlugin()) // فایل را به پلاگین ارجاع می‌دهد
    .pipe(gulp.dest('destination'))
     // فایل جدید را در آدرس اعلامی نمایش می دهد 
}
~~~
متد gulp.src فایل های ورودی به task را مشخص می کند و با استفاده از pipe فایل های ورودی به پلاگین یا کتابخانه اعلامی ارجاع می شوند و بعد از اینکه پلاگین عملیات لازم را روی فایل ورودی انجام داد ، فایل جدید با استفاده از متد gulp.dest در مسیری مشخص ( که در این پروژه پوشه dist است ) ساخته خواهد شد. 

## تبدیل فایل SASS به CSS

برای تبدیل فایل های SASS به CSS به پلاگین [gulp-sass](https://www.npmjs.com/package/gulp-sass) نیاز داریم ، می توانید این پلاگین را با استفاده از npm نصب کنید.

~~~bash
npm install gulp-sass --save-dev
~~~

مانند کاری که برای پلاگین gulp انجام دادید با استفاده از تابع require پلاگین جدید را فراخوانی کنید.

~~~javascript
var gulp = require('gulp');
// Requires the gulp-sass plugin
var sass = require('gulp-sass');
~~~

task ی که در بخش قبلی بعنوان نمونه نوشتید را در نظر بگیرید ، کافیست به جای aGulpPlugin  پلاگین sass را جایگزین کنید و نام تابع را عوض کنید.

~~~javascript
function sassTask(){
  return gulp.src('source-files')
    .pipe(sass()) //استفاده از پلاگین gulp-sass
    .pipe(gulp.dest('destination'))
};
~~~
حالا کافیست فایل ورودی و محل ذخیره فایل نهایی را مشخص کنید و تابع را export کنید؛ در پوشه scss یک فایل جدید بنام styles.scss بسازید ، این فایل ورودی تابع gulp.src خواهد بود و فایل styles.css نهایی هم باید در مسیر ‘app/css’ قرار گیرد. 

~~~javascript
function sassTask(){
  return gulp.src('app/scss/styles.scss')
    .pipe(sass()) // تبدیل فایل های SASS به  CSS
    .pipe(gulp.dest('app/css'))
};

exports.sass = sassTask;
~~~

قبل از اینکه task را اجرا کنید با استفاده از ویژگی های sass یه سری استایل در فایل styles.scss بنویسید تا مطمئن شوید فایل به درستی تبدیل می‌شود.

~~~css
// styles.scss
body {
  .test {
     Background-color: pink;
  }
}
~~~

با اجرای دستور `gulp sass` در خط فرمان ، task اجرا و فایل styles.css شامل کدهای زیر در مسیر ‘app/css’ ساخته خواهد شد.

~~~css
// styles.css
body .test {
  Background-color: pink; }

~~~

اما اگر بخواهید چند فایل SASS را همزمان به css تبدیل کنید ، چه راه حلی دارید؟

## استفاده از ویژگی Globbing 

Globe ها اصطلاحا matching patterns هستند یعنی الگوهایی که با استفاده از آنها می‌توانید گروهی از فایل‌ها که با الگو مطابقت دارند را انتخاب کنید ، برای مثال با استفاده از این ویژگی می‌توانید همه فایل‌هایی که از یک نوع هستند مانند فایل‌های با پسوند scss را انتخاب و بعنوان ورودی تابع gulp.src در نظر بگیرید.

الگوهایی که در این مقاله نیاز دارید ، موارد زیر هستند :

* `scss.*` : با استفاده از این الگو می توانید همه فایل ها با پسوند scss که در پوشه اصلی پروژه قرار دارند را انتخاب کنید.
* `scss.*/**` : با استفاده از الگوی قبلی فقط فایل‌های scss داخل پوشه اصلی انتخاب می شدند ، اما با استفاده از این الگو پوشه هایی که در داخل پوشه اصلی قرار دارند هم بررسی خواهند شد.
* `example.scss!` : با کمک این الگو می توانید فایل example را از جستجو مستثنی کنید.
* `(scss|sass)+.*` : با این الگو هم فایل هایی که پسوند scss یا sass دارند و در پوشه اصلی قرار گرفته اند ، انتخاب می شوند. 

حالا مقدار ‘app/scss/style.scss’ را که بعنوان ورودی تسک sassTask نوشتید را با الگوی `scss/**/*.css` جایگزین کنید ، با این کار همه فایل های SASS که در مسیر ‘app/scss’ قرار دارند بعنوان ورودی تسک در نظر گرفته می شوند.

~~~javascript
function sassTask() {
  return gulp.src('app/scss/**/*.scss')
    .pipe(sass())
    .pipe(gulp.dest('app/css'))
}
~~~

هر فایل SASS که در مسیر ‘app/css’ وجود داشته باشه بصورت خودکار به وسیله تسک gulpSass به فایل css تبدیل خواهد شد ، برای نمونه اگر یک فایل به نام print.scss ایجاد و بعد gulpSass را اجرا کنید ، فایلی مشابه بصورت css در مسیر ‘app/css’ ساخته می شود.

تا اینجا توانستید با استفاده از دستور `gulp sass` همه فایل های SASS را به css تبدیل کنید، اما باید بعد از هر تغییری که در فایل های SASS ایجاد کردید دستور `gulp sass` را اجرا کنید تا فایل ها تبدیل شوند ؛ برای اینکه  بعد از انجام تغییرات دستور `gulp sass` بصورت خودکار اجرا شود می توانید از متد watch استفاده کنید.

## رصد تغییرات فایل های SASS

Gulp با استفاده از متد watch فایل‌ها را رصد کرده و زمانی که تغییری در فایل‌ها ایجاد و ذخیره شد ، توابعی که مشخص کرده اید اجرا می‌کند.

~~~javascript
gulp.watch('files-to-watch', gulp.series(['tasks', 'to', 'run']));
~~~

این متد دو آرگومان ورودی دریافت می کند اول : فایل یا فایل هایی که باید رصد شوند ، دوم : task هایی که باید بعد از شناسایی تغییرات در فایل های اعلامی بعنوان ارگومان اول این متد، اجرا شوند.

با توجه به اینکه قصد داریم تمام فایل های SASS رصد شوند برای آرگومان اول `app/scss/**/*.scss` و برای آرگومان دوم [‘sass’] را در نظر بگیرید .

~~~javascript
gulp.watch('app/scss/**/*.scss', gulp.series(['sass']));
~~~

 برای اجرای قطعه کد بالا task جدیدی بنام watchTask بنویسید ، در مراحل بعد موارد جدیدی به این task اضافه خواهیم کرد چون نیاز داریم فایل هایی با پسوندهای دیگرهم رصد و تسک‌هایی برای آنها انجام شود .

~~~javascript
function watchTask(){
  gulp.watch('app/scss/**/*.scss', ['sass']); 
  // Other watchers
}
exports.watch = watchTask;
~~~

با اجرای دستور `gulp watch` درخط فرمان ، gulp فایل های SASS را رصد می‌کند و بعد از انجام تغییرات در فایل های SASS و ذخیره کردن ، تسک sass بصورت اتوماتیک اجرا می‌شود.

## Live-reloading با استفاده از Browser Sync
با استفاده از [Browser Sync](https://www.browsersync.io/) می‌توانید یک وب سرور راه اندازی کنید و زمانی که در حال توسعه یک اپلیکیشن هستید ، بعد از انجام تغییرات در کدها یا فایل‌ها ، مرورگر بصورت خودکار reload شود و تغییرات اعمال شده را ببینید.

برای نصب Browser Sync دستور زیر را در خط فرمان اجرا کنید.

~~~bash
npm install browser-sync --save-dev
~~~

شاید متوجه شده باشید که از gulp- در ابتدای browser-sync استفاده نشده ، چون Browser Sync بدون نیاز به پلاگین‌های جانبی می تواند با Gulp کار کند.

برای استفاده از Browser Sync ابتدا باید با استفاده از متد require فراخوانی اش کنید.

~~~javascript
var browserSync = require('browser-sync').create();
~~~

یک task جدید به شکل زیر برای browserSync ایجاد و پوشه App را بعنوان مسیر اصلی یا root مشخص کنید .

~~~javascript
function browserSyncTask() {
  browserSync.init({
    server: {
      baseDir: 'app'
    },
  })
} 
~~~

همچنین باید تابع sassTask را هم ویرایش کنید تا  با کمک browser sync بعد از هر بار اجرای task ، مرورگر Reload شود.

~~~javascript
function sassTask() {
  return gulp.src('app/scss/**/*.scss')
    .pipe(sass())
    .pipe(gulp.dest('app/css'))
    .pipe(browserSync.reload({
      stream: true
    }))

}
~~~

تا اینجا تنظیمات لازم browser sync انجام شده و برای اینکه مرورگر بصورت خودکار Reload شود، باید تسک های watchTask و browserSyncTask را بصورت همزمان اجرا کنید ؛ برای انجام این کار می توانید دوتا خط فرمان باز کنید و دستورات `run watchTask` و `run browserSyncTask` را بصورت جدا در هر کدام از پنجره های خط فرمان اجرا کنید یا اینکه از متدهای Gulp استفاده کنید.

Gulp متدی به نام parallel دارد که با استفاده از آن می توانید چند task را بصورت همزمان اجرا کنید و کافیست برای اجرای watchTask و browserSyncTask بصورت همزمان بشکل زیر عمل کنید.

~~~javascript
exports.start = gulp.parallel(browserSyncTask, sassTask , watchTask);
~~~

 می توانید export هایی که قبلا انجام دادید را حذف کنید ، با دستور gulp start در خط فرمان هر سه task بصورت همزمان اجرا شده و صفحه ی جدیدی در مرورگر بارگذاری می‌شود که محتوای index.htm را نمایش می‌دهد ، هنگامی که فایل style.scss را ویرایش کنید ، مرورگر بصورت خودکار Reload شده و تغییرات اعمال خواهند شد.

 نکته : برای متوقف کردن تسک هایی که در حال اجرا هستند از کلیدهای Ctrl + c استفاده کنید.

 در کنار فایل‌های SASS ، فایل‌های جاوا اسکریپت و ‘index.html’ هم باید رصد شده و بعد از انجام تغییرات ، مرورگر Reload شود ؛ برای این کار کدهای زیر را به WatchTask اضافه کنید.

~~~javascript
   // Reloads the browser whenever HTML or JS files change
  gulp.watch("app/*.html").on('change', browserSync.reload);
  gulp.watch("app/js/**/*.js").on('change', browserSync.reload);
~~~

تا اینجا یاد گرفتید چطور یک وب سرور برای توسعه راه اندازی ، فایل های SASS را به css تبدیل و بعد از ذخیره تغییرات مرورگر را بصورت اتوماتیک Reload کنید.

## بهینه سازی فایل‌های Javascript و CSS
برای بهینه‌سازی (optimization ) فایل‌های javascript و CSS باید عمل الحاق (concatenation) و فشرده کردن (minification) انجام شود ، اگر با این دو معقوله آشنایی ندارید پاراگراف های بعدی را مطالعه کنید.

فرض کنید با استفاده از تگ script چند فایل js را از مسیرهای مختلف در index.html این پروژه فراخوانی کردید و در هنگام توسعه از آنها استفاده می کنید. 

~~~html
<body>
  <!-- other stuff -->
  <script src="js/lib/a-library.js"></script>
  <script src="js/lib/another-library.js"></script>
  <script src="js/main.js"></script>
</body>
~~~

یا اینکه ، با استفاده از تگ link چند فایل css به پروژه اضافه کردید.

~~~html
<head>
  <link rel="stylesheet" href="css/styles.css">
  <link rel="stylesheet" href="css/another-stylesheet.css">
</head>
~~~

هر تگ script یا link که فایلی را در یک صفحه وب فراخوانی کند هنگام بارگذرای صفحه ، مرورگر یک درخواست HTTP به سرور ارسال می کند ، بنابراین با تجمیع یا الحاق فایل هایی که پسوند مشابه دارند در یک فایل واحد، تعداد درخواست های HTTP کمتر شده و صفحه با سرعت بیشتری بارگذاری خواهد شد.

با فشرده سازی هم حجم فایل های js و css کمتر می‌شود و سرعت بارگذاری صفحه در مرورگرها افزایش می‌یابد.

برای الحاق از پلاگین [gulp-usref](https://www.npmjs.com/package/gulp-useref) استفاده کنید ، این پلاگین از syntax ی که برای کامنت گذاری در فایل های html استفاده می‌شود بشکل زیر استفاده می کند.

~~~html
<!-- build:<type> <path> -->
... لیست تگ‌های script یا link
<!-- endbuild -->
~~~

Type باید یکی از مقادیر js ، css و یا remove باشد ، اگر remove را انتخاب کنید فایل‌هایی که بین دو کامنت قرار دارند حذف خواهند شد ، Path هم مسیری است که فایل نهایی در آنجا ساخته خواهد شد.

چون می خواهیم فایل‌های javascript بصورت تجمیع شده در فایلی بنام main.min.js درمسیر ‘dist/js’ ساخته شود کدهای بالا را بشکل زیر تغییر دهید.

~~~html
<!--build:js js/main.min.js -->
<script src="js/lib/a-library.js"></script>
<script src="js/lib/another-library.js"></script>
<script src="js/main.js"></script>
<!-- endbuild -->
~~~

برای فایل های css هم مشابه فایل های js بشکل زیر عمل کنید.

~~~html
<!--build:css css/styles.min.css-->
<link rel="stylesheet" href="css/styles.css">
<link rel="stylesheet" href="css/another-stylesheet.css">
<!--endbuild-->
~~~

برای نوشتن task ابتدا gulp-useref را نصب و بعد در فایل gulpfile فراخوانی‌اش کنید.

~~~bash
npm install gulp-useref --save-dev
~~~
~~~javascript
var useref = require('gulp-useref');
~~~

و تابع optTask را بنویسید.

~~~javascript
function optTask(){
  return gulp.src('app/*.html')
    .pipe(useref())
    .pipe(gulp.dest('dist'))
}
Exports.opt = optTask;
~~~

با اجرای تابع بالا فایل های styles.min.css و main.min.js در مسیرهایی که مشخص کردید ، ساخته خواهند شد؛ فایل index.html نیز بازنویسی شده و می توانید یک کپی از آن را در پوشه dist مشاهده کنید.

برای فشرده سازی فایل‌های css و js باید از دو پلاگین مختلف استفاده کنید ، بنابراین لازم است فرمت فایل‌ها را بصورت شرطی بررسی کنید تا بتوانید پلاگین صحیح را به کار ببرید؛ برای بررسی شرطی فرمت‌ها از پلاگین [gulp-if](https://github.com/robrich/gulp-if) و فشرده سازی فایل‌های JS از پلاگین [gulp-uglify](https://www.npmjs.com/package/gulp-uglify) استفاده کنید.

برای نصب gulp-if و gulp-uglify بطور همزمان دستور زیر را در خط فرمان اجرا کنید.

~~~bash
npm install gulp-uglify gulp-if --save-dev 
~~~

و تابع optTask را بصورت زیر تغییر دهید.

~~~javascript
// Other requires...
var uglify = require('gulp-uglify'),
    gulpIf = require('gulp-if');

 function optTask(){
  return gulp.src('app/*.html')
    .pipe(useref())
    // Minifies only if it's a JavaScript file
    .pipe(gulpIf('*.js', uglify()))
    .pipe(gulp.dest('dist'))
};
~~~


برای فایل‌های CSS در این مرحله علاوه بر فشرده سازی می‌توانید بصورت خودکار پیشوند (prefix) نیز اضافه کنید . پیشوند‌ها کمک می کنند کدهای CSS در مرورگرهای مختلف کار کنند. بدین منظور از ابزار [postCSS](https://github.com/postcss/postcss) استفاده خواهیم کرد . این ابزار کدهای CSS را با استفاده از پلاگین های جاوا اسکریپتی به کدهای جدید تبدیل می‌کند. برای نمونه با استفاده از پلاگین Autoprefixer بصورت خودکار به کدهای CSS پیشوند اضافه می‌کند یا با استفاده از پلاگین cssnano کدها را فشرده می‌سازد.

با دستور زیر پلاگین های  postcss ، gulp-postcss ، cssnano و Autoprefixer را نصب کنید.

~~~bash
npm install gulp-postcss cssnano autoprefixer --save-dev   
~~~

و به غیر از پلاگین postcss باقی پلاگین‌ها را در فایل gulpfile فراخوانی کنید.

~~~javascript
var postcss = require('gulp-postcss'),
    cssnano = require('cssnano'),
    autoprefixer = require('autoprefixer');

~~~

postCSS آرایه ای از پلاگین‌ها را بعنوان ورودی دریافت و با استفاده از آنها کدهای CSS را تبدیل می کند. برای راحتی کار و متغییری بنام plugins با نوع آرایه درون تابع optTask تعریف و پلاگین هایی که قصد استفاده از آنها را دارید بعنوان آیتم های متغییر مشخص کنید.

~~~javascript
var plugins = [
      autoprefixer({browsers: ['last 3 version']}),
      cssnano()
  ];
~~~

می‌توانید با مراجعه به صفحه گیتهاب autoprefixer ، تنظیمات مختلفی که برای این پلاگین وجود دارد را  مشاهده و مشخص کنید برای چه مرورگرهایی پیشوند گذاری انجام دهد ؛ در این پروژه سه نسخه آخر مرورگرهای اصلی در نظر گرفته شده است.

در ادامه با استفاده از gulp-if بصورت زیر ، پلاگین postcss را با متغییر plugins بعنوان ورودی ؛ برای فایل‌های CSS اجرا کنید.

~~~javascript 
.pipe(gulpIf('*.css', postcss(plugins)))
~~~
برای اینکه بتوانید فایل index.html را هم فشرده کنید از پلاگین [gulp-htmlmin](https://www.npmjs.com/package/gulp-htmlmin) کمک بگیرید.

~~~bash
npm install gulp-htmlmin
~~~

و کد زیر را به تابع optTask اضافه کنید.

~~~javascript
.pipe(htmlmin({ collapseWhitespace: true }))
~~~

در انتها تابع optTask باید بشکل زیر باشد.

~~~javascript
function optTask(){
  var plugins = [
    autoprefixer({browsers: ['last 3 version']}),
    cssnano()
  ];
  return gulp.src('app/*.html')
    .pipe(useref())
    .pipe(htmlmin({ collapseWhitespace: true }))
    .pipe(gulpIf('*.js', uglify()))
    .pipe(gulpIf('*.css', postcss(plugins)))
    .pipe(gulp.dest('dist'))
}
~~~
خب حالا با دستور gulp opt در خط فرمان ، می توانید فایل‌های JS و CSS را تجمیع و فشرده کنید.

## بهینه سازی تصاویر

برای بهینه سازی تصاویر از پلاگین gulp-imagemin استفاده کنید.

~~~bash
npm install gulp-imagemin --save-dev
~~~

~~~javascript
var imagemin = require('gulp-imagemin');
~~~

می توانید فایل‌های png ، jpg ، gif و حتی svg را با استفاده از این پلاگین فشرده کنید. برای این منظور تابعی بنام imgTask بشکل زیر بنویسید.

~~~javascript
function imgTask(){
  return gulp.src('app/images/**/*.+(png|jpg|gif|svg)')
  .pipe(imagemin())
  .pipe(gulp.dest('dist/images'))
};
~~~

همچنین برای فایل‌های مختلف تنظیمات خاصی در نظر بگیرید .

~~~javascript
function imgTask(){
  return gulp.src('app/images/**/*.+(png|jpg|gif|svg)')
.pipe(imagemin([
    imagemin.gifsicle({interlaced: true}),
    imagemin.mozjpeg({quality: 75, progressive: true}),
    imagemin.optipng({optimizationLevel: 5}),
]))
  .pipe(gulp.dest('dist/images'))
};
~~~

بهینه‌سازی یک فرایند آهسته و زمانبر است و برای اینکه از تکرار بی مورد این فرایند جلوگیری شود ، باید از پلاگین gulp-cache استفاده کنید.

~~~bash
npm install gulp-cache --save-dev
~~~

~~~javascript
function imgTask(){
  return gulp.src('app/images/**/*.+(png|jpg|gif|svg)')
.pipe(imagemin([
    imagemin.gifsicle({interlaced: true}),
    imagemin.mozjpeg({quality: 75, progressive: true}),
    imagemin.optipng({optimizationLevel: 5}),
]))
  .pipe(gulp.dest('dist/images'))
};
~~~

## انتقال فونت‌ها به پوشه dist 

Task های مربوط به بهینه‌سازی نوشته شدند و فقط کافی است فونت‌ها را به مسیر ‘dist/fonts’ انتقال دهید؛ برای اینکار یک تابع جدید بنویسید و از متدهای src و dest پلاگین Gulp استفاده کنید.


~~~javascript
function fontTask() {
  return gulp.src('app/fonts/**/*')
  .pipe(gulp.dest('dist/fonts'))
};
~~~

## دسته‌بندی و اجرای task ها

می توانیم task هایی که نوشتیم را به دو دسته کلی تقسیم کنیم ، دسته اول موادی هستند که هنگام توسعه از آنها استفاده می شود شامل تبدیل SASS به CSS ، رصد تغییرات در فایل‌ها و Reload خودکار مرورگر ؛ دسته دوم task هایی هستند که بعد از پایان توسعه برای بهینه‌سازی استفاده می شوند که الحاق فایل‌های CSS و JS و فشرده‌سازی فایل‌های CSS ، JS ، HTML و تصاویر را شامل می‌شود.

جهت یادآوری دسته اول task ها را با استفاده از متد parallel پلاگین گالپ بصورت زیر دسته‌بندی کردیم و برای اجرا کافی است از دستور `gulp start` در خط فرمان استفاده شود.

~~~javascript
exports.start = gulp.parallel(browserSyncTask, sassTask , watchTask);
~~~

دسته دوم task هایی که نوشتیم باید بصورت سری و به ترتیب اجرا شوند ، بدین منظور از متد series پلاگین gulp بهره بگیرید. این متد تسک هایی که بعنوان ورودی دریافت می کند را بصورت سری اجرا خواهد کرد .

~~~javascript
exports.build = gulp.series(optTask, imgTask, fontTask);
~~~

همانطور که حتما متوجه شدید با استفاده از دستور `gulp build` در خط فرمان این گروه از task ها اجرا خواهند شد.

## جمع بندی

تا اینجا یاد گرفتید چطور یک سرور راه اندازی کنید، تغییرات را در فایل‌ها رصد، فایل‌های SASS را به CSS تبدیل و فایل‌ها را الحاق و فشرده‌ کنید؛ اما این پایان کار نیست و با یادگیری نحوه کار این پلاگین، خودتان به راحتی می توانید task های جدید بنویسید و کارهایی که هنگام توسعه بصورت دستی انجام می‌دادید را خودکار کنید یا performance پروژه هایتان را افزایش دهید . 

در ادامه می توانید با استفاده از unCSS کدهای بلااستفاده را حذف کنید ، از sourcemap برای ساده کردن فرایند دیباگ استفاده کنید، با استفاده از Babel کدهای جاوا اسکریپت را با استاندار ES6 بنویسید یا کدهای Typescript را کامپایل و به جاوا اسکریپت تبدیل کنید و ...

کدهای نوشته شده را می توانید بصورت کامل در [مخزن گیتهابی](https://github.com/arman-borkhani/git-workflow)که برای این پروژه ساخته ام مشاهده کنید .

[منبع اصلی](https://css-tricks.com/gulp-for-beginners/)