# Silgy

In 1995, when I landed my first computer job, my PC had an Intel 286 processor and 1 MB of RAM. Disks had spinning plates, nobody heard of SSD. Our office had Novell file server. And whatever we'd do, programs responded **immediately**.

Fast forward to 2018 and my PC has Intel i5 processor and 8 GB of RAM. Everyone can download GCC for free and there's Stackoverflow.com. And guess what? Web applications doing the same things in my intranet now are **painfully slow**.

That's why I've written Silgy. I think all the web applications in the world should be written in it. World would be much better off.

In Silgy you just compile and link your logic into one executable that responds immediately to HTTP requests, without creating a new thread or — God forbid — process. No layers, no dependencies, no layers, translations, layers, converters, layers...

What you get with Silgy:

- **Speed** − measured in µ-seconds.
- **Safety** − nobody can ever see your application logic nor wander through your filesystem nor run scripts. It has build-in protection against most popular attacks.
- **Small memory footprint** − a couple of MB for demo app − can be easily reduced for embedded apps.
- **Simple coding** − straightforward approach, easy to understand even for a beginner programmer ([jump to Hello World](https://github.com/silgy/silgy#hello-world)).
- **All-In-One** − no need to install external modules; Silgy source already contains all the logic required to run the application.
- **Simple deployment / cloud vendor independency** − only one executable file (or files in gateway/services model) to move around.
- **Low TCO** − ~$3 per month for hosting small web application with MySQL server (AWS t2.micro), not even mentioning planet-friendliness.

Silgy is written in ANSI C in order to support as many platforms as possible and it's C++ compilers compatible. Sample [silgy_app.cpp](https://github.com/silgy/silgy/blob/master/src/silgy_app.cpp) source module can be C as well as C++ code. Typical application code will look almost the same as in any of the C family language: C++, Java, JavaScript (before it has been destroyed with ES6 and all that arrow functions hell everyone thinks must be used, and hardly anyone really understands). All that could be automated, is automated.

It aims to be All-In-One solution for writing typical web application — traditional HTML rendering model, SPA or mixed. It handles HTTPS, and anonymous and registered user sessions — even with forgotten passwords. Larger applications or those using potentially blocking resources may want to split logic into the set of services talking to the gateway via POSIX queues in an asynchronous manner, using Silgy's [ASYNC](https://github.com/silgy/silgy/wiki/Silgy-compilation-switches#async) facility. Macros [CALL_ASYNC](https://github.com/silgy/silgy#void-call_asyncconst-char-service-const-char-data-int-timeout) and [CALL_ASYNC_NR](https://github.com/silgy/silgy#void-call_async_nrconst-char-service-const-char-data) make it as simple as possible.

Web applications like [Budgeter](https://budgeter.org) or [minishare](https://minishare.com) based on Silgy, fit in free 1GB AWS t2.micro instance, together with MySQL server. Typical processing time (between reading HTTP request and writing response to a socket) on 1 CPU t2.micro is around 100 µs (microseconds). Even with the network latency [it still shows](https://tools.pingdom.com/#!/bu4p3i/https://budgeter.org).
  
<div align="center">
<img src="https://minishare.com/show?p=MWPcAbmY&i=2" width=418/>
</div>

## Simplicity
In silgy_app.cpp:
```source.c++
int app_process_req(int ci)
{
    OUT("Hello World!");
    return OK;
}
```
Compile with `m` script and run `silgy_app` binary (`silgy_app.exe` on Windows). That's it, your application is now listening on the port 80 :) (If you want different port, add it as a command line argument)

## Requirements
Silgy is being developed around the idea of using as much generic environment as possible. Therefore it requires only three things:

1. Computer with operating system (Linux / UNIX / Windows),
2. C/C++ compiler. I recommend GCC (which is known as MinGW on Windows, AFAIK it is also used by CodeBlocks).
3. Silgy [src](https://github.com/silgy/silgy/tree/master/src).

Fuss-free deployment and cloud vendor independency means that production machine requires only operating system and silgy_app executable file(s), and optionally database server if your application uses one.

## Priorities / tradeoffs
Every project on Earth has them. So you'd better know.

1. *Speed*. Usually whenever I get to choose between speed and code duplication, I choose speed.

2. *Speed*. Usually whenever I get to choose between speed and saving memory, I choose speed. So there are mostly statics, stack and arrays, only a few of mallocs. For the same reason static files are read only at the startup and served straight from the memory.

3. *User code simplicity*. Usually whenever I get to choose between versatility and simplicity, I choose simplicity. 99.999% of applications do not require 10 levels of nesting in JSON. If you do need this, there is selection of [libraries](http://json.org/) to choose from, or you're a beginner like every programmer once was, and you still need to sweat your way to simple and clean code.

4. *Independency*. I try to include everything I think a typical web application may need in Silgy engine. If there are external libraries, I try to use most ubiquitous and generic ones, like i.e. OpenSSL and link statically. Of course you may prefer to add any library you want and/or link dynamically, there's nothing in Silgy that prevents you from doing so.

5. *What does deployment mean?* If you've written your app in Silgy, it means copying executable file to production machine which has nothing but operating system installed. OK, add jpegs and css. Oh — wait a minute — you prefer to learn [how to develop on Kubernetes](https://kubernetes.io/blog/2018/05/01/developing-on-kubernetes/) first, because everyone talks so cool about it... Then I can't help you. I'm actually learning it but only because my organization handles tens or hundreds of thousands requests per second, we have money for servers, development teams, admin teams and my boss made me. If you're Google or Amazon then you definitely need to have something. There is also a [hundred or so](https://en.wikipedia.org/wiki/List_of_build_automation_software) of other build automation software. Good luck with choosing the right one. And good luck with paying for the infrastructure. One of my priorities was to make Silgy app not needing this at all.

## Step-by-Step on Windows

### 0. Install C++ compiler
I recommend free, open source [MinGW](http://mingw.org).

### 1. Create project folder
I.e. `C:\cpp\silgy_app`

### 2. Download Silgy
On the [Silgy project's main page](https://github.com/silgy/silgy/tree/master/src) click **Clone or download** button, then choose ZIP version, open it and unpack **src** folder to your project folder.

### 3. Open a command line window

### 4. Go to your project folder (directory)
and then src:
```source.sh
cd \cpp\silgy_app\src
```

### 5. Compile:
```source.sh
m
```

### 6. Run:
```source.sh
silgy_app
```

### 7. Open browser and navigate to `localhost`
You should see something like this:

<img src="https://minishare.com/show?p=v1EcflRY&i=2">

## Step-by-Step on Linux
I assume that you know how to log in to your Linux. If not, and you're on Windows, like me, I recommend installing [PuTTY](https://www.putty.org/) and [WinSCP](https://winscp.net/eng/index.php).

I've managed to produce a [step by step video tutorial](https://www.youtube.com/watch?v=OtatIkHQAzs) on how to set Silgy Hello World up on a free AWS EC2 instance. It shows AWS part (creating an instance, assigning public IP address, connecting to it via WinSCP and PuTTY) as well as the below steps.

### 0. Install C++ compiler
```source.sh
sudo yum install gcc-c++
```
Optionally, if you're planning to use [HTTPS](https://github.com/silgy/silgy/wiki/Silgy-compilation-switches#https):
```source.sh
sudo yum install openssl-devel
```
Optionally, if you're planning to use MySQL:
```source.sh
sudo yum install mysql-devel
```

### 1. In your $HOME, create a project directory, i.e. web:  
```source.sh
mkdir web
```

### 2. Set SILGYDIR environment variable to your project directory
If you use bash, that would be in .bash_profile in your home directory:
```source.sh
export SILGYDIR=/home/ec2-user/web
```
Then you need to either restart your shell session or execute above command.

### 3. Add SILGYDIR to sudoers
Without it sudo won't see it.
```source.sh
sudo visudo
```
then find the block starting with:
```
Defaults    env_reset
```
and add a new line like this:
```
Defaults    env_keep += "SILGYDIR"
```
(If you don't know **vi** editor yet, here is a [cheat sheet](http://www.atmos.albany.edu/daes/atmclasses/atm350/vi_cheat_sheet.pdf).)

### 4. In your project directory, create some others:
```source.sh
cd web
mkdir src      # all the sources from this repository's src
mkdir bin      # executable(s), silgy.conf, blacklist.txt will be there
mkdir res      # static resources like pictures, html-s, robots.txt etc.
mkdir resmin   # resources to be minified (CSS and JS)
mkdir logs     # peek there if there's something wrong
```

### 5. Throw all the files from [src](https://github.com/silgy/silgy/tree/master/src) directory here to your src.
If you are on Windows, click **Clone or download** button, then choose ZIP version, unpack it locally and copy content of **src** to your instance — I use WinSCP for this.

Back to terminal window and **m** and **te** scripts must have executable flag:
```source.sh
cd src
chmod u+x m
chmod u+x te
```

### 6. Compile:
```source.sh
./m
```

### 7. Run:
```source.sh
sudo ../bin/silgy_app
```
That's it. Your app should now be online.

In a 'no-hung-up mode' you'll be able to exit terminal after starting the app:
```source.sh
sudo nohup ../bin/silgy_app &
```
or you can just use scripts provided in [bin](https://github.com/silgy/silgy/tree/master/bin) directory: [silgystart](https://github.com/silgy/silgy/blob/master/bin/silgystart) and [silgystop](https://github.com/silgy/silgy/blob/master/bin/silgystop).

There will also be a daemon mode in the near future.

### Note for Amazon Web Services (AWS) users
For your own good, fresh EC2 instances have a very restricted security policy with only SSH port (22) open. If you haven't done so, you need to edit that policy and add HTTP port (80) before AWS lets your browser in.

## Where's my program?
Your app logic is in [silgy_app.cpp](https://github.com/silgy/silgy/blob/master/src/silgy_app.cpp) and [app_process_req()](https://github.com/silgy/silgy#int-app_process_reqint-ci) is your main, called with every browser request. After downloading Silgy, there's a third version of [Hello World](https://github.com/silgy/silgy#hello-world) there to help you get on.

## Static Resources
Static resources are simply any content that you rarely change and keep as ordinary disk files, as opposed to dynamic content that is generated in your code, as a unique response to user request. In this regard, Silgy is like any other web server (except it's extremely fast). Statics usually include pictures, css, robots.txt etc.

Static resources are read into memory on startup from **res** directory. Static resources you want to serve minified (CSS and JS), are read into memory and minified on startup from **resmin** directory. Then, every 60 seconds (provided no traffic, that is select() timeouted) both directories are scanned for changes.

Static resources are handled automatically, you don't have to add anything in your app.

In addition to placing your statics in res and resmin directories, you can generate text statics from within your code at the start, and add them to the statics using [silgy_add_to_static_res()](https://github.com/silgy/silgy#void-silgy_add_to_static_resconst-char-name-char-src).

## Response Header
Response header is generated automatically, however you can overwrite defaults with a couple of [macros](https://github.com/silgy/silgy#void-res_statusint-code).

## Hello World
Simplest Hello World:
```source.c++
int app_process_req(int ci)
{
    OUT("Hello World!");
    return OK;
}
```

Simple HTML with 2 pages:
```source.c++
int app_process_req(int ci)
{
    if ( REQ("") )  // landing page
    {
        OUT_HTML_HEADER;
        OUT("<h1>%s</h1>", APP_WEBSITE);
        OUT("<h2>Welcome to my web app!</h2>");
        OUT_HTML_FOOTER;
    }
    else if ( REQ("about") )
    {
        OUT_HTML_HEADER;
        OUT("<h1>%s</h1>", APP_WEBSITE);
        OUT("<h2>About</h2>");
        OUT("<p>Hello World Sample Silgy Web Application</p>");
        OUT("<p><a href=\"/\">Back to landing page</a></p>");
        OUT_HTML_FOOTER;
    }
    else  // page not found
    {
        ret = ERR_NOT_FOUND;
    }

    return OK;
}
```

And this is a tad extended Hello World example to demonstrate query string handling:
```source.c++
int app_process_req(int ci)
{
    int ret=OK;

    if ( REQ("") )  // landing page
    {
        OUT_HTML_HEADER;
        OUT("<h1>%s</h1>", APP_WEBSITE);
        OUT("<h2>Welcome to my web app!</h2>");
        OUT("<p>Click <a href=\"welcome\">here</a> to try my welcoming bot.</p>");
        OUT_HTML_FOOTER;
    }
    else if ( REQ("welcome") )  // welcoming bot
    {
        OUT_HTML_HEADER;
        OUT("<h1>%s</h1>", APP_WEBSITE);

        // show form

        OUT("<p>Please enter your name:</p>");
        OUT("<form action=\"welcome\"><input name=\"firstname\" autofocus> <input type=\"submit\" value=\"Run\"></form>");

        QSVAL qs_firstname;  // query string value

        // bid welcome

        if ( QS("firstname", qs_firstname) )  // firstname present in query string, copy it to qs_firstname
        {
            DBG("query string arrived with firstname %s", qs_firstname);  // this will write to the log file
            OUT("<p>Welcome %s, my dear friend!</p>", qs_firstname);
        }

        // show link to main page

        OUT("<p><a href=\"/\">Back to landing page</a></p>");

        OUT_HTML_FOOTER;
    }
    else  // page not found
    {
        ret = ERR_NOT_FOUND;  // this will return status 404 to the browser
    }

    return ret;
}
```

## Configuration File
You can change default behaviour with [configuration parameters](https://github.com/silgy/silgy/wiki/Silgy-configuration-parameters). Note that you can also use config file to pass your own parameters which you can read with [silgy_read_param()](https://github.com/silgy/silgy#bool-silgy_read_paramconst-char-param-char-dest).

## Compilation Switches
Because speed is Silgy's priority, every possible decision is taken at a compile time rather than at runtime. Therefore, unless you specify you want to use some features, they won't be in your executable.

Add your switches to [m](https://github.com/silgy/silgy/blob/master/src/m) before compilation, i.e.:
```
g++ silgy_app.cpp silgy_eng.c silgy_lib.c \
-D HTTPS -D DBMYSQL ...
```
[Compilation switches specification](https://github.com/silgy/silgy/wiki/Silgy-compilation-switches)

## Functions and macros

[Full reference is now moving to Wiki](https://github.com/silgy/silgy/wiki/Silgy-functions-and-macros).

Below I'll leave just the most basic ones that are essential for building any web application in Silgy ([REQ](https://github.com/silgy/silgy/wiki/REQ()), [OUT](https://github.com/silgy/silgy/wiki/OUT()) and [QS](https://github.com/silgy/silgy/wiki/QS())).

### bool REQ(const char \*string)
Return TRUE if first part of request URI matches *string*. 'First part' means everything until **/** or **?**, for example:

URI|Catched by
----|----
`/calc?first=2&second=3`|REQ("calc")
`/customers/123`|REQ("customers")
`/about.html`|REQ("about.html")

Example:  
```source.c++
if ( REQ("calc") )
    process_calc(ci);
else if ( REQ("about") )
    gen_page_about(ci);
else if ( REQ("") )
    gen_page_main(ci);
else
    ret = ERR_NOT_FOUND;
```
### void OUT(const char \*string[, ...])
Send *string* to a browser. Optionally it takes additional arguments, as per [printf function family specification](https://en.wikipedia.org/wiki/Printf_format_string).

Examples:
```source.c++
OUT("<!DOCTYPE html>");
OUT("<p>There are %d records in the table.</p>", records);
```
### bool QS(const char \*param, QSVAL variable)
Search query string for *param* and if found, URI-decode it, copy its value to *variable* and return TRUE. Otherwise return FALSE. For POST, PUT and DELETE methods it assumes query string is in payload.  
QSVAL is just a typedef for C-style string, long enough to hold the value, as QS makes the check.

Example:  
```source.c++
QSVAL qs_firstname;

if ( QS("firstname", qs_firstname) )
    OUT("<p>Welcome %s!</p>", qs_firstname);
```
QS comes in four SQL- and XSS-injection security flavours:  
  
QS - default - behaviour depends on [QS_DEF_](https://github.com/silgy/silgy#qs_def_html_escape-qs_def_sql_escape-qs_def_dont_escape) compilation switch (by default it's QS_DEF_HTML_ESCAPE).  
QS_HTML_ESCAPE - value is HTML-escaped  
QS_SQL_ESCAPE - value is SQL-escaped  
QS_DONT_ESCAPE - value is not escaped  
  
And the fifth one:  
  
QS_RAW - value is not URI-decoded  

### char* REQ_URI
Request URI.

### bool REQ_GET
Return TRUE if request method is GET.

### bool REQ_POST
Return TRUE if request method is POST.

### bool REQ_PUT
Return TRUE if request method is PUT.

### bool REQ_DELETE
Return TRUE if request method is DELETE.

### bool REQ_DSK
Return TRUE if request user agent is desktop.

### bool REQ_MOB
Return TRUE if request user agent is mobile.  
Example:  
```source.c++
if ( REQ_MOB )
    OUT("<meta name=\"viewport\" content=\"width=device-width\">");
```
### bool REQ_BOT
Return TRUE if user agent in a request is a bot.  
Silgy maintains its own internal library of known bots to determine whether request comes from one. If the request comes from a bot, user session is not created. You can also use this to better estimate number of visits to your site.  
Example:  
```source.c++
if ( !REQ_BOT )
    ++real_visits;
```
### char* REQ_LANG
User agent primary language code.

### bool HOST(const char \*string)
Return TRUE if HTTP request *Host* header matches *string*. Case is ignored.

Example:
```source.c++
if ( HOST("example.com") )
    process_example(ci);
```
### void RES_STATUS(int code)
Set response status to *code*.

Example:
```source.c++
RES_STATUS(501);
```
### void RES_CONTENT_TYPE(const char \*string)
Set response content type to *string*.

Example:
```source.c++
RES_CONTENT_TYPE("text/plain");
```
### void RES_LOCATION(const char \*string)
Redirect browser to *string*.

Example:
```source.c++
RES_LOCATION("login");
```
### void RES_CONTENT_DISPOSITION(const char \*string[, ...])
Add Content-Disposition to response header.

Example:
```source.c++
RES_CONTENT_DISPOSITION("attachment; filename=\"%s.csv\"", doc_name);
```
### void RES_DONT_CACHE
Prevent response from being cached by browser.

### void REDIRECT_TO_LANDING
Redirect browser to landing page.

### void ALWAYS(const char \*str[, ...]), void ERR(const char \*str[, ...]), void WAR(const char \*str[, ...]), void INF(const char \*str[, ...]), void DBG(const char \*str[, ...])
Write *str* to log, depending on log level set in [conf file](https://github.com/silgy/silgy/wiki/Silgy-configuration-parameters). Optionally it takes additional arguments, as per [printf function family specification](https://en.wikipedia.org/wiki/Printf_format_string).
```
ALWAYS - regardless of log level  
ERR - only if log level >= 1, writes ERROR: before string  
WAR - only if log level >= 2, writes WARNING: before string  
INF - only if log level >= 3  
DBG - only if log level >= 4  
```
Log file is created on every start in `$SILGYDIR/logs`. File name contains date and time: *YYYYMMDD_HHmm.log* or — if test=1 in config — *YYYYMMDD_HHmm_t.log*. Every midnight new log file is started.  
Examples:
```source.c++
ALWAYS("Server is starting");
DBG("in a while loop, i = %d", i);
```
If log level is set to 4, there's quite a lot of information logged, including full request and response HTTP headers, and every call flushes the buffer straight away, to help in investigation in case of crash.

## Asynchronous calls

### void CALL_ASYNC(const char \*service, const char \*data)
Call *service* with default timeout (ASYNC_DEF_TIMEOUT, currently 30 seconds). When the response arrives or timeout passes, [app_async_done()](https://github.com/silgy/silgy#void-app_async_doneint-ci-const-char-service-const-char-data-int-err_code) will be called with the same *service*.

Example:
```source.c++
CALL_ASYNC("getCustomer", cust_id);
```

### void CALL_ASYNC_TM(const char \*service, const char \*data, int timeout)
Call *service*. *timeout* is in seconds. When the response arrives or timeout passes, [app_async_done()](https://github.com/silgy/silgy#void-app_async_doneint-ci-const-char-service-const-char-data-int-err_code) will be called with the same *service*. If timeout is < 1 or > ASYNC_MAX_TIMEOUT (currently 1800 seconds), it is set to ASYNC_MAX_TIMEOUT.

Example:
```source.c++
CALL_ASYNC_TM("getCustomer", cust_id, 10);
```

### void CALL_ASYNC_NR(const char \*service, const char \*data)
Call *service*. Response is not required.

Example:
```source.c++
CALL_ASYNC_NR("setCounter", counter);
```

### bool S(const char \*string)
Return TRUE if service matches *string*.  
Example: see [app_async_done()](https://github.com/silgy/silgy#void-app_async_doneint-ci-const-char-service-const-char-data-int-err_code).

## Functions

### void silgy_add_to_static_res(const char \*name, char \*src)
Expose string *src* as a [static resource](https://github.com/silgy/silgy#static-resources). Instead of using a file, you may sometimes want to generate something like CSS. Once you've added it, it's visible the same way other statics are. *src* has to be a 0-terminated string.

Example:
```source.c++
#define COLOR_RED "#b40508"  // carefully crafted 'red' we want to use across the entire app
                             // as well as many times in CSS
// ...

void create_css()  // called from app_init()
{
    char dsk[2048];
    char mob[2048];
    char dsk_min[2048];
    char mob_min[2048];

    // .w_border style used in .....
    sprintf(dsk, ".w_border {color:%s; border:1px solid grey;}", COLOR_RED);
    sprintf(mob, ".w_border {color:%s; border:2px solid grey;}", COLOR_RED);

    // .no_border style used in .....
    sprintf(dsk, " %s .no_border {color:%s; border:0;}", dsk, COLOR_RED);
    sprintf(mob, " %s .no_border {color:%s; border:0;}", mob, COLOR_RED);
    // ...

    silgy_minify(dsk_min, dsk);
    silgy_add_to_static_res("dsk.css", dsk_min);

    silgy_minify(mob_min, mob);
    silgy_add_to_static_res("mob.css", mob_min);
}
```

### char \*silgy_html_esc(const char \*str)
HTML-escape *str*, return pointer to a new string. Max length is 64 kB.

### char \*silgy_html_unesc(const char \*str)
HTML-unescape *str*, return pointer to a new string. Max length is 64 kB.

### char \*silgy_sql_esc(const char \*str)
SQL-escape *str*, return pointer to a new string. Max length is 64 kB.

### int silgy_minify(char \*dest, const char \*src)
Minify CSS or JS. Return new length. Example: see [silgy_add_to_static_res()](https://github.com/silgy/silgy#void-silgy_add_to_static_resconst-char-name-char-src).

### void silgy_random(char \*dest, int len)
Generate random string of *len* length and copy it to *dest*. Generated string can contain letters (lower- and upper-case) and digits.

### bool silgy_read_param(const char \*param, char \*dest)
Copy config file parameter to a variable. Returns true if found. *dest* can be NULL to only do presence check.

Example:
```source.c++
static char M_someSetting[256]=SOME_SETTING_DEFAULT_VALUE;

    // in app_init()
    silgy_read_param("someSetting", M_someSetting);
```

### bool silgy_read_param_int(const char \*param, int \*dest)
Copy config file parameter to an integer variable. Returns true if found. *dest* can be NULL to only do presence check.

Example:
```source.c++
static int M_someSetting=SOME_SETTING_DEFAULT_VALUE;

    // in app_init()
    silgy_read_param_int("someSetting", &M_someSetting);
```

### void silgy_set_auth_level(const char \*resource, char level)
Set required authorization level for a resource.

*level* can have one of the following values:

macro|notes
-----|-----
AUTH_LEVEL_NONE|No user session is required.
AUTH_LEVEL_ANONYMOUS|Anonymous user session is required. If there's no valid **as** cookie, anonymous user session is started.
AUTH_LEVEL_LOGGEDIN|Logged in user session is required. If request does not have valid **ls** cookie, it's redirected to URI defined in [silgy_app.h](https://github.com/silgy/silgy/blob/master/src/silgy_app.h) APP_LOGIN_URI.
AUTH_LEVEL_ADMIN|Logged in as admin is required. If USERSBYLOGIN is used, current user must be "admin" or — if USERSBYEMAIL is used — current user must be equal to APP_ADMIN_EMAIL in [silgy_app.h](https://github.com/silgy/silgy/blob/master/src/silgy_app.h). Otherwise request will receive 404.

Resources not set with silgy_set_auth_level() get default level specified in [silgy_app.h](https://github.com/silgy/silgy/blob/master/src/silgy_app.h) APP_DEF_AUTH_LEVEL.  
[Static resources](https://github.com/silgy/silgy#static-resources) always have AUTH_LEVEL_NONE.  
Example:
```source.c++
// in app_init()
silgy_set_auth_level("about", AUTH_LEVEL_NONE);
silgy_set_auth_level("dashboard", AUTH_LEVEL_LOGGEDIN);
silgy_set_auth_level("blockIP", AUTH_LEVEL_ADMIN);
```

## Engine callbacks

### void app_done()
Called once, during termination.

### bool app_init(int argc, char \*argv[])
Called once at the beginning, but after server init. Returning *true* means successful initialization. Good place to set authorization levels, generate statics, etc.

### void app_luses_init(int ci)
Called when logged in user session is created.

### int app_process_req(int ci)
This is the main entry point for Silgy web application logic. *ci* is a connection index, as there can be many connections served asynchronously at the same time. **Always pass ci down the calling stack** as this is required by most macros and functions. For examples, see [Hello World](https://github.com/silgy/silgy#hello-world).

### void app_uses_init(int ci)
Called when a new user session is created.

### void app_uses_reset(int usi)
Called when user session is closed.

### void app_async_done(int ci, const char \*service, const char \*data, int err_code)
[ASYNC](https://github.com/silgy/silgy/wiki/Silgy-compilation-switches#async) compilation switch is required.

Finish page rendering after CALL_ASYNC has returned service response.

Example:
```source.c++
void app_async_done(int ci, const char *service, const char *data, int err_code)
{
    if ( S("getCustomer") )
    {
        if ( err_code == ERR_ASYNC_TIMEOUT )
        {
            WAR("getCustomer timeout-ed");
            OUT("<p>There was no response from getCustomer service</p>");
        }
        else if ( err_code == OK )
        {
            OUT("<p>Customer data: %s</p>", data);
        }

        OUT_HTML_FOOTER;
    }
}
```
  
