# Изменить имя триггера в {{ sf-name }}

{% list tabs %}

- Консоль управления

    1. В [консоли управления]({{ link-console-main }}) перейдите в каталог, в котором находится триггер.
    1. Выберите сервис **{{ sf-name }}**.
    1. На панели слева выберите ![image](../../../_assets/functions/triggers.svg) **Триггеры**.
    1. Выберите триггер, имя которого хотите изменить.
    1. В правом верхнем углу страницы нажмите **Редактировать**.
    1. Измените имя и нажмите кнопку **Сохранить**.

- CLI

    {% include [cli-install](../../../_includes/cli-install.md) %}

    {% include [default-catalogue](../../../_includes/default-catalogue.md) %}

    Чтобы изменить имя триггера, выполните команду:

    ```bash
    yc serverless trigger update <имя триггера> --new-name <новое имя триггера>
    ```

    Результат:

    ```text
    id: dd0gj5tsj2**********
    folder_id: aoek49ghmk**********
    created_at: "2019-08-28T12:26:25.675Z"
    name: my-trigger
    rule:
      message_queue:
        queue_id: yrn:yc:ymq:{{ region-id }}:aoek49ghmk**********:my-mq
        service_account_id: bfbqqeo6jk**********
        batch_settings:
          size: "10"
          cutoff: 10s
        invoke_function:
          function_id: b09e5lu91t**********
          function_tag: $latest
          service_account_id: bfbqqeo6jk**********
    status: ACTIVE
    ```

- API

    Изменить имя триггера можно с помощью метода API [update](../../triggers/api-ref/Trigger/update.md).

- {{ TF }}

  Информацию о {{ TF }} [читайте в документации](../../../tutorials/infrastructure-management/terraform-quickstart.md#install-terraform).

  {% include [terraform-definition](../../../_tutorials/terraform-definition.md) %}

  Чтобы изменить имя триггера:

  1. Откройте файл конфигурации {{ TF }} и измените поле `name` в описании триггера:

     ```hcl
     ...
     resource "yandex_function_trigger" "my_trigger" {
       name        = "some_name"
       description = "any description"
       timer {
         cron_expression = "* * * * ? *"
       }
       function {
         id = "tf-test"
       }
     }
     ...
     ```

     Более подробную информацию о параметрах ресурса `yandex_function_trigger` в {{ TF }} см. в [документации провайдера]({{ tf-provider-link }}/function_trigger).

  1. Проверьте конфигурацию командой:

     ```
     terraform validate
     ```
     
     Если конфигурация является корректной, появится сообщение:
     
     ```
     Success! The configuration is valid.
     ```

  1. Выполните команду:

     ```
     terraform plan
     ```
  
     В терминале будет выведен список ресурсов с параметрами. На этом этапе изменения не будут внесены. Если в конфигурации есть ошибки, {{ TF }} на них укажет.

  1. Примените изменения конфигурации:

     ```
     terraform apply
     ```
     
  1. Подтвердите изменения: введите в терминал слово `yes` и нажмите **Enter**.

     Проверить изменение триггера можно в [консоли управления]({{ link-console-main }}) или с помощью команды [CLI](../../../cli/quickstart.md):

     ```
     yc serverless trigger get <идентификатор триггера>
     ```

{% endlist %}