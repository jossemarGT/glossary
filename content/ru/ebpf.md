---
title: eBPF
status: Completed
category: Technology
tags: ["architecture", "networking", "security"]
---

Технология eBPF (англ. extended Berkley Packet Filter — расширенный фильтр пакетов Беркли) 
позволяет небольшим программам или скриптам запускаться в пространстве ядра ОС Linux 
без необходимости изменять код ядра или загружать модули ядра.

В ОС Linux есть два пространства: пространство ядра (kernel space) и пространство пользователя (user space). 
Ядро представляет основу операционной системы и имеет неограниченный доступ к аппаратному обеспечению.

Приложения находятся в пространстве пользователя. 
Когда возникает необходимость в привилегированном доступе, приложения отправляют запрос к ядру.
Для приложений, требующих большей свободы действий, например, прямого доступа к аппаратному обеспечению, 
ядро может быть расширено за счёт модулей ядра Linux. 
Такой подход расширяет функциональность ядра, доступную по умолчанию, открывая приложениям доступ к более низкоуровневым компонентам.
Однако расширение ядра с помощью модулей влечет за собой риски и потенциально снижает безопасность, что делает eBPF привлекательной альтернативой.

## Какую проблему решает

Как правило, приложения работают в пространстве пользователя.
Если возникает необходимость в привилегированном доступе (например, к аппаратному обеспечению),
приложение отправляет запрос к ядру. такой запрос называется «системный вызов».
В большинстве случает такой подход прекрасно работает. 
Однако бывают ситуации, когда разработчики нуждаются в более низкоуровневом доступе к системе.
Такие ситуации, как правило, связаны с наблюдаемостью (observability), безопасностью и работой с сетью.
Использование модулей ядра Linux позволяет решить вышеупомянутую проблему без изменения исходного кода ядра.
К сожалению, у этого подхода есть не только плюсы, но и минусы (в первую очередь, связанные с безопасностью).
Модули ядра работают в пространстве ядра. Это означает, что сбой модуля неминуемо приводит к сбою ядра и, как следствие, падению всей системы.
Кроме того, модули ядра наделены повышенными привилегиями и имеют прямой доступ к системным ресурсам. Если не обеспечить должную защиту, этими свойствами модулей могут воспользоваться злоумышленники.

## Как именно решает проблему

В отличие от модулей ядра, eBPF позволяет запускать пользовательские программы в более контролируемой и ограниченной среде.
eBPF-программа работает в «песочнице» внутри ядра. Тем самым обеспечивается необходимая изоляция и снижаются риски.
Даже если в eBPF-программе обнаружится уязвимость или какой-то изъян, их воздействие ограничится «песочницей».
Более того, прежде чем программа сможет запуститься внутри ядра, она должна пройти процесс верификации.
Верификатор проверяет eBPF-программу на наличие потенциально небезопасного кода: например, доступ к памяти за пределами выделенного буфера, бесконечные циклы и неавторизованный доступ к функциям ядра.
Верификация гарантирует, что eBPF-программа не войдет в бесконечный цикл и не приведет к сбою ядра.
Такие меры безопасности делают eBPF более надежным вариантом запуска приложений в ядре по сравнению с модулями ядра Linux.

