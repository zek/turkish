# Dilimler

Dilimler verilen `T` türünde, `T[]` türü şeklinde gösterilen nesnelerdir .
Dilimler `T` değerinde bir dizinin alt kümesine bir bakış sağlar veya
sadece bütün diziyi işaret eder.
**Dilimler ve dinamik diziler aynıdır.**

Bir dilim, iki üyeden oluşur. Başlangıç noktasını işaret eden `ptr` ve
dilimin uzunluğunu ifade eden `length`.

    T* ptr;

    size_t length;
    //64 bit sistemlerde işaretsiz 64 bit
    //32 bit sistemlerde işaretsiz 32 bit

### new ifadesi ile dilim oluşturma

Eğer yeni bir dinamik dizi oluşturulduysa, yeni oluşturulan
bu alan için bir dilim döndürülür. 

    auto arr = new int[5];
    assert(arr.length == 5); // arr.ptr deki belleğe başvuruldu

Bu durumda tahsis edilen gerçek bellek tamamen çöp toplayıcı
tarafından yönetilmektedir. Döndürülen dilim altta yatan öğeler üzerinde
"görünüm" olarak işlev görür.

Actual allocated memory in this case is completely managed by the garbage
collector. The returned slice acts as a "view" on underlying elements.

### Varolan belleğe dilim almak


Bir dilimleme operatörü kullanarak, halihazırda var olan belleği işaret eden
bir dilim de elde edilebilir. Dilimleme operatörü başka bir dilime, statik
diziye, `opSlice` metoduna sahip yapılara/sınıflara ve diğer yapılara da
uygulanabilir.

Bu örnekteki `kaynak[başlangıç .. bitiş]` ifadesi, `kaynak` üzerinde
`başlangıç`tan `bitiş`e kadarki elemanlardan bir dilim oluşturur.

    auto yeniDizi = kaynak[1 .. 4]; // indeks 4 dahil değil.
    assert(yeniDizi.length == 3);
    yeniDizi[0] = 10; // yeniDizi[0]'ı yani kaynak[1]'i değiştirir.

Bu tür dilimler mevcut bellek üzerinde yeni bir görünüm sağlar.
Yeni bir kopya *oluşturmazlar*. Eğer hibir dilim bu belleği işaret
etmezse veya *dilim parçası*, bu alan çöp toplayıcı tarafından otomatik
olarak serbest bırakılacaktır.

Using slices, it's possible to write very efficient code for things (like parsers, for example)
that only operate on one memory block, and slice only the parts they really need
to work on. In this way, there's no need to allocate new memory blocks.

Bir [önceki bölümde](basics/arrays) gördüğünüz üzere, `[$]` ifadesi `dizi.length`
ifadesinin kısaltmasıdır. Bu yüzden `dizi[$]`, en son elemandan sonraki indeksi
işaret eder ve bu `RangeError` hatasına neden olur (sınır-denetimi kapalı değilse).

### Derinlemesine

- [Introduction to Slices in D](http://dlang.org/d-array-article.html)
- [Dilimler](http://ddili.org/ders/d/dilimler.html)

## {SourceCode}

```d
import std.stdio : writeln;

void main()
{
    int[] test = [ 3, 9, 11, 7, 2, 76, 90, 6 ];
    test.writeln;
    writeln("First element: ", test[0]);
    writeln("Last element: ", test[$ - 1]);
    writeln("Exclude the first two elements: ",
        test[2 .. $]);

    writeln("Slices are views on the memory:");
    auto test2 = test;
    auto subView = test[3 .. $];
    test[] += 1; // increment each element by 1
    test.writeln;
    test2.writeln;
    subView.writeln;

    // Create an empty slice
    assert(test[2 .. 2].length == 0);
}
```
