<template >
    <div>
        <h1>Lấy thông tin student</h1>
        <ul>
            <li v-for="student in students" :key="student.id">{{ student.student_name }}
                <button @click="getStudentById(student.id)">get Student</button>
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

    const getStudentById = async (id)=>{
        const res = await axios.get(`http://localhost:3000/students/${id}`)
        if(res.data){
            console.log(res.data);
        }else{
            console.log("Không tìm thấy bản ghi");
            
        }
        
    }
</script>
<style >
    
</style>