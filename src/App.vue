<template>
  <div>
    <h1>API axios</h1>
    <ul>
      <li v-for="user in users" :key="user.id">{{ user.name }}
        <button @click="deleteUser(user.id)">delete</button>
        <button @click="UpdataUser(user.id)">Update</button>
      </li>
    </ul>
  <button @click="addUser">add</button>
  <select @change="handleChange"> 
    <option value="asc">A-Z</option>
    <option value="desc">Z-A</option>
  </select>
  <input type="text" v-model="inputValue">
  <button @click="handleSearch">tìm kiếm</button>

  <!-- <Bt02></Bt02>
  <Bt03></Bt03>
  <Bt04></Bt04>
  <Bt05></Bt05>
  <Bt06></Bt06> -->
  <Bt07></Bt07>
  </div>
  
</template>

<script setup>
import axios from 'axios';
import { ref } from 'vue';
import Bt02 from './components/Bt02.vue';
import Bt03 from './components/Bt03.vue';
import Bt04 from './components/Bt04.vue';
import Bt05 from './components/Bt05.vue';
import Bt06 from './components/Bt06.vue';
import Bt07 from './components/bt07_08_09_10_11_12/Bt07.vue';

const users = ref([])
const inputValue = ref("")

// lấy 
const getUsers = async ()=>{
  let res = await axios.get("http://localhost:3000/user")
  users.value = res.data
}

getUsers()

const addUser = async ()=>{
  const newUser = {
    name:"Văn A"
  }
  let res = await axios.post("http://localhost:3000/user", newUser)
  getUsers();
}
const deleteUser = async (id)=>{
  
  let res = await axios.delete(`http://localhost:3000/user/${id}`)
  getUsers();
}

const UpdataUser = async (id)=>{

  const updateName = {
    name:"Văn B"
  }

  let res = await axios.patch(`http://localhost:3000/user/${id}`, updateName)

  getUsers()
}

const handleChange = async (event)=>{
  let res = await axios.get(`http://localhost:3000/user?_sort=name&_order=${event.target.value}`)
  users.value = res.data
}

const handleSearch = async ()=>{
  let res = await axios.get(`http://localhost:3000/user?name_like=${inputValue.value.toLowerCase()}`)

  users.value = res.data
}


</script>

<style>

</style>