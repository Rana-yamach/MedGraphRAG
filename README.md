# MedGraphRAG: Bütünleyici Tıp için Dinamik Bilgi Çizgesi ve Hibrit Akıl Yürütme Altyapısı
MedGraphRAG, bütünleyici ve fonksiyonel tıp literatüründeki karmaşık biyokimyasal nedensellik zincirlerini (Multi-hop reasoning) çözümlemek ve tıbbi halüsinasyonları engellemek amacıyla tasarlanmış uçtan uca bir **Klinik Karar Destek Sistemi (CDSS)** mimarisidir.

# 🚀 Proje Özeti
Geleneksel Vektör tabanlı RAG (Retrieval-Augmented Generation) sistemleri, tıp metinlerini anlamsal benzerlik üzerinden getirir ancak "Bağlantılı Mantık" kuramazlar. MedGraphRAG, sadece bir tıp kitabının özetini değil, "Integrative and Functional Medical Nutrition Therapy" kitabında (Özellikle Bölüm 23: Bağırsak-İmmünite ekseni) referans gösterilen temel akademik makalelerin tam metinlerini (Full-text PDFs) okuyarak veriyi yapılandırılmış bir Bilgi Çizgesi'ne (Knowledge Graph) dönüştürür.

**Temel Başarılar:**
* Halüsinasyon Kontrolü: "Chain-of-Thought" prompt mimarisi ile eksik veride %100 Faithfulness (Sadakat) oranı.

* Akademik Veri İşleme: Kitaplardan alınan ham referans makalelerin yapay zeka ile temizlenip graf formatına dökülmesi.

* Teknik Derinlik: 4-bit NF4 kuantizasyonu ile kısıtlı donanımda (16GB VRAM) devasa model (Gemma 7B) çalıştırma.

* Yapısal Arama: Vektör benzerliği yerine Neo4j üzerinde çalışan özel Cypher tabanlı arama algoritması.

# 🗂️ Veri Seti ve "Altın Standart" Sentetik Karşılaştırma (Golden QA Dataset)
Tıbbi LLM projeleri genellikle PubMedQA gibi açık veri setleriyle test edilmektedir. Ancak bu projede, dış veri setlerinin kullanımı **"Kapsam Uyuşmazlığı" (Out-of-Domain)** sorununa yol açmıştır (Örneğin, PubMedQA'da yer alan spesifik Cyclosporine ilacının bizim kapalı alan veritabanımızda bulunmaması).

Bu metodolojik sorunu çözmek için RAGAS framework yönergelerine uygun olarak, doğrudan projenin veritabanından (Bölüm 23: The GUT-Immune System) türetilen **MedGraphRAG Golden QA Dataset** isimli özel bir veri seti tasarlanmıştır.

* Veri Setinin İçeriği: Vektör arama motorlarını yanıltmak ve Graph arama motorunun "Çoklu Sıçrama (Multi-Hop)" yeteneğini test etmek üzere tasarlanmış 5 adet yüksek karmaşıklıkta (High-Complexity) tıbbi nedensellik sorusu ve bu soruların uzman hekim seviyesindeki ideal cevapları.

* Örnek Soru Yapısı: "Otoimmün hastalıkların gelişiminde bağırsak bariyerinin bozulması bağışıklık sistemini nasıl tetikler?"

# 🏗️ Sistem Mimarisi
Sistem, beş ana katmandan oluşan sofistike bir veri boru hattı (pipeline) üzerine kuruludur:

1. Makale Temizleme (Data Cleansing): Temel kitaptan çekilen referans makale PDF'lerindeki tablo, kaynakça ve çoklu sütun gürültülerinin Unstructured kütüphanesi kullanılarak ayıklanması.

2. Varlık ve İlişki Çıkarımı (Triplet Extraction): Gemma 7B modeli aracılığıyla temizlenmiş akademik metinlerden (Varlık)--[İlişki]-->(Varlık) üçlülerinin çıkarılması.

3. Graf Veritabanı: Akademik makalelerden elde edilen 1.330 Düğüm ve 3.652 İlişkinin bulut tabanlı Neo4j AuraDB üzerinde orkestrasyonu.

4. Özel Retriever (Neo4jGraphRetriever): Haystack framework'ü üzerinde kodlanmış, anahtar kelimeleri dinamik Cypher sorgularına dönüştüren arama motoru.

5. Güvenli Yanıt Üretimi: "Chain-of-Thought" (CoT) prensibiyle kısıtlanmış, tıbbi kanıta dayalı yanıt katmanı.

# 📊 Performans Değerlendirmesi (A/B Testi)
Geliştirdiğimiz Golden QA Dataset üzerinden literatürdeki RAGAS metrikleri kullanılarak yapılan A/B testi sonuçları:

| Metrik  | Vektör-RAG (Baseline) | Graph-RAG (MedGraphRAG) |
| ------------- | ------------- |:-------------:|
| Faithfulness (Sadakat)	| %95 |	%100 (Sıfır Halüsinasyon) |
| Context Precision	| %30 (Gürültülü Metinler)	| %85 (Nokta Atışı İlişkiler)|
|Multi-Hop Reasoning	| Başarısız	| Başarılı|

**Önemli Bulgu:**
Sistem, veritabanında bulunmayan PubMedQA kapsam dışı sorularıyla karşılaştığında, uydurma bir cevap vermek yerine "Graf verilerinde bu madde bulunmadığı için sonuca varılamamaktadır" diyerek klinik güvenliği en üst düzeye çıkarmaktadır.

# 📂 Bilgi Çizgesi İstatistikleri (Makale Kaynaklı)
* **Toplam Düğüm (Nodes)**: 1.330

* **Toplam İlişki (Relationships):** 3.652

* **Ana İlişki Tipleri:** CAUSES, TREATS, LOCATED_IN, INVOLVES, PREVENTS.

# 🔮 Gelecek Çalışmalar
* **Hybrid Retriever:** Vektör aramasının genişliği ile Graf aramasının derinliğini birleştiren hibrit yapılar.

* **Entity Resolution:** Makalelerden çekilen "Gut" ve "Intestine" gibi anlamsal olarak yakın düğümlerin LLM kümelemesi ile otomatik konsolidasyonu.

* **Agentic Graph Walk:** Karmaşık sorular için otonom çok adımlı (multi-step) gezinme algoritmaları.

# 📚 Referanslar
* **Noland, D., Drisko, J. A., & Wagner, L. (Eds.). (2020).** Integrative and functional medical nutrition therapy: principles and practices. Springer Nature. (Bölüm 23, 24, 25, 26 referans makalelerinin seçildiği ana kaynak eser).

* **Edge, D., et al. (2024).** From Local to Global: A Graph RAG Approach to Query-Focused Summarization. arXiv:2404.16130.

* **Esjas, S., et al. (2023).** RAGAS: Automated Evaluation of Retrieval Augmented Generation. arXiv:2309.15217.

# ⚖️ Lisans
Bu proje MIT Lisansı altında lisanslanmıştır. Daha fazla bilgi için LICENSE dosyasına göz atabilirsiniz.

**Geliştirici: Hatice Rana Yamaç**
