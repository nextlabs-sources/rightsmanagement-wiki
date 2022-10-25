**Bug**


Usually, when a button is disabled using ng-disabled attribute, the ng-click attribute function can be fired as well. Ideally when a button is disabled the ng-click should not get fired. NOTE: This does not happen for button element, but for button-like elements like: div, a


**Solution**


Refer to this link: [https://baudehlo.com/2014/02/24/angular-js-ng-click-still-fires-when-div-is-ngdisabled/](https://baudehlo.com/2014/02/24/angular-js-ng-click-still-fires-when-div-is-ngdisabled/)  