<template>
  <div id="studentFormModal" class="modal fade">
    <div class="modal-dialog">
      <div class="modal-content">
        <form @submit.prevent="handleSubmit">
          <div class="modal-header">
            <h4 class="modal-title">
              {{ isEdit ? "Cập nhật nhân viên" : "Thêm mới nhân viên" }}
            </h4>
            <button
              type="button"
              class="close"
              data-dismiss="modal"
              aria-hidden="true"
            >
              &times;
            </button>
          </div>
          <div class="modal-body">
            <div class="form-group">
              <label>Tên nhân viên</label>
              <input
                v-model="student.student_name"
                type="text"
                class="form-control"
              />
              <span v-if="errors.name" class="text-danger">{{
                errors.name
              }}</span>
            </div>
            <div class="form-group">
              <label>Email</label>
              <input
                v-model="student.email"
                type="email"
                class="form-control"
                @blur="validateEmail"
              />
              <span v-if="errors.email" class="text-danger">{{
                errors.email
              }}</span>
            </div>
            <div class="form-group">
              <label>Địa chỉ</label>
              <textarea
                v-model="student.address"
                class="form-control"
              ></textarea>
            </div>
            <div class="form-group">
              <label>Số điện thoại</label>
              <input
                v-model="student.phone"
                type="text"
                class="form-control"
                @input="validatePhone"
              />
              <span v-if="errors.phone" class="text-danger">{{
                errors.phone
              }}</span>
            </div>
          </div>
          <div class="modal-footer">
            <input
              type="button"
              class="btn btn-default"
              data-dismiss="modal"
              value="Cancel"
              @click="closeForm"
            />
            <input
              type="submit"
              class="btn btn-success"
              value="Lưu"
              :disabled="!isValid"
            />
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, watch } from "vue";

// Props
const props = defineProps({
  studentToEdit: Object,
  isEdit: Boolean,
});

// Emit sự kiện
const emit = defineEmits(["save", "cancel"]);

// Khởi tạo state
const student = ref({ student_name: "", email: "", address: "", phone: "" });
const errors = ref({ name: "", email: "", phone: "" });
const isValid = ref(false);

// Đồng bộ giá trị với props
watch(
  () => props.studentToEdit,
  (newVal) => {
    if (newVal) {
      student.value = { ...newVal };
    }
  }
);

// Kiểm tra hợp lệ email
const validateEmail = () => {
  const emailPattern = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  if (!student.value.email) {
    errors.value.email = "Email không được để trống";
  } else if (!emailPattern.test(student.value.email)) {
    errors.value.email = "Email không đúng định dạng";
  } else {
    errors.value.email = "";
  }
  checkFormValidity();
};

// Kiểm tra hợp lệ số điện thoại
const validatePhone = () => {
  const phonePattern = /^[0-9]*$/;
  if (!student.value.phone) {
    errors.value.phone = "Số điện thoại không được để trống";
  } else if (!phonePattern.test(student.value.phone)) {
    errors.value.phone = "Số điện thoại chỉ được phép nhập số";
  } else {
    errors.value.phone = "";
  }
  checkFormValidity();
};

// Kiểm tra tính hợp lệ của form
const checkFormValidity = () => {
  errors.value.name = student.value.student_name
    ? ""
    : "Tên nhân viên không được để trống";
  isValid.value =
    !errors.value.name &&
    !errors.value.email &&
    !errors.value.phone &&
    student.value.address;
};

// Xử lý khi submit form
const handleSubmit = () => {
  if (isValid.value) {
    emit("save", student.value);
  }
};

// Hàm đóng form
const closeForm = () => {
  emit("cancel");
};

// Kiểm tra hợp lệ ngay khi có thay đổi
watch(student, checkFormValidity, { deep: true });
</script>
<style>
body {
  color: #566787;
  background: #f5f5f5;
  font-family: "Varela Round", sans-serif;
  font-size: 13px;
}
.table-responsive {
  margin: 30px 0;
}
.table-wrapper {
  background: #fff;
  padding: 20px 25px;
  border-radius: 3px;
  min-width: 1000px;
  box-shadow: 0 1px 1px rgba(0, 0, 0, 0.05);
}
.table-title {
  padding-bottom: 15px;
  background: #435d7d;
  color: #fff;
  padding: 16px 30px;
  min-width: 100%;
  margin: -20px -25px 10px;
  border-radius: 3px 3px 0 0;
}
.table-title h2 {
  margin: 5px 0 0;
  font-size: 24px;
}
.table-title .btn-group {
  float: right;
}
.table-title .btn {
  color: #fff;
  float: right;
  font-size: 13px;
  border: none;
  min-width: 50px;
  border-radius: 2px;
  border: none;
  outline: none !important;
  margin-left: 10px;
}
.table-title .btn i {
  float: left;
  font-size: 21px;
  margin-right: 5px;
}
.table-title .btn span {
  float: left;
  margin-top: 2px;
}
table.table tr th,
table.table tr td {
  border-color: #e9e9e9;
  padding: 12px 15px;
  vertical-align: middle;
}
table.table tr th:first-child {
  width: 60px;
}
table.table tr th:last-child {
  width: 100px;
}
table.table-striped tbody tr:nth-of-type(odd) {
  background-color: #fcfcfc;
}
table.table-striped.table-hover tbody tr:hover {
  background: #f5f5f5;
}
table.table th i {
  font-size: 13px;
  margin: 0 5px;
  cursor: pointer;
}
table.table td:last-child i {
  opacity: 0.9;
  font-size: 22px;
  margin: 0 5px;
}
table.table td a {
  font-weight: bold;
  color: #566787;
  display: inline-block;
  text-decoration: none;
  outline: none !important;
}
table.table td a:hover {
  color: #2196f3;
}
table.table td a.edit {
  color: #ffc107;
}
table.table td a.delete {
  color: #f44336;
}
table.table td i {
  font-size: 19px;
}
table.table .avatar {
  border-radius: 50%;
  vertical-align: middle;
  margin-right: 10px;
}
.pagination {
  float: right;
  margin: 0 0 5px;
}
.pagination li a {
  border: none;
  font-size: 13px;
  min-width: 30px;
  min-height: 30px;
  color: #999;
  margin: 0 2px;
  line-height: 30px;
  border-radius: 2px !important;
  text-align: center;
  padding: 0 6px;
}
.pagination li a:hover {
  color: #666;
}
.pagination li.active a,
.pagination li.active a.page-link {
  background: #03a9f4;
}
.pagination li.active a:hover {
  background: #0397d6;
}
.pagination li.disabled i {
  color: #ccc;
}
.pagination li i {
  font-size: 16px;
  padding-top: 6px;
}
.hint-text {
  float: left;
  margin-top: 10px;
  font-size: 13px;
}
/* Custom checkbox */
.custom-checkbox {
  position: relative;
}
.custom-checkbox input[type="checkbox"] {
  opacity: 0;
  position: absolute;
  margin: 5px 0 0 3px;
  z-index: 9;
}
.custom-checkbox label:before {
  width: 18px;
  height: 18px;
}
.custom-checkbox label:before {
  content: "";
  margin-right: 10px;
  display: inline-block;
  vertical-align: text-top;
  background: white;
  border: 1px solid #bbb;
  border-radius: 2px;
  box-sizing: border-box;
  z-index: 2;
}
.custom-checkbox input[type="checkbox"]:checked + label:after {
  content: "";
  position: absolute;
  left: 6px;
  top: 3px;
  width: 6px;
  height: 11px;
  border: solid #000;
  border-width: 0 3px 3px 0;
  transform: inherit;
  z-index: 3;
  transform: rotateZ(45deg);
}
.custom-checkbox input[type="checkbox"]:checked + label:before {
  border-color: #03a9f4;
  background: #03a9f4;
}
.custom-checkbox input[type="checkbox"]:checked + label:after {
  border-color: #fff;
}
.custom-checkbox input[type="checkbox"]:disabled + label:before {
  color: #b8b8b8;
  cursor: auto;
  box-shadow: none;
  background: #ddd;
}
/* Modal styles */
.modal .modal-dialog {
  max-width: 400px;
}
.modal .modal-header,
.modal .modal-body,
.modal .modal-footer {
  padding: 20px 30px;
}
.modal .modal-content {
  border-radius: 3px;
  font-size: 14px;
}
.modal .modal-footer {
  background: #ecf0f1;
  border-radius: 0 0 3px 3px;
}
.modal .modal-title {
  display: inline-block;
}
.modal .form-control {
  border-radius: 2px;
  box-shadow: none;
  border-color: #dddddd;
}
.modal textarea.form-control {
  resize: vertical;
}
.modal .btn {
  border-radius: 2px;
  min-width: 100px;
}
.modal form label {
  font-weight: normal;
}
</style>
