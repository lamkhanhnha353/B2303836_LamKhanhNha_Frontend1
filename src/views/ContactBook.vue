<template>
  <div class="page row">
    <!-- Ô tìm kiếm -->
    <div class="col-md-10">
      <InputSearch v-model="searchText" />
    </div>

    <!-- Danh sách liên hệ -->
    <div class="mt-3 col-md-6">
      <h4>
        Danh bạ
        <i class="fas fa-address-book"></i>
      </h4>

      <ContactList
        v-if="filteredContactsCount > 0"
        :contacts="filteredContacts"
        v-model:activeIndex="activeIndex"
      />

      <p v-else>Không có liên hệ nào.</p>

      <div class="mt-3 row justify-content-around align-items-center">
        <button class="btn btn-sm btn-primary" @click="refreshList()">
          <i class="fas fa-redo"></i> Làm mới
        </button>
        <button class="btn btn-sm btn-success" @click="goToAddContact">
          <i class="fas fa-plus"></i> Thêm mới
        </button>
        <button class="btn btn-sm btn-danger" @click="removeAllContacts">
          <i class="fas fa-trash"></i> Xóa tất cả
        </button>
      </div>
    </div>

    <!-- Thông tin chi tiết liên hệ -->
    <div class="mt-3 col-md-6">
      <div v-if="activeContact">
        <h4>
          Chi tiết Liên hệ
          <i class="fas fa-address-card"></i>
        </h4>
        <ContactCard :contact="activeContact" />
      </div>
    </div>
  </div>
</template>

<script>
import ContactCard from "@/components/ContactCard.vue";
import InputSearch from "@/components/InputSearch.vue";
import ContactList from "@/components/ContactList.vue";
import ContactService from "@/services/contact.service";

export default {
  components: {
    ContactCard,
    InputSearch,
    ContactList,
  },

  data() {
    return {
      contacts: [],      // Danh sách tất cả các liên hệ lấy từ server
      activeIndex: -1,   // Chỉ số của liên hệ đang được chọn trong danh sách
      searchText: "",    // Chuỗi tìm kiếm nhập từ ô InputSearch
    };
  },

  watch: {
    // Khi searchText thay đổi → bỏ chọn contact đang chọn
    // Giám sát các thay đổi của biến searchText.
    // Bỏ chọn phần tử đang được chọn trong danh sách.
    searchText() {
      this.activeIndex = -1;
    },
  },

  computed: {
    // 1️. Chuyển mỗi contact thành một chuỗi để dễ tìm kiếm
    contactStrings() {
      return this.contacts.map((contact) => {
        const { name, email, address, phone } = contact;
        return [name, email, address, phone].join("");
      });
    },

    // 2️. Lọc ra các contact có chứa chuỗi searchText
    filteredContacts() {
      if (!this.searchText) return this.contacts;
      return this.contacts.filter((_contact, index) =>
        this.contactStrings[index].includes(this.searchText)
      );
    },

    // 3️. Lấy ra contact đang được chọn
    activeContact() {
      if (this.activeIndex < 0) return null;
      return this.filteredContacts[this.activeIndex];
    },

    // 4️. Đếm số lượng contact sau khi lọc
    filteredContactsCount() {
      return this.filteredContacts.length;
    },
  },

  methods: {
    //  Lấy danh sách contact từ server thông qua ContactService
    async retrieveContacts() {
      try {
        this.contacts = await ContactService.getAll();
      } catch (error) {
        console.log(error);
      }
    },

    //  Làm mới danh sách liên hệ
    refreshList() {
      this.retrieveContacts();
      this.activeIndex = -1;
    },

    //  Xóa toàn bộ danh bạ sau khi xác nhận
    async removeAllContacts() {
      if (confirm("Bạn muốn xóa tất cả Liên hệ?")) {
        try {
          await ContactService.deleteAll();
          this.refreshList();
        } catch (error) {
          console.log(error);
        }
      }
    },

    //  Chuyển sang trang thêm liên hệ mới (router)
    goToAddContact() {
      this.$router.push({ name: "contact.add" });
    },
  },

  //  Khi component được tải lần đầu → tự động lấy dữ liệu từ server
  mounted() {
    this.refreshList();
  },
};
</script>

<style scoped>
.page {
  text-align: left;
  max-width: 750px;
}
</style>
