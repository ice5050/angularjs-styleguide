# แนวทางการเขียน AngularJS

*โดย [@john_papa](//twitter.com/john_papa)*

ถ้าคุณกำลังหาแนวทาง รูปแบบ โครงสร้างการเขียน AngularJS คุณมาถูกทางแล้ว !!!
แนวทางนี้มาจากประสบการณ์โดยตรง [การสอน](http://pluralsight.com/training/Authors/Details/john-papa) รวมถึงการทำงานร่วมกับทีม

>ถ้าชอบก็ไปดูคอร์สการสอน [AngularJS Patterns: Clean Code](http://jpapa.me/ngclean) ของผมที่ Pluralsight ได้นะ

ผมสร้างแนวทางนี้ขึ้นมาเพื่อแสดงให้เห็นว่าผมใช้รูปแบบอะไร และที่สำคัญทำไมผมถึงใช้มัน

## ความยอดเยี่ยมของชุมชน AngularJS
ชุมชน AngularJS เป็นกลุ่มที่เจ๋งมาก เราได้แลกเปลี่ยนประสบการณ์กัน อีกทั้งการต้อนรับอย่างอบอุ่นเหมือนเพื่อนฝูง รวมถึงได้เจอกับผู้เชี่ยวชาญอย่าง Todd Motto ผมได้ทำงานกับหลายคน ซึ่งทำให้เราได้รูปแบบการเขียนที่ทั้งเหมือนและแตกต่างกัน ผมแนะนำให้คุณไปดูแนวทางจาก [Todd's  guidelines](https://github.com/toddmotto/angularjs-styleguide) ด้วยนะ แล้วก็อย่าลืมลองเปรียบเทียบกันหล่ะ

หลาย ๆ รูปแบบการเขียนมาจากการ pair programming ซึ่งหลาย ๆ ครั้งเราก็มีความเห็นไม่ค่อยตรงกัน แต่ก็ได้ [Ward Bell](http://twitter.com/wardbell) ซึ่งเป็นเพื่อนของผมช่วยทำให้แนวทางนี้ได้สำเร็จ

## ดูรูปแบบในตัวอย่างแอปพลิเคชั่น
มันจะดีมากถ้าคุณเข้าไปดู [ตัวอย่างแอปพลิเคชั่น](https://github.com/johnpapa/ng-demos)(ในโฟลเดอร์  modular) ที่ทำตามแนวทางนี้ เพราะว่าแนวทางนี้แค่แสดงถึงรูปแบบอะไร ทำไมถึงใช้รูปแบบนี้ และใช้มันอย่างไร

## สารบัญ

  1. [Single Responsibility](#single-responsibility)
  1. [IIFE](#iife)
  1. [Modules](#modules)
  1. [Controllers](#controllers)
  1. [Services](#services)
  1. [Factories](#factories)
  1. [Data Services](#data-services)
  1. [Directives](#directives)
  1. [Resolving Promises for a Controller](#resolving-promises-for-a-controller)
  1. [Manual Annotating for Dependency Injection](#manual-annotating-for-dependency-injection)
  1. [Minification and Annotation](#minification-and-annotation)
  1. [Exception Handling](#exception-handling)
  1. [Naming](#naming)
  1. [Application Structure LIFT Principle](#application-structure-lift-principle)
  1. [Application Structure](#application-structure)
  1. [Modularity](#modularity)
  1. [Startup Logic](#startup-logic)
  1. [Angular $ Wrapper Services](#angular--wrapper-services)
  1. [Testing](#testing)
  1. [Animations](#animations)
  1. [Comments](#comments)
  1. [JSHint](#js-hint)
  1. [Constants](#constants)
  1. [File Templates and Snippets](#file-templates-and-snippets)
  1. [AngularJS Docs](#angularjs-docs)
  1. [Contributing](#contributing)
  1. [License](#license)

## Single Responsibility

### กฎข้อที่ 1
###### [รูปแบบ [Y001](#style-y001)]

  - ประกาศ 1 component ต่อ 1 ไฟล์

  ตัวอย่างข้างล่างนี้คือการประกาศ `app` module และ dependencies รวมถึงประกาศ controller และ factory ในไฟล์เดียวกัน

  ```javascript
  /* avoid */
  angular
      .module('app', ['ngRoute'])
      .controller('SomeController', SomeController)
      .factory('someFactory', someFactory);

  function SomeController() { }

  function someFactory() { }
  ```

  และข้างล่างนี้คือทุก ๆ component ถูกแยกส่วนออกจากกัน

  ```javascript
  /* recommended */

  // app.module.js
  angular
      .module('app', ['ngRoute']);
  ```

  ```javascript
  /* recommended */

  // someController.js
  angular
      .module('app')
      .controller('SomeController', SomeController);

  function SomeController() { }
  ```

  ```javascript
  /* recommended */

  // someFactory.js
  angular
      .module('app')
      .factory('someFactory', someFactory);

  function someFactory() { }
  ```

**[กลับไปที่สารบัญ](#table-of-contents)**

## IIFE
### JavaScript Closures
###### [รูปแบบ [Y010](#style-y010)]

  - ใส่ AngularJS component ไว้ใน Immediately Invoked Function Expression (IIFE)

  *ทำไม?*: เพราะว่า IIFE จะกำจัดตัวแปรแบบ Global ออกไป ซึ่งป้องกันการประกาศตัวแปรซ้ำกันใน Global scope

  *ทำไม?*: เมื่อโค้ดถูก minified และรวมกันเป็นไฟล์เดียวเพื่อจะนำขึ้น Production จะทำให้ อาจมีการซ้ำกันของตัวแปร และมีตัวแปรใน Global scope มากมาย ซึ่ง IIFE จะช่วยป้องกันปัญหาเหล่านี้

  ```javascript
  /* avoid */
  // logger.js
  angular
      .module('app')
      .factory('logger', logger);

  // logger function is added as a global variable
  function logger() { }

  // storage.js
  angular
      .module('app')
      .factory('storage', storage);

  // storage function is added as a global variable
  function storage() { }
  ```


  ```javascript
  /**
   * recommended
   *
   * no globals are left behind
   */

  // logger.js
  (function() {
      'use strict';

      angular
          .module('app')
          .factory('logger', logger);

      function logger() { }
  })();

  // storage.js
  (function() {
      'use strict';

      angular
          .module('app')
          .factory('storage', storage);

      function storage() { }
  })();
  ```

  - หมายเหตุ: เพื่อความกะทัดรัด ตัวอย่างในแนวทางนี้จะไม่ใช้ IIFE เนื่องจากมันจะยาวนั่นเอง

  - Note: IIFE's prevent test code from reaching private members like regular expressions or helper functions which are often good to unit test directly on their own. However you can test these through accessible members or by exposing them through their own component. For example placing helper functions, regular expressions or constants in their own factory or constant.

**[กลับไปที่สารบัญ](#table-of-contents)**

## Modules

### หลีกเลี่ยงการตั้งชื่อซ้ำกัน
###### [รูปแบบ [Y020](#style-y020)]

  - ใช้ชื่อที่เฉพาะเจาะจงต่อด้วย sub-modules

  *ทำไม?*: ชื่อที่เฉพาะเจาะจงและการตั้งชื่อแบบเป็นลำดับขั้นของ submodule จะช่วยป้องกันการซ้ำกัน เช่น `app` เป็น root module ขณะที่ `app.dashboard` และ `app.users` เป็น submodule ที่ถูกใช้ใน `app` (dependencies)

### การประกาศ Module (หรือ Setters)
###### [รูปแบบ [Y021](#style-y021)]

  - ประกาศ module โดยไม่ต้องมีตัวแปรมารับ

  *ทำไม?*: เนื่องจากเราประกาศ 1 component ต่อ 1 ไฟล์เท่านั้น จึงไม่จำเป็นจะต้องมีตัวแปรมารับ

  ```javascript
  /* avoid */
  var app = angular.module('app', [
      'ngAnimate',
      'ngRoute',
      'app.shared',
      'app.dashboard'
  ]);
  ```

  ```javascript
  /* recommended */
  angular
      .module('app', [
          'ngAnimate',
          'ngRoute',
          'app.shared',
          'app.dashboard'
      ]);
  ```

### Getters
###### [รูปแบบ [Y022](#style-y022)]

  - เวลาที่ใช้ module ให้หลีกเลี่ยงการใช้ตัวแปร โดยที่ให้ใช้การ chaining แทน

  *ทำไม?* : เพราะว่าจะช่วยให้โค้ดอ่านง่ายขึ้นและป้องกันการซ้ำกันของชื่อตัวแปร

  ```javascript
  /* avoid */
  var app = angular.module('app');
  app.controller('SomeController', SomeController);

  function SomeController() { }
  ```

  ```javascript
  /* recommended */
  angular
      .module('app')
      .controller('SomeController', SomeController);

  function SomeController() { }
  ```

### Setting vs Getting
###### [รูปแบบ [Y023](#style-y023)]

  - กำหนดค่าเพียงครั้งเดียวและใช้กับทุก instances

  *ทำไม?*: 1 module ควรถูกสร้างเพียงครั้งเดียว และเมื่อต้องการจะใช้ module นั้น ๆ ก็ให้ใช้ getter

    - ใช้ `angular.module('app', []);` เมื่อต้องการกำหนดค่า module
    - ใช้  `angular.module('app');` เมื่อต้องการ module

### Named vs Anonymous Functions
###### [รูปแบบ [Y024](#style-y024)]

  - ใช้ named functions แทนการส่ง anonymous function ไปเป็น callback.

  *ทำไม?*: เพราะว่าจะช่วยให้โค้ดอ่านง่ายขึ้นและลดจำนวน callback code

  ```javascript
  /* avoid */
  angular
      .module('app')
      .controller('Dashboard', function() { })
      .factory('logger', function() { });
  ```

  ```javascript
  /* recommended */

  // dashboard.js
  angular
      .module('app')
      .controller('Dashboard', Dashboard);

  function Dashboard() { }
  ```

  ```javascript
  // logger.js
  angular
      .module('app')
      .factory('logger', logger);

  function logger() { }
  ```

**[กลับไปที่สารบัญ](#table-of-contents)**

## Controllers

### controllerAs View Syntax
###### [รูปแบบ [Y030](#style-y030)]

  - ใช้ [`controllerAs`](http://www.johnpapa.net/do-you-like-your-angular-controllers-with-or-without-sugar/) แทนการใช้ `controller with $scope`

  *ทำไม?*: Controllers are constructed, "newed" up, and provide a single new instance, and the `controllerAs` syntax is closer to that of a JavaScript constructor than the `classic $scope syntax`.

  *ทำไม?*: เพราะว่าทำให้ต้องใช้ "dotted" กับออปเจ็คใน View (เช่น `customer.name` แทนการใช้ `name`), ซึ่งทำให้มันบ่งบอกความหมายในตัวมันเอง และทำให้อ่านโค้ดได้ง่ายขึ้น อีกทั้งยังป้องกันปัญหาจากการอ้างอึงตัวแปรแบบไม่ใช้ "dotting" อีกด้วย

  *ทำไม?*: ช่วยหลีกเลี่ยงการใช้ `$parent` ใน Views ที่อยู่ใน nested controllers

  ```html
  <!-- avoid -->
  <div ng-controller="Customer">
      {{ name }}
  </div>
  ```

  ```html
  <!-- recommended -->
  <div ng-controller="Customer as customer">
     {{ customer.name }}
  </div>
  ```

### controllerAs Controller Syntax
###### [รูปแบบ [Y031](#style-y031)]

  - ใช้ `controllerAs` แทนการใช้ `controller with $scope`

  - `controllerAs` ใช้ `this` ใน controller ซึ่งก็คือ `$scope`

  *ทำไม?*: `controllerAs` [syntactic sugar](http://en.wikipedia.org/wiki/Syntactic_sugar) มากกว่า `$scope` และยังสามารถใช้ผูกกับ View และเข้าถึง `$scope` methods.

  *ทำไม?*: Helps avoid the temptation of using `$scope` methods inside a controller when it may otherwise be better to avoid them or move them to a factory. Consider using `$scope` in a factory, or if in a controller just when needed. เช่นเมื่อต้องการ publishing และ subscribing events ด้วย [`$emit`](https://docs.angularjs.org/api/ng/type/$rootScope.Scope#$emit), [`$broadcast`](https://docs.angularjs.org/api/ng/type/$rootScope.Scope#$broadcast), หรือ [`$on`](https://docs.angularjs.org/api/ng/type/$rootScope.Scope#$on) ซึ่งควรจะถูกใช้ใน factory และ invoke จาก controller

  ```javascript
  /* avoid */
  function Customer($scope) {
      $scope.name = {};
      $scope.sendMessage = function() { };
  }
  ```

  ```javascript
  /* recommended - but see next section */
  function Customer() {
      this.name = {};
      this.sendMessage = function() { };
  }
  ```

### controllerAs with vm
###### [Style [Y032](#style-y032)]

  - ใช้ตัวแปรแทนที่การใช้ `this` เมื่อใช้ `controllerAs` โดยเลือกชื่อให้เป็นมาตรฐานเช่น `vm` ซึ่งหมายถึง ViewModel

  *ทำไม?*: `this` เป็นตัวแปรที่เปลี่ยนไปตรามบริบทเช่น this ที่อยู่ใน function ใน controller อาจจะไม่ใช่ this ใน controller ก็ได้ ดังนั้นการใช้ตัวแปรแทน `this` จะช่วยป้องกันปัญหาเหล่านี้

  ```javascript
  /* avoid */
  function Customer() {
      this.name = {};
      this.sendMessage = function() { };
  }
  ```

  ```javascript
  /* recommended */
  function Customer() {
      var vm = this;
      vm.name = {};
      vm.sendMessage = function() { };
  }
  ```

  หมายเหตุ: คุณสามารถปิดคำเตือนใน [jshint](http://www.jshint.com/) ด้วยการใส่คอมเมนต์ที่อยู่ข้างล่างนี้เข้าไปก่อนโค้ดบรรทัดนั้น แต่มันก็ไม่จำเป็นเวลาที่ฟังก์ชั่นถูกตั้งชื่อด้วยตัวใหญ่เพราะมันหมายความว่าเป็น constructor function ซึ่งมันก็คือ controller ใน AngularJS

  ```javascript
  /* jshint validthis: true */
  var vm = this;
  ```

  หมายเหตุ: เมื่อสร้าง watches ใน controller ที่ใช้ `controller as` คุณสามารถที่จะ watch `vm.*` member ด้วยรูปแบบข้างล่างนี้ (Create watches with caution as they add more load to the digest cycle.)

  ```html
  <input ng-model="vm.title"/>
  ```

  ```javascript
  function SomeController($scope, $log) {
      var vm = this;
      vm.title = 'Some Title';

      $scope.$watch('vm.title', function(current, original) {
          $log.info('vm.title was %s', original);
          $log.info('vm.title is now %s', current);
      });
  }
  ```

### Bindable Members Up Top
###### [รูปแบบ [Y033](#style-y033)]

  - ใส่ bindable members ไว้ส่วนหัวของ controller โดยเรียงลำดับตามตัวอักษร

    *ทำไม?*: การใส่ bindable members ไว้ส่วนหัวช่วยทำให้อ่านโค้ดได้ง่ายขึ้น และยังแสดงถึง members ที่สามารถใช้ใน View ได้อีกด้วย

    *ทำไม?*: การกำหนดค่าด้วย anonymous functions มันง่ายก็จริง แต่มันทำให้โค้ดอ่านยากขึ้น

  ```javascript
  /* avoid */
  function Sessions() {
      var vm = this;

      vm.gotoSession = function() {
        /* ... */
      };
      vm.refresh = function() {
        /* ... */
      };
      vm.search = function() {
        /* ... */
      };
      vm.sessions = [];
      vm.title = 'Sessions';
  ```

  ```javascript
  /* recommended */
  function Sessions() {
      var vm = this;

      vm.gotoSession = gotoSession;
      vm.refresh = refresh;
      vm.search = search;
      vm.sessions = [];
      vm.title = 'Sessions';

      ////////////

      function gotoSession() {
        /* */
      }

      function refresh() {
        /* */
      }

      function search() {
        /* */
      }
  ```

    ![Controller Using "Above the Fold"](https://raw.githubusercontent.com/johnpapa/angularjs-styleguide/master/assets/above-the-fold-1.png)

  หมายเหตุ: ถ้าฟังก์ชั่นยาวแค่บรรทัดเดียวก็สามารถนำมาไว้ด้านบนได้ (ถ้าไม่ทำให้โค้ดอ่านยากขึ้น)

  ```javascript
  /* avoid */
  function Sessions(data) {
      var vm = this;

      vm.gotoSession = gotoSession;
      vm.refresh = function() {
          /**
           * lines
           * of
           * code
           * affects
           * readability
           */
      };
      vm.search = search;
      vm.sessions = [];
      vm.title = 'Sessions';
  ```

  ```javascript
  /* recommended */
  function Sessions(dataservice) {
      var vm = this;

      vm.gotoSession = gotoSession;
      vm.refresh = dataservice.refresh; // 1 liner is OK
      vm.search = search;
      vm.sessions = [];
      vm.title = 'Sessions';
  ```

### Function Declarations to Hide Implementation Details
###### [รูปแบบ [Y034](#style-y034)]

  - ใช้การประกาศฟังก์ชั่นเพื่อซ่อนเนื้อหาภายในฟังก์ชั่น และให้ bindable members อยู่ส่วนหัว โดยเมื่อต้องการ bind ฟังก์ชั่นใน controller ก็แค่ทำการอ้างถึงฟังก์ชั่นที่ประกาศไว้ส่วนนี้จะเหมือนกับส่วน Bindable Members Up Top ซึ่งอ่านเพิ่มเติมได้[ที่นี่](http://www.johnpapa.net/angular-function-declarations-function-expressions-and-readable-code).

    *ทำไม?*: การใส่ bindable members ไว้ส่วนหัวช่วยทำให้อ่านโค้ดได้ง่ายขึ้น และยังแสดงถึง members ที่สามารถใช้ใน View ได้อีกด้วย (เหมือนข้างบน)

    *ทำไม?*: การประกาศฟังก์ชั่นไว้ส่วนท้ายจะทำให้ลดความซับซ้อนในไฟล์นั้น ๆ ได้ ซึ่งทำให้เห็น members ได้ทั้งหมดในส่วนหัวของไฟล์

    *ทำไม?*: การประกาศฟังก์ชั่นทำให้ไม่ต้องกังวลว่าต้องประกาศข้างบนก่อนเรียกใช้ ไม่เหมือนกับ Function Expression

    *ทำไม?*: You never have to worry with function declarations that moving `var a` before `var b` will break your code because `a` depends on `b`.

    *ทำไม?*: Function Expression จะทำให้มีปัญหาเรื่องลำดับการประกาศ

  ```javascript
  /**
   * avoid
   * Using function expressions.
   */
  function Avengers(dataservice, logger) {
      var vm = this;
      vm.avengers = [];
      vm.title = 'Avengers';

      var activate = function() {
          return getAvengers().then(function() {
              logger.info('Activated Avengers View');
          });
      }

      var getAvengers = function() {
          return dataservice.getAvengers().then(function(data) {
              vm.avengers = data;
              return vm.avengers;
          });
      }

      vm.getAvengers = getAvengers;

      activate();
  }
  ```

  Notice that the important stuff is scattered in the preceding example. In the example below, notice that the important stuff is up top. For example, the members bound to the controller such as `vm.avengers` and `vm.title`. The implementation details are down below. This is just easier to read.

  ```javascript
  /*
   * recommend
   * Using function declarations
   * and bindable members up top.
   */
  function Avengers(dataservice, logger) {
      var vm = this;
      vm.avengers = [];
      vm.getAvengers = getAvengers;
      vm.title = 'Avengers';

      activate();

      function activate() {
          return getAvengers().then(function() {
              logger.info('Activated Avengers View');
          });
      }

      function getAvengers() {
          return dataservice.getAvengers().then(function(data) {
              vm.avengers = data;
              return vm.avengers;
          });
      }
  }
  ```

### Defer Controller Logic
###### [รูปแบบ [Y035](#style-y035)]

  - ย้าย logic จาก controller โดยการแยกส่วนการทำงานไปไว้ใน services และ factories.

    *ทำไม?*: Logic อาจจะถูกใช้บ่อย ๆ ใน controller ต่าง ๆ โดยการเรียกผ่านฟังก์ชั่น

    *ทำไม?*: Logic ที่อยู่ใน service สามารถแยกส่วนการทดสอบได้ โดยที่การเรียกใช้ผ่านฟังก์ชั่นใน controller ก็สามารถที่จะ mock ได้ง่ายด้วย

    *ทำไม?*: กำจัด dependencies และซ่อนการทำงานจาก controller

  ```javascript

  /* avoid */
  function Order($http, $q, config, userInfo) {
      var vm = this;
      vm.checkCredit = checkCredit;
      vm.isCreditOk;
      vm.total = 0;

      function checkCredit() {
      var settings = {};
          // Get the credit service base URL from config
          // Set credit service required headers
          // Prepare URL query string or data object with request data
          // Add user-identifying info so service gets the right credit limit for this user.
          // Use JSONP for this browser if it doesn't support CORS
          return $http.get(settings)
              .then(function(data) {
               // Unpack JSON data in the response object
                 // to find maxRemainingAmount
                 vm.isCreditOk = vm.total <= maxRemainingAmount
              })
              .catch(function(error) {
                 // Interpret error
                 // Cope w/ timeout? retry? try alternate service?
                 // Re-reject with appropriate error for a user to see
              });
      };
  }
  ```

  ```javascript

  /* recommended */
  function Order(creditService) {
      var vm = this;
      vm.checkCredit = checkCredit;
      vm.isCreditOk;
      vm.total = 0;

      function checkCredit() {
         return creditService.isOrderTotalOk(vm.total)
      .then(function(isOk) { vm.isCreditOk = isOk; })
            .catch(showServiceError);
      };
  }
  ```

### Keep Controllers Focused
###### [รูปแบบ [Y037](#style-y037)]

  - สร้าง 1 controller สำหรับ 1 view และอย่าใช้ซ้ำกับ view อื่น ๆ อีก และทำการย้าย logic ที่สามารถใช้ซ้ำได้ไปไว้ใน factory และทำให้ controller โฟกัสแค่ view นั้น ๆ

    *ทำไม?*: การใช้ controller กับหลาย ๆ view ทำให้มันพังได้ง่าย and good end to end (e2e) test coverage is required to ensure stability across large applications.

### Assigning Controllers
###### [รูปแบบ [Y038](#style-y038)]

  - When a controller must be paired with a view and either component may be re-used by other controllers or views, define controllers along with their routes.

    Note: If a View is loaded via another means besides a route, then use the `ng-controller="Avengers as vm"` syntax.

    *Why?*: Pairing the controller in the route allows different routes to invoke different pairs of controllers and views. When controllers are assigned in the view using [`ng-controller`](https://docs.angularjs.org/api/ng/directive/ngController), that view is always associated with the same controller.

 ```javascript
  /* avoid - when using with a route and dynamic pairing is desired */

  // route-config.js
  angular
      .module('app')
      .config(config);

  function config($routeProvider) {
      $routeProvider
          .when('/avengers', {
            templateUrl: 'avengers.html'
          });
  }
  ```

  ```html
  <!-- avengers.html -->
  <div ng-controller="Avengers as vm">
  </div>
  ```

  ```javascript
  /* recommended */

  // route-config.js
  angular
      .module('app')
      .config(config);

  function config($routeProvider) {
      $routeProvider
          .when('/avengers', {
              templateUrl: 'avengers.html',
              controller: 'Avengers',
              controllerAs: 'vm'
          });
  }
  ```

  ```html
  <!-- avengers.html -->
  <div>
  </div>
  ```

**[กลับไปที่สารบัญ](#table-of-contents)**

## Services

### Singletons
###### [รูปแบบ [Y040](#style-y040)]

  - Services จะถูกใช้โดยการ `new` ดังนั้นใช้ `this` สำหรับ public methods และตัวแปรต่าง ๆ ด้วยความที่ Service มันคล้ายกับ factory มาก ๆ ดังนั้นให้ใช้ factory แทน เพื่อความเป็นมาตรฐาน

    หมายเหตุ: [ทุก ๆ  Service เป็น Singletons](https://docs.angularjs.org/guide/services) This means that there is only one instance of a given service per injector.

  ```javascript
  // service
  angular
      .module('app')
      .service('logger', logger);

  function logger() {
    this.logError = function(msg) {
      /* */
    };
  }
  ```

  ```javascript
  // factory
  angular
      .module('app')
      .factory('logger', logger);

  function logger() {
      return {
          logError: function(msg) {
            /* */
          }
     };
  }
  ```

**[กลับไปที่สารบัญ](#table-of-contents)**

## Factories

### Single Responsibility
###### [รูปแบบ [Y050](#style-y050)]

  - Factories ควรจะมี [single responsibility](http://en.wikipedia.org/wiki/Single_responsibility_principle) that is encapsulated by its context. Once a factory begins to exceed that singular purpose, a new factory should be created.

### Singletons
###### [รูปแบบ [Y051](#style-y051)]

  - Factories เป็น singletons และคืนค่าออปเจ็คที่ประกอบด้วย members of the service.

    Note: [ทุก ๆ Services เป็น Singletons](https://docs.angularjs.org/guide/services).

### Accessible Members Up Top
###### [รูปแบบ [Y052](#style-y052)]

  - ใส่ callable members ไว้ส่วนหัวของ factory ซึ่งใช้เทคนิคจาก [Revealing Module Pattern](http://addyosmani.com/resources/essentialjsdesignpatterns/book/#revealingmodulepatternjavascript).

    *ทำไม?*: การใส่ callable members ไว้ส่วนหัวนั้นช่วยให้อ่านโค้ดได้ง่ายขึ้น และยังแสดงถึง members ที่สามารถถูกเรียกใช้ได้อีกด้วย

    *ทำไม?*: การประกาศ functions โดยตรงมันง่ายก็จริง แต่มันทำให้โค้ดอ่านยากขึ้น

  ```javascript
  /* avoid */
  function dataService() {
    var someValue = '';
    function save() {
      /* */
    };
    function validate() {
      /* */
    };

    return {
        save: save,
        someValue: someValue,
        validate: validate
    };
  }
  ```

  ```javascript
  /* recommended */
  function dataService() {
      var someValue = '';
      var service = {
          save: save,
          someValue: someValue,
          validate: validate
      };
      return service;

      ////////////

      function save() {
          /* */
      };

      function validate() {
          /* */
      };
  }
  ```

  This way bindings are mirrored across the host object, primitive values cannot update alone using the revealing module pattern

    ![Factories Using "Above the Fold"](https://raw.githubusercontent.com/johnpapa/angularjs-styleguide/master/assets/above-the-fold-2.png)

### Function Declarations to Hide Implementation Details
###### [รูปแบบ [Y053](#style-y053)]

  - ใช้การประกาศฟังก์ชั่นเพื่อซ่อนเนื้อหาภายในฟังก์ชั่นนั้น ๆ และนำไปไว้ส่วนท้ายของไฟล์ และนำ accessible members ไว้ส่วนหัวของไฟล์ ซึ่งรายละเอียดเพิ่มเติมสามารถดูได้[ที่นี่](http://www.johnpapa.net/angular-function-declarations-function-expressions-and-readable-code).

    *ทำไม?*: การใส่ accessible members ไว้ส่วนหัวช่วยทำให้อ่านโค้ดได้ง่ายขึ้น และยังแสดงถึงฟังก์ชั่นใน factory ที่ถูกเรียกใช้จากภายนอกได้

    *ทำไม?*: การใส่เนื้อหาฟังก์ชั่นไว้ส่วนท้ายจะทำให้ลดความซับซ้อนในไฟล์นั้น ๆ ได้

    *ทำไม?*: การประกาศฟังก์ชั่นทำให้ไม่ต้องกังวลว่าต้องประกาศข้างบนก่อนเรียกใช้ ไม่เหมือนกับ Function Expression

    *ทำไม?*: You never have to worry with function declarations that moving `var a` before `var b` will break your code because `a` depends on `b`.

    *ทำไม?*: Function Expression จะทำให้มีปัญหาเรื่องลำดับการประกาศ

  ```javascript
  /**
   * avoid
   * Using function expressions
   */
   function dataservice($http, $location, $q, exception, logger) {
      var isPrimed = false;
      var primePromise;

      var getAvengers = function() {
         // implementation details go here
      };

      var getAvengerCount = function() {
          // implementation details go here
      };

      var getAvengersCast = function() {
         // implementation details go here
      };

      var prime = function() {
         // implementation details go here
      };

      var ready = function(nextPromises) {
          // implementation details go here
      };

      var service = {
          getAvengersCast: getAvengersCast,
          getAvengerCount: getAvengerCount,
          getAvengers: getAvengers,
          ready: ready
      };

      return service;
  }
  ```

  ```javascript
  /**
   * recommended
   * Using function declarations
   * and accessible members up top.
   */
  function dataservice($http, $location, $q, exception, logger) {
      var isPrimed = false;
      var primePromise;

      var service = {
          getAvengersCast: getAvengersCast,
          getAvengerCount: getAvengerCount,
          getAvengers: getAvengers,
          ready: ready
      };

      return service;

      ////////////

      function getAvengers() {
         // implementation details go here
      }

      function getAvengerCount() {
          // implementation details go here
      }

      function getAvengersCast() {
         // implementation details go here
      }

      function prime() {
          // implementation details go here
      }

      function ready(nextPromises) {
          // implementation details go here
      }
  }
  ```

**[กลับไปที่สารบัญ](#table-of-contents)**

## Data Services

### Separate Data Calls
###### [รูปแบบ [Y060](#style-y060)]

  - ย้าย logic สำหรับการจัดการข้อมูลไปไว้ใน factory และทำให้ data services รับผิดชอบส่วนการเรียก XHR, local storage, stashing in memory หรือการกระทำกับข้อมูลใด ๆ ก็ตาม

    *ทำไม?*: หน้าที่ของ controller คือการแสดงผลและการเก็บข้อมูลจาก View ดังนั้น controller ไม่ควรสนใจวิธีการจัดการข้อมูล สิ่งที่ controller ทำควรเป็นแค่การเรียกใช้เท่านั้น ซึ่งจะช่วยให้ controller โฟกัสเพียงแค่ View

    *ทำไม?*: เพราะทำให้การทดสอบซึ่งไม่ว่าจะจำลองหรือใช้ข้อมูลจริงเป็นไปได้ง่ายขึ้น

    *ทำไม?*: Data service implementation may have very specific code to handle the data repository. This may include headers, how to talk to the data, or other services such as $http. Separating the logic into a data service encapsulates this logic in a single place hiding the implementation from the outside consumers (perhaps a controller), also making it easier to change the implementation.

  ```javascript
  /* recommended */

  // dataservice factory
  angular
      .module('app.core')
      .factory('dataservice', dataservice);

  dataservice.$inject = ['$http', 'logger'];

  function dataservice($http, logger) {
      return {
          getAvengers: getAvengers
      };

      function getAvengers() {
          return $http.get('/api/maa')
              .then(getAvengersComplete)
              .catch(getAvengersFailed);

          function getAvengersComplete(response) {
              return response.data.results;
          }

          function getAvengersFailed(error) {
              logger.error('XHR Failed for getAvengers.' + error.data);
          }
      }
  }
  ```

  ```javascript
  /* recommended */

  // controller calling the dataservice factory
  angular
      .module('app.avengers')
      .controller('Avengers', Avengers);

  Avengers.$inject = ['dataservice', 'logger'];

  function Avengers(dataservice, logger) {
      var vm = this;
      vm.avengers = [];

      activate();

      function activate() {
          return getAvengers().then(function() {
              logger.info('Activated Avengers View');
          });
      }

      function getAvengers() {
          return dataservice.getAvengers()
              .then(function(data) {
                  vm.avengers = data;
                  return vm.avengers;
              });
      }
  }
  ```

### Return a Promise from Data Calls
###### [รูปแบบ [Y061](#style-y061)]

  - ในการเรียก data service ที่ทำการคืนค่า promise ให้ทำการคืนค่า promise ต่อไปด้วย

    *ทำไม?*: เพราะจะทำให้สามารถที่จะส่งต่อ promises ไปได้เรื่อย ๆ

  ```javascript
  /* recommended */

  activate();

  function activate() {
      /**
       * Step 1
       * Ask the getAvengers function for the
       * avenger data and wait for the promise
       */
      return getAvengers().then(function() {
          /**
           * Step 4
           * Perform an action on resolve of final promise
           */
          logger.info('Activated Avengers View');
      });
  }

  function getAvengers() {
        /**
         * Step 2
         * Ask the data service for the data and wait
         * for the promise
         */
        return dataservice.getAvengers()
            .then(function(data) {
                /**
                 * Step 3
                 * set the data and resolve the promise
                 */
                vm.avengers = data;
                return vm.avengers;
        });
  }
  ```

    **[กลับไปที่สารบัญ](#table-of-contents)**

## Directives
### Limit 1 Per File
###### [รูปแบบ [Y070](#style-y070)]

  - สร้าง 1 directive ต่อ 1 ไฟล์เท่านั้น

    *ทำไม?*: ถึงแม้ว่าการรวมหลาย ๆ directive ในไฟล์เดียวจะง่ายก็จริง แต่มันทำให้ยากต่อการแชร์ directive กันระหว่าง app หรือ module

    *ทำไม?*: เนื่องจากง่ายต่อการจัดการ

  ```javascript
  /* avoid */
  /* directives.js */

  angular
      .module('app.widgets')

      /* order directive that is specific to the order module */
      .directive('orderCalendarRange', orderCalendarRange)

      /* sales directive that can be used anywhere across the sales app */
      .directive('salesCustomerInfo', salesCustomerInfo)

      /* spinner directive that can be used anywhere across apps */
      .directive('sharedSpinner', sharedSpinner);


  function orderCalendarRange() {
      /* implementation details */
  }

  function salesCustomerInfo() {
      /* implementation details */
  }

  function sharedSpinner() {
      /* implementation details */
  }
  ```

  ```javascript
  /* recommended */
  /* calendarRange.directive.js */

  /**
   * @desc order directive that is specific to the order module at a company named Acme
   * @example <div acme-order-calendar-range></div>
   */
  angular
      .module('sales.order')
      .directive('acmeOrderCalendarRange', orderCalendarRange);

  function orderCalendarRange() {
      /* implementation details */
  }
  ```

  ```javascript
  /* recommended */
  /* customerInfo.directive.js */

  /**
   * @desc spinner directive that can be used anywhere across the sales app at a company named Acme
   * @example <div acme-sales-customer-info></div>
   */
  angular
      .module('sales.widgets')
      .directive('acmeSalesCustomerInfo', salesCustomerInfo);

  function salesCustomerInfo() {
      /* implementation details */
  }
  ```

  ```javascript
  /* recommended */
  /* spinner.directive.js */

  /**
   * @desc spinner directive that can be used anywhere across apps at a company named Acme
   * @example <div acme-shared-spinner></div>
   */
  angular
      .module('shared.widgets')
      .directive('acmeSharedSpinner', sharedSpinner);

  function sharedSpinner() {
      /* implementation details */
  }
  ```

    หมายเหตุ: มีหลายวิธีที่ใช้ในการตั้งชื่อ ซึ่งให้เลือกชื่อที่ชัดเจน สามารถดูตัวอย่างได้ข้างล่าง แต่อย่าลืมดูหัวข้อการตั้งชื่อด้วยหล่ะ

### Manipulate DOM in a Directive
###### [รูปแบบ [Y072](#style-y072)]

  - ใช้ directive จัดการกับ DOM แต่ถ้าสามารถใช้วิธีอื่น ๆ เช่น CSS, [animation services](https://docs.angularjs.org/api/ngAnimate), Angular templating, [`ngShow`](https://docs.angularjs.org/api/ng/directive/ngShow) หรือ [`ngHide`](https://docs.angularjs.org/api/ng/directive/ngHide) ได้ ให้ใช้วิธีเหล่านี้แทน

    *ทำไม?*: เนื่องจากการจัดการกับ DOM จะทำให้ทดสอบได้ยาก และมันมีวิธีอื่น ๆ ที่สามารถใช้แทนกันได้ เช่น CSS, animations, templates

### Provide a Unique Directive Prefix
###### [รูปแบบ [Y073](#style-y073)]

  - ให้ใช้ชื่อที่สั้น เฉพาะเจาะจง และบ่งบอกความหมายไว้หน้า directive เช่น `acmeSalesCustomerInfo` โดยการใช้ใน html คือ `acme-sales-customer-info`

    *ทำไม?*: คำนำหน้าสามารถบ่งบอกความหมายได้ เช่น คำนำหน้า `cc-` หมายถึง directive ที่เป็นส่วนหนึ่งของแอป CodeCamper หรือ `acme-` หมายถึง directive สำหรับบริษัท Acme

    หมายเหตุ: ให้หลีกเลี่ยงการใช้ `ng-` เป็นคำนำหน้าเนื่องจากเป็นคำสงวนไว้สำหรับ AngularJS directives หรือ `ion-` ที่ใช้กับ [Ionic Framework](http://ionicframework.com/) เป็นต้น

### Restrict to Elements and Attributes
###### [รูปแบบ [Y074](#style-y074)]

  - การสร้าง directive จะใช้เพียง `E` (custom element) และ `A` (custom attribute) เท่านั้น ซึ่งปกติจะใช้ `EA` ซึ่ง `E` หมายถึง standalone element และ `A` ก็คือใช้กับ DOM ที่มีอยู่แล้ว

    *ทำไม?*: ก็มันเข้าท่า

    *ทำไม?*: While we can allow the directive to be used as a class, if the directive is truly acting as an element it makes more sense as an element or at least as an attribute.

    หมายเหตุ: EA เป็นค่าเริ่มต้นใน AngularJS 1.3 ขึ้นไป

  ```html
  <!-- avoid -->
  <div class="my-calendar-range"></div>
  ```

  ```javascript
  /* avoid */
  angular
      .module('app.widgets')
      .directive('myCalendarRange', myCalendarRange);

  function myCalendarRange() {
      var directive = {
          link: link,
          templateUrl: '/template/is/located/here.html',
          restrict: 'C'
      };
      return directive;

      function link(scope, element, attrs) {
        /* */
      }
  }
  ```

  ```html
  <!-- recommended -->
  <my-calendar-range></my-calendar-range>
  <div my-calendar-range></div>
  ```

  ```javascript
  /* recommended */
  angular
      .module('app.widgets')
      .directive('myCalendarRange', myCalendarRange);

  function myCalendarRange() {
      var directive = {
          link: link,
          templateUrl: '/template/is/located/here.html',
          restrict: 'EA'
      };
      return directive;

      function link(scope, element, attrs) {
        /* */
      }
  }
  ```

### Directives and ControllerAs
###### [รูปแบบ [Y075](#style-y075)]

  - ใช้ `controller as` กับ directive เพื่อความเป็นมาตรฐาน

    *ทำไม?*: ก็มันเข้าท่าแถมมันก็ไม่ได้ยากอะไรด้วย

    หมายเหตุ: directive ข้างล่างนี้จะแสดงถึงวิธีที่สามารถใช้ scope และ controller ด้วย controllerAs ซึ่งผมไว้ในไฟล์เดียวกันเพราะต้องการให้มันอยู่ที่เดียวกัน (ไม่อยากแยกในตัวอย่าง)

    หมายเหตุ: เกี่ยวกับ dependency injection ดูเพิ่มเติมได้ที่ [Manually Identify Dependencies](#manual-annotating-for-dependency-injection).

    หมายเหตุ: Note that the directive's controller is outside the directive's closure. This style eliminates issues where the injection gets created as unreachable code after a `return`.

  ```html
  <div my-example max="77"></div>
  ```

  ```javascript
  angular
      .module('app')
      .directive('myExample', myExample);

  function myExample() {
      var directive = {
          restrict: 'EA',
          templateUrl: 'app/feature/example.directive.html',
          scope: {
              max: '='
          },
          link: linkFunc,
          controller : ExampleController,
          controllerAs: 'vm'
      };

      return directive;

      function linkFunc(scope, el, attr, ctrl) {
          console.log('LINK: scope.max = %i', scope.max);
          console.log('LINK: scope.vm.min = %i', scope.vm.min);
          console.log('LINK: scope.vm.max = %i', scope.vm.max);
      }
  }

  ExampleController.$inject = ['$scope'];

  function ExampleController($scope) {
      // Injecting $scope just for comparison
      var vm = this;

      vm.min = 3;
      vm.max = $scope.max;
      console.log('CTRL: $scope.max = %i', $scope.max);
      console.log('CTRL: vm.min = %i', vm.min);
      console.log('CTRL: vm.max = %i', vm.max);
  }
  ```

  ```html
  /* example.directive.html */
  <div>hello world</div>
  <div>max={{vm.max}}<input ng-model="vm.max"/></div>
  <div>min={{vm.min}}<input ng-model="vm.min"/></div>
  ```

**[กลับไปที่สารบัญ](#table-of-contents)**

## Resolving Promises for a Controller

### Controller Activation Promises
###### [รูปแบบ [Y080](#style-y080)]

  - Resolve start-up logic for a controller in an `activate` function.

    *ทำไม?*: การใส่ logic เริ่มต้นไว้ในที่ ๆ เป็นมาตรฐานช่วยให้สามารถหาหรือแก้ไขได้ง่ายขึ้น

    หมายเหตุ: If you need to conditionally cancel the route before you start use the controller, use a route resolve instead.

  ```javascript
  /* avoid */
  function Avengers(dataservice) {
      var vm = this;
      vm.avengers = [];
      vm.title = 'Avengers';

      dataservice.getAvengers().then(function(data) {
          vm.avengers = data;
          return vm.avengers;
      });
  }
  ```

  ```javascript
  /* recommended */
  function Avengers(dataservice) {
      var vm = this;
      vm.avengers = [];
      vm.title = 'Avengers';

      activate();

      ////////////

      function activate() {
          return dataservice.getAvengers().then(function(data) {
              vm.avengers = data;
              return vm.avengers;
          });
      }
  }
  ```

### Route Resolve Promises
###### [รูปแบบ [Y081](#style-y081)]

  - When a controller depends on a promise to be resolved, resolve those dependencies in the `$routeProvider` before the controller logic is executed. If you need to conditionally cancel a route before the controller is activated, use a route resolver.

    *ทำไม?*: บางครั้ง controller ก็ต้องการข้อมูลก่อนที่มันจะถูกโหลด ซึ่งข้อมูลนั้นอาจจะมาจาก promise โดยเรียกผ่าน factory หรือ [$http](https://docs.angularjs.org/api/ng/service/$http) โดยการใช้ [route resolve](https://docs.angularjs.org/api/ngRoute/provider/$routeProvider) จะทำให้สามารถรอ resolve promise ก่อนที่ controller จะถูกโหลดได้

  ```javascript
  /* avoid */
  angular
      .module('app')
      .controller('Avengers', Avengers);

  function Avengers(movieService) {
      var vm = this;
      // unresolved
      vm.movies;
      // resolved asynchronously
      movieService.getMovies().then(function(response) {
          vm.movies = response.movies;
      });
  }
  ```

  ```javascript
  /* better */

  // route-config.js
  angular
      .module('app')
      .config(config);

  function config($routeProvider) {
      $routeProvider
          .when('/avengers', {
              templateUrl: 'avengers.html',
              controller: 'Avengers',
              controllerAs: 'vm',
              resolve: {
                  moviesPrepService: function(movieService) {
                      return movieService.getMovies();
                  }
              }
          });
  }

  // avengers.js
  angular
      .module('app')
      .controller('Avengers', Avengers);

  Avengers.$inject = ['moviesPrepService'];
  function Avengers(moviesPrepService) {
        var vm = this;
        vm.movies = moviesPrepService.movies;
  }
  ```

    หมายเหตุ: โค้ดตัวอย่างตรงส่วน dependency `movieService` ไม่ปลอดภัยเวลาที่ถูก minification(ลดขนาดไฟล์) ซึ่งวิธีที่ทำให้ปลอดภัยนั้นอ่านได้ที่ส่วน [dependency injection](#manual-annotating-for-dependency-injection) และ [minification and annotation](#minification-and-annotation).

**[กลับไปที่สารบัญ](#table-of-contents)**

## Manual Annotating for Dependency Injection

### UnSafe from Minification
###### [รูปแบบ [Y090](#style-y090)]

  - หลีกเลี่ยงการประกาศ dependencies แบบไม่ปลอดภัย

    *ทำไม?*: เนื่องจากเวลาที่ลดขนาดไฟล์นั้น parameters จะถูกเปลี่ยนชื่อเป็นตัวแปรย่อ ๆ เช่น `common` และ `dataservice` อาจจะกลายเป็น `a` และ `b` และ AngularJS จะหามันไม่เจอ

    ```javascript
    /* avoid - not minification-safe*/
    angular
        .module('app')
        .controller('Dashboard', Dashboard);

    function Dashboard(common, dataservice) {
    }
    ```

    โค้ดนี้อาจจะทำให้เกิด runtime errors หลังจากที่ถูกลดขนาดไฟล์ ซึ่งโค้ดข้างล่างนี้เป็นตัวอย่างหลังจากลดขนาดไฟล์

    ```javascript
    /* avoid - not minification-safe*/
    angular.module('app').controller('Dashboard', d);function d(a, b) { }
    ```

### Manually Identify Dependencies
###### [รูปแบบ [Y091](#style-y091)]

  - ใช้ `$inject` ในการบอกถึง dependencies โดยตรง

    *ทำไม?*: เนื่องจากวิธีนี้ถูกใช้โดย [`ng-annotate`](https://github.com/olov/ng-annotate) ซึ่งแนะนำให้ใช้เพื่อป้องกันปัญหาหลังจากการลดขนาดไฟล์ ถ้า `ng-annotate` ตรวจพบเจอ injection มันจะไม่สร้างขึ้นมาซ้ำอีก

    *ทำไม?*: วิธีนี้จะช่วยป้องกัน dependencies ของคุณจากปัญหาการลดขนาดไฟล์

    *ทำไม?*: วิธีนี้จะหลีกเลี่ยงการประกาศ dependencies แบบยาว ๆ ในบรรทัดที่ประกาศ controller ซึ่งบางครั้งทำให้อ่านยากขึ้น

    ```javascript
    /* avoid */
    angular
        .module('app')
        .controller('Dashboard',
            ['$location', '$routeParams', 'common', 'dataservice',
                function Dashboard($location, $routeParams, common, dataservice) {}
            ]);
    ```

    ```javascript
    /* avoid */
    angular
      .module('app')
      .controller('Dashboard',
         ['$location', '$routeParams', 'common', 'dataservice', Dashboard]);

    function Dashboard($location, $routeParams, common, dataservice) {
    }
    ```

    ```javascript
    /* recommended */
    angular
        .module('app')
        .controller('Dashboard', Dashboard);

    Dashboard.$inject = ['$location', '$routeParams', 'common', 'dataservice'];

    function Dashboard($location, $routeParams, common, dataservice) {
    }
    ```

    หมายเหตุ: ให้ระวังเมื่อฟังก์ชั่นอยู่ข้างล่างคำสั่ง return มันจะทำให้ไม่พบ $inject (อาจเกิดขึ้นได้ใน directive) ซึ่งสามารถแก้ไขได้ด้วยการย้าย $inject ไปไว้ข้างบนคำสั่ง return หรือการใช้ array injection

    หมายเหตุ: [`ng-annotate 0.10.0`](https://github.com/olov/ng-annotate) introduced a feature where it moves the `$inject` to where it is reachable.

    ```javascript
    // inside a directive definition
    function outer() {
        return {
            controller: DashboardPanel,
        };

        DashboardPanel.$inject = ['logger']; // Unreachable
        function DashboardPanel(logger) {
        }
    }
    ```

    ```javascript
    // inside a directive definition
    function outer() {
        DashboardPanel.$inject = ['logger']; // reachable
        return {
            controller: DashboardPanel,
        };

        function DashboardPanel(logger) {
        }
    }
    ```

### Manually Identify Route Resolver Dependencies
###### [รูปแบบ [Y092](#style-y092)]

  - ใช้ $inject ในการบอกถึง dependencies โดยตรง

    *ทำไม?*: วิธีนี้จะกำจัดการใช้ anonymous function สำหรับ route resolver ออกไป ซึ่งช่วยให้โค้ดอ่านได้ง่ายขึ้น

    *ทำไม?*: คำสั่ง `$inject` จะช่วยให้ปลอดภัยจากการลดขนาดไฟล์

    ```javascript
    /* recommended */
    function config($routeProvider) {
        $routeProvider
            .when('/avengers', {
                templateUrl: 'avengers.html',
                controller: 'Avengers',
                controllerAs: 'vm',
                resolve: {
                    moviesPrepService: moviePrepService
                }
            });
    }

    moviePrepService.$inject =  ['movieService'];
    function moviePrepService(movieService) {
        return movieService.getMovies();
    }
    ```

**[กลับไปที่สารบัญ](#table-of-contents)**

## Minification and Annotation

### ng-annotate
###### [รูปแบบ [Y100](#style-y100)]

  - ใช้ [ng-annotate](//github.com/olov/ng-annotate) กับ [Gulp](http://gulpjs.com) or [Grunt](http://gruntjs.com) และทำการคอมเมนต์ฟังก์ชั่นที่ต้องการ automated dependency injection ด้วย `/** @ngInject */`

    *ทำไม?*: วิธีนี้จะช่วยป้องกันปัญหาจากการลดขนาดไฟล์ในกรณีที่ใช้การประกาศแบบไม่ปลอดภัย

    *ทำไม?*: [`ng-min`](https://github.com/btford/ngmin) is deprecated

    >ผมชอบ Gulp มากกว่านะ ผมรู้สึกว่ามันเขียนง่าย และอ่านง่าย

    โค้ดข้างล่างนี้เป็นการประกาศ dependencies แบบไม่ปลอดภัย(แต่ใช้ ng-annotate)

    ```javascript
    angular
        .module('app')
        .controller('Avengers', Avengers);

    /* @ngInject */
    function Avengers(storageService, avengerService) {
        var vm = this;
        vm.heroSearch = '';
        vm.storeHero = storeHero;

        function storeHero(){
            var hero = avengerService.find(vm.heroSearch);
            storageService.save(hero.name, hero);
        }
    }
    ```

    เมื่อโค้ดข้างบนผ่าน ng-annotate มันจะสร้าง `$inject`  ให้ ซึ่งกลายมาเป็นการประกาศแบบปลอดภัย

    ```javascript
    angular
        .module('app')
        .controller('Avengers', Avengers);

    /* @ngInject */
    function Avengers(storageService, avengerService) {
        var vm = this;
        vm.heroSearch = '';
        vm.storeHero = storeHero;

        function storeHero(){
            var hero = avengerService.find(vm.heroSearch);
            storageService.save(hero.name, hero);
        }
    }

    Avengers.$inject = ['storageService', 'avengerService'];
    ```

    หมายเหตุ: ถ้า `ng-annotate` ตรวจพบ injection มันจะไม่สร้างขึ้นมาซ้ำอีก

    หมายเหตุ: When using a route resolver you can prefix the resolver's function with `/* @ngInject */` and it will produce properly annotated code, keeping any injected dependencies minification safe.

    ```javascript
    // Using @ngInject annotations
    function config($routeProvider) {
        $routeProvider
            .when('/avengers', {
                templateUrl: 'avengers.html',
                controller: 'Avengers',
                controllerAs: 'vm',
                resolve: { /* @ngInject */
                    moviesPrepService: function(movieService) {
                        return movieService.getMovies();
                    }
                }
            });
    }
    ```

    > หมายเหตุ: Starting from AngularJS 1.3 use the [`ngApp`](https://docs.angularjs.org/api/ng/directive/ngApp) directive's `ngStrictDi` parameter. When present the injector will be created in "strict-di" mode causing the application to fail to invoke functions which do not use explicit function annotation (these may not be minification safe). Debugging info will be logged to the console to help track down the offending code.
    `<body ng-app="APP" ng-strict-di>`

### Use Gulp or Grunt for ng-annotate
###### [รูปแบบ [Y101](#style-y101)]

  - ใช้ [gulp-ng-annotate](https://www.npmjs.org/package/gulp-ng-annotate) หรือ [grunt-ng-annotate](https://www.npmjs.org/package/grunt-ng-annotate) ใน automated build task และใส่ `/* @ngInject */` ไปที่ทุก ๆ ฟังก์ชั่นที่มี dependencies

    *ทำไม?*: ถึงแม้ว่า ng-annotate จะตรวจเจอ dependencies เกือบทั้งหมด แต่บางครั้งมันก็ต้องใช้ `/* @ngInject */`

    โค้ดข้างล่างนี้เป็นตัวอย่างของ gulp task ที่ใช้ ngAnnotate

    ```javascript
    gulp.task('js', ['jshint'], function() {
        var source = pkg.paths.js;
        return gulp.src(source)
            .pipe(sourcemaps.init())
            .pipe(concat('all.min.js', {newLine: ';'}))
            // Annotate before uglify so the code get's min'd properly.
            .pipe(ngAnnotate({
                // true helps add where @ngInject is not used. It infers.
                // Doesn't work with resolve, so we must be explicit there
                add: true
            }))
            .pipe(bytediff.start())
            .pipe(uglify({mangle: true}))
            .pipe(bytediff.stop())
            .pipe(sourcemaps.write('./'))
            .pipe(gulp.dest(pkg.paths.dev));
    });

    ```

**[กลับไปที่สารบัญ](#table-of-contents)**

## Exception Handling

### decorators
###### [รูปแบบ [Y110](#style-y110)]

  - Use a [decorator](https://docs.angularjs.org/api/auto/service/$provide#decorator), at config time using the [`$provide`](https://docs.angularjs.org/api/auto/service/$provide) service, on the [`$exceptionHandler`](https://docs.angularjs.org/api/ng/service/$exceptionHandler) service to perform custom actions when exceptions occur.

    *ทำไม?*: เพื่อเป็นการสร้างวิธีที่มาตรฐานในการจัดการกับ exception ที่ตรวจจับไม่ได้

    หมายเหตุ: Another option is to override the service instead of using a decorator. This is a fine option, but if you want to keep the default behavior and extend it a decorator is recommended.

    ```javascript
    /* recommended */
    angular
        .module('blocks.exception')
        .config(exceptionConfig);

    exceptionConfig.$inject = ['$provide'];

    function exceptionConfig($provide) {
        $provide.decorator('$exceptionHandler', extendExceptionHandler);
    }

    extendExceptionHandler.$inject = ['$delegate', 'toastr'];

    function extendExceptionHandler($delegate, toastr) {
        return function(exception, cause) {
            $delegate(exception, cause);
            var errorData = {
                exception: exception,
                cause: cause
            };
            /**
             * Could add the error to a service's collection,
             * add errors to $rootScope, log errors to remote web server,
             * or log locally. Or throw hard. It is entirely up to you.
             * throw exception;
             */
            toastr.error(exception.msg, errorData);
        };
    }
    ```

### Exception Catchers
###### [รูปแบบ [Y111](#style-y111)]

  - สร้าง factory เพื่อเป็น interface ในการดักจับและจัดการกับ exceptions

    *ทำไม?*: เพื่อเป็นการสร้างวิธีที่มาตรฐานสำหรับการดักจับ exceptions (เช่น ระหว่างการเรียก XHR หรือ การล้มเหลวของ promise)

    หมายเหตุ: ตัวจับ exception มันดีต่อการกระทำกับ exceptions ที่เฉพาะเจาะจงในจุดที่คุณรู้ว่ามันอาจจะเกิด exception ตัวอย่างเช่น เมื่อเรียก XHR เพื่อรับข้อมูลและคุณต้องการที่จะจับทุก ๆ  exceptions ที่อาจเกิดระหว่างการเรียก XHR นั้น และทำการจัดการแบบที่คุณต้องการ

    ```javascript
    /* recommended */
    angular
        .module('blocks.exception')
        .factory('exception', exception);

    exception.$inject = ['logger'];

    function exception(logger) {
        var service = {
            catcher: catcher
        };
        return service;

        function catcher(message) {
            return function(reason) {
                logger.error(message, reason);
            };
        }
    }
    ```

### Route Errors
###### [รูปแบบ [Y112](#style-y112)]

  - จัดการและเก็บ log สำหรับทุก ๆ  routing errors โดยใช้ [`$routeChangeError`](https://docs.angularjs.org/api/ngRoute/service/$route#$routeChangeError)

    *ทำไม?*: เพื่อสร้างวิธีที่มาตรฐานสำหรับการจัดการกับ routing errors

    *ทำไม?*: เพื่อทำการสื่อสารกับผู้ใช้ได้ดีขึ้นในกรณีที่เกิด routing error

    ```javascript
    /* recommended */
    function handleRoutingErrors() {
        /**
         * Route cancellation:
         * On routing error, go to the dashboard.
         * Provide an exit clause if it tries to do it twice.
         */
        $rootScope.$on('$routeChangeError',
            function(event, current, previous, rejection) {
                var destination = (current && (current.title || current.name || current.loadedTemplateUrl)) ||
                    'unknown target';
                var msg = 'Error routing to ' + destination + '. ' + (rejection.msg || '');
                /**
                 * Optionally log using a custom service or $log.
                 * (Don't forget to inject custom service)
                 */
                logger.warning(msg, [current]);
            }
        );
    }
    ```

**[กลับไปที่สารบัญ](#table-of-contents)**

## Naming

### Naming Guidelines
###### [รูปแบบ [Y120](#style-y120)]

  - ใช้ชื่อที่เป็นมาตรฐานสำหรับทุก ๆ components โดยใช้การบอกถึง feature ของ component แล้วตามด้วยชนิดของ component นั้น ๆ ซึ่งแนะนำให้ใช้ `feature.type.js` ซึ่งจะมี 2 ชื่อประกอบด้วย
    *   ชื่อไฟล์ (`avengers.controller.js`)
    *   ชื่อ component ใน AngularJS (`AvengersController`)

    *ทำไม?*: เนื่องจากการตั้งชื่อที่เป็นมาตรฐานจะช่วยให้สามารถหาไฟล์ได้ง่ายขึ้น และทำให้การทำงานเป็นทีมมีประสิทธิภาพ

    *ทำไม?*: เนื่องจากทำให้สามารถเข้าใจโค้ดได้ง่ายขึ้น

### Feature File Names
###### [รูปแบบ [Y121](#style-y121)]

  - ใช้ชื่อที่เป็นมาตรฐานสำหรับทุก ๆ components โดยใช้การบอกถึง feature ของ component แล้วตามด้วยชนิดของ component นั้น ๆ ซึ่งแนะนำให้ใช้ `feature.type.js`

    *ทำไม?*: วิธีนี้จะทำให้ชื่อบ่งบอกถึงความหมายของ component นั้น ๆ

    *ทำไม?*: ทำให้สามารถทำงานร่วมกับทุก ๆ automated tasks

    ```javascript
    /**
     * common options
     */

    // Controllers
    avengers.js
    avengers.controller.js
    avengersController.js

    // Services/Factories
    logger.js
    logger.service.js
    loggerService.js
    ```

    ```javascript
    /**
     * recommended
     */

    // controllers
    avengers.controller.js
    avengers.controller.spec.js

    // services/factories
    logger.service.js
    logger.service.spec.js

    // constants
    constants.js

    // module definition
    avengers.module.js

    // routes
    avengers.routes.js
    avengers.routes.spec.js

    // configuration
    avengers.config.js

    // directives
    avenger-profile.directive.js
    avenger-profile.directive.spec.js
    ```

  หมายเหตุ: มีมาตรฐานอื่นที่ตั้งชื่อ controller โดยไม่มีคำว่า `controller` ในชื่อไฟล์ เช่นใช้ `avengers.js` แทนการใช้ `avengers.controller.js` ซึ่งแนะนำให้เลือกมา 1 วิธีและใช้วิธีนั้นเป็นมาตรฐานสำหรับทีมของคุณ

    ```javascript
    /**
     * recommended
     */
    // Controllers
    avengers.js
    avengers.spec.js
    ```

### Test File Names
###### [รูปแบบ [Y122](#style-y122)]

  - ตั้งชื่อไฟล์ทดสอบเหมือนกับ component โดยมี `spec` ต่อท้าย

    *ทำไม?*: การตั้งชื่อที่เป็นมาตรฐานจะบ่งบอกความหมาย

    *ทำไม?*: เป็นหลักการตั้งชื่อสำหรับ [karma](http://karma-runner.github.io/) หรือตัวทดสอบอื่น ๆ

    ```javascript
    /**
     * recommended
     */
    avengers.controller.spec.js
    logger.service.spec.js
    avengers.routes.spec.js
    avenger-profile.directive.spec.js
    ```

### Controller Names
###### [รูปแบบ [Y123](#style-y123)]

  - ใช้ชื่อที่เป็นมาตรฐานสำหรับทุก ๆ controller หลังชื่อ feature โดยใช้ UpperCamelCase

    *ทำไม?*: การตั้งชื่อที่เป็นมาตรฐานจะบ่งบอกความหมาย

    *ทำไม?*: UpperCamelCase เป็นมาตรฐานสำหรับ object ที่สามารถถูกสร้างโดยใช้ constructor ได้

    ```javascript
    /**
     * recommended
     */

    // avengers.controller.js
    angular
        .module
        .controller('HeroAvengers', HeroAvengers);

    function HeroAvengers(){ }
    ```

### Controller Name Suffix
###### [รูปแบบ [Y124](#style-y124)]

  - เลือกที่จะเพิ่มคำว่า `Controller` ต่อจากชื่อหรือไม่เพิ่มก็ได้ โดยเลือกเพียงวิธีเดียวเท่านั้น

    *ทำไม?*: คำว่า `Controller` ถูกใช้เป็นมาตรฐานทั่วไปและช่วยบอกว่าเป็น controller

    *ทำไม?*: การไม่ต่อท้ายด้วยคำว่า `Controller` ช่วยใช้ชื่อ controller รวบรัดมากขึ้น

    ```javascript
    /**
     * recommended: Option 1
     */

    // avengers.controller.js
    angular
        .module
        .controller('Avengers', Avengers);

    function Avengers(){ }
    ```

    ```javascript
    /**
     * recommended: Option 2
     */

    // avengers.controller.js
    angular
        .module
        .controller('AvengersController', AvengersController);

    function AvengersController(){ }
    ```

### Factory Names
###### [รูปแบบ [Y125](#style-y125)]

  - ใช้ชื่อ factory ต่อจากชื่อ feature และใช้ camel-casing สำหรับ service และ factory

    *ทำไม?*: การตั้งชื่อที่เป็นมาตรฐานจะบ่งบอกความหมาย

    ```javascript
    /**
     * recommended
     */

    // logger.service.js
    angular
        .module
        .factory('logger', logger);

    function logger(){ }
    ```

### Directive Component Names
###### [รูปแบบ [Y126](#style-y126)]

  - ใช้ camel-case สำหรับทุก ๆ directive และใช้คำนำหน้าสั้น ๆ สำหรับอธิบายขอบเขตของ directive เช่น ชื่อบริษัทหรือชื่อโปรเจ็ค เป็นต้น

    *ทำไม?*: การตั้งชื่อที่เป็นมาตรฐานจะบ่งบอกความหมาย

    ```javascript
    /**
     * recommended
     */

    // avenger-profile.directive.js
    angular
        .module
        .directive('xxAvengerProfile', xxAvengerProfile);

    // usage is <xx-avenger-profile> </xx-avenger-profile>

    function xxAvengerProfile(){ }
    ```

### Modules
###### [รูปแบบ [Y127](#style-y127)]

  -  เมื่อมีหลาย ๆ module มากขึ้น ให้ทำการตั้งชื่อ module หลักด้วยชื่อ `app.module.js` ขณะที่ module อื่น ๆ ตั้งชื่อเป็นความหมายของ module นั้น ๆ เช่น สำหรับ admin module ก็ตั้งชื่อ `admin.module.js` โดยใช้ชื่อใน Angular คือ  `app` และ `admin`

    *ทำไม?*: สร้างมาตรฐานสำหรับแอปที่มีหลาย module และเพื่อสำหรับการขยายไปสู่แอปพลิเคชั่นขนาดใหญ่

    *ทำไม?*: สร้างวิธีที่ง่ายในการทำ automation สำหรับการโหลดทุก ๆ module แล้วตามด้วยโหลดไฟล์อื่น ๆ

### Configuration
###### [รูปแบบ [Y128](#style-y128)]

  - แยกการตั้งค่าออกมาสำหรับแต่ละ module โดยใช้ชื่อไฟล์เป็นชื่อ module ตามด้วย `config` เช่น สำหรับ `app` module ให้ตั้งชื่อ `app.config.js` หรือสำหรับ `admin.module.js` ให้ตั้งชื่อ `admin.config.js` เป็นต้น

    *ทำไม?*: เพื่อแยกการตั้งค่าออกมาจากส่วนอื่น ๆ

    *ทำไม?*: เพื่อสร้างไฟล์สำหรับการตั้งค่าของ module

### Routes
###### [รูปแบบ [Y129](#style-y129)]

  - แยก route ออกเป็นไฟล์แยกสำหรับ module เช่น `app.route.js` สำหรับ module หลัก และ `admin.route.js` สำหรับ `admin` module และถึงแม้จะเป็นแอปขนาดเล็กผมก็ยังชอบวิธีนี้มากกว่าการรวม route ไว้ในไฟล์เดียว

**[กลับไปที่สารบัญ](#table-of-contents)**

## Application Structure LIFT Principle
### LIFT
###### [Style [Y140](#style-y140)]

  - Structure your app such that you can `L`ocate your code quickly, `I`dentify the code at a glance, keep the `F`lattest structure you can, and `T`ry to stay DRY. The structure should follow these 4 basic guidelines.

    *Why LIFT?*: Provides a consistent structure that scales well, is modular, and makes it easier to increase developer efficiency by finding code quickly. Another way to check your app structure is to ask yourself: How quickly can you open and work in all of the related files for a feature?

    When I find my structure is not feeling comfortable, I go back and revisit these LIFT guidelines

    1. `L`ocating our code is easy
    2. `I`dentify code at a glance
    3. `F`lat structure as long as we can
    4. `T`ry to stay DRY (Don’t Repeat Yourself) or T-DRY

### Locate
###### [Style [Y141](#style-y141)]

  - Make locating your code intuitive, simple and fast.

    *Why?*: I find this to be super important for a project. If the team cannot find the files they need to work on quickly,  they will not be able to work as efficiently as possible, and the structure needs to change. You may not know the file name or where its related files are, so putting them in the most intuitive locations and near each other saves a ton of time. A descriptive folder structure can help with this.

    ```
    /bower_components
    /client
      /app
        /avengers
        /blocks
          /exception
          /logger
        /core
        /dashboard
        /data
        /layout
        /widgets
      /content
      index.html
    .bower.json
    ```

### Identify
###### [Style [Y142](#style-y142)]

  - When you look at a file you should instantly know what it contains and represents.

    *Why?*: You spend less time hunting and pecking for code, and become more efficient. If this means you want longer file names, then so be it. Be descriptive with file names and keeping the contents of the file to exactly 1 component. Avoid files with multiple controllers, multiple services, or a mixture. There are deviations of the 1 per file rule when I have a set of very small features that are all related to each other, they are still easily identifiable.

### Flat
###### [Style [Y143](#style-y143)]

  - Keep a flat folder structure as long as possible. When you get to 7+ files, begin considering separation.

    *Why?*: Nobody wants to search 7 levels of folders to find a file. Think about menus on web sites … anything deeper than 2 should take serious consideration. In a folder structure there is no hard and fast number rule, but when a folder has 7-10 files, that may be time to create subfolders. Base it on your comfort level. Use a flatter structure until there is an obvious value (to help the rest of LIFT) in creating a new folder.

### T-DRY (Try to Stick to DRY)
###### [Style [Y144](#style-y144)]

  - Be DRY, but don't go nuts and sacrifice readability.

    *Why?*: Being DRY is important, but not crucial if it sacrifices the others in LIFT, which is why I call it T-DRY. I don’t want to type session-view.html for a view because, well, it’s obviously a view. If it is not obvious or by convention, then I name it.

**[Back to top](#table-of-contents)**

## Application Structure

### Overall Guidelines
###### [Style [Y150](#style-y150)]

  -  Have a near term view of implementation and a long term vision. In other words, start small and but keep in mind on where the app is heading down the road. All of the app's code goes in a root folder named `app`. All content is 1 feature per file. Each controller, service, module, view is in its own file. All 3rd party vendor scripts are stored in another root folder and not in the `app` folder. I didn't write them and I don't want them cluttering my app (`bower_components`, `scripts`, `lib`).

    Note: Find more details and reasoning behind the structure at [this original post on application structure](http://www.johnpapa.net/angular-app-structuring-guidelines/).

### Layout
###### [Style [Y151](#style-y151)]

  - Place components that define the overall layout of the application in a folder named `layout`. These may include a shell view and controller may act as the container for the app, navigation, menus, content areas, and other regions.

    *Why?*: Organizes all layout in a single place re-used throughout the application.

### Folders-by-Feature Structure
###### [Style [Y152](#style-y152)]

  - Create folders named for the feature they represent. When a folder grows to contain more than 7 files, start to consider creating a folder for them. Your threshold may be different, so adjust as needed.

    *Why?*: A developer can locate the code, identify what each file represents at a glance, the structure is flat as can be, and there is no repetitive nor redundant names.

    *Why?*: The LIFT guidelines are all covered.

    *Why?*: Helps reduce the app from becoming cluttered through organizing the content and keeping them aligned with the LIFT guidelines.

    *Why?*: When there are a lot of files (10+) locating them is easier with a consistent folder structures and more difficult in flat structures.

    ```javascript
    /**
     * recommended
     */

    app/
        app.module.js
        app.config.js
        app.routes.js
        components/
            calendar.directive.js
            calendar.directive.html
            user-profile.directive.js
            user-profile.directive.html
        layout/
            shell.html
            shell.controller.js
            topnav.html
            topnav.controller.js
        people/
            attendees.html
            attendees.controller.js
            speakers.html
            speakers.controller.js
            speaker-detail.html
            speaker-detail.controller.js
        services/
            data.service.js
            localstorage.service.js
            logger.service.js
            spinner.service.js
        sessions/
            sessions.html
            sessions.controller.js
            session-detail.html
            session-detail.controller.js
    ```

      ![Sample App Structure](https://raw.githubusercontent.com/johnpapa/angularjs-styleguide/master/assets/modularity-2.png)

      Note: Do not use structuring using folders-by-type. This requires moving to multiple folders when working on a feature and gets unwieldy quickly as the app grows to 5, 10 or 25+ views and controllers (and other features), which makes it more difficult than folder-by-feature to locate files.

    ```javascript
    /*
    * avoid
    * Alternative folders-by-type.
    * I recommend "folders-by-feature", instead.
    */

    app/
        app.module.js
        app.config.js
        app.routes.js
        controllers/
            attendees.js
            session-detail.js
            sessions.js
            shell.js
            speakers.js
            speaker-detail.js
            topnav.js
        directives/
            calendar.directive.js
            calendar.directive.html
            user-profile.directive.js
            user-profile.directive.html
        services/
            dataservice.js
            localstorage.js
            logger.js
            spinner.js
        views/
            attendees.html
            session-detail.html
            sessions.html
            shell.html
            speakers.html
            speaker-detail.html
            topnav.html
    ```

**[Back to top](#table-of-contents)**

## Modularity

### Many Small, Self Contained Modules
###### [Style [Y160](#style-y160)]

  - Create small modules that encapsulate one responsibility.

    *Why?*: Modular applications make it easy to plug and go as they allow the development teams to build vertical slices of the applications and roll out incrementally.  This means we can plug in new features as we develop them.

### Create an App Module
###### [Style [Y161](#style-y161)]

  - Create an application root module whose role is pull together all of the modules and features of your application. Name this for your application.

    *Why?*: AngularJS encourages modularity and separation patterns. Creating an application root module whose role is to tie your other modules together provides a very straightforward way to add or remove modules from your application.

### Keep the App Module Thin
###### [Style [Y162](#style-y162)]

  - Only put logic for pulling together the app in the application module. Leave features in their own modules.

    *Why?*: Adding additional roles to the application root to get remote data, display views, or other logic not related to pulling the app together muddies the app module and make both sets of features harder to reuse or turn off.

    *Why?*: The app module becomes a manifest that describes which modules help define the application.

### Feature Areas are Modules
###### [Style [Y163](#style-y163)]

  - Create modules that represent feature areas, such as layout, reusable and shared services, dashboards, and app specific features (e.g. customers, admin, sales).

    *Why?*: Self contained modules can be added to the application with little or no friction.

    *Why?*: Sprints or iterations can focus on feature areas and turn them on at the end of the sprint or iteration.

    *Why?*: Separating feature areas into modules makes it easier to test the modules in isolation and reuse code.

### Reusable Blocks are Modules
###### [Style [Y164](#style-y164)]

  - Create modules that represent reusable application blocks for common services such as exception handling, logging, diagnostics, security, and local data stashing.

    *Why?*: These types of features are needed in many applications, so by keeping them separated in their own modules they can be application generic and be reused across applications.

### Module Dependencies
###### [Style [Y165](#style-y165)]

  - The application root module depends on the app specific feature modules and any shared or reusable modules.

    ![Modularity and Dependencies](https://raw.githubusercontent.com/johnpapa/angularjs-styleguide/master/assets/modularity-1.png)

    *Why?*: The main app module contains a quickly identifiable manifest of the application's features.

    *Why?*: Each feature area contains a manifest of what it depends on, so it can be pulled in as a dependency in other applications and still work.

    *Why?*: Intra-App features such as shared data services become easy to locate and share from within `app.core` (choose your favorite name for this module).

    Note: This is a strategy for consistency. There are many good options here. Choose one that is consistent, follows AngularJS's dependency rules, and is easy to maintain and scale.

    > My structures vary slightly between projects but they all follow these guidelines for structure and modularity. The implementation may vary depending on the features and the team. In other words, don't get hung up on an exact like-for-like structure but do justify your structure using consistency, maintainability, and efficiency in mind.

    > In a small app, you can also consider putting all the shared dependencies in the app module where the feature modules have no direct dependencies. This makes it easier to maintain the smaller application, but makes it harder to reuse modules outside of this application.

**[Back to top](#table-of-contents)**

## Startup Logic

### Configuration
###### [Style [Y170](#style-y170)]

  - Inject code into [module configuration](https://docs.angularjs.org/guide/module#module-loading-dependencies) that must be configured before running the angular app. Ideal candidaes include providers and constants.

    *Why?*: This makes it easier to have a less places for configuration.

  ```javascript
  angular
      .module('app')
      .config(configure);

  configure.$inject =
      ['routerHelperProvider', 'exceptionHandlerProvider', 'toastr'];

  function configure (routerHelperProvider, exceptionHandlerProvider, toastr) {
      exceptionHandlerProvider.configure(config.appErrorPrefix);
      configureStateHelper();

      toastr.options.timeOut = 4000;
      toastr.options.positionClass = 'toast-bottom-right';

      ////////////////

      function configureStateHelper() {
          routerHelperProvider.configure({
              docTitle: 'NG-Modular: '
          });
      }
  }
  ```

### Run Blocks
###### [Style [Y171](#style-y171)]

  - Any code that needs to run when an application starts should be declared in a factory, exposed via a function, and injected into the [run block](https://docs.angularjs.org/guide/module#module-loading-dependencies).

    *Why?*: Code directly in a run block can be difficult to test. Placing in a factory makes it easier to abstract and mock.

  ```javascript
  angular
      .module('app')
      .run(runBlock);

    runBlock.$inject = ['authenticator', 'translator'];

    function runBlock(authenticator, translator) {
        authenticator.initialize();
        translator.initialize();
    }
  ```

**[Back to top](#table-of-contents)**

## Angular $ Wrapper Services

### $document and $window
###### [Style [Y180](#style-y180)]

  - Use [`$document`](https://docs.angularjs.org/api/ng/service/$document) and [`$window`](https://docs.angularjs.org/api/ng/service/$window) instead of `document` and `window`.

    *Why?*: These services are wrapped by Angular and more easily testable than using document and window in tests. This helps you avoid having to mock document and window yourself.

### $timeout and $interval
###### [Style [Y181](#style-y181)]

  - Use [`$timeout`](https://docs.angularjs.org/api/ng/service/$timeout) and [`$interval`](https://docs.angularjs.org/api/ng/service/$interval) instead of `setTimeout` and `setInterval` .

    *Why?*: These services are wrapped by Angular and more easily testable and handle AngularJS's digest cycle thus keeping data binding in sync.

**[Back to top](#table-of-contents)**

## Testing
Unit testing helps maintain clean code, as such I included some of my recommendations for unit testing foundations with links for more information.

### Write Tests with Stories
###### [Style [Y190](#style-y190)]

  - Write a set of tests for every story. Start with an empty test and fill them in as you write the code for the story.

    *Why?*: Writing the test descriptions helps clearly define what your story will do, will not do, and how you can measure success.

    ```javascript
    it('should have Avengers controller', function() {
        //TODO
    });

    it('should find 1 Avenger when filtered by name', function() {
        //TODO
    });

    it('should have 10 Avengers', function() {
        //TODO (mock data?)
    });

    it('should return Avengers via XHR', function() {
        //TODO ($httpBackend?)
    });

    // and so on
    ```

### Testing Library
###### [Style [Y191](#style-y191)]

  - Use [Jasmine](http://jasmine.github.io/) or [Mocha](http://visionmedia.github.io/mocha/) for unit testing.

    *Why?*: Both Jasmine and Mocha are widely used in the AngularJS community. Both are stable, well maintained, and provide robust testing features.

    Note: When using Mocha, also consider choosing an assert library such as [Chai](http://chaijs.com).

### Test Runner
###### [Style [Y192](#style-y192)]

  - Use [Karma](http://karma-runner.github.io) as a test runner.

    *Why?*: Karma is easy to configure to run once or automatically when you change your code.

    *Why?*: Karma hooks into your Continuous Integration process easily on its own or through Grunt or Gulp.

    *Why?*: Some IDE's are beginning to integrate with Karma, such as [WebStorm](http://www.jetbrains.com/webstorm/) and [Visual Studio](http://visualstudiogallery.msdn.microsoft.com/02f47876-0e7a-4f6c-93f8-1af5d5189225).

    *Why?*: Karma works well with task automation leaders such as [Grunt](http://www.gruntjs.com) (with [grunt-karma](https://github.com/karma-runner/grunt-karma)) and [Gulp](http://www.gulpjs.com) (with [gulp-karma](https://github.com/lazd/gulp-karma)).

### Stubbing and Spying
###### [Style [Y193](#style-y193)]

  - Use Sinon for stubbing and spying.

    *Why?*: Sinon works well with both Jasmine and Mocha and extends the stubbing and spying features they offer.

    *Why?*: Sinon makes it easier to toggle between Jasmine and Mocha, if you want to try both.

### Headless Browser
###### [Style [Y194](#style-y194)]

  - Use [PhantomJS](http://phantomjs.org/) to run your tests on a server.

    *Why?*: PhantomJS is a headless browser that helps run your tests without needing a "visual" browser. So you do not have to install Chrome, Safari, IE, or other browsers on your server.

    Note: You should still test on all browsers in your environment, as appropriate for your target audience.

### Code Analysis
###### [Style [Y195](#style-y195)]

  - Run JSHint on your tests.

    *Why?*: Tests are code. JSHint can help identify code quality issues that may cause the test to work improperly.

### Alleviate Globals for JSHint Rules on Tests
###### [Style [Y196](#style-y196)]

  - Relax the rules on your test code to allow for common globals such as `describe` and `expect`.

    *Why?*: Your tests are code and require the same attention and code quality rules as all of your production code. However, global variables used by the testing framework, for example, can be relaxed by including this in your test specs.

    ```javascript
    /* global sinon, describe, it, afterEach, beforeEach, expect, inject */
    ```

  ![Testing Tools](https://raw.githubusercontent.com/johnpapa/angularjs-styleguide/master/assets/testing-tools.png)

**[Back to top](#table-of-contents)**

## Animations

### Usage
###### [Style [Y210](#style-y210)]

  - Use subtle [animations with AngularJS](https://docs.angularjs.org/guide/animations) to transition between states for views and primary visual elements. Include the [ngAnimate module](https://docs.angularjs.org/api/ngAnimate). The 3 keys are subtle, smooth, seamless.

    *Why?*: Subtle animations can improve User Experience when used appropriately.

    *Why?*: Subtle animations can improve perceived performance as views transition.

### Sub Second
###### [Style [Y211](#style-y211)]

  - Use short durations for animations. I generally start with 300ms and adjust until appropriate.

    *Why?*: Long animations can have the reverse affect on User Experience and perceived performance by giving the appearance of a slow application.

### animate.css
###### [Style [Y212](#style-y212)]

  - Use [animate.css](http://daneden.github.io/animate.css/) for conventional animations.

    *Why?*: The animations that animate.css provides are fast, smooth, and easy to add to your application.

    *Why?*: Provides consistency in your animations.

    *Why?*: animate.css is widely used and tested.

    Note: See this [great post by Matias Niemelä on AngularJS animations](http://www.yearofmoo.com/2013/08/remastered-animation-in-angularjs-1-2.html)

**[Back to top](#table-of-contents)**

## Comments

### jsDoc
###### [Style [Y220](#style-y220)]

  - If planning to produce documentation, use [`jsDoc`](http://usejsdoc.org/) syntax to document function names, description, params and returns. Use `@namespace` and `@memberOf` to match your app structure.

    *Why?*: You can generate (and regenerate) documentation from your code, instead of writing it from scratch.

    *Why?*: Provides consistency using a common industry tool.

    ```javascript
    /**
     * Logger Factory
     * @namespace Factories
     */
    (function() {
      angular
          .module('app')
          .factory('logger', logger);

      /**
       * @namespace Logger
       * @desc Application wide logger
       * @memberOf Factories
       */
      function logger($log) {
          var service = {
             logError: logError
          };
          return service;

          ////////////

          /**
           * @name logError
           * @desc Logs errors
           * @param {String} msg Message to log
           * @returns {String}
           * @memberOf Factories.Logger
           */
          function logError(msg) {
              var loggedMsg = 'Error: ' + msg;
              $log.error(loggedMsg);
              return loggedMsg;
          };
      }
    })();
    ```

**[Back to top](#table-of-contents)**

## JS Hint

### Use an Options File
###### [Style [Y230](#style-y230)]

  - Use JS Hint for linting your JavaScript and be sure to customize the JS Hint options file and include in source control. See the [JS Hint docs](http://www.jshint.com/docs/) for details on the options.

    *Why?*: Provides a first alert prior to committing any code to source control.

    *Why?*: Provides consistency across your team.

    ```javascript
    {
        "bitwise": true,
        "camelcase": true,
        "curly": true,
        "eqeqeq": true,
        "es3": false,
        "forin": true,
        "freeze": true,
        "immed": true,
        "indent": 4,
        "latedef": "nofunc",
        "newcap": true,
        "noarg": true,
        "noempty": true,
        "nonbsp": true,
        "nonew": true,
        "plusplus": false,
        "quotmark": "single",
        "undef": true,
        "unused": false,
        "strict": false,
        "maxparams": 10,
        "maxdepth": 5,
        "maxstatements": 40,
        "maxcomplexity": 8,
        "maxlen": 120,

        "asi": false,
        "boss": false,
        "debug": false,
        "eqnull": true,
        "esnext": false,
        "evil": false,
        "expr": false,
        "funcscope": false,
        "globalstrict": false,
        "iterator": false,
        "lastsemic": false,
        "laxbreak": false,
        "laxcomma": false,
        "loopfunc": true,
        "maxerr": false,
        "moz": false,
        "multistr": false,
        "notypeof": false,
        "proto": false,
        "scripturl": false,
        "shadow": false,
        "sub": true,
        "supernew": false,
        "validthis": false,
        "noyield": false,

        "browser": true,
        "node": true,

        "globals": {
            "angular": false,
            "$": false
        }
    }
    ```

**[Back to top](#table-of-contents)**

## Constants

### Vendor Globals
###### [Style [Y240](#style-y240)]

  - Create an AngularJS Constant for vendor libraries' global variables.

    *Why?*: Provides a way to inject vendor libraries that otherwise are globals. This improves code testability by allowing you to more easily know what the dependencies of your components are (avoids leaky abstractions). It also allows you to mock these dependencies, where it makes sense.

    ```javascript
    // constants.js

    /* global toastr:false, moment:false */
    (function() {
        'use strict';

        angular
            .module('app.core')
            .constant('toastr', toastr)
            .constant('moment', moment);
    })();
    ```

**[Back to top](#table-of-contents)**

## File Templates and Snippets
Use file templates or snippets to help follow consistent styles and patterns. Here are templates and/or snippets for some of the web development editors and IDEs.

### Sublime Text
###### [Style [Y250](#style-y250)]

  - AngularJS snippets that follow these styles and guidelines.

    - Download the [Sublime Angular snippets](assets/sublime-angular-snippets.zip?raw=true)
    - Place it in your Packages folder
    - Restart Sublime
    - In a JavaScript file type these commands followed by a `TAB`

    ```javascript
    ngcontroller // creates an Angular controller
    ngdirective // creates an Angular directive
    ngfactory // creates an Angular factory
    ngmodule // creates an Angular module
    ```

### Visual Studio
###### [Style [Y251](#style-y251)]

  - AngularJS file templates that follow these styles and guidelines can be found at [SideWaffle](http://www.sidewaffle.com)

    - Download the [SideWaffle](http://www.sidewaffle.com) Visual Studio extension (vsix file)
    - Run the vsix file
    - Restart Visual Studio

### WebStorm
###### [Style [Y252](#style-y252)]

  - AngularJS snippets and file templates that follow these styles and guidelines. You can import them into your WebStorm settings:

    - Download the [WebStorm AngularJS file templates and snippets](assets/webstorm-angular-file-template.settings.jar?raw=true)
    - Open WebStorm and go to the `File` menu
    - Choose the `Import Settings` menu option
    - Select the file and click `OK`
    - In a JavaScript file type these commands followed by a `TAB`:

    ```javascript
    ng-c // creates an Angular controller
    ng-f // creates an Angular factory
    ng-m // creates an Angular module
    ```

**[Back to top](#table-of-contents)**

## AngularJS docs
For anything else, API reference, check the [Angular documentation](//docs.angularjs.org/api).

## Contributing

Open an issue first to discuss potential changes/additions. If you have questions with the guide, feel free to leave them as issues in the repository. If you find a typo, create a pull request. The idea is to keep the content up to date and use github’s native feature to help tell the story with issues and PR’s, which are all searchable via google. Why? Because odds are if you have a question, someone else does too! You can learn more here at about how to contribute.

*By contributing to this repository you are agreeing to make your content available subject to the license of this repository.*

### Process
    1. Discuss the changes in an Issue.
    2. Open a Pull Request, reference the issue, and explain the change and why it adds value.
    3. The Pull Request will be evaluated and either merged or declined.

## License

_tldr; Use this guide. Attributions are appreciated._

### (The MIT License)

Copyright (c) 2014 [John Papa](http://johnpapa.net)

Permission is hereby granted, free of charge, to any person obtaining
a copy of this software and associated documentation files (the
'Software'), to deal in the Software without restriction, including
without limitation the rights to use, copy, modify, merge, publish,
distribute, sublicense, and/or sell copies of the Software, and to
permit persons to whom the Software is furnished to do so, subject to
the following conditions:

The above copyright notice and this permission notice shall be
included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED 'AS IS', WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.
IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY
CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT,
TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE
SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

**[Back to top](#table-of-contents)**
