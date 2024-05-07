Copy code
<template>
  <div class="chat-container">
    <div class="message-list">
      <!-- Tampilkan daftar pesan di sini -->
      <div
        v-for="(message, index) in displayedMessages"
        :key="index"
        class="message"
        @click="selectMessage(index)"
      >
        <div class="message-content">
          <div class="reply-indicator" v-if="message.replyTo">
            <i class="fas fa-reply"></i>
            <!-- Gunakan ikon balasan -->
          </div>
          <div v-if="message.type === 'text'" class="message-text">
            {{ message.text }}
          </div>
          <a
            v-if="message.type === 'image'"
            :href="message.src"
            data-lightbox="image"
          >
            <img :src="message.src" :alt="message.alt" class="message-image" />
          </a>
          <!-- Tampilkan foto jika pesan bertipe gambar -->
          <button @click="editMessage(index)" class="edit-button">Edit</button>
          <button @click.stop="deleteMessage(index)" class="delete-button">
            Delete
          </button>
          <!-- Tambahkan tombol delete -->
        </div>
        <div class="message-sender">{{ message.sender }}</div>
      </div>
    </div>
    <div class="input-container">
      <input
        v-model="newMessage"
        type="text"
        :placeholder="
          replyTo ? `Replying to: ${replyTo.text}` : 'Type a message...'
        "
        class="message-input"
        @keyup.enter="sendMessage"
      />
      <!-- Tambahkan input tipe file untuk unggah foto/dokumen -->
      <input
        id="fileInput"
        type="file"
        ref="fileInput"
        @change="uploadFile"
        accept="image/*,.docx,.pdf,.ppt,.pptx"
        class="file-input"
      />
      <!-- Tombol "Choose File" yang di-styled -->
      <label for="fileInput" class="choose-file-button">Choose File</label>
      <!-- Tombol "Send" dan elemen lainnya -->
      <button @click="sendMessage" class="send-button">Send</button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      newMessage: "",
      messages: [], // Tambahkan array untuk menyimpan pesan
      selectedMessageIndex: null, // Menyimpan index pesan yang dipilih
    };
  },
  computed: {
    displayedMessages() {
      // Di sini, Anda dapat menambahkan logika untuk menyaring atau mengubah pesan yang ditampilkan
      return this.messages;
    },
  },
  methods: {
    sendMessage() {
      // Handle the message sending logic here
      if (this.newMessage) {
        const message = {
          text: this.newMessage,
          sender: "Hammam", // Ganti "user" dengan nama atau peran yang sesuai
          type: "text", // Tambahkan tipe pesan
        };
        this.messages.push(message);
        this.newMessage = "";
        this.saveMessagesToLocalStorage(); // Simpan pesan ke Local Storage setelah pengiriman berhasil
      } else {
        alert("Please enter a message."); // Peringatkan pengguna jika pesan kosong
      }
    },
    editMessage(index) {
      // Metode untuk mengedit pesan yang dipilih
      const editedMessage = prompt(
        "Edit your message:",
        this.messages[index].text
      );
      if (editedMessage !== null) {
        this.messages[index].text = editedMessage;
        this.saveMessagesToLocalStorage(); // Simpan pesan ke Local Storage setelah pengeditan berhasil
      }
    },
    uploadFile(event) {
      const file = event.target.files[0];
      // Lakukan validasi file, misalnya tipe dan ukuran file
      if (file) {
        // Buat objek URL untuk menampilkan foto yang diunggah
        const imageUrl = URL.createObjectURL(file);
        // Tambahkan pesan ke dalam array messages dengan tipe gambar
        this.messages.push({
          src: imageUrl,
          alt: "Image", // Tambahkan teks alternatif untuk gambar
          sender: "Hammam", // Ganti "user" dengan nama atau peran yang sesuai
          type: "image", // Tambahkan tipe pesan
        });
        // Bersihkan input file setelah unggahan selesai
        this.$refs.fileInput.value = "";
        this.saveMessagesToLocalStorage(); // Simpan pesan ke Local Storage setelah pengiriman berhasil
      }
    },
    selectMessage(index) {
      // Atur index pesan yang dipilih
      this.selectedMessageIndex = index;
    },
    deleteMessage(index) {
      // Hapus pesan berdasarkan index
      this.messages.splice(index, 1);
      // Setel kembali pesan yang dipilih menjadi null setelah dihapus
      this.selectedMessageIndex = null;
      this.saveMessagesToLocalStorage(); // Simpan pesan ke Local Storage setelah penghapusan berhasil
    },
    saveMessagesToLocalStorage() {
      // Simpan data chat ke Local Storage
      localStorage.setItem("chatMessages", JSON.stringify(this.messages));
    },
    loadMessagesFromLocalStorage() {
      // Muat data chat dari Local Storage saat halaman dimuat
      const storedMessages = localStorage.getItem("chatMessages");
      if (storedMessages) {
        this.messages = JSON.parse(storedMessages);
      }
    },
  },
  mounted() {
    // Muat data chat dari Local Storage saat komponen dimuat
    this.loadMessagesFromLocalStorage();
  },
};
</script>

<style scoped>
/* CSS internal di dalam file ChatComponent.vue */
/* Gaya CSS untuk tombol "Choose File" */
.choose-file-button {
  padding: 8px 16px;
  border: none;
  border-radius: 8px;
  background-color: #075e54;
  color: white;
  cursor: pointer;
}

.choose-file-button:hover {
  background-color: #128c7e;
}

/* Gaya tombol edit */
.edit-button {
  padding: 8px 16px;
  border: 1px solid #5bc0de; /* Warna border biru */
  border-radius: 8px;
  background-color: #5bc0de; /* Warna background biru */
  color: white; /* Warna teks putih */
  cursor: pointer;
  transition: background-color 0.3s ease, color 0.3s ease; /* Transisi smooth saat di-hover */
}

.edit-button:hover {
  background-color: #31b0d5; /* Warna background biru yang sedikit lebih gelap saat di-hover */
  border-color: #269abc; /* Warna border biru yang sedikit lebih gelap saat di-hover */
}

/* Gaya tombol delete */
.delete-button {
  padding: 8px 16px;
  border: 1px solid #d9534f; /* Warna border merah */
  border-radius: 8px;
  background-color: #d9534f; /* Warna background merah */
  color: white; /* Warna teks putih */
  cursor: pointer;
  transition: background-color 0.3s ease, color 0.3s ease; /* Transisi smooth saat di-hover */
}

.delete-button:hover {
  background-color: #c9302c; /* Warna background merah yang sedikit lebih gelap saat di-hover */
  border-color: #c12e2a; /* Warna border merah yang sedikit lebih gelap saat di-hover */
}

/* Sembunyikan input tipe file bawaan */
.file-input {
  display: none;
}

/* Gaya lainnya */
.message-image {
  max-width: 200px; /* Atur lebar maksimum gambar */
  height: auto; /* Biarkan ketinggian mengikuti proporsi gambar */
  cursor: pointer; /* Ubah kursor menjadi tangan saat gambar diklik */
}

/* Gaya CSS untuk kotak pesan */
.chat-box {
  background-color: #f0f0f0;
  border-radius: 8px;
  padding: 12px;
  margin-bottom: 10px;
}

/* Gaya CSS untuk pesan teks */
.message-text {
  background-color: #ffffff;
  border-radius: 8px;
  padding: 8px 12px;
  margin-bottom: 6px;
}

/* Gaya CSS untuk pesan gambar */
.message-image {
  max-width: 100%;
  height: auto;
  border-radius: 8px;
  margin-bottom: 6px;
}
</style>
