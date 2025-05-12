# Union-Find (Disjoint Set) Algoritması

## Algoritmanın Amacı

Union-Find algoritması, bir veri kümesindeki elemanların hangi alt gruba (kümeye) ait olduğunu verimli bir şekilde takip etmek için kullanılır. Temel olarak kümeleri birleştirme (union) ve bir elemanın hangi kümeye ait olduğunu bulma (find) işlemlerini sağlar.

## Algoritma Yapısı ve Türü

Union-Find bir veri yapısı algoritmasıdır. Genellikle açgözlü (greedy) algoritmalarla birlikte çalışır. İki temel işlevi vardır:

- *Find(x)*: x elemanının ait olduğu kümenin temsilcisini (kökünü) bulur.
- *Union(x, y)*: x ve y elemanlarının ait olduğu kümeleri birleştirir.

## Kullanım Alanları

- *Minimum Spanning Tree algoritmaları* (örneğin Kruskal)
- *Dinamik bağlantı takibi*
- *Sosyal ağlarda topluluk belirleme*
- *Kümeleme işlemleri*
- *Oyunlarda grup/lonca yapılarının yönetimi*

## Örnek Senaryo: Kruskal ile Kullanım

Aşağıdaki gibi bir grafik üzerinde minimum ağırlıklı döngüsüz bir yapı (MST) oluşturmak için:

Kenarlar: (0-1):1, (1-3):3, (2-3):2, (0-2):4

Kenarlar ağırlığa göre sıralanır ve en düşükten başlanarak Union-Find kullanılarak döngü oluşturulmadan kenarlar seçilir.

## Örnek Senaryo: Sosyal Ağda Arkadaşlık Bağlantıları

Bir sosyal ağda, kullanıcılar arasında arkadaşlık ilişkilerini takip etmek için Union-Find algoritması kullanılabilir. Diyelim ki 6 kullanıcı var ve aralarındaki bazı arkadaşlıklar şu şekilde:

- 0 ve 1 arkadaş
- 2 ve 3 arkadaş
- 4 ve 5 arkadaş
- 1 ve 2 arkadaş

Bu bağlantılarla, aşağıdaki işlemleri gerçekleştirebiliriz:

- 0 ve 2'nin aynı arkadaş grubunda olup olmadığını kontrol etmek için find(0) ve find(2) kullanırız. Bu durumda, 0 ve 2'nin farklı kümelerde olduğu görülür.
- unionSets(0, 2) işlemi ile 0 ve 2'nin arkadaşlık grubunu birleştirebiliriz.
- Sonra, connected(0, 3) ile 0 ve 3'ün aynı grupta olup olmadığını kontrol edebiliriz. Bu durumda, 0 ve 3 artık aynı gruptadır çünkü 0 ve 2 arkadaş olmuştu.

## Zaman ve Bellek Karmaşıklığı

| İşlem     | Zaman Karmaşıklığı | Bellek Kullanımı |
|-----------|--------------------|------------------|
| Find      | O(α(n))            | O(n)             |
| Union     | O(α(n))            | O(n)             |

Buradaki α(n), inverse Ackermann fonksiyonudur. Uygulamada neredeyse sabit sürede işlem yapılabilir.

## Temel Adımlar

1. Her eleman başlangıçta kendi kümesindedir.
2. Find işlemi ile bir elemanın temsilcisi bulunur.
3. Union işlemi ile iki farklı küme birleştirilir.
4. Path Compression ile Find işlemi hızlandırılır.
5. Union by Rank veya Union by Size ile kümeler dengeli biçimde birleştirilir.

## Avantajları

- Hızlı ve verimli çalışır
- Büyük veri yapılarıyla başa çıkabilir
- Minimum Spanning Tree gibi algoritmalarla kolayca entegre olur

## Dezavantajları

- Sadece bağlantı ve birleşme işlemleri yapılabilir
- Karmaşık veri yapısı kavramları yeni başlayanlar için zor olabilir

## C++ Örnek Kod

#include <iostream>
#include <vector>

using namespace std;

class UnionFind {
private:
    vector<int> parent, rank; // Her eleman için bir 'parent' (kok) ve 'rank' (derinlik) tutuyoruz.

public:
    // Yapici fonksiyon: Union-Find veri yapisini baslatiyoruz. Her eleman kendi kümesinin temsilcisidir.
    UnionFind(int n) {
        parent.resize(n); // 'n' tane eleman için bir parent dizisi olusturuyoruz.
        rank.resize(n, 0); // Her eleman için baslangicta 0 derinligiyle rank dizisi olusturuyoruz.
        for(int i = 0; i < n; i++)
            parent[i] = i; // Baslangicta her eleman kendi kendisinin kokudur.
    }

    // 'Find' fonksiyonu: Bu fonksiyon, bir elemanin hangi kümeye ait oldugunu bulmamiza yardimci olur.
    int find(int x) {
        if(parent[x] != x) // Eger 'x' kendisiyle ayni degere sahip degilse, yani kendi koku degilse
            parent[x] = find(parent[x]); // Rekursif olarak üst kümenin kokunu bulup, bu elemani dogrudan koke baglariz (yol sikistirma).
        return parent[x]; // Sonunda, koku döndürüyoruz.
    }

    // 'Union' fonksiyonu: Iki elemanin ait oldugu kümeleri birlestirmek için kullanilir.
    void unionSets(int x, int y) {
        int rootX = find(x); // x elemaninin kokunu buluyoruz.
        int rootY = find(y); // y elemaninin kokunu buluyoruz.
        if(rootX == rootY) return; // Eger zaten ayni kümeye aitlerse, birlestirmeye gerek yoktur.

        // Kümeleri birlestirme islemi. 'Union by Rank' ile, daha derin olan kümeyi digerinin koku yapıyoruz.
        if(rank[rootX] < rank[rootY]) 
            parent[rootX] = rootY; // Eger x'in kümesi daha sigysa, x'in kokunu y'nin kokuna baglariyoruz.
        else if(rank[rootX] > rank[rootY])
            parent[rootY] = rootX; // Eger y'nin kümesi daha sigysa, y'nin kokunu x'in kokuna baglariyoruz.
        else {
            parent[rootY] = rootX; // Eger her iki küme ayni derinlikte ise, biri kok olacak, digeri bagli olacak.
            rank[rootX]++; // x'in derinligini arttiriyoruz.
        }
    }

    // 'Connected' fonksiyonu: x ve y'nin ayni kümeye ait olup olmadigini kontrol eder.
    bool connected(int x, int y) {
        return find(x) == find(y); // Eger x ve y'nin kokleri ayniysa, bu iki eleman ayni kümeye aittir.
    }
};

int main() {
    UnionFind uf(10); // 10 elemanlik bir Union-Find veri yapisi olusturuyoruz.

    // Küme birlestirme islemleri
    uf.unionSets(1, 2); // 1 ve 2'yi ayni kümeye dahil ediyoruz.
    uf.unionSets(2, 3); // 2 ve 3'ü de ayni kümeye bagliyoruz.
    uf.unionSets(4, 5); // 4 ve 5'i birbirine bagliyoruz.

    // Baglanti kontrolü
    cout << "1 ve 3 bagli mi? " << (uf.connected(1, 3) ? "Evet" : "Hayir") << "\n";
    cout << "3 ve 5 bagli mi? " << (uf.connected(3, 5) ? "Evet" : "Hayir") << "\n";

    return 0;
}
## Kaynaklar
CLRS - Introduction to Algorithms (Cormen, Leiserson, Rivest, Stein)

Wikipedia - Union-Find

GeeksforGeeks - Union-Find Algorithm

Competitive Programming Books (For example, Steven Halim's "Competitive Programming 3")
