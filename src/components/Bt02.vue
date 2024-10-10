<template >
    <div>
        <h1>Danh sách sinh viên</h1>
        <ul>
            <li v-for="student in students" :key="student.id">{{ student.student_name }}</li>
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
</script>
<style >
    
</style>