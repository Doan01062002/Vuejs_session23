<template>
  <div>
    <div class="container-xl">
      <div class="table-responsive">
        <div class="table-wrapper">
          <div class="table-title">
            <div class="row">
              <div class="col-sm-6">
                <h2>Quản lý <b>sinh viên</b></h2>
              </div>
              <div @click="showAddForm" class="col-sm-6">
                <a
                  href="#addEmployeeModal"
                  class="btn btn-success"
                  data-toggle="modal"
                  ><i class="material-icons">&#xE147;</i>
                  <span>Thêm mới sinh viên</span></a
                >
              </div>
            </div>
          </div>
          <table class="table table-striped table-hover">
            <thead>
              <tr>
                <th>
                  <span class="custom-checkbox">
                    <input type="checkbox" id="selectAll" />
                    <label for="selectAll"></label>
                  </span>
                </th>
                <th>Tên sinh viên</th>
                <th>Email</th>
                <th>Địc chỉ</th>
                <th>Số điện thoại</th>
                <th>Lựa chọn</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="student in students" :key="student.id">
                <td>
                  <span class="custom-checkbox">
                    <input
                      type="checkbox"
                      id="checkbox5"
                      name="options[]"
                      value="1"
                    />
                    <label for="checkbox5"></label>
                  </span>
                </td>
                <td>{{ student.student_name }}</td>
                <td>{{ student.email }}</td>
                <td>{{ student.address }}</td>
                <td>{{ student.phone }}</td>
                <td>
                  <a
                    @click="showEditForm(student)"
                    href="#editEmployeeModal"
                    class="edit"
                    data-toggle="modal"
                    ><i
                      class="material-icons"
                      data-toggle="tooltip"
                      title="Edit"
                      >&#xE254;</i
                    >
                  </a>
                  <a
                    href="#deleteEmployeeModal"
                    class="delete"
                    data-toggle="modal"
                    @click="deleteStudent(student.id)"
                    ><i
                      class="material-icons"
                      data-toggle="tooltip"
                      title="Delete"
                      >&#xE872;</i
                    >
                  </a>
                </td>
              </tr>
            </tbody>
          </table>
          <div class="clearfix">
            <div class="hint-text">
              Hiển thị <b>{{ limit }}</b> / <b>{{ totalPages * limit }}</b> bản
              ghi
            </div>
            <ul class="pagination">
              <li class="page-item" :class="{ disabled: currentPage === 1 }">
                <a href="#" @click.prevent="goToPage(currentPage - 1)">Trước</a>
              </li>
              <li
                v-for="page in totalPages"
                :key="page"
                class="page-item"
                :class="{ active: currentPage === page }"
              >
                <a href="#" class="page-link" @click.prevent="goToPage(page)">{{
                  page
                }}</a>
              </li>
              <li
                class="page-item"
                :class="{ disabled: currentPage === totalPages }"
              >
                <a href="#" @click.prevent="goToPage(currentPage + 1)">Sau</a>
              </li>
            </ul>
          </div>
        </div>
      </div>
    </div>

    <!-- Form -->
    <FormModal
      v-if="showForm"
      :studentToEdit="currentStudent"
      :students="students"
      :isEdit="isEdit"
      @save="saveStudent"
      @cancel="closeForm"
    ></FormModal>

    <!-- Delete Modal HTML -->
    <div id="deleteEmployeeModal" class="modal" v-show="showFormDelete">
      <div class="modal-dialog">
        <div class="modal-content">
          <form>
            <div class="modal-header">
              <h4 class="modal-title">Xóa nhân viên</h4>
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
              <p>Bạn chắc chắn muốn xóa sinh viên&lt;ST001&gt;?</p>
            </div>
            <div class="modal-footer">
              <input
                type="button"
                class="btn btn-default"
                data-dismiss="modal"
                value="Hủy"
                @click="handleFormDelete"
              />
              <input
                type="button"
                class="btn btn-danger"
                value="Xóa"
                @click="aceptDelete"
              />
            </div>
          </form>
        </div>
      </div>
    </div>
  </div>
</template>
<script setup>
import FormModal from "@/components/bt07_08_09_10_11_12/FormModal.vue";
import axios from "axios";
import { ref } from "vue";

let showForm = ref(false);
const currentStudent = ref(null);
const isEdit = ref(false);

let students = ref([]);
let showFormDelete = ref(false);
const getAllUser = async () => {
  const res = await axios.get("http://localhost:3000/students");

  students.value = res.data;
};

getAllUser();

// ************Hàm Xóa****************

// Hàm đóng form delete
const handleFormDelete = () => {
  showFormDelete.value = !showFormDelete.value;
};

const getIdStudent = ref();

const deleteStudent = (id) => {
  getIdStudent.value = id;
  handleFormDelete();
};

// Hàm xác nhận xóa
const aceptDelete = async () => {
  console.log(getIdStudent);

  const res = await axios.delete(
    `http://localhost:3000/students/${getIdStudent.value}`
  );
  handleFormDelete();
  getAllUser();
};

// Show add form
const showAddForm = () => {
  currentStudent.value = null;
  isEdit.value = false;
  showForm.value = true;
};

// Show edit form
const showEditForm = (student) => {
  currentStudent.value = { ...student };
  isEdit.value = true;
  showForm.value = true;
};

// Close form
const closeForm = () => {
  showForm.value = false;
};

// Handle form save
const saveStudent = async (student) => {
  if (isEdit.value) {
    // Update existing product
    await axios.put(`http://localhost:3000/students/${student.id}`, student);
  } else {
    // Add new product
    await axios.post("http://localhost:3000/products", student);
  }
  closeForm();
  getAllUser();
};

let currentPage = ref(1);
let totalPages = ref(0);
let limit = ref(5);

const getAllStudents = async () => {
  const res = await axios.get(
    `http://localhost:3000/students?_page=${
      students.value.length / limit.value
    }&_limit=${limit.value}`
  );
  students.value = res.data;
  totalPages.value = Math.ceil(students.value.length / limit.value);
};

getAllStudents();

// Pagination logic
const goToPage = (page) => {
  if (page > 0 && page <= totalPages.value) {
    currentPage.value = page;
    getAllStudents(page); // Fetch data for selected page
  }
};
</script>
<style scoped>
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
