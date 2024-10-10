<template >
    <div>
        <h1>Cập nhật</h1>
        <ul>
            <li v-for="student in students" :key="student.id">{{ student.student_name }}
                <button @click="updateStudentById(student.id)">updateStudentById</button>
            </li>
        </ul>
        
    </div>
</template>
<script setup>
import axios from 'axios';
import { ref } from 'vue';

    let students = ref([])
    const getAllUser = async ()=>{
        const res = await axios.get("http://localhost:3000/students")

        students.value = res.data
    }

    getAllUser()

    const updateStudentById = async (id)=>{
        const newStudent = {
            student_name: "Nguyễn Văn N",  
        email: "nguyenvanN@example.com",  
        address: "654 Đường MNO, Quận 5, TP.HCM",  
        phone: "0123456790",  
        status: true,  
        created_at: "2023-10-05"  
        }
        const res = await axios.patch(`http://localhost:3000/students/${id}`, newStudent)
        getAllUser()
        
    }
</script>
<style >
    
</style>