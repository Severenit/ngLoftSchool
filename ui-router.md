* [Back to Main]
[Back to Main]: <https://github.com/Severenit/ngLoftSchool/>
<hr/>

# Работа с ui-router

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

## ui-view

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

## Углубление в state

Одной из самых важных особенностей **ui-route** является возможность вложенности **ui-view**. Таким образом ваше приложение может быть более гибким и глубоким.

```js
    $stateProvider.state('home', {
        url: '/home',
        template: '<h1>Home Page</h1>'+
            '<ui-view></ui-view>'
    });
    $stateProvider.state('home.user', {
        url: '/user',
        template: '<h1>User Page</h1>'
    });
```

Тут стоит дать пару комментариев:
- Самый верхний уровень это **state: home**, его путь http://localhost:8000/#/home и он будет отображаться в самом верхнем **ui-view**, т.е. в index.html;
- Второй state это **state: home.user**, его путь будет уже http://localhost:8000/#/home/user, **заметьте что в url не указан home он подставляется автоматически**, и этот темплейт будет отображаться не в верхнем **ui-view** а во вложенном **home**, который вы можете найти в template;

Но это не все осообенности связанные с вложенным *ui-view*

Одной из ключевых его особенностей является в том, что мы можем давать имя *ui-view* и говорить в каком состоянии мы хотим использовать определенный из них.

Так например:
```js
    $stateProvider.state('home', {
        url: '/home',
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
    $stateProvider.state('home.user', {
        url: '/user',
        views: {
            "@": {
                template: '<h1>New State</h1>'
            },
            "header@": {
                template: '<h1>Header 2</h1>'
            },
            "footer@": {
                template: '<h1>Footer 2</h1>'
            }
        }
        template: '<h1>Home Page</h1>'
    });
```

В данном конкретном примере, мы добавили вложенное состояние от состояния home и теперь по ссылке http://localhost:8000/#/home/user будут заменены все *ui-view* на новые.

> Тут сразу стоит отметить что именно символ `@` говорит нам о том что нужно смотреть на конкретное имя, и его менять, если не указать его то ваше приложение будет пытаться внутри **ui-view** найти **ui-view** с такими именами.

Если вы не укажете шаблон для конкретного *view* то по умолчанию он будет брать *view* из предыдущего.
