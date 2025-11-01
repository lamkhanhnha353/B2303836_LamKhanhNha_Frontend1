<template>
    <div class="page">
        <h4>Thêm Liên hệ</h4>
        <ContactForm
            :contact="contact"
            @submit:contact="createContact"
        />
        <p>{{ message }}</p>
    </div>
</template>

<script>
import ContactForm from "@/components/ContactForm.vue";
import ContactService from "@/services/contact.service";

export default {
    components: {
        ContactForm,
    },
    data() {
        return {
            // Khởi tạo contact là một đối tượng rỗng
            // ContactForm sẽ điền dữ liệu vào đối tượng này
            contact: {},
            message: "",
        };
    },
    methods: {
        async createContact(data) {
            try {
                await ContactService.create(data);
                alert("Liên hệ được tạo thành công.");
                // Chuyển về trang chủ (danh sách liên hệ)
                this.$router.push({ name: "contactbook" });
            } catch (error) {
                console.log(error);
            }
        },
    },
    created() {
        // Khởi tạo message
        this.message = "";
    },
};
</script>
