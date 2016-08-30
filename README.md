# ng-money
test

export default angular.module('ng-currency', [])
  .directive('ngCurrency', ($filter) => {
    return {
      require: 'ngModel',
      link: (scope, element, attrs, controller) => {
        let active = true;
        attrs.$observe('ng-currency', (value) => {
          active = (value !== 'false');
          if (active) {
            reformat();
          } else {
            controller.$viewValue = controller.$modelValue;
            controller.$render();
          }
        });
        controller.$parsers.push((value) => {
          return value;
        });

        controller.$formatters.push((value) => {
          if (active && [undefined, null, ''].indexOf(value) === -1) {
            console.log(value);
            return $filter('currency')(value, '', 2);
          }
          return value;
        });

        function reformat() {
          if (active) {
            let value = controller.$modelValue;
            for (let i = controller.$formatters.length - 1; i >= 0; i--) {
              value = controller.$formatters[i](value);
            }
            controller.$viewValue = value;
            // console.log(value);
            controller.$render();
          }
        }
        element.bind('blur', reformat);
      }
    };
  }).name;
