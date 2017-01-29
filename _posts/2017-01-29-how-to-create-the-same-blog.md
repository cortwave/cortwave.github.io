---
layout: post
title: "Как создать такой же блог"
description: "Инструкция по созданию блога на основе github pages и jekyll, а заодно тестовая статья"
category: guides 
tags: ["jekyll", "guide"]
---
{% include JB/setup %}

В данной статье я расскажу как создать похожий блог на основе [Github Pages](https://pages.github.com/) и [Jekyll](https://jekyllrb.com/), а заодно сам потренируюсь в создании новых постов.

Jeckyll - генератор статических сайтов, написанный на Ruby. Основные преимущества Jekyll:

- Отсутствие базы данных. Вся информация, необходимая для генерации сайта находится в исходниках. Комментарии можно подключить, например, с помощью [Disqus](https://disqus.com).
- HTML генерируется на основе [Markdown](https://ru.wikipedia.org/wiki/Markdown) файлов, что позволяет писать статьи в любимом текстовом редакторе на привычном для многих Github-пользователей языке.
- Минимальное количество динамических элементов, js-вставок. Весь HTML-код необходимый для отображения генерируется в момент запуска сайта, что делает страницы доступными для быстрой загрузки.

Для быстрого старта я использовал [Jekyll bootstrap](http://jekyllbootstrap.com/). Для создания блога требуются только профиль на [Github](https://github.com) и базовые навыки работы с git. Прежде всего необходимо создать репозиторий на Github с именем `USERNAME.github.io`, где `USERNAME` - имя Github-профиля. Далее переходим в директорию на локальной машине, где в последующем будут храниться исходники блога и выполняем следующую последовательность команд:

```bash
git clone https://github.com/plusjade/jekyll-bootstrap.git USERNAME.github.io
cd USERNAME.github.io
git remote set-url origin git@github.com:USERNAME/USERNAME.github.io.git
git push origin master
```

В результате описанных выше действий будет скопирован Jeckyll bootstrap репозиторий и запушен в созданный ранее `USERNAME.github.io` репозиторий. Практически сразу после пуша страница по адресу `https://USERNAME.github.io` становится доступной. Поздравляю! На данной стадии мы уже имеем минимально функционирующий блог. 

### Тема

Прежде всего в глаза бросается тема (точнее ее отсутствие) по умолчанию. Для придания странице минимально читабельного вида можно установить одну из [bootstrap тем](http://themes.jekyllbootstrap.com/). Выбираем понравившеюся тему и в директории с исходниками блока запускаем команду:

```bash
rake theme:install git="https://github.com/jekyllbootstrap/THEME_NAME.git"
```

Для выполнения данной команды потребуется [Rack](https://github.com/ruby/rake) - билд-утилита для Ruby. В целом, наличие Ruby потребуется в дальнейшем для локального запуска сайта. После выполнения rake-команды было бы неплохо убедиться в том, что тема действительно применилась. Для этого мы можем запустить наш сайт локально:

```bash
gem install jekyll
bundle install
jekyll serve
```

По умолчанию сайт запустится на `localhost:4000`. Убедившись в успешности изменений, публикуем их в гит. Перейдем к следующим доработкам.

### Заголовок, главная страница

По умолчанию на сайте на главной странице находится небольшое руководство для новых пользователей Jekyll и статья с более детальным руководством. Прежде всего имеет смысл отредактировать `_config.yml` файл в корневой директории блога, переменные из которого используются для генерации сайта. Заменим заголовок (title), информацию об авторе на актуальные. Само содержимое главной страницы мы можем изменить, отредактировав `index.md` файл в корне проекта ([шпаргалка по markdown](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet)). Блог уже начинает приобретать некоторые очертания.

На главной странице по умолчанию находится список статей:

{% raw %}
```html
<ul class="posts">
  {% for post in site.posts %}
    <li>
      <span>{{ post.date | date_to_string }}</span> &raquo; 
      <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>
```
{% endraw %}

Данный кусок кода генерирует HTML со списком всех статей. Расширим его функциональность, добавив отображение первых N-слов статьи в режиме предпросмотра. Заменим приведенный выше кусок кода на следующий:

{% raw %}
```html
<ul >
    {% for post in site.posts limit 4 %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
        {{ post.content | strip_html | truncatewords:75}}<br>
            <a href="{{ post.url }}">Read more...</a><br><br>
    {% endfor %}
</ul>
```
{% endraw %}

Количество слов в предпросмотре можем изменить, поменяв параметр `truncatewords`.

### Комментарии

Последней доработкой предлагаю подключить комментарии к постам. Для этого регистрируемся на сайте [Disqus](https://disqus.com). Процесс регистрации аккаунта и сайта в нем довольно тривиален, поэтому опустим его.
В последнем шаге необходимо отредактировать `_layout/post.html` файл. Добавим следующий кусок кода в самый низ файла:

{% raw  %}
```html
{% if page.comments %}
<div id="disqus_thread"></div>
<script type="text/javascript">
  /* * * CONFIGURATION VARIABLES: EDIT BEFORE PASTING INTO YOUR WEBPAGE * * */
  var disqus_shortname = ''; // required: replace example with your forum shortname
  var disqus_identifier = "{{ page.url }}";

  /* * * DON'T EDIT BELOW THIS LINE * * */
(function() {
    var dsq = document.createElement('script'); dsq.type = 'text/javascript'; dsq.async = true;
    dsq.src = '//' + disqus_shortname + '.disqus.com/embed.js';
    (document.getElementsByTagName('head')[0] || document.getElementsByTagName('body')[0]).appendChild(dsq);
})();
</script>
<noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript">comments powered by Disqus.</a></noscript>
<a href="https://disqus.com" class="dsq-brlink">comments powered by <span class="logo-disqus">Disqus</span></a>
{% endif %}
```
{% endraw  %}

Переменной `disqus_shortname` укажем имя блога.

### P.S.

При написании этой статьи мне довольно пригодился raw Liquid-tag ([Liquid](http://shopify.github.io/liquid/) - язык шаблонизации для jekyll). Пример его применения можно найти [здесь](http://stackoverflow.com/a/13582517/3830108). Область внутри данного тега игнорируется генератором и в ней можно размещать liquid-код для демонстрации.

В целом на этом все, в случае улучшения блога буду писать об этом новые статьи или дополнять эту.
