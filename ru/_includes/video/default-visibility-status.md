По умолчанию все видео после загрузки опубликовываются — переходят в состояние `{{ ui-key.yacloud_video.videos.status_published }}`.

Если вы хотите [снять видео с публикации](../../video/operations/video/unpublish.md), выключите опцию **{{ ui-key.yacloud_video.videos.label_allow-access }}**. Значение поля `{{ ui-key.yacloud_video.videos.label_visibility-status }}` изменится на `{{ ui-key.yacloud_video.videos.status_unpublished }}`, а видео станет недоступно по [ссылкам](../../video/operations/video/get-link.md).