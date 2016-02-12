# Продвинутый UI-Route

Вся работа с `ui-route` происходит в `angular.config`

Не забывайте в `angular.config` передать зависимости `$stateProvider` и `$urlRouterProvider`

- `$stateProvider` нужен для определение state вашей страницы
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
Для того что бы вывести ваше `template` на странице используется `<ui-view></ui-view>`
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
