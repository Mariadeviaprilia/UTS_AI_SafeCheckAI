# SafeCheck AI — Sistem Deteksi Penipuan Digital

**Mata Kuliah:** Artificial Intelligence  
**Nama:** Maria Devi Aparilia  
**NIM:** 24110400032  

---

## Deskripsi

SafeCheck AI adalah AI Assistant yang menggunakan sistem **RAG (Retrieval-Augmented Generation)** untuk membantu pengguna mendeteksi pesan penipuan digital seperti phishing, scam hadiah, social engineering, dan penipuan lowongan kerja.

**Target User:** Pengguna internet umum, mahasiswa, dan nasabah perbankan digital yang tidak memiliki latar belakang teknis keamanan siber.

---

## Arsitektur RAG Pipeline

```
User Query
    │
    ▼
[1] Query Embedding
    │  (paraphrase-multilingual-MiniLM-L12-v2)
    ▼
[2] Vector Search (FAISS)
    │  Cosine Similarity terhadap Knowledge Base
    ▼
[3] Retrieved Chunks (top-k)
    │  + Metadata: category, modus_type, risk_level, source
    ▼
[4] Prompt Augmentation
    │  System Prompt (Guardrails) + Context + Query
    ▼
[5] LLM Generation (Claude API)
    │
    ▼
Jawaban + Risk Level + Source + Rekomendasi
```

---

## Sumber Dokumen (Knowledge Base)

| Chunk | Kategori | Sumber | Author |
|-------|----------|--------|--------|
| chunk_A | phishing_email | BSSN Lanskap Keamanan Siber Indonesia 2024 | BSSN |
| chunk_B | scam_hadiah | BSSN Lanskap Keamanan Siber Indonesia 2024 | BSSN |
| chunk_C | social_engineering | OJK Panduan Strategi Anti Fraud (ITSK) 2024 | OJK |
| chunk_D | data_harvesting | BSSN Lanskap Keamanan Siber Indonesia 2024 | BSSN |
| chunk_E | sms_spam_example | Indonesia SMS Spam Dataset | bopbi |

**Link Dokumen:**
- BSSN 2024: https://alika.pesisirbaratkab.go.id/ebook/lanskap-keamanan-siber-indonesia-2024
- OJK Anti Fraud: https://ojk.go.id/id/Publikasi/Roadmap-dan-Pedoman/ITSK/Documents/PANDUAN%20STRATEGI%20ANTI%20FRAUD%20(ITSK)%202024.pdf
- SMS Dataset: https://github.com/bopbi/indonesia-sms-spam-dataset

## 10 Pertanyaan User
1.	Saya dapat SMS katanya menang hadiah dari BRI, beneran gak?
2.	Ada yang minta OTP saya lewat WhatsApp ngaku dari Tokopedia, aman?
3.	Dapat email dari BCA minta verifikasi akun, link-nya harus diklik?
4.	Link bit.ly dikirim orang asing di WhatsApp, bahaya gak kalau dibuka?
5.	Nomor ini ngaku CS Shopee tapi bukan dari aplikasi resmi, gimana?
6.	Diminta transfer dulu buat cairkan hadiah, ini penipuan?
7.	SMS bilang paket tertahan di bea cukai dan harus bayar, valid?
8.	Ada lowongan kerja minta foto KTP dan selfie sebelum interview, normal?
9.	Dapat pesan akun akan diblokir dalam 24 jam, perlu direspons?
10.	WhatsApp blast dari nomor tidak dikenal kirim link promo diskon 90%, aman?


## Contoh Retrieved Context

```python
# Cek pesan mencurigakan
result = safecheck_analyze(
    "Dapat SMS menang hadiah Rp50 juta dari BRI, diminta klik link bit.ly"
)
```

**Output:**
```
🔴 STATUS RISIKO: Tinggi
📋 ANALISIS: Pesan ini mengandung ciri-ciri scam hadiah yang umum di Indonesia...
⚠️  INDIKATOR BAHAYA:
   - Klaim hadiah tanpa partisipasi sebelumnya
   - Penggunaan URL shortener (bit.ly)
   - Tekanan waktu untuk segera klaim
✅ REKOMENDASI: Jangan klik link. Abaikan pesan ini...
📚 SOURCE: BSSN Lanskap Keamanan Siber Indonesia 2024
📞 KONTAK RESMI: OJK 157 / Call Center BRI 14017
```

---

## Resiko Hallucination

Risiko utama dalam sistem ini adalah hallucination, yaitu AI menjawab dengan informasi yang tidak ada di dokumen. Selain itu, AI bisa salah mengambil chunk karena kata kunci mirip, atau memberi rasa aman palsu jika jawaban terdengar meyakinkan tetapi tidak akurat

---


## Guardrails yang Diterapkan

| Guardrail | Deskripsi |
|-----------|-----------|
| **Berbasis dokumen** | AI hanya menjawab dari dokumen yang ada di knowledge base |
| **Wajib source** | Setiap jawaban mencantumkan dokumen sumber |
| **Akui ketidaktahuan** | Jika tidak ada di dokumen, AI menyatakan tidak tahu |
| **Domain restriction** | AI menolak pertanyaan di luar topik keamanan digital |
| **Kontak resmi** | Jika risiko tinggi, ditampilkan kontak OJK 157 dan bank |

---

## Evaluasi

Evaluasi dilakukan terhadap 3 pertanyaan uji:

| Query | Retrieval Relevan | Source Tersedia | Risk Level Benar |
|-------|:-----------------:|:---------------:|:----------------:|
| SMS hadiah dari bank | ✅ | ✅ | ✅ |
| Telepon minta OTP | ✅ | ✅ | ✅ |
| Email domain mirip Tokopedia | ✅ | ✅ | ✅ |

---

## Dependensi

```
anthropic
sentence-transformers
faiss-cpu
pandas
numpy
matplotlib
```

---

## Rencana Pengembangan (Next Step)

- [ ] Load PDF dokumen asli secara otomatis (bukan manual)
- [ ] Chunking otomatis dengan LangChain atau LlamaIndex
- [ ] Vector database yang persisten (ChromaDB / Pinecone)
- [ ] Tambah evaluasi edge case dan adversarial queries
- [ ] Antarmuka web sederhana (Gradio / Streamlit)
- [ ] Ekspansi knowledge base dengan lebih banyak modus penipuan

---

## Lisensi

Proyek ini dibuat untuk keperluan akademik (Tugas UTS Artificial Intelligence).  
Dokumen sumber milik BSSN dan OJK — digunakan sesuai ketentuan publikasi pemerintah.
