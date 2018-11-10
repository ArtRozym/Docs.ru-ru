В следующей таблице представлены параметры генератора кода ASP.NET Core.

| Параметр               | Описание:|
| ----------------- | ------------ |
| -m  | Имя модели |
| -dc  | Контекст данных |
| -udl | Использование макета по умолчанию |
| -outDir | Относительный путь к папке выходных данных для создания представлений |
| --referenceScriptLibraries | Добавляет `_ValidationScriptsPartial` для страниц редактирования и создания |

Чтобы получить справку по команде `aspnet-codegenerator razorpage`, используйте коммутатор `h`.

```console
dotnet aspnet-codegenerator razorpage -h
```

<a name="test"></a>

### <a name="test-the-app"></a>Тестирование приложения

* Запустите приложение и добавьте `/Movies` к URL-адресу в браузере (`http://localhost:port/Movies`).
* Протестируйте ссылку **Создать**.

  ![Страница "Создать"](../../tutorials/razor-pages/model/_static/conan.png)

<a name="scaffold"></a>

* Протестируйте ссылки **Изменить**, **Сведения** и **Удалить**.

Если появляется ошибка, аналогичная приведенной ниже, убедитесь, что выполнены миграции и база данных обновлена:

`An unhandled exception occurred while processing the request. 'no such table: Movie'.`
