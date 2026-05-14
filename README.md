# MedGraphRAG: Bütünleyici Tıp için Dinamik Bilgi Çizgesi ve Hibrit Akıl Yürütme Altyapısı
MedGraphRAG, bütünleyici ve fonksiyonel tıp literatüründeki karmaşık biyokimyasal nedensellik zincirlerini (Multi-hop reasoning) çözümlemek ve tıbbi halüsinasyonları engellemek amacıyla tasarlanmış uçtan uca bir **Klinik Karar Destek Sistemi (CDSS)** mimarisidir.

# 🚀 Proje Özeti
Geleneksel Vektör tabanlı RAG (Retrieval-Augmented Generation) sistemleri, tıp metinlerini anlamsal benzerlik üzerinden getirir ancak "Bağlantılı Mantık" kuramazlar. MedGraphRAG, "Integrative and Functional Medical Nutrition Therapy" referans kaynağını kullanarak veriyi yapılandırılmış bir **Bilgi Çizgesi'ne (Knowledge Graph)** dönüştürür.

**Temel Başarılar:**
* **Halüsinasyon Kontrolü:** "Chain-of-Thought" prompt mimarisi ile eksik veride **%100 Faithfulness (Sadakat)** oranı.

* **Teknik Derinlik:** 4-bit NF4 kuantizasyonu ile kısıtlı donanımda (16GB VRAM) devasa model (Gemma 7B) çalıştırma.

* **Yapısal Arama:** Vektör benzerliği yerine Neo4j üzerinde çalışan özel Cypher tabanlı arama algoritması.

# 🗂️ Veri Seti ve "Altın Standart" Sentetik Karşılaştırma (Golden QA Dataset)
Tıbbi LLM projeleri genellikle PubMedQA gibi açık veri setleriyle test edilmektedir. Ancak bu projede, dış veri setlerinin kullanımı **"Kapsam Uyuşmazlığı" (Out-of-Domain)** sorununa yol açmıştır (Örneğin, PubMedQA'da yer alan spesifik Cyclosporine ilacının bizim kapalı alan veritabanımızda bulunmaması).

Bu metodolojik sorunu çözmek için RAGAS framework yönergelerine uygun olarak, doğrudan projenin veritabanından (Bölüm 23: The GUT-Immune System) türetilen MedGraphRAG Golden QA Dataset isimli özel bir veri seti tasarlanmıştır.

Veri Setinin İçeriği: Vektör arama motorlarını yanıltmak ve Graph arama motorunun "Çoklu Sıçrama (Multi-Hop)" yeteneğini test etmek üzere tasarlanmış 5 adet yüksek karmaşıklıkta (High-Complexity) tıbbi nedensellik sorusu ve bu soruların uzman hekim seviyesindeki ideal cevapları.

Örnek Soru Yapısı: "Otoimmün hastalıkların gelişiminde bağırsak bariyerinin bozulması bağışıklık sistemini nasıl tetikler?"
