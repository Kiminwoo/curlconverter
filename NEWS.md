## 1.0.0

WIP

* removed V8 dependency
* now uses `docopt` package
* Fixed #20
* Added "query = list(...)" to the created function if the original URL
  had query parameters
* Nicer formatting of make_req() functions via styler

## 0.8.0
* support for `-v / --verbose` and `-u / --user` `curl` command-line parameters.

## 0.7.0
* calls to `add_headers` & `set_cookies` now have an `httr::` prefix so 
  it's not necessary to load `httr`
* the `url_parts` slot of the return value of `straighten()` is classed as
  `url` so `httr::build_url()` can create a URL from it. see next bullet
  for why.
* `make_req()` has a new parameter - `use_parts`. If `TRUE` it will call
  `httr::build_url()` on the `url_parts` slot of the return value of
  `straighten()` vs use the passed-in cURL URL. This means you can
  modify the `url_parts` before calling `make_req()`. This is `FALSE` by
  default.

## 0.6.7
* Fixed bug in js module that caused the header parsing to fail if
  there was only one header (Fixes #4)
* Added single header test to test suite

## 0.6.6
* Code cleanup & documentation update

## 0.6.5
* reads/sets cookies

## 0.6.1
* Improved README <https://github.com/hrbrmstr/curlconverter/issues/3>

## 0.6.0
* changed idiom, see README

## 0.5.0
* `make_req` now actually _returns a working/callable R function_

## 0.4.0
* `make_req` turns the cURLs into `httr` requests

## 0.3.1
* Handles --header

## 0.3.0
* Added `parse_query`

## 0.2.0
* Added parsed URL to return value of `straighten()`

## 0.1.0
* initial release
