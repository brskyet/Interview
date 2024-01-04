# Желтая контора
Что будет выведено в консоль?
```csharp
public class MyClass
{
    public int Num { get; set; }
}

public struct MyStruct
{
    public int Num { get; set; }
}

public class Program
{
    static void Main()
    {
        var myClassObj = new MyClass();
        var myStructObj = new MyStruct();

        MethodA(myClassObj.Num);
        MethodB(myStructObj);
        MethodC(myClassObj);
        MethodD(myClassObj);

        Console.WriteLine(myClassObj.Num); // ?
        Console.WriteLine(myStructObj.Num); // ?
    }

    private static void MethodA(int num)
    {
        num = num + 1;
    }

    private static void MethodB(MyStruct myStruct)
    {
        myStruct.Num += 1;
    }

    private static void MethodC(MyClass myClass)
    {
        myClass.Num += 1;
    }

    private static void MethodD(MyClass myClass)
    {
        myClass = new MyClass
        {
            Num = myClass.Num * 2
        };
    }
}
```
MethodA не изменит объект myClassObj т.к. значимые типы копируются при передаче как параметр.
MethodB также не изменит, т.к. структура значимый тип и тоже копируется
MethodC объект класса передается по ссылке и поэтому повлияет на оригинал
MethodD этот метод принимает ссылку на объект класса, но затем присваивает ей новый объект. Однако это присваивание не повлияет на внешний объект myClassObj, так как ссылка на объект передается по значению.
Таким образом myClassObj.Num = 1, myStructObj.Num = 0

---

Что будет выведено в консоль?
```csharp
static void Main()
{
    var s1 = "Text";
    var s2 = s1;
    var s3 = "Text";

    Console.WriteLine(s1 == s3); // ?

    s2 += " Changed";
    ChangeString(s1);

    Console.WriteLine(s1 == s2); // ?

}

private static void ChangeString(string str)
{
    str += " Changed";
}
```

-   Строки в C# являются неизменяемыми. Это означает, что при изменении строки создается новый объект строки, и ссылка обновляется для указания на новый объект.
-   Оператор == для строк сравнивает содержимое строк, а не ссылки на объекты.

1.  Сначала создается строка "Text" и ссылка `s1` указывает на нее. Затем `s2` также указывает на ту же строку, и `s3` создает новый экземпляр строки "Text". Но, так как строки в .NET интернируются (то есть одинаковые литеральные строки указывают на одну и ту же область памяти), `s1`, `s2` и `s3` все указывают на одну и ту же строку "Text" в памяти.
    
    `Console.WriteLine(s1 == s3);` - это сравнение будет `true`, поскольку содержимое `s1` и `s3` одинаково.
    
2.  Затем `s2` изменяется добавлением к ней " Changed", что приводит к созданию нового объекта строки. Теперь `s2` указывает на "Text Changed", в то время как `s1` все еще указывает на "Text".
    
3.  Метод `ChangeString` не изменяет исходную строку, на которую указывает `s1`. Вместо этого он создает новую строку в локальном контексте метода, но эта новая строка никак не влияет на `s1`.
    
    `Console.WriteLine(s1 == s2);` - это сравнение будет `false`, так как `s1` указывает на "Text", а `s2` указывает на "Text Changed".

---

Что произойдет в результате вызова кода?
```csharp
class Program
{
    static void Main(string[] args)
    {
        var childObj = new ChildClass();
        childObj.Method1(); // ?
        childObj.Method2(); // ?
        
        LogInfo(childObj);
    }

    private static void LogInfo(BaseClass obj)
    {
        obj.Method1(); // ?
        obj.Method2(); // ?
    }
}

public abstract class BaseClass
{
    public virtual void Method1()
    {
        Console.WriteLine("Base");
    }

    public void Method2()
    {
        Console.WriteLine("Base");
    }
}

public class ChildClass : BaseClass
{
    public override void Method1()
    {
        Console.WriteLine("Child");
    }

    public void Method2()
    {
        Console.WriteLine("Child");
    }
}

```

public void Method2() в ChildClass эквивалентно new public void Method2().

1. При выполнении `childObj.Method1();` вызывается переопределенный метод `Method1` из класса `ChildClass`, что приводит к выводу: *Child*
2. 2.  При выполнении `childObj.Method2();` вызывается метод `Method2` из класса `ChildClass` (поскольку он не переопределен в базовом классе и существует только в классе `ChildClass`), что приводит к выводу: *Child*
3. В методе `LogInfo`, передается объект типа `ChildClass`, но приведенный к базовому типу `BaseClass`.
	-   При вызове `obj.Method1();` будет использован механизм виртуальных методов и вызовется переопределенный метод `Method1` из класса `ChildClass`, что приводит к выводу: *Child*
4. При вызове `obj.Method2();` вызывается метод `Method2` из базового класса `BaseClass`, потому что метод `Method2` не виртуальный и не был переопределен в классе `ChildClass`, что приводит к выводу: *Base*

---

```csharp
class Program
{
    static void Main()
    {
        var list = new List<int>();

        var i = 10;

        var query = list.Where(x => x == i).Where(x => x < 20);

        list.Add(10);
        list.Add(15);
        list.Add(20);

        i = 15;

        var result = query.ToList();

        list.Clear();

        Console.WriteLine(result.Count); // ?
        Console.WriteLine(result.FirstOrDefault()); // ?
    }
}

```
LINQ выполняет запросы отложено, только когда запрашивается результат, в данном случае ToList().
При этом переменные в лямбда выражениях захватываются по ссылке. 
Таким образом значение i мы возьмем в тот момент, когда вызывается ToList(), т.е. 15. Вывод в консоль будет 1 15.

---

```csharp
class Program  
{  
    private static string message;  
  
    static void Main()  
    {        
	    SaySomething();  
        Console.WriteLine(message); // ?  
    }  
  
    static async Task<string> SaySomething()  
    {        
	    await Task.Delay(5);  
        message = "Hello world!";  
        return "Something";  
    }
}
```
В данном случае не выведется ничего не выведется т.к. мы не дожидаемся выполнения задачи и основной поток закончит выполнение раньше.
Спросили, как можно дождаться выполнения задачи, но без await. Варианты либо `SaySomething().Wait()`, либо `SaySomething().Result`, либо `SaySomething().GetAwaiter().GetResult()`.
Все три варианта заблокируют поток, но последний чуть более правильный, т.к. при возникновении исключения оно выбросится напрямую, а в иных случаях оно будет обёрнуто в `AggregateException`. Это исключение имеет свойство `InnerExceptions`, которое содержит коллекцию исключений, возникших во время выполнения задачи.

# Алготрейдинг контора
```csharp
using System.Diagnostics;  
using static System.Console;  
  
var data = Enumerable.Range(0, 5);  
  
async Task process(int x)  
{  
    Thread.Sleep(TimeSpan.FromSeconds(1)); // Stub for some synchronous work (e.g. serialization)  
  
    await Task.Delay(TimeSpan.FromSeconds(1)); // Stub for some asynchronous work (e.g. API request)  
};  
  
var stopwatch = Stopwatch.StartNew();  
  
await Task.WhenAll(data.Select(process));  
  
stopwatch.Stop();  
  
var elapsedSeconds = TimeSpan.FromMilliseconds(stopwatch.ElapsedMilliseconds).TotalSeconds;  
  
WriteLine(elapsedSeconds >= 1);  
WriteLine(elapsedSeconds >= 2);  
WriteLine(elapsedSeconds >= 5);  
WriteLine(elapsedSeconds >= 6);
```

Enumerable.Range(0, 5) - это коллекция из 5 элементов.
Т.к. до слова await код выполняется синхронно, а итерация по коллекции происходит последовательно, то при запуске каждой задачи мы блокируемся на секунду а затем происходит асинхронное ожидание на ещё секунду. Когда встречаем await - внешнему коду возвращается управление и запускается новая задача. Таким образом 5 секунд мы ждем Thread.Sleep и потом для последней задачи дожидаемся 1 секунду Task.Delay, итого **6 секунд**. При этом, если поменять местами Thread.Sleep и await Task.Delay, то время выполнения изменится и станет **2 секунды**, т.к. все задачи успеют запуститься параллельно.
Ещё интересный нюанс, что при втором варианте время так же зависит от ресурсов системы, количества ядер и т.д. Например, на моем ПК, если поставить `Enumerable.Range(0, 30)`, то время выполнения будет 4 секунды.

---

```csharp
using static System.Console;  
  
async Task call()  
{  
    throw new NotImplementedException();  
}  
  
Task task = Task.CompletedTask;  
  
try  
{  
    task = call();  
}  
catch (Exception caught)  
{  
    Console.WriteLine($"M0: {caught.GetType().Name}");  
}  
  
try  
{  
    await task;  
}  
catch (Exception caught)  
{  
    Console.WriteLine($"M1: {caught.GetType().Name}");  
}  
  
try  
{  
    task.Wait();  
}  
catch (Exception caught)  
{  
    Console.WriteLine($"M2: {caught.GetType().Name}");  
}
```

Вывод будет 
`M1: NotImplementedException`
`M2: AggregateException`

1. В первом блоке вызывается асинхронный метод `call()`, который сразу же выбрасывает исключение `NotImplementedException`. Однако, так как это асинхронный метод, исключение будет захвачено и сохранено в возвращаемой задаче (`task`), и не будет выброшено на этом этапе. Поэтому блок `catch` не будет выполнен, и в консоли ничего не выведется.
2. Во втором мы ожидаем завершения задачи с использованием `await`. Поскольку задача уже завершилась и содержит исключение, оно будет выброшено на этом этапе. В результате, блок `catch` выполнится, и в консоли будет напечатано:
3. В третьем мы используем метод `Wait()` для синхронного ожидания завершения задачи. Этот метод также выбросит исключение, содержащееся в задаче, но с одним отличием: исключение будет обернуто в `AggregateException`. Это происходит, когда мы вызываем `Wait()` или `Result`, в нем содержится список исключений всех задач, которые выполнялись в Task.

Был доп. вопрос, в первом блоке в каком потоке происходит работа в методе. Ответ - в основном, т.к. до await мы работаем с методом синхронно. 

---

```csharp
using static System.Console;

IEnumerable<int> iterate(int startingValue)
{
    if (startingValue is 2 or 5)
    {
        throw new ArgumentException("Starting from 2 or 5 is not allowed.");
    }

    while (true)
    {
        yield return startingValue++;
    }
}

try
{
    var fromOne = iterate(1);
    var fromTwo = iterate(2);

    var fromThree = iterate(3);
    WriteLine(
        string.Join(
            ',',
            fromThree.Take(4)));

    var fromFour = iterate(4);
    WriteLine(fromFour.Count());
}
catch { }
```

- fromOne создает IEnumerable коллекцию
- fromTwo также создает, но, т.к. нет обращения к элементам, то исключения выброшено не будет
- fromThree создает последовательность начиная с 3. Далее в консоль выведется 3,4,5,6
- при попытке вывести `fromFour.Count()` вылетит исключение OverflowException из-за переполнения int, т.к. будет бесконечная итерация

---

```csharp
using static System.Console;  
  
try  
{  
    var dictionary = new Dictionary<Key, int>();  
  
    var firstKey = new Key(5);  
    dictionary[firstKey] = 42;  
  
    var secondKey = new Key(5);  
    WriteLine($"M0: {dictionary[secondKey]}");  
}  
catch { }  
  
try  
{  
    var dictionary = new Dictionary<SKey, int>();  
  
    var firstKey = new SKey(5);  
    dictionary[firstKey] = 42;  
  
    var secondKey = new SKey(5);  
    WriteLine($"M1: {dictionary[secondKey]}");  
}  
catch { }  
  
try  
{  
    var dictionary = new Dictionary<RKey, int>();  
  
    var firstKey = new RKey(5);  
    dictionary[firstKey] = 42;  
  
    var secondKey = new RKey(5);  
    WriteLine($"M2: {dictionary[secondKey]}");  
}  
catch { }  
  
sealed class Key  
{  
    public long InnerKey { get; }  
  
    public Key(long value)  
    {        
	    InnerKey = value;  
    }  
    public override int GetHashCode()  
    {        
	    return HashCode.Combine(InnerKey);  
    }
}  
  
struct SKey  
{  
    public long InnerKey { get; }  
  
    public SKey(long value)  
    {        
	    InnerKey = value;  
    }
}  
  
sealed record RKey(long InnerKey)  
{  
    public override int GetHashCode()  
    {        
	    return base.GetHashCode();  
    }
}
```

Вывод:
1. Исключение `KeyNotFoundException`. Т.к. `GetHashCode()` вернет равенство, а equals будет проверять равенство ссылок в реализации по умолчанию
2. 42, т.к. структуры будут проверяться в equals по значению
3. Исключение `KeyNotFoundException`. По умолчанию в record автоматически переопределяют `GetHashCode` и `Equals`, чтобы сравнение проводилось на основе содержимого. Однако из-за переопределения `GetHashCode()` и вызова базовой версии, которая проверяет по ссылкам, объекты будут считаться разными.

Доп. вопрос, что если в RKey переопределить `GetHashCode()` так, чтобы он всегда возвращал `return 17;`. Ответ - тогда выведет 42, т.к. и `GetHashCode()` и `Equals()` вернут `true`.

# Букмекер контора
```csharp
// Наше приложение общается с удаленным сервисом: шлет запросы и получает ответы. 
// Каждый запрос содержит Id (уникальный Guid). Ответ на запрос содержит его же. 
public record Request(Guid Id/*, ещё какие-то поля*/);
public record Response(Guid Id/*, ещё какие-то поля*/);

// С удаленным сервером установлено единственное (мультиплексированное) соединение,
// по которому мы шлем запросы и получаем ответы.
// Взаимодействие с удаленным сервером осуществляется через INetworkAdapter.
// ❗️ INetworkAdapter не является потокобезопасным объектом.
// ❗️ Ответы на запросы могут приходить в произвольном порядке и с произвольными задержками.
public interface INetworkAdapter
{
    // Отправляет запрос
    Task WriteAsync(Request request, CancellationToken cancellationToken);

    // Вычитывает ответ
    Task<Response> ReadAsync(CancellationToken cancellationToken);
}

// Необходимо реализовать интерфейс IRequestProcessor, который абстрагирует факт такого
// мультиплексирования - отправили Request -> получили соответствующий ему  Response.
// ❗ Реализация IRequestProcessor обязана быть потокобезопасной.
public interface IRequestProcessor
{
    // ❗ При отмене CancellationToken не обязательно гарантировать то,
    // ❗ что мы не отправим запрос на сервер, но клиент должен получить отмену задачи
    Task<Response> SendAsync(Request request, CancellationToken cancellationToken);

    // Вспомогательный метод. Гарантированно вызывается 1 раз при старте приложения
    Task RunAsync(CancellationToken cancellationToken);
}

public class RequestProcessor : IRequestProcessor
{
    private INetworkAdapter Adapter {get;}
    private ConcurrentList List {get; set;}
    public RequestProcessor(INetworkAdapter adapter)
    {
        Adapter = adapter;
        List = new ConcurrentList<Request>();
    }    
    
    public async Task<Response> SendAsync(Request request, CancellationToken cancellationToken)
    {
        await Adapter.WriteAsync(request, cancellationToken);
        
        while(!cancellationToken.IsCancelled)
        {
            var result = await Adapter.ReadAsync(cancellationToken);
            List.Add(result);
            if(List.Contains(x => x.Id == request.Id))
            {
                List.Remove(y);
                return y;
            }
            
        }
    }
    
    public async Task RunAsync(CancellationToken cancellationToken)
    {
        
    }
}
```

Нужно написать реализацию IRequestProcessor, в которой будет использоваться INetworkAdapter. Общий алгоритм подразумевался следующий - отправляем запрос WriteAsync, затем вызываем ReadAsync. Если Response пришел не соответствующий тому, которого мы ожидали, то кладем в некую общую коллекцию и в цикле с проверкой на cancellationToken проверяем, появился ли нужный Response (его по логике должен положить когда-нибудь другой клиент, вызывающий этот же метод). Суть в том, что ReadAsync получает ответ рандомного запроса, не обязательно соответствующего запросу в WriteAsync.

---

Скомпилируется ли такой код
```csharp
lock (typeof(int))  
{  
    Console.WriteLine(typeof(int));  
}
```

Код скомпилируется, но делать так неправильно, т.к. используя объект, который мы сами не создавали, у нас нет гарантии, что на этот же объект не будет поставлен лок в другом месте. 
Напоминание, как работает typeof
```csharp
public class Animal { }

public class Giraffe : Animal { }

public static class TypeOfExample
{
    public static void Main()
    {
        object b = new Giraffe();
        Console.WriteLine(b is Animal);  // output: True
        Console.WriteLine(b.GetType() == typeof(Animal));  // output: False

        Console.WriteLine(b is Giraffe);  // output: True
        Console.WriteLine(b.GetType() == typeof(Giraffe));  // output: True
    }
}
```

Доп. вопросы:
Чем отличается GetType() от typeof()?
typeof - оператор для получения типа данных, известного во время компиляции, например, класса.
GetType() - вызывается как метод расширения на созданном объекте и возвращает тип данных во время выполнения программы.