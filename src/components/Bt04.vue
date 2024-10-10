<template >
    <div>
        <h1>Xóa Student</h1>
        <ul>
            <li v-for="student in students" :key="student.id">{{ student.student_name }}
                <button @click="removeById(student.id)">removeById</button>
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

    const removeById = async (id)=>{
        const res = await axios.delete(`http://localhost:3000/students/${id}`)
        getAllUser()
        
    }
</script>
<style >
    
</style>