1. Процент заполнения хранилища, при котором хранилище будет увеличено в следующее окно обслуживания, в параметре `configSpec.diskSizeAutoscaling.plannedUsageThreshold`.

    Значение задается в процентах от `0` до `100`. По умолчанию — `0` (автоматическое расширение отключено).

1. Процент заполнения хранилища, при котором хранилище будет увеличено немедленно, в параметре `configSpec.diskSizeAutoscaling.emergencyUsageThreshold`.

    Значение задается в процентах от `0` до `100`. По умолчанию — `0` (автоматическое расширение отключено). Если значение больше `0`, то оно не может быть меньше значения, переданного в параметре `configSpec.diskSizeAutoscaling.plannedUsageThreshold`.

1. Новый размер хранилища (в байтах), который будет установлен при достижении одного из заданных процентов заполнения, в параметре `configSpec.diskSizeAutoscaling.diskSizeLimit`.