# Продвинутый UI-Route

Вся работа с `ui-route` происходит в `angular.config`

Не забывайте в `angular.config` передать зависимости `$stateProvider` и `$urlRouterProvider`

- `$stateProvider` нужен для определение состояния вашей страницы
- `$urlRouterProvider` служит для указания url который будет выполняться если ни один из выше перечисленных состояний не найдется

```js
angular.config(function($stateProvider, $urlRouterProvider) {
    $urlRouterProvider.otherwise('/');
    
    $stateProvider.state('home', {
        url: '/',
        template: '<h1>Home Page</h1>'
    });
});
```
Для того что бы вывести ваш `template` на странице используется `<ui-view></ui-view>`
### ui-view
`ui-view` может быть именнованным, таким образом мы можем указать несколько **view** на одной странице и загружать в них разные шаблоны.
```js
    $stateProvider.state('home', {
        url: '/',
        views: {
            "": {
                template: '<h1>This is not name view</h1>'
            },
            "header": {
                template: '<h1>This is header view</h1>'
            },
            "footer": {
                template: '<h1>This is footer view</h1>'
            }
        }
        template: '<h1>Home Page</h1>'
    });
```
```html
    <ui-view></ui-view>
    <ui-view name='header'></ui-view>
    <ui-view name='footer'></ui-view>
```
## ui-sref
Для перехода между состояниями можно использовать знакомый `href="..."` или использовать директиву `ui-sref="..."`

`ui-sref` принимает имя вашего состояния, таким образом делает ваш код более гибким, т.к. его путь настраивается в параметру `url` и вы всегда можете его поменять на тот который нужен вашему приложению, оставляя одно и тоже состояние.
```html
    <a ui-sref="home">Home Page</a>
```
Можно использовать аттрибут `ui-sref-opts="{Object}"` для расширения функионала вашей ссылки. Аттрибует `ui-sref-opts` принимает все значения что содержит метод `$state.go()`
```html
    <a ui-sref="home" ui-sref-opts="{reload: true}">Home</a>
```
В данном примере при клике на ссылку *home* будет происходит перезагрузка состояния, если вы находитесь на этой странице.

Каждое ваше состояние может принимать ряд параметров, они будут передаваться объектом в ваше состояние
```html
    <a ui-sref="home({id: 123, personal: 'Jhon'})">Home</a>
```
Надо не забыть описать то как будет выглядить ваше *url*
```js
    $stateProvider.state('home', {
        url: '/home/:id/:personal',
        template: '<h1>Home Page</h1>'
    });
```
В таком примере на выходе мы получим `http://localhost:8000/#/home/123/Jhon`

Так же можно передать параметры и как доп.аргументы к вашей ссылке
```js
    $stateProvider.state('home', {
        url: '/home?id&personal',
        template: '<h1>Home Page</h1>'
    });
```
В таком примере на выходе мы получим `http://localhost:8000/#/home?id=123&personal=Jhon`

Все эти парамерты в вашем `controller` вы можете принять при помощи сервиса `$stateParams`
```js
    $stateProvider.state('home', {
        url: '/home/:id/:personal',
        template: '<h1>Home Page</h1>',
        controller: function($scope, $stateParams) {
            $scope.id = $stateParams.id;
            $scope.personal = $stateParams.personal;
        }
    });
```
> По рекомендации ui-route советуют выносить `$state` и `$stateParams` в основной метод `angular.run()`, где его присвоить пременным `$rootScope.$state = $state` и `$rootScope.$stateParams = $stateParams`. Таким образом в любом месте нашей страницы мы можем вызвать параметры которые мы передаем в **url** например `{{$stateParams.personal}}`
