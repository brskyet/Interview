# SOLID принципы:

## The Single Responsibility Principle (SRP) — Принцип единственной обязанности. У класса должна быть только одна причина для изменения.

Под обязанностью здесь понимается набор функций, которые выполняют единую задачу. Главная парадигма этого принципа заключается в том, что класс должен выполнять одну единственную функцию. Весь функционал класса должен быть целостным, обладать высокой связностью (high cohesion).

## The Open Closed Principle (OCP) — Принцип открытости/закрытости Программные сущности (классы, модули, функции и т. п.) должны быть открыты для расширения, но закрыты для модификации.

У модулей, согласованных с принципом OCP, есть две основных характеристики:

1.  Они открыты для расширения. Это означает, что поведение модуля можно расширить. Когда требования к приложению изменяются, мы добавляем в модуль новое поведение, отвечающее изменившимся требованиям. Иными словами, мы можем изменить состав функций модуля.
    
2.  Они закрыты для модификации. Расширение поведения модуля не сопряжено с изменениями в исходном или двоичном коде модуля. Двоичное исполняемое представление модуля, будь то компонуемая библиотека, DLL или EXE-файл, остается неизменным.
    

## The Liskov Substitution Principle (LSP) — Принцип подстановки Лисков. Должна быть возможность вместо базового типа подставить любой его подтип.

То есть иными словами класс S может наследоваться от T, если замена объектов T на объекты S не приведет к изменению работы программы. Смысл в том, что мы можем только расширять функционал классов, но не заменять реализацию методов. Иными словами проектировать надо таким образом, чтобы тем, кто наследуется от класса не приходилось кардинально изменять что-либо или в целом заменять реализацию на выбрасывание исключения. Условно, если в базовом классе Animal если метод Fly и в дочернем классе Dog нам придется выбрасывать из этого метода исключение, то проектировка неправильная и надо было метод Fly выносить куда-то ниже в иерархии. 

## The Interface Segregation Principle (ISP) — Принцип разделения интерфейсов. Клиенты не должны вынужденно зависеть от методов, которыми не пользуются. Интерфейсы принадлежат клиентам, а не иерархиям.

Клиенты не должны вынужденно зависеть от методов, которыми не пользуются.

При нарушении этого принципа клиент, использующий некоторый интерфейс со всеми его методами, зависит от методов, которыми не пользуется, и поэтому оказывается восприимчив к изменениям в этих методах. В итоге мы приходим к жесткой зависимости между различными частями интерфейса, которые могут быть не связаны при его реализации.

В этом случае интерфейс класса разделяется на отдельные части, которые составляют раздельные интерфейсы. Затем эти интерфейсы независимо друг от друга могут применяться и изменяться. В итоге применение принципа разделения интерфейсов делает систему слабосвязанной, и тем самым ее легче модифицировать и обновлять.

## The Dependency Inversion Principle (DIP) — Принцип инверсии зависимости. Абстракции не должны зависеть от деталей. Детали должны зависеть от абстракций.

# Другие принципы:

## Don’t Repeat Youself (DRY)

расшифровывается как Don’t Repeat Youself — не повторяйся, также известен как DIE — Duplication Is Evil. Этот принцип заключается в том, что нужно избегать повторений одного и того же кода. Лучше использовать универсальные свойства и функции.

## Keep It Simple, Stupid! (KISS) 

KISS - не усложняй! Смысл этого принципа программирования заключается в том, что стоит делать максимально простую и понятную архитектуру, применять шаблоны проектирования и не изобретать велосипед.

## You Ain’t Gonna Need It YAGNI 

Не добавляйте функциональность пока она вам не нужна. Вы только потратите время на реализацию, отладку, тестирование и поддержку кода который может и не использоваться.

## Release Equivalence Principle (REP) — Принцип эквивалентности повторного использования и выпуска Единица повторного использования равна единице выпуска.

принцип REP гласит, что единица повторного использования, компонент, не может быть меньше единицы выпуска. Все, что предназначено для повторного использования, должно управляться какой-то системой учета выпусков. Не может быть так, чтобы разработчик просто написал какой-то класс и объявил его повторно используемым. О возможности повторного использования можно говорить только тогда, когда внедрена некая система учета выпусков и обеспечены га- рантии извещения, надежности и поддержки, в которых так нуждаются потенциальные пользователи.

## Сommon Closure Principle (CCP) — Принцип общей закрытости Все классы внутри пакета должны быть закрыты относительно изменений одного и того же вида. Изменение, затрагивающее пакет, должно затрагивать все классы в этом пакете и только в нем.

Принцип CCP рекомендует собирать в одном месте классы, которые могут изменяться по одним и тем же причинам. Если два класса настолько тесно связаны, физически или концептуально, что всегда изменяются вместе, то их естественно поместить в один компонент. Это уменьшит трудозатраты на повторный выпуск, проверку и распространение ПО. Этот принцип неотъемлем от принципа открытости/закрытости (OCP). Ибо именно о «закрытости» в смысле OCP и идет речь в CCP. OCP гласит, что классы должны быть закрыты для модификации, но открыты для расширения. Но, как мы узнали, достичь стопроцентной закрытости невозможно. Закрытость должна быть стратегической целью. Мы проектируем систему так, чтобы она была закрыта относительно ти- пичных изменений, с которыми нам приходилось ранее сталкиваться

## Сommon Reuse Principle (CRP) — Принцип совместного повторного использования Все классы внутри компонента используются совместно. Если вы можете повторно использовать один класс, то можете использовать и все остальные.

Классы редко используются изолированно. Обычно повторно исполь- зуемые классы кооперируются с другими классами, являющимися частями одной и той же абстракции. CRP утверждает, что такие классы должны входить в один компонент. Естественно ожидать, что между классами в этом компоненте будет много взаимных зависимостей. Простой пример – класс контейнера и сопутствующие ему итераторы. Эти классы используются совместно, потому что они тесно связаны. И, значит, должны принадлежать одному компоненту.

## Acyclic Dependencies Principle (ADP) — Принцип ацикличности зависимостей В графе зависимостей между пакетами не должно быть циклов.

## Stable Dependencies Principle (SDP) — Принцип устойчивых зависимостей Зависимости должны быть направлены в сторону устойчивости.

 SDP говорит, что от модулей, которые изначально спроектированы так, чтобы их легко можно было изменять, не должны зависеть модули, изменение которых затруднено. Это так называемые устойчивые модули.

## Stable Abstractions Principle (SAP) — Принцип устойчивых абстракций Пакет должен быть столь же абстрактным, сколь и устойчивым

Этот принцип устанавливает соотношение между устойчивостью и абстрактностью. Он говорит, что устойчивый компонент должен быть также и абстрактным, чтобы устойчивость не препятствовала его расширению. Если компонент предполагается сделать устойчивым, то он должен состоять из абстрактных классов, допускающих расширение. Расширяемые устойчивые компоненты оказываются гибкими и не налагают чрезмерных ограничений на дизайн. В сочетании принципы SAP и SDP дают аналог принципа DIP для ком- понентов. Действительно, SDP говорит, что зависимости должны быть направлены в сторону устойчивости, а SAP – что из устойчивости вытекает абстрактность. Следовательно, зависимости направлены в сторону абстрактности.