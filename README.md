# Dalak Async Task


1. [Semafor](https://github.com/furkandalak/Examples/blob/main/README.md#semafor-semaphore)
2. [Asenkron](https://github.com/furkandalak/Examples/blob/main/README.md#asenkron-asynchronous)
   1. [Await](https://github.com/furkandalak/Examples/blob/main/README.md#await)
3. [Thread Lock](https://github.com/furkandalak/Examples/blob/main/README.md#thread-lock)
4. [Thread Pool](https://github.com/furkandalak/Examples/blob/main/README.md#thread-pool)
5. [Thread.Run()](https://github.com/furkandalak/Examples/blob/main/README.md#taskrun)
6. [Thread VS Thread.Run()](https://github.com/furkandalak/Examples/blob/main/README.md#taskrun-vs-thread)
7. [Fiber Thread](https://github.com/furkandalak/Examples/blob/main/README.md#fiber-thread)
8. [Race Condition](https://github.com/furkandalak/Examples/blob/main/README.md#race-condition)

## Semafor (Semaphore)[^1]


Semafor senkronizasyon mekanizmalarından biridir ve çokly iş parçacığı veya çoklu işlemci ortamlarında kaynaklara erişimi kontrol etmek için kullanılır.

Belirli bir kaynağa aynı anda kaç iş parçacığının erişebileceğini kontrol eder.

Semafor bir sayaç ve bir kuyruk içerir. Sayaç, aynı anda izin verilen iş sayısını, kuyruk, sayaç doluyken sonraki işlemleri tutar.

1. Bir iş parçacığı semafora erişim talebinde bulunur.
2. Semafor sayaç değerini kontrol eder.
   1. Eğer sayaç sıfırsa, iş parçacığı beklemeye alınır, kuyruğa eklenir.
   2. Eğer sayaç sıfır değilse, sayaç bir azaltılır ve iş parçacığı kaynağı kullanmaya başlar.
5. İş parçacığı kaynağı kullandıktan sonra semafora kaynağı bıraktığını bildirir.
6. Bekleyen diğer iş parçacıklarından biri, bıraklıan kaynağı alır ve sayaç artar.

- Wait ve Release Metotları: Wait semafora erişim talep eder ve kaynağı kullanmaya başlar, Release kaynağı bırakarak diğer iş parçacıklarının kullanabilmesini sağlar
- FIFO Kuyruk: Bekleyen iş parçacıkları düzenlenir.

### Örnek
```
using System;
using System.Threading;

class Program
{
    static Semaphore semaphore = new Semaphore(2, 2); // İzin verilen iş parçacığı sayısı: 2

    static void Main()
    {
        Thread t1 = new Thread(Worker);
        Thread t2 = new Thread(Worker);

        t1.Start();
        t2.Start();

        t1.Join();
        t2.Join();

        Console.WriteLine("İşlem tamamlandı.");
    }

    static void Worker()
    {
        Console.WriteLine($"İş parçacığı {Thread.CurrentThread.ManagedThreadId} çalışmaya başladı.");

        // Kaynağa erişim talebi
        semaphore.WaitOne();

        Console.WriteLine($"İş parçacığı {Thread.CurrentThread.ManagedThreadId} kaynağı kullanıyor.");

        // Simüle edilmiş bir işlem süresi
        Thread.Sleep(2000);

        Console.WriteLine($"İş parçacığı {Thread.CurrentThread.ManagedThreadId} kaynağı kullanımını tamamladı.");

        // Kaynağı bırakma
        semaphore.Release();
    }
}
```

[Microsoft](https://learn.microsoft.com/en-us/dotnet/api/system.threading.semaphore?view=net-8.0#examples)

### Join();
Thread nesnesinin tamamlanmasını beklemek için kullanılan bir metotdur. 

Join ile kullanılan iş parçacıklarının işlerinin tamamlamadan önce ana iş parçacığının devam etmesini engeller.

[Microsoft Join](https://learn.microsoft.com/tr-tr/dotnet/api/system.threading.thread.join?view=net-8.0#system-threading-thread-join)

## Critical Section (Kritik Bölge)
Çoklu iş parçacığı programlamasında ve senkronizasyon konseptinde önemli bir terimdir. 

Kritik Bölge, aynı anda sadece bir iş parçacığının erişmesine izin verilen kod bloğunu ifade eder.

Bu bölgeler, paylaşılan kaynaklara güvenli bir şekilde erişim sağlayarak eş zamanlılık sorunlarını önlemek için kullanılır.

Temel amacı paylaşılan verilere paralel erişimi kontrol etmek ve güvenli bir ortam sağlamaktır. [Race Condition](https://github.com/furkandalak/Examples/blob/main/README.md#race-condition)'u engeller.

1. Mutual Exclusion: Kritik bölge, aynı anda sadece bir iş parçacığının içine girebildiği bir yapıdır.
2. Veri Bütünlüğü: Kritik Bölge içindeki kodun çalışması tamamlanınca, veri bütünlüğü korunmuş olur.

```
lock (lockObject)
{
    // Kritik bölgeye ait kod
    // ...
    // Paylaşılan verilere güvenli bir şekilde erişim
}
```

## Priority Inversion (Öncelik Çevirme)
Gerçek zamanlı sistemlerde ortaya çıkabilen bir durumdur ve öncelik tabanlı bir işletim sistemi ortamında meydana gelir.

Normalde daha yüksek önceliğe sahip bir iş parçacığının daha düşük bir önceliğe sahip bir iş parçacığı tarafından bloke edilmesini ifade eder.

> [!WARNING]
> Düşük öncelikli iş parçacığı ortak bir Kritik Bölge'de işlem yapmıyorsa Priority Inversion problemi yaşanmaz.

### Örnek

Bu örnek için Mutex senkronizasyon metodu kullanılmaktadır.

- Yüksek Öncelikli İş Parçacığı (High Priority Task) > **H**
- Orta Öncelikli İş Parçacığı (Medium Priority Task) > **M**
- Düşük Öncelikli İş Parçacığı (Low Priority Task) > **L**
- Kritik Bölge (Critical Section) > **CS**

**L ve H aynı CS'i paylaşmaktadır. M paylaşmamaktadır.**

*L < M < H*

1. **L** *CS* içinde çalışmaktadır.
2. **H** başlatılır ve *CS*'e erişim talep eder, **L**'nin *CS*'den çıkmasını bekler.
3. **M** başlatılır.
4. **M** **L**'yi durdurur ve işlemine başlar.
5. **M** tüm işlemini gerçekleştirir ve bitirir.
6. **L** kaldığı yerden devam eder.
7. **L** *CS*'den çıkar ve **H** *CS*'e girer.

Görüldüğü üzere bu durumda **M**, **L** ve **H** iş parçalarını geciktirmiştir. **H** daha yüksek öncelikli olmasına ve **M** ile aynı *CS*'i paylaşmamasına rağmen beklemiştir.

## Asenkron (Asynchronous) 
İşlemin başka bir işlemin tamamlanmasını beklemeden devam edebilmesi yeteneği 
 
Bir işlemin sonucu beklenirken başka bir işlem devam edebilir. Örn.: Uzun süreli işlemler, dosya okuma, ağ çağrıları/bağlantıları beklenirken program başka yanıtlar verebilir. 
 
### Await 
Asenkron görevin tamamlanmasını bekletir. 
```
async Task<int> LongRunningOperationAsync()
{
    // Uzun süren bir işlem simülasyonu
    await Task.Delay(2000);
    
    return 42;
}

async void MyAsyncMethod()
{
    Console.WriteLine("İşlem başlıyor...");

    int result = await LongRunningOperationAsync();

    Console.WriteLine("İşlem tamamlandı. Sonuç: " + result);
}
```

## Thread Lock 
Ortak kaynağa aynı anda birden fazla iş parçasının erişimin engellemeye yarayan mekanizmadır. 

Eş zamanlılık sorunlarını, [Race Condition](https://github.com/furkandalak/Examples/blob/main/README.md#race-condition)’ları ve veri bütünlüğü sorunlarını engellemeye yardımcı olur. 

* Locking: İş parçacığı belirli bir kaynağa erişim sağlarken locklamaya çalışır 
* Lock Check: O kaynağa erişilemiyorsa iş parçacığı bu kaynağı kullanabilir veya kilitleyebilir 
* Lock Wait: Eğer kaynağa zaten bir erişim varsa kilit açılana kadar bekler 
* Lock Release: İş parçacığı kullanımı tamamladığında serbest bırakır 



### Locking Örneği
```
private object lockObject = new object();

public void ExampleMethod()
{
    // Kilit alma işlemi
    lock (lockObject)
    {
        // Kritik bölgeye ait işlemler
    }
    // Kilit serbest bırakma işlemi
}
```

[Detaylı Örnek](https://github.com/furkandalak/Examples/blob/main/Locking%20Example)

[Microsoft](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/statements/lock#example)

## Thread Pool 
Sistem tarafından kullanılacak olan işlemlerin daha etkili yönetilmesi ve kullanılması için tasarlanmış bir mekanizmadır.  

![](https://github.com/furkandalak/Examples/blob/main/400px-Thread_pool.svg.png)

Bu sistem belirli ve sınırlı sayıda iş parçası oluşturur  
1. İlk oluşturmada belirli sayıda iş parçası oluşturur 
2. İş parçası istendiğinde pooldan mevcut boş iş parçacığı atanır, yoksa oluşturulur 
3. İş parçacığının görevi tamamlandığında poola geri gönderilir  
4. Sınırlı sayıda iş parçacığı tutar ve fazladan iş parçacığı oluşturma önüne geçer 

Avantajlar: 

- Performans: İş parçacığının oluşturma ve sonlandırma maaliyetini azaltır. 
- Kaynak Yönetimi: İş parçacıkları etkili yönetilebilir ve kaynakları verimli kullanır. 
- Asenkron: Task-Based Async/Await operasyonlarını destekler. 
- Kuyruk: Kuyruk sistemi içinde düzenler, belirli sırayla önceliklendirme yapılabilir 

Wikipedia [Thread Pool](https://en.wikipedia.org/wiki/Thread_pool#:~:text=In%20computer%20programming%2C%20a%20thread,execution%20by%20the%20supervising%20program.), [Embarrassingly Parallel](https://en.wikipedia.org/wiki/Embarrassingly_parallel)

[Microsoft](https://learn.microsoft.com/tr-tr/dotnet/api/system.threading.threadpool?view=net-8.0#examples)

[Örnek](https://github.com/furkandalak/Examples/blob/main/Thread%20Pool) 

## Task.Run()
C#'da Task Parallel Library ([TPL](https://learn.microsoft.com/en-us/dotnet/standard/parallel-programming/task-parallel-library-tpl)) kapsamında sunulan bir yöntemdir.

```
Task.Run(() =>
{
    // Asenkron çalışacak kod bloğu
});
```

- Asenkron Olay Başlatma: İçine aldığı metodu asenkron olarak başlatır ve bir "Task" döndürür.
- Task Döndürme: Bir "Task" döndürür ve bu sayede operasyonun tamamlanma durumu takip etmek ve sonucu ele almak mümkündür.
- Kullanım: Async/Await anahtar kelimeleri ile kullanılarak okunurluk artırılır.
- Thread Pool: İş parçacığı havuzunu kullanarak operasyonları çalıştırır.

> [!WARNING]
> ```Task.Run()``` her zaman daha iyi performans vermeyebilir. Küçük ve hızlı işlemler i.in doğrudan asenkron yöntemler tercih edilebilir.

Detaylı Örnek [1](https://github.com/furkandalak/Examples/blob/main/Task%20Run%201), [2](https://github.com/furkandalak/Examples/blob/main/Task%20Run%202)

## Task.Run() VS Thread

1. Thread
   - Doğrudan iş parçacığı oluşturmayı sağlar.
   - İş parçacığı oluşturmak. yönetmek ve sonlandırmak direkt geliştiricinin sorumluluğundadır.
   - Düşük seviyelidir. İş parçacığını manuel kontrol etme imkanı verir.
   - Genellikle düşük seviyeli senkronizasyon mekanizmalarını gerektirir. Örnek: Lock, Mutex.
2. Task.Run()
   - .NET Task Parallel Library tarafından sağlanan yüksek seviyeli API'dır.
   - .NET çalışma zamanı thread pool'u yönetimi ve iş parçacığı oluşturma gibi karmaşık görevleri otomatik yönetir.
   - Asenkron programlamayı destekler ve Async/Await kullanımını kolaylaştırır.
   - İş parçacıkları arasında veri paylaşımını kontrol etmeyi sağlayan yüksek seviyeli senkronizasyon özelliklerini içerir.
  
### Thread:
#### Avantajlar
- Düşük seviyeli kontrol
- Özelleştirilmiş senkronizasyon durumlarına uygun
- Doğrudan iş parçacığı oluşturmak/kontrol etmek isteyen geliştiricilere esneklik
#### Dezavantajlar
- Daha fazla manuel yönetim ve potansiyel hatalar
- Daha fazla kaynak tüketebilir, iş parçacığı oluşturup yönetme maliyeti
### Task.Run():
#### Avantajlar
- Yüksek seviyeli bir API sunar ve iş parçacığı kontrolünü otomatikleştirir
- Asenkron programlamayı destekler, verimli ve okunabilir kod yazmaya yardımcı olur
- Task'ler thread pool'u etkili kullanarak performans artırabilir
#### Dezavantajlar
- Daha fazla soyutlamayla gelir, geliştiricinin kontrolünü niraz kaybetmesine neden olabilir
- Daha karmaşık senkronizasyon senaryolarında kullanımı zor olabilir

Tercih, uygulamanın ihtiyaçlarına, ölçeklerine, gereksinimlerine ve geliştiricinin tercihine bağlıdır. Genellikle modern C# uygulamalarında Task.Run() ve Task-Based asenkron programlama daha yaygındır.

## Fiber Thread 
C# içinde doğrudan kullanılan bir terim değil. Daha hafif iş parçalarına verilen isim. .NET Core 2.1 sonrasında 
```BoundedCapacity (System.Threading.Tasks.DataFlow)``` konsepti var. 

[Microsoft](https://learn.microsoft.com/en-us/windows/win32/procthread/fibers)

## Race Condition
Birden fazla iş parcacığının aynı anda bir kaynağa erişmeye çalıştığı durumu ifade eder. Bu durum kontrol edilmeze veri bütünlüğü sorunları ortaya çıkabilir.

Wikipedia [Örnek](https://en.wikipedia.org/wiki/Race_condition#Example)

## Ekstra
Pathfinder olayı [TR](https://medium.com/@gokhansengun/mars-ke%C5%9Fif-arac%C4%B1-pathfinderdaki-i%CC%87lgin%C3%A7-yaz%C4%B1l%C4%B1m-problemi-5b6ebe771d55), [EN](https://www.rapitasystems.com/blog/what-really-happened-software-mars-pathfinder-spacecraft)


[^1]: (*Fransızca sémaphore*) Demir yollarında gündüz mekanik olarak kırmızı bir kolla, gece kırmızı ışıkla işaret veren alet.
İki gemi veya gemi ile kıyı istasyonu arasında haberleşmede kullanılan üç kollu işaret sütunu.
