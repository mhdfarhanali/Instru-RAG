# 🤖 InstruRAG – Agentic Chatbot for Instrumentation and Control Engineering

**InstruRAG** adalah aplikasi **Agentic Chatbot berbasis Retrieval Augmented Generation (RAG)** yang dirancang untuk menjawab pertanyaan seputar **instrumentasi, sensor, dan sistem kontrol industri**.  
Proyek ini dibangun secara bertahap, mulai dari **ekstraksi teks dari buku PDF** hingga menjadi chatbot cerdas yang bisa diajak berdiskusi layaknya seorang **asisten engineer**.

---

## Tujuan Proyek

Tujuan dari proyek ini adalah membuat chatbot yang:

- Bisa memahami dan menjawab pertanyaan berdasarkan *knowledge base* teknikal (bukan hafalan).  
- Mampu melakukan reasoning teknis seputar sensor, kontrol, dan instrumentasi.  
- Menggunakan pendekatan **RAG (Retrieval Augmented Generation)** agar jawaban lebih relevan dan berbasis data.  
- Bisa di update dengan pengetahuan baru dari dokumen atau API publik.

---

## Arsitektur Sistem

Berikut adalah alur utama dari sistem Instru RAG:

📘 **PDF (Buku Teknik)**  
⬇️  
📄 **Extract Text (Langchain + PyMuPDF)**  
⬇️  
🧩 **Split Text menjadi Chunks**  
⬇️  
🧠 **Embedding (HuggingFace all-MiniLM-L6-v2)**  
⬇️  
💾 **Vector DB (Qdrant)**  
⬇️  
🗣️ **Agentic Chatbot (LangChain + Groq/OpenAI)**  
⬇️  
💬 **Jawaban berbasis konteks teknikal**

---

## Langkah Implementasi

### **1.  Ekstraksi PDF**
- File sumber: *Introduction to Instrumentation, Sensors, and Process Control* oleh William C. Dunn.  
- Menggunakan library **PyMuPDF** untuk membaca dan mengekstrak teks dari **349 halaman**.  
- Hasil ekstraksi disimpan di:  
data/raw/instrumentation_sensors.txt
- Total teks hasil ekstraksi: **670,951 karakter.**

---

### **2. Ingestion dan Text Chunking**
- Menggunakan `RecursiveCharacterTextSplitter` dari LangChain.  
- Ukuran potongan teks: **500 karakter**, overlap **100 karakter**.  
- Total potongan: **1,677 chunks.**  
- Tujuan: supaya tiap potongan cukup kecil agar embedding lebih efisien.

---

### **3. Indexing ke Vector Database (Qdrant)**
- Menggunakan **Qdrant** sebagai vector storage.  
- Model embedding: `sentence-transformers/all-MiniLM-L6-v2`  
→ ringan, cepat, dan akurat untuk teks teknikal.  
- Setelah upload, muncul log:

    Data berhasil diunggah ke Qdrant
    Koneksi ke koleksi 'instru_collection' berhasil. Siap untuk query!

---

### **4. Membuat Agentic Chatbot**
- Chatbot dibuat dengan `ConversationalRetrievalChain` dari LangChain.  
- Model LLM: **Groq API (Llama 3)** — gratis, cepat, dan efisien untuk query teknikal.  
- Fitur utama:
- Memory buffer (mengingat percakapan)  
- Prompt Template teknikal  
- Reset percakapan  

---

## Hasil Uji Chatbot

### Pertanyaan 1
**You:** What is PID control?  
**InstruRAG:** Menjelaskan PID (Proportional-Integral-Derivative) control, rumus, dan fungsi setiap komponen (Kp, Ki, Kd).

---

### Pertanyaan 2
**You:** What is PLC?  
**InstruRAG:** PLC adalah komputer industri untuk otomatisasi mesin dan proses produksi.

---

### Pertanyaan 3
**You:** What is DCS?  
**InstruRAG:** DCS adalah sistem kontrol terdistribusi yang digunakan dalam industri besar seperti pabrik kimia dan pembangkit listrik.

---

### Pertanyaan 4
**You:** What is SCADA?  
**InstruRAG:** SCADA adalah sistem pemantauan jarak jauh yang mengintegrasikan PLC dan RTU untuk mengawasi proses industri secara real-time.

---

### Kesimpulan
- Jawaban chatbot akurat, ringkas, dan mudah dipahami bahkan oleh pemula.  
- Sistem mampu mengingat konteks percakapan sebelumnya dan menyesuaikan jawaban berdasarkan topik.

---

## Analisis Pemilihan Komponen RAG

### 1. Pemilihan LLM (Large Language Model)
**Model:** Groq API – Llama 3  
**Alasan:**
- Gratis (free tier available)  
- Cepat dan efisien untuk teks teknikal  
- Kemampuan reasoning bagus di bidang engineering  
- **Alternatif:** OpenAI GPT-4o mini / Gemini Flash  

---

### 2. Pemilihan Model Embedding
**Model:** HuggingFace – all-MiniLM-L6-v2  
**Alasan:**
- Ringan dan open-source  
- Cocok untuk teks panjang berbahasa teknikal  
- Dimensi vektor 384 → efisien dan presisi  
- Sudah terintegrasi dengan LangChain HuggingFace  

---

### 3. Pemilihan Vector Database
**Database:** Qdrant  
**Alasan:**
- Open-source dan gratis  
- Mendukung semantic search (cosine similarity)  
- Mudah dijalankan melalui Docker  
- Dashboard lokal: [http://localhost:6333/dashboard](http://localhost:6333/dashboard)

---

### 4. Cara Update Knowledge Base
1. Tambahkan PDF baru ke folder `data/pdf`.  
2. Jalankan ulang notebook:  
0_extract_pdf.ipynb → 2_index_data.ipynb
3. Bisa juga mengganti teks lama dengan sumber lain (Wikipedia, API publik, dll).  

---

### 5. Kekurangan Aplikasi
- Belum memiliki UI web (masih berbasis terminal).  
- Belum fine-tuning istilah instrumentasi.  
- Belum mendukung input gambar atau diagram.  
- Model LLM gratis masih terbatas konteks panjangnya.

**Rencana Pengembangan:**
- Tambahkan **Streamlit UI** untuk tampilan interaktif.  
- Implementasi **Hybrid RAG (Text + API)** untuk data real-time.  
- Fine-tuning embedding khusus domain teknik industri.  

---

##  Requirements
```bash
langchain==0.3.7
langchain-core==0.3.14
langchain-community==0.3.7
langchain-huggingface==0.1.2
langchain-openai==0.3.4
qdrant-client==1.15.1
sentence-transformers==5.1.1
python-dotenv==1.0.1
pymupdf==1.24.13
Pillow==11.0.0
pandas==2.2.3
tqdm==4.66.5
requests==2.32.3
openai==1.53.0
groq==0.11.0
```

---

##  Cara Menjalankan Aplikasi

1. **Aktifkan virtual environment**
   ```bash
   source .venv/bin/activate
2. Jalankan Docker Qdrant
    ```bash
    docker compose up
3. Jalankan Chatbot
    ```bash
    python notebooks/3_agentic_chatbot.ipynb
4. Mulai Bertanya
    ```bash
    You: What is PID control?
5. Keluar
    ```bash
    You: exit

---
## Hasil Akhir & Pembelajaran
Dari proyek ini saya belajar banyak hal:
- Konsep **Retrieval-Augmented Generation (RAG)** secara praktis.
- Integrasi **LLM + Embedding + Vector DB** dalam satu pipeline.
- Menyusun pipeline AI dari data mentah hingga chatbot siap pakai.
- Bagaimana AI bisa menjadi asisten teknik yang relevan dan efisien.

---

## Penutup
Proyek **Instru RAG** membuktikan bahwa dengan satu buku teknik, kita bisa membangun chatbot cerdas yang memahami konsep Instrumentation & Control Engineering secara mendalam.

    “AI bukan hanya untuk menjawab, tapi untuk memahami dan membantu engineer berpikir lebih cepat.”
---

## 👨‍💻 Author
Muhammad Farhan Ali
    
🎓 Electrical Engineering – Instrumentation & Control
 
💼 Transitioning to AI Engineer

[![linkedin](https://img.shields.io/badge/linkedin-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/mhdfarhanali)