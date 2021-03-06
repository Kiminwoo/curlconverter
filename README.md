
[![Build
Status](https://travis-ci.org/hrbrmstr/curlconverter.svg?branch=master)](https://travis-ci.org/hrbrmstr/curlconverter)
[![AppVeyor Build
Status](https://ci.appveyor.com/api/projects/status/github/hrbrmstrcurlconverter?branch=master&svg=true)](https://ci.appveyor.com/project/hrbrmstr/curlconverter)
[![CRAN\_Status\_Badge](https://www.r-pkg.org/badges/version/curlconverter)](https://cran.r-project.org/package=curlconverter)
[![](http://cranlogs.r-pkg.org/badges/curlconverter)](https://cran.r-project.org/package=curlconverter)
[![Coverage
Status](https://img.shields.io/codecov/c/github/%3CUSERNAME%3E/%3CREPO%3E/master.svg)](https://codecov.io/github/%3CUSERNAME%3E/%3CREPO%3E?branch=master)

# curlconverter

Tools to Transform ‘cURL’ Command-Line Calls to ‘httr’ Requests

## Description

Deciphering web/‘REST’ ‘API’ and ‘XHR’ calls can be tricky, which is one
reason why internet browsers provide “Copy as cURL” functionality within
their “Developer Tools”pane(s). These ‘cURL’ command-lines can be
difficult to wrangle into an ‘httr’ ‘GET’ or ‘POST’ request, but you can
now “straighten” these ‘cURLs’ either from data copied to the system
clipboard or by passing in a vector of ‘cURL’ command-lines and getting
back a list of parameter elements which can be used to form ‘httr’
requests. These lists can be passed to another function to automagically
make ‘httr’ functions.

### WIP

This is the path back to CRAN and to 1.0.0.

The V8 dependency has been removed and the package now uses `docopt`.
This will make it easier to install on many systems and enable rapid
addition of support for new `cURL` command-line parameters.

The “HAR” functions are not working well with the new methods but will
be for full release.

## What’s Inside The Tin

The following functions are implemented:

  - `straighten`: convert one or more *“Copy as cURL”* command lines
    into useful data
  - `parse_query`: parse URL query parameters into a named list
  - `make_req`: turn parsed cURL command lines into a `httr` request
    functions (i.e. returns working R functions)

The following functions are implemented:

## Installation

``` r
devtools::install_github("hrbrmstr/curlconverter")
```

## Usage

``` r
library(curlconverter)
library(jsonlite)
library(httr)

# current verison
packageVersion("curlconverter")
```

    ## [1] '1.0.0'

Simple example using a call to
<https://httpbin.org/headers>:

``` r
httpbinrhcurl <- "curl 'https://httpbin.org/headers' -H 'pragma: no-cache' -H 'accept-encoding: gzip, deflate, sdch' -H 'accept-language: en-US,en;q=0.8' -H 'upgrade-insecure-requests: 1' -H 'user-agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_3) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/49.0.2623.39 Safari/537.36' -H 'accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8' -H 'cache-control: no-cache' -H 'referer: https://httpbin.org/' --compressed"

straight <- straighten(httpbinrhcurl)
```

    ## curl 'https://httpbin.org/headers' -H 'pragma: no-cache' -H 'accept-encoding: gzip, deflate, sdch' -H 'accept-language: en-US,en;q=0.8' -H 'upgrade-insecure-requests: 1' -H 'user-agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_3) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/49.0.2623.39 Safari/537.36' -H 'accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8' -H 'cache-control: no-cache' -H 'referer: https://httpbin.org/' --compressed

``` r
res <- make_req(straight)

# or 

straighten(httpbinrhcurl) %>% 
  make_req() -> res
```

    ## curl 'https://httpbin.org/headers' -H 'pragma: no-cache' -H 'accept-encoding: gzip, deflate, sdch' -H 'accept-language: en-US,en;q=0.8' -H 'upgrade-insecure-requests: 1' -H 'user-agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_3) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/49.0.2623.39 Safari/537.36' -H 'accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8' -H 'cache-control: no-cache' -H 'referer: https://httpbin.org/' --compressed

``` r
toJSON(content(res[[1]](), as="parsed"), auto_unbox = TRUE, pretty=TRUE)
```

    ## {
    ##   "headers": {
    ##     "Accept": "application/json, text/xml, application/xml, */*,text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8",
    ##     "Accept-Encoding": "gzip, deflate, sdch",
    ##     "Accept-Language": "en-US,en;q=0.8",
    ##     "Cache-Control": "no-cache",
    ##     "Connection": "close",
    ##     "Host": "httpbin.org",
    ##     "Pragma": "no-cache",
    ##     "Referer": "https://httpbin.org/",
    ##     "Upgrade-Insecure-Requests": "1",
    ##     "User-Agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_3) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/49.0.2623.39 Safari/537.36"
    ##   }
    ## }

Slightly more complex
one:

``` r
toJSON(straighten("curl 'http://financials.morningstar.com/ajax/ReportProcess4HtmlAjax.html?&t=XNAS:MSFT&region=usa&culture=en-US&cur=&reportType=is&period=12&dataType=A&order=asc&columnYear=5&curYearPart=1st5year&rounding=3&view=raw&r=973302&callback=jsonp1454021128757&_=1454021129337' -H 'Cookie: JSESSIONID=5E43C98903E865D72AA3C2DCEF317848; sfhabit=asc%7Craw%7C3%7C12%7CA%7C5%7Cv0.14; ScrollY=0' -H 'DNT: 1' -H 'Accept-Encoding: gzip, deflate, sdch' -H 'Accept-Language: en-US,en;q=0.8' -H 'User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_3) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/47.0.2526.111 Safari/537.36' -H 'Accept: text/javascript, application/javascript, */*' -H 'Referer: http://financials.morningstar.com/income-statement/is.html?t=MSFT&region=usa&culture=en-US' -H 'X-Requested-With: XMLHttpRequest' -H 'Connection: keep-alive' -H 'Cache-Control: max-age=0' --compressed"), auto_unbox = TRUE, pretty=TRUE)
```

    ## curl 'http://financials.morningstar.com/ajax/ReportProcess4HtmlAjax.html?&t=XNAS:MSFT&region=usa&culture=en-US&cur=&reportType=is&period=12&dataType=A&order=asc&columnYear=5&curYearPart=1st5year&rounding=3&view=raw&r=973302&callback=jsonp1454021128757&_=1454021129337' -H 'Cookie: JSESSIONID=5E43C98903E865D72AA3C2DCEF317848; sfhabit=asc%7Craw%7C3%7C12%7CA%7C5%7Cv0.14; ScrollY=0' -H 'DNT: 1' -H 'Accept-Encoding: gzip, deflate, sdch' -H 'Accept-Language: en-US,en;q=0.8' -H 'User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_3) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/47.0.2526.111 Safari/537.36' -H 'Accept: text/javascript, application/javascript, */*' -H 'Referer: http://financials.morningstar.com/income-statement/is.html?t=MSFT&region=usa&culture=en-US' -H 'X-Requested-With: XMLHttpRequest' -H 'Connection: keep-alive' -H 'Cache-Control: max-age=0' --compressed

    ## [
    ##   {
    ##     "url": "http://financials.morningstar.com/ajax/ReportProcess4HtmlAjax.html?&t=XNAS:MSFT&region=usa&culture=en-US&cur=&reportType=is&period=12&dataType=A&order=asc&columnYear=5&curYearPart=1st5year&rounding=3&view=raw&r=973302&callback=jsonp1454021128757&_=1454021129337",
    ##     "method": "GET",
    ##     "cookies": {
    ##       "JSESSIONID": "5E43C98903E865D72AA3C2DCEF317848",
    ##       "sfhabit": "asc%7Craw%7C3%7C12%7CA%7C5%7Cv0.14",
    ##       "ScrollY": "0"
    ##     },
    ##     "username": {},
    ##     "password": {},
    ##     "user_agent": {
    ##       "DNT": "1",
    ##       "Accept-Encoding": "gzip, deflate, sdch",
    ##       "Accept-Language": "en-US,en;q=0.8",
    ##       "Accept": "text/javascript, application/javascript, */*",
    ##       "Referer": "http://financials.morningstar.com/income-statement/is.html?t=MSFT&region=usa&culture=en-US",
    ##       "X-Requested-With": "XMLHttpRequest",
    ##       "Connection": "keep-alive",
    ##       "Cache-Control": "max-age=0"
    ##     },
    ##     "referer": {},
    ##     "data": {},
    ##     "headers": {
    ##       "DNT": "1",
    ##       "Accept-Encoding": "gzip, deflate, sdch",
    ##       "Accept-Language": "en-US,en;q=0.8",
    ##       "User-Agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_3) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/47.0.2526.111 Safari/537.36",
    ##       "Accept": "text/javascript, application/javascript, */*",
    ##       "Referer": "http://financials.morningstar.com/income-statement/is.html?t=MSFT&region=usa&culture=en-US",
    ##       "X-Requested-With": "XMLHttpRequest",
    ##       "Connection": "keep-alive",
    ##       "Cache-Control": "max-age=0"
    ##     },
    ##     "verbose": false,
    ##     "url_parts": {
    ##       "scheme": "http",
    ##       "hostname": "financials.morningstar.com",
    ##       "port": {},
    ##       "path": "ajax/ReportProcess4HtmlAjax.html",
    ##       "query": {
    ##         "1": "",
    ##         "t": "XNAS:MSFT",
    ##         "region": "usa",
    ##         "culture": "en-US",
    ##         "cur": "",
    ##         "reportType": "is",
    ##         "period": "12",
    ##         "dataType": "A",
    ##         "order": "asc",
    ##         "columnYear": "5",
    ##         "curYearPart": "1st5year",
    ##         "rounding": "3",
    ##         "view": "raw",
    ##         "r": "973302",
    ##         "callback": "jsonp1454021128757",
    ##         "_": "1454021129337"
    ##       },
    ##       "params": {},
    ##       "fragment": {},
    ##       "username": {},
    ##       "password": {}
    ##     },
    ##     "orig_curl": "curl 'http://financials.morningstar.com/ajax/ReportProcess4HtmlAjax.html?&t=XNAS:MSFT&region=usa&culture=en-US&cur=&reportType=is&period=12&dataType=A&order=asc&columnYear=5&curYearPart=1st5year&rounding=3&view=raw&r=973302&callback=jsonp1454021128757&_=1454021129337' -H 'Cookie: JSESSIONID=5E43C98903E865D72AA3C2DCEF317848; sfhabit=asc%7Craw%7C3%7C12%7CA%7C5%7Cv0.14; ScrollY=0' -H 'DNT: 1' -H 'Accept-Encoding: gzip, deflate, sdch' -H 'Accept-Language: en-US,en;q=0.8' -H 'User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_3) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/47.0.2526.111 Safari/537.36' -H 'Accept: text/javascript, application/javascript, */*' -H 'Referer: http://financials.morningstar.com/income-statement/is.html?t=MSFT&region=usa&culture=en-US' -H 'X-Requested-With: XMLHttpRequest' -H 'Connection: keep-alive' -H 'Cache-Control: max-age=0' --compressed"
    ##   }
    ## ]

There are some built-in test files you can play
with:

``` r
(curl_line <- readLines(system.file("extdata/curl5.txt", package="curlconverter"), warn=FALSE))
```

    ## [1] "curl -i -X POST http://1.2.3.4/endpoint -H \"Content-Type:application/json\" -H 'key:abcdefg'"

``` r
toJSON(straighten(curl_line, quiet=TRUE), auto_unbox = TRUE, pretty=TRUE)
```

    ## [
    ##   {
    ##     "url": "http://1.2.3.4/endpoint",
    ##     "method": "POST",
    ##     "cookies": {},
    ##     "username": {},
    ##     "password": {},
    ##     "user_agent": {},
    ##     "referer": {},
    ##     "data": {},
    ##     "headers": {
    ##       "Content-Type": "application/json",
    ##       "key": "abcdefg"
    ##     },
    ##     "verbose": false,
    ##     "url_parts": {
    ##       "scheme": "http",
    ##       "hostname": "1.2.3.4",
    ##       "port": {},
    ##       "path": "endpoint",
    ##       "query": {},
    ##       "params": {},
    ##       "fragment": {},
    ##       "username": {},
    ##       "password": {}
    ##     },
    ##     "orig_curl": "curl -i -X POST http://1.2.3.4/endpoint -H \"Content-Type:application/json\" -H 'key:abcdefg'"
    ##   }
    ## ]

``` r
(curl_line <- readLines(system.file("extdata/curl8.txt", package="curlconverter"), warn=FALSE))
```

    ## [1] "curl 'https://research.stlouisfed.org/fred2/series/MKTGDPSAA646NWDB/downloaddata' -H 'Pragma: no-cache' -H 'Origin: https://research.stlouisfed.org' -H 'Accept-Encoding: gzip, deflate' -H 'Accept-Language: en-US,en;q=0.8' -H 'Upgrade-Insecure-Requests: 1' -H 'User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_3) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/49.0.2623.39 Safari/537.36' -H 'Content-Type: application/x-www-form-urlencoded' -H 'Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8' -H 'Cache-Control: no-cache' -H 'Referer: https://research.stlouisfed.org/fred2/series/MKTGDPSAA646NWDB/downloaddata' -H 'Connection: keep-alive' -H 'DNT: 1' --data 'form%5Bnative_frequency%5D=Annual&form%5Bunits%5D=lin&form%5Bfrequency%5D=Annual&form%5Baggregation%5D=Average&form%5Bobs_start_date%5D=1968-01-01&form%5Bobs_end_date%5D=2014-01-01&form%5Bfile_format%5D=csv&form%5Bdownload_data_2%5D=' --compressed"

``` r
# example with query parameters in the body
req <- straighten(curl_line, quiet=FALSE)
```

    ## curl 'https://research.stlouisfed.org/fred2/series/MKTGDPSAA646NWDB/downloaddata' -H 'Pragma: no-cache' -H 'Origin: https://research.stlouisfed.org' -H 'Accept-Encoding: gzip, deflate' -H 'Accept-Language: en-US,en;q=0.8' -H 'Upgrade-Insecure-Requests: 1' -H 'User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_3) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/49.0.2623.39 Safari/537.36' -H 'Content-Type: application/x-www-form-urlencoded' -H 'Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8' -H 'Cache-Control: no-cache' -H 'Referer: https://research.stlouisfed.org/fred2/series/MKTGDPSAA646NWDB/downloaddata' -H 'Connection: keep-alive' -H 'DNT: 1' --data 'form%5Bnative_frequency%5D=Annual&form%5Bunits%5D=lin&form%5Bfrequency%5D=Annual&form%5Baggregation%5D=Average&form%5Bobs_start_date%5D=1968-01-01&form%5Bobs_end_date%5D=2014-01-01&form%5Bfile_format%5D=csv&form%5Bdownload_data_2%5D=' --compressed

``` r
# ugh
(req[[1]]$data)
```

    ## [1] "form%5Bnative_frequency%5D=Annual&form%5Bunits%5D=lin&form%5Bfrequency%5D=Annual&form%5Baggregation%5D=Average&form%5Bobs_start_date%5D=1968-01-01&form%5Bobs_end_date%5D=2014-01-01&form%5Bfile_format%5D=csv&form%5Bdownload_data_2%5D="

``` r
#yay!
toJSON(parse_query(req[[1]]$data), auto_unbox = TRUE, pretty=TRUE)
```

    ## {
    ##   "form[native_frequency]": "Annual",
    ##   "form[units]": "lin",
    ##   "form[frequency]": "Annual",
    ##   "form[aggregation]": "Average",
    ##   "form[obs_start_date]": "1968-01-01",
    ##   "form[obs_end_date]": "2014-01-01",
    ##   "form[file_format]": "csv",
    ##   "form[download_data_2]": ""
    ## }

Spinning straw into
gold

``` r
curl_line <- c('curl "http://anasim.iet.unipi.it/moniqa/php/from_js.php" -H "Origin: http://anasim.iet.unipi.it" -H "Accept-Encoding: gzip, deflate" -H "Accept-Language: it-IT,it;q=0.8,en-US;q=0.6,en;q=0.4" -H "User-Agent: Mozilla/5.0 (Windows NT 6.3; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/48.0.2564.103 Safari/537.36" -H "Content-Type: application/x-www-form-urlencoded; charset=UTF-8" -H "Accept: */*" -H "Referer: http://anasim.iet.unipi.it/moniqa/" -H "X-Requested-With: XMLHttpRequest" -H "Connection: keep-alive" --data "deviceid=65&function_name=extract_measurements" --compressed')

straighten(curl_line) %>% 
  make_req() -> get_data
```

    ## curl "http://anasim.iet.unipi.it/moniqa/php/from_js.php" -H "Origin: http://anasim.iet.unipi.it" -H "Accept-Encoding: gzip, deflate" -H "Accept-Language: it-IT,it;q=0.8,en-US;q=0.6,en;q=0.4" -H "User-Agent: Mozilla/5.0 (Windows NT 6.3; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/48.0.2564.103 Safari/537.36" -H "Content-Type: application/x-www-form-urlencoded; charset=UTF-8" -H "Accept: */*" -H "Referer: http://anasim.iet.unipi.it/moniqa/" -H "X-Requested-With: XMLHttpRequest" -H "Connection: keep-alive" --data "deviceid=65&function_name=extract_measurements" --compressed

``` r
cat(capture.output(get_data[[1]]), sep="\n")
```

    ## function () 
    ## httr::VERB(verb = "GET", url = "http://anasim.iet.unipi.it/moniqa/php/from_js.php", 
    ##     httr::add_headers(Origin = "http://anasim.iet.unipi.it", 
    ##         `Accept-Encoding` = "gzip, deflate", `Accept-Language` = "it-IT,it;q=0.8,en-US;q=0.6,en;q=0.4", 
    ##         `User-Agent` = "Mozilla/5.0 (Windows NT 6.3; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/48.0.2564.103 Safari/537.36", 
    ##         Accept = "*/*", Referer = "http://anasim.iet.unipi.it/moniqa/", 
    ##         `X-Requested-With` = "XMLHttpRequest", Connection = "keep-alive"), 
    ##     body = list(deviceid = "65", function_name = "extract_measurements"), 
    ##     encode = "form")
    ## <environment: 0x7ff274fb0030>

That also sends this to the console and clipboard:

``` r
httr::VERB(
  verb = "GET", url = "http://anasim.iet.unipi.it/moniqa/php/from_js.php",
  httr::add_headers(
    Origin = "http://anasim.iet.unipi.it",
    `Accept-Encoding` = "gzip, deflate",
    `Accept-Language` = "it-IT,it;q=0.8,en-US;q=0.6,en;q=0.4",
    `User-Agent` = "Mozilla/5.0 (Windows NT 6.3; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/48.0.2564.103 Safari/537.36",
    Accept = "*/*", Referer = "http://anasim.iet.unipi.it/moniqa/",
    `X-Requested-With` = "XMLHttpRequest",
    Connection = "keep-alive"
  ),
  body = list(
    deviceid = "65",
    function_name = "extract_measurements"
  ),
  encode = "form"
)
```

### Code of Conduct

Please note that this project is released with a [Contributor Code of
Conduct](CONDUCT.md).

By participating in this project you agree to abide by its terms.
