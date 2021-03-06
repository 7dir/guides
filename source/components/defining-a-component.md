Чтобы определить компонент, запустите:

```bash
ember generate component my-component-name
```

Компоненты должны иметь как минимум один знак тире в имени. Поэтому `blog-post` — приемлемое имя, так же как и `audio-player-controls`. А `post` уже не подойдет. Это предотвращает конфликты с текущими или будущими именами  элементов HTML, приводит компоненты Ember в соответствие со спецификацией W3C [Custom Elements](https://dvcs.w3.org/hg/webcomponents/raw-file/tip/spec/custom/index.html) и гарантирует, что Ember автоматически обнаружит компоненты.

Простой шаблон компонента будет выглядеть так:

`app/templates/components/blog-post.hbs`
```hbs
<article class="blog-post">
  <h1>{{title}}</h1>
  <p>{{yield}}</p>
  <p>Edit title: {{input type="text" value=title}}</p>
</article>
```

Учитывая шаблон выше, теперь можно использовать компонент `{{blog-post}}`:

`app/templates/index.hbs`
```hbs
{{#each model as |post|}}
  {{#blog-post title=post.title}}
    {{post.body}}
  {{/blog-post}}
{{/each}}

```app/routes/index.js
export default Ember.Route.extend({
  model() {
    return this.store.findAll('post');
  }
});
```

Каждый компонент «изнутри» поддерживается элементом. По умолчанию, Ember будет использовать элемент `<div>`, чтобы включить шаблон компонента. Если хотите узнать, как изменять области применения элемента Ember для компонента, смотрите раздел [Индивидуальная настройка элемента компонента](http://emjs.ru/v2/components/customizing-a-components-element/).

## Определение подкласса компонента 

Чаще всего ваши компоненты будут просто инкапсулировать определенные фрагменты шаблонов Handlebars, которые вы будете использовать снова и снова. В таких случаях вам не нужно писать какой-либо код JavaScript. Просто определите шаблон Handlebars, как описано выше, и используйте созданный компонент.

Если вам нужно индивидуально настроить поведение компонента, то необходимо назначить подкласс `Ember.Component`. Например, вам нужен индивидуальный подкласс, если вы хотите изменить элемент компонента, реагировать на действия от шаблона компонента или вручную внести изменения в элемент компонента с помощью JavaScript.

Ember по имени файла узнает, какой подкласс поддерживает компонент. Например, если у вас есть компонент под названием `blog-post`, вы создаете файл в `app/components/blog-post.js`. Если ваш компонент был назван `audio-player-controls`, имя файла будет в `app/components/audio-player-controls.js`.

## Динамическое отображение компонента

Помощник `{{component}}` можно использовать, чтобы отложить выбор компонента во время работы приложения. Синтаксис `{{my-component}}` всегда отображает одинаковый компонент, пока используемый помощник `{{component}}` позволяет на ходу выбирать компонент для отображения. Это полезно в случаях, где вам нужно взаимодействовать с разными внешними библиотеками в зависимости от данных. Использование помощника `{{component}}` позволит вам сохранять разную логику разделенной.

Первый параметр помощника — имя компонента для отображения в виде строки. Поэтому `{{component 'blog-post'}}` то же самое, что и `{{blog-post}}`.

Настоящая ценность `{{component}}` заключается в способности динамически выбирать компонент для отображения. Ниже приведен пример использования помощника и способы выбора различных компонентов для отображения разного рода постов:

`app/templates/components/foo-component.hbs`
```hbs
<h3>Hello from foo!</h3>
<p>{{post.body}}</p>
```

`app/templates/components/bar-component.hbs`
```hbs
<h3>Hello from bar!</h3>
<div>{{post.author}}</div>
```

`app/routes/index.js`
```js
export default Ember.Route.extend({
  model() {
    return this.store.findAll('post');
  }
});
```

`app/templates/index.hbs`
```hbs
{{#each model as |post|}}
  {{!-- either foo-component or bar-component --}}
  {{component post.componentName post=post}}
{{/each}}
```

Когда параметр, переданный `{{component}}`, выражает `null` или `undefined`, помощник ничего не отображает. Когда параметр меняется, текущий отображенный компонент убирается, а новый создается и ставится на его место.

Выбор разных компонентов для отображения в ответ на данные позволяет иметь разные шаблоны и поведение для каждого случая. Помощник `{{component}}` — мощный инструмент для улучшения модульности кода.