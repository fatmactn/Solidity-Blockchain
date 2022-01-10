# Koleksiyon Görsellerinin Oluşturulması
## Kurulum
Koleksiyon oluşturmamıza yardımcı olacak depoyu indiriyoruz.
```bash
    git clone https://github.com/HashLips/hashlips_art_engine.git
```

Dosyayı açıyoruz.
```bash
    cd hashlips_art_engine/
    code .
```

npm i yüklememiz gerekiyor.
```bash
    npm install
```
 Ardından kendi oluşturuduğumuz katmanları  <font color="green"> __/layers__ </font> klasöründeki dosyalar ile değiştiriyoruz.

Katmanları ekledikten sonra <font color="green"> __src\config.js__</font> dosyasını açıyoruz. <br><br>
## Görsellerimizin Kullanım Oranını Ayarlama
Görsellerimizi isimlendirirdikten sonra yanlarına # ile beraber bir sayı yazmamız gerekmektedir. Bu sayı layers dosyasındaki diğer sayılar ile toplanacaktır. Burada değer
<br/><font color="blue">Örnek:<br/> Green#10.png => Seçilme olasılığı:  10/(10+20+30)=1/6  <br/>Red#20.png ==> Seçilme olasılığı:  20/(10+20+30)=1/3<br/> Blue#30.png ==> Seçilme olasılığı:  30/(10+20+30)=1/2</font><br/>
```js
const rarityDelimiter = "#";
```

İstersek # yerine * da ya da başka bir sembol de koyabiliriz. Ama bu sefer görsellerin isimlerinide değiştirmemiz gerektiğini unutmayalım.

## Koleksiyonumuzu İsimlendirme
Aşağıdaki kısımlara koleksiyonumuzun ismini ve açıklamasını giriyoruz.
```js
const namePrefix = "Your Collection";
const description = "Remember to replace this description";
```

## Koleksiyonun Sayısının Ayarlanması
```js
const layerConfigurations = [
  {
    growEditionSizeTo: 5,
    layersOrder: [
      { name: "Background" },
      { name: "Eyeball" },
      { name: "Eye color" },
      { name: "Iris" },
      { name: "Shine" },
      { name: "Bottom lid" },
      { name: "Top lid" },
    ],
  },
];
```
:warning: Bu kısımda __layersOrder__ objesinin içine kendi görselleri sıralı bir biçimde yerleştirmemiz gerekmektedir.
## Koleksiyonu Özelleştirme
Koleksiyonu özelleştirmek istersek aşağıdaki kodu kullanabiliriz. Sonuç olarak en sondaki __growEditionSizeTo__ değerinde görsel oluşturulacaktır.
```js
const layerConfigurations = [
  {
    // Creates up to 50 artworks
    growEditionSizeTo: 50,
    layersOrder: [
      { name: "Background" },
      { name: "Head" },
      { name: "Mouth" },
      { name: "Eyes" },
      { name: "Eyeswear" },
      { name: "Headwear" },
    ],
  },
  {
    // Creates an additional 100 artworks
    growEditionSizeTo: 150,
    layersOrder: [
      { name: "Background" },
      { name: "Head" },
      { name: "Eyes" },
      { name: "Mouth" },
      { name: "Eyeswear" },
      { name: "Headwear" },
      { name: "AlienHeadwear" },
    ],
  },
];
```
:warning: Oluşturduğunuz katmanlardaki görsellerin boyutlarının aynı olduğuna ve görsellerin üst üste gelecek şekilde tasarlanmasına dikkat ediniz.:warning:

## Görsellerin Boyutunu Ayarlama 
Aşağıdaki kısımla görsellerin boyutu ile oynayabilirsiniz.
```js
const format = {
  width: 512,
  height: 512,
  smoothing: false,
};
```

## Görselleri Oluşturma
Aşağıdaki komut ile görselleri oluşturabilirsiniz.<br/>
:warning: Bu kodu her çalıştırdığınızda mevcut koleksiyonu silip baştan oluşturmaktadır.
```js
node index.js
```

Bu komut çalıştıktan sonra <font color="green"> __/build__ </font> klasörü altında _images_ ve _json_ adında iki klasör oluşacaktır. İmage klasörü görsellerimizin olduğu dosyadır. Json klasöründe ise görsellerimizin metadataları yani detaylı bilgileri bulunmaktadır.

Örnek Json
```json
{
  "name": "My Collection #2",
  "description": "My Collection Description",
  "image": "ipfs://NewUriToReplace/2.png",
  "dna": "3ec3f91d2f49ef6b0ab061c4873f812a017c3e85",
  "edition": 2,
  "date": 1641218170170,
  "attributes": [
    {
      "trait_type": "Background",
      "value": "Black"
    },
    {
      "trait_type": "Eyeball",
      "value": "Red"
    },
    {
      "trait_type": "Eye color",
      "value": "Yellow"
    },
    {
      "trait_type": "Iris",
      "value": "Medium"
    },
    {
      "trait_type": "Shine",
      "value": "Shapes"
    },
    {
      "trait_type": "Bottom lid",
      "value": "High"
    },
    {
      "trait_type": "Top lid",
      "value": "High"
    }
  ],
  "compiler": "HashLips Art Engine"
}
```

## Özel oluşturulmuş görselleri koruma
```js
const shuffleLayerConfigurations = false;
```

Eğer bu şekilde bırakırsak program sıralı olarak çalışacaktır. Eğer değişkeni true yaparsak bu sefer karışık olarak görselleri oluşturacaktır. Örneğin 15 normal 5 adet altın görsel oluşturduğumuzda görsellerin ön satışında altın olanlar on altında sonraki görseller özel olacağından sorun oluşma olasılı bulunabilir.

## Utils Preview Image
:warning:Bu fonksiyonları kullanabilmek için ilk önce görsellerin oluşturulmuş olması gerekmektedir. <br/>
```bash
node utils/preview.js
```

Komutu ile bir preview görseli oluştururuz. Oluşan görsel <font color="green">__/build__</font> klasörü içinde oluşacaktır. İstersek bu preview dosyasını da <font color="green"> __src\config.js__</font> dosyasında ağaşıdaki kısımlardan güncelleyebiliriz.
```js
const preview = {
  thumbPerRow: 5, 
  thumbWidth: 50, 
  imageRatio: format.height / format.width,
  imageName: "preview.png",
};
```

## Utils Preview Gift
```bash
node utils/preview_gif.js
```

Komutu ile bir preview gift görseli oluştururuz. Oluşan gift <font color="green">__/build__</font> klasörü içinde oluşacaktır. İstersek bu gift dosyasın da <font color="green"> __src\config.js__</font> dosyasında ağaşıdaki kısımlardan güncelleyebiliriz.
```js
const preview_gif = {
  numberOfImages: 5,
  order: "ASC", // ASC, DESC, MIXED
  repeat: 0,
  quality: 100,
  delay: 500,
  imageName: "preview.gif",
};
```

## Utils Pixalete 
```bash
node utils/pixelate.js
```

Komutu ile var olan görsellerin pixel hallerini oluştururuz. Oluşan görseller <font color="green">__/build/pixel_images__</font> klasörü içinde oluşacaktır. İstersek burada oluşturulan görselleri <font color="green"> __src\config.js__</font> dosyasında ağaşıdaki kısımlardan güncelleyebiliriz.
<p id="utils_update"></p>
```js
const pixelFormat = {
  ratio: 2 / 128,
};
```
<br><br>

## Utils Update Info
```bash
node utils/update_info.js
```

Bu komut görsellerin metadatasındaki bilgileri güncellemek için kullanılmaktadır. Güncellenen kısımlar <font color="green"> __src\config.js__</font> dosyasındaki şu değişkenlerdir.
```js
const namePrefix = "My Collection";
const description = "My Collection Description";
const baseUri = "ipfs://NewUriToReplace";
```

Bu kısmı görselleri pinata gibi bir alana yükledikten sonra görsellerin baseUri adresini güncellemek için kullanılmaktadır.
## Utils Rarity
```bash
node utils/rarity.js
```

Bu komut görsellerin metadatasındaki bilgileri görmek için kullanılmaktadır. Görsellerde hangi katmanın daha çok yada daha az olduğunu görmek için bu konut kulanılabilir.
```json
{
  trait: 'High',
  weight: '30',
  occurrence: '2 in 5 editions (40.00 %)'
}
{ trait: 'Low', 
  weight: '20', 
  occurrence: '1 in 5 editions (20.00 %)' }
{
  trait: 'Middle',
  weight: '50',
  occurrence: '2 in 5 editions (40.00 %)'
}
```

# Görselleri Pinata Hesabına Kaydetme
İlk önce oluşturduğumuz <font color="green"> __/build/images__ </font> klasörünü yükleyeceğiz. Ardından aşağıdaki gibi bir görsel oluşacaktır.<br/>
<img src="https://904361.smushcdn.com/2256130/wp-content/uploads/2021/11/allcode-nft-ipfs-2.png?lossy=1&strip=1&webp=1">
<br/>

Buradaki CID adresini kopyalıyoruz. Ardından <font color="green"> __src\config.js__</font> dosyasını açıyoruz.  <a href="#utils_update">Utils Update Info</a> kısmında bahsedilen işlemleri yapacağız.

```js
const namePrefix = "My Collection";
const description = "My Collection Description";
const baseUri = "ipfs://new-my-cid";
```
Metadataları güncelledikten sonra pinataya metaları yükleyeceğiz. Kontratta görsellere bu metadatalar aracılığıyla ulaşacağız. Görselleri images, jsonları metadata olarak kaydederseniz daha kullanışlı bir şekilde kullanabilirsiniz.

<a href="https://docs.opensea.io/docs/metadata-standards">Daha detaylı bilgi için buraya tıklayabilirsiniz</a>

# Kurallar
- <font color="green"> Dosyanın konumu yeşil renk ile gösterilecektir.</font> 
- __Kod içindeki objeler kalın olarak gözükecektir.__
- _Bir klasörden bahsediliyor ise klasör ismi italik yazacaktır._
- <font color="blue">Örnekler mavi renk ile gösterilecektir.</font>
