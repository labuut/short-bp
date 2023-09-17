# short-bp
Sass helper to write sass code with big amount of breakpoints fast

---
# recommendations on the use

В short-bp появились новые миксины, которые помогут легко и просто описывать даже большие селекторы для многих разрешений в плоском стиле, основанном на объектах (мапах):

```scss
mobile-props(mobile-props($args-map);
desktop-props(mobile-props($args-map);
all-props(mobile-props($args-map);
```
Все они внутри просто вызывают миксину restricted-props с различными параметрами, но это не так важно.

## Общий принцип описания селекторов:
```scss
.className {
    @include mobile-props((
        // статичные свойства
        font-size: 12px,
        color: red,

        // изменяющиеся свойства
        width: (
            360: 16px,
            400: 14px,
        ),
        height: (
            0: 14px,
            360: 16px,
        ),
    ));
    @include desktop-props((
        font-size: 22px,
        width: (
            1024: 16px,
            1366: 14px,
        ),
        height: (
            1366: 14px,
            2048: 16px,
        ),
    ));
}

.someResponsiveElment {
    @include responsive-mobile-props(375px, (
        width: 200px,
        font-size: 14px,
    ));

    @include responsive-desktop-props(1720px, (
        width: 700px,
        font-size: 18px,
    ));
}
```

## Основные пункты:
не определять стили просто внутри селектора, а обязательно задавать их область действия через миксины mobile/desktop/all-props;

Использовать responisve-миксины для числовых значений css-свойств, которые должны быть масштабироваться в зависимости от ширины вьюпорта

использовать all-props в редких случаях: на страницах лендингов, для срочной верстки и для описания стилей, явно относящихся ко всем разрешениям (например для задания параметров анимаций);

Пример:
```scss
.className {
    @include mobile-props((
        font-size: 12px,
        width: (
            360: 16px,
            400: 14px,
        ),
    ));
    @include desktop-props((
        font-size: 22px,
        width: (
            1024: 16px,
            1366: 14px,
        ),
    ));
    @include all-props((
        transition: width 500ms;
    ));
}
```

## Границы действия:
При использовании этих миксин можно больше не бояться, что мобильные стили при изменении изменят десткопные или наоборот, т.к. при определении стилей таким образом они действуют только на те разрешения (mobile/desktop/all-props), для которых заданы (главное, правильно определить max/min-ширины для мобильных/десктопных разрешений в файле const.scss):
```scss
.className {
    @include mobile-props(( // действует только на мобильные разрешения
        width: (
            360: 16px,
            1440: 14px, // работать не будет
        ),
    ));
}
```

## Вложенные селекторы:
В миксинах mobile/desktop/all-props/responsive-* нельзя определять вложенные  селекторы/псевдоэлементы/псевдоклассы. В этом случае их нужно выносить на уровень основного селектора и также использовать mobile/desktop/all-props внутри вложенного элемента:

```scss
.className {
    @include mobile-props((
        width: (
            360: 16px,
            400: 14px,
        ),
    ));

    // вложенный селектор
    &:last-child {
        @include mobile-props((
            width: (
                360: 26px,
                400: 24px,
            ),
        ));
    }
}
```
