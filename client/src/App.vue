<script setup>
import { ref, onMounted } from 'vue'
const API = 'http://localhost:4000/api/items'
const items = ref([])
const form = ref({ name: '', description: '', price: '' })
const editId = ref(null)

async function load() {
  items.value = await fetch(API).then(r => r.json())
}

async function save() {
  if (editId.value) {
    await fetch(`${API}/${editId.value}`, {
      method: 'PUT', headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(form.value)
    })
    editId.value = null
  } else {
    await fetch(API, {
      method: 'POST', headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(form.value)
    })
  }
  form.value = { name: '', description: '', price: '' }
  load()
}

function startEdit(item) {
  editId.value = item.id
  form.value = { name: item.name, description: item.description, price: item.price }
}

async function remove(id) {
  await fetch(`${API}/${id}`, { method: 'DELETE' })
  load()
}

onMounted(load)
</script>

<template>
  <main>
    <h1>Items</h1>

    <form @submit.prevent="save">
      <input v-model="form.name" placeholder="Name" required />
      <input v-model="form.description" placeholder="Description" />
      <input v-model="form.price" placeholder="Price">
      <button type="submit">{{ editId ? 'Update' : 'Add' }}</button>
      <button v-if="editId" type="button" class="cancel" @click="editId = null; form = { name: '', description: '', price: '' }">
        Cancel
      </button>
    </form>

    <ul>
      <li v-for="item in items" :key="item.id">
        <div class="item-text">
          <div class="name-price">
            <span class="item-name">{{ item.name }}</span>
            <span class="item-price"> ₱ {{ item.price }}</span>
          </div>
        
          <span class="item-desc">{{ item.description }}</span>
          
        </div>
        <div class="item-actions">
          <button @click="startEdit(item)">Edit</button>
          <button class="del" @click="remove(item.id)">Delete</button>
        </div>
      </li>
    </ul>
  </main>
</template>

<style scoped>
* { box-sizing: border-box; }

main {
  background: #111;
  border: 1px solid #222;
  border-radius: 12px;
  width: 520px;
  padding: 24px;
  font-family: system-ui, sans-serif;
}

h1 {
  font-size: 15px;
  font-weight: 600;
  color: #50FF6A;
  margin: 0 0 20px;
  letter-spacing: 0.03em;
}

form {
  display: flex;
  gap: 8px;
  margin-bottom: 20px;
  flex-wrap: wrap;
}

input {
  flex: 1;
  min-width: 100px;
  background: #1a1a1a;
  border: 1px solid #2a2a2a;
  border-radius: 6px;
  color: #ddd;
  font-size: 13px;
  padding: 7px 10px;
  outline: none;
}

input:focus {
  border-color: #50FF6A;
}

input::placeholder {
  color: #444;
}

button {
  background: #50FF6A;
  color: #111;
  border: none;
  border-radius: 6px;
  font-size: 13px;
  font-weight: 600;
  padding: 7px 14px;
  cursor: pointer;
}

button:hover {
  background: #6effa0;
}

button.cancel {
  background: transparent;
  color: #555;
  border: 1px solid #2a2a2a;
}

button.cancel:hover {
  color: #999;
  background: transparent;
}

ul {
  list-style: none;
  padding: 0;
  margin: 0;
  display: flex;
  flex-direction: column;
  gap: 2px;
}

li {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 10px 12px;
  border-radius: 6px;
  background: #171717;
}

li:hover {
  background: #1d1d1d;
}

.item-text {
  display: flex;
  flex-direction: column;
  gap: 2px;
  min-width: 0;
}



.item-name {
  font-size: 13px;
  color: #ccc;
  font-weight: 500;
  margin-right: 20px;
}

.item-price{
  font-size: 12px;
  font-weight: 500;
  color: #F4C542;
}

.item-desc {
  font-size: 12px;
  color: #808080;
}

.item-actions {
  display: flex;
  gap: 6px;
  flex-shrink: 0;
  margin-left: 12px;
}

.item-actions button {
  background: transparent;
  color: #555;
  border: 1px solid #2a2a2a;
  font-weight: 400;
  padding: 4px 10px;
  font-size: 12px;
}

.item-actions button:hover {
  color: #ccc;
  background: #222;
}

.item-actions button.del:hover {
  color: #ff5f57;
  border-color: #3a1a1a;
  background: #1e1111;
}

@media (max-width: 500px) {
  main { width: 100%; border-radius: 0; }
}
</style>