# nz-ird-validator-clj

_A validator for New Zealand tax numbers (IRD)_

## Installation

[![Clojars Project](https://clojars.org/jarden/nz-ird-validator/latest-version.svg)](http://clojars.org/jarden/nz-ird-validator)

## Usage

Function requires one string parameter:
* IRD number (normal or dashed format)
Returns a boolean.

```
(ns nz-ird-validator-clj.test
  (:require [nz-ird-validator-clj.core :refer [nz-ird-validator]]))
  
(nz-ird-validator "123-123-12")
;; => false
```

## Algorithm

The algorithm is based on the 'Non-Resident Withholding Tax And Resident Withholding Tax Specification Document' issues by the Inland Revenue on the 31st of March 2016.

### IR number validation - modulus 11 digit check

The IRD number format used by Inland Revenue is an eight or nine digit number consisting of the following parts -

* A seven or eight digit base number
* A trailing check digit

### Check digit validation

The following steps are to be performed:

* Check the valid range
    * If the IRD number is < 10-000-000 or > 150-000-000 then the number is invalid. This step ensures that the IRD number is in the already issued range, or is in the range expected to be issued in the next 10 years.
* Form the eight digit base number
    * Remove the trailing check digit.
    * If the resulting number is seven digits long, pad to eight digits by adding a leading zero.
* Calculate the check digit
    * To each of the base number’s eight digits a weight factor is assigned. From left to right these are: 3, 2, 7, 6, 5, 4, 3, 2.
    * Sum together the products of the weight factors and their associated digits.
    * Divide the sum by 11. If the remainder is 0 then the calculated check digit is 0.
    * If the remainder is not 0 then subtract the remainder from 11, giving the calculated check digit.
    * If the calculated check digit is in the range 0 to 9, go to step 5.
    * If the calculated check digit is 10, continue with step 4.
* Re-calculate the check digit
    * To each of the base number’s eight digits a secondary weight factor is assigned. From left to right these are: 7, 4, 3, 2, 5, 2, 7, 6.
    * Sum together the products of the weight factors and their associated digits.
    * Divide the sum by 11. If the remainder is 0 then the calculated check digit is 0.
    * If the remainder is not 0 then subtract the remainder from 11, giving the 00 calculated check digit.
    * If the calculated check digit is 10, the IRD number is invalid. 5. Compare the check digit :
    * Compare the calculated check digit to the last digit of the original IRD number.
    * If they match, the IRD number is valid.

## [Changelog](https://github.com/FNZC/nz-bank-account-validator/blob/master/CHANGELOG.md)

## Contributing

Pull requests are welcome.

## [License](https://github.com/FNZC/nz-bank-account-validator/blob/master/LICENSE)

